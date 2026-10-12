# BUG-09 : Un avis peut être laissé sans avoir jamais commandé dans le restaurant

## Contexte
Ce bug a été trouvé en fin de campagne lors de la vérification des fonctionnalités secondaires. La fiche
restaurant propose un formulaire "Laisser un avis" (note + commentaire) accessible à tout
utilisateur connecté, sans aucune vérification qu'il a effectivement commandé dans ce restaurant.

## Préconditions
Compte `admin@quickorder.local` connecté, n'ayant **jamais** passé de commande auprès du restaurant
"Couscous Atlas" (id=5).

## Étapes de reproduction
1. Vérifier en base qu'aucune commande de ce compte n'existe pour le restaurant 5.
2. Soumettre un avis : `POST /restaurant/5/review` avec `rating=1` et un commentaire.
3. Requêter la base pour confirmer la création de l'avis.

## Résultat attendu
L'avis devrait être refusé (ou au minimum signalé comme non vérifié) tant que l'utilisateur n'a pas de
commande complétée pour ce restaurant.

## Résultat obtenu
L'avis est enregistré sans restriction :
```
(3, 3, 'admin@quickorder.local', 5, 1, "Je n'ai jamais commande ici mais je mets 1 etoile (avis QA non verifie).")
```
Requête de contrôle sur les commandes du même compte pour ce restaurant : `[]` (aucune commande).

## Preuve
Requête SQL exécutée le 21/09/2026 sur `quickorder.db` :
`SELECT ... FROM review WHERE restaurant_id=5` → avis présent ;
`SELECT ... FROM "order" WHERE user=admin AND restaurant_id=5` → aucun résultat.

## Sévérité
Entre basse et moyenne : le problème n'impacte pas directement les transactions, mais il fausse la
fiabilité des notes et des avis affichés aux futurs clients. Rien n'empêche actuellement un faux avis,
de complaisance ou malveillant.

## Priorité
Moyenne. Restreindre le formulaire d'avis aux utilisateurs ayant au moins une commande `delivered` pour
le restaurant concerné réglerait le problème simplement.
