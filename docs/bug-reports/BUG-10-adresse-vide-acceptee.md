# BUG-10 : Une commande peut être confirmée avec une adresse de livraison vide

## Contexte
En s'inscrivant, chaque nouvel utilisateur reçoit automatiquement une adresse "Domicile" créée avec des
champs **vides** (`street=""`, `postal_code=""`, `city=""`). Cette adresse est ensuite proposée par
défaut au checkout, sans qu'aucun contrôle n'empêche de commander avec.

## Préconditions
Compte fraîchement créé (aucune adresse renseignée manuellement dans le profil), panier non vide.

## Étapes de reproduction
1. S'inscrire avec un nouvel email via `POST /register`.
2. Ajouter un produit au panier.
3. Ouvrir `/checkout` : l'adresse proposée s'affiche littéralement "Domicile — ,  " dans le menu
   déroulant (rue, code postal et ville vides, d'où les virgules collées).
4. Confirmer la commande avec cette adresse (`POST /checkout` avec `address_id` de cette adresse vide).

## Résultat attendu
Le checkout devrait bloquer la confirmation tant que l'adresse n'a pas de rue, code postal et ville
renseignés, comme c'est déjà le cas pour l'ajout manuel d'une adresse dans le profil, qui exige tous les
champs.

## Résultat obtenu
La commande est confirmée sans erreur (redirection vers `/orders/2`, aucun message de blocage). Vérifié
en base :
```
Address(id=3, street='', postal_code='', city='', label='Domicile')
```
associée à une commande au statut `confirmed`.

## Preuve
Séquence exécutée le 22/09/2026 (base Docker) : inscription → adresse "Domicile — ,  " visible au
checkout → confirmation acceptée (HTTP 302 vers la commande) → requête SQL confirmant les 3 champs vides
sur l'adresse utilisée.

## Sévérité
Haute. Une commande peut être payée et confirmée alors qu'elle est **physiquement impossible à
livrer**, faute d'adresse. C'est le genre de problème qui ne se voit qu'après coup, quand le livreur n'a
nulle part où aller.

## Priorité
Haute. Soit on exige que l'adresse par défaut soit complétée avant tout premier checkout, soit on
applique au checkout la même validation stricte que celle déjà utilisée pour l'ajout manuel d'une adresse
dans le profil (`profile.add_address`).
