# BUG-02 : Le filtre "frais de livraison maximum" retourne l'inverse de ce qui est demandé

## Contexte
Lors du test des filtres de la page d'accueil, le filtre des frais de livraison a retourné un résultat
inattendu. La page propose un filtre "livraison max X€"
(`GET /?max_delivery=X`), et l'intention est évidemment d'afficher les restaurants dont les frais de
livraison sont **inférieurs ou égaux** à X.

## Préconditions
Aucune (page publique, pas de connexion nécessaire).

## Étapes de reproduction
1. Ouvrir `http://127.0.0.1:5000/?max_delivery=2.50`.
2. Relever les restaurants retournés et leurs frais de livraison.

## Résultat attendu
Seuls les restaurants avec frais de livraison ≤ 2,50 € apparaissent (ex : Green Kitchen 1,99 €,
Pizza Roma 2,49 €).

## Résultat obtenu
Les restaurants retournés ont tous des frais de livraison **≥** 2,50 € :
- Couscous Atlas : 2,99 €
- Tokyo Bowl : 3,49 €
- Burger Factory : 2,99 €
- Night Sushi : 2,99 €

Green Kitchen (1,99 €) et Pizza Roma (2,49 €), qui respectent pourtant le critère demandé, sont exclus.

## Preuve
```
curl "http://127.0.0.1:5000/?max_delivery=2.50"
```
→ fragments HTML retournés le 21/09/2026 : "Couscous Atlas 2.99 €", "Tokyo Bowl 3.49 €",
"Burger Factory 2.99 €", "Night Sushi 2.99 €" (aucun restaurant ≤ 2,50 € affiché).

## Sévérité
Moyenne. Il n'y a pas de risque de sécurité ou de perte financière directe, mais la fonctionnalité
retourne l'inverse du résultat attendu.

## Priorité
Haute : le correctif consiste à remplacer l'opérateur de comparaison dans la requête SQL (`>=` → `<=`).
