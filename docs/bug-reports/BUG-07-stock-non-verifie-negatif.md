# BUG-07 : Le stock n'est jamais vérifié : un produit en rupture reste commandable et le stock devient négatif

## Contexte
En parcourant la fiche du restaurant Couscous Atlas, j'ai vu le produit "Tajine Légumes" (id=22) affiché
avec la mention "HORS STOCK" (stock=0), mais le bouton "Ajouter" du panier restait cliquable juste à
côté. Ça m'a mis la puce à l'oreille : soit c'est juste visuel, soit le serveur ne vérifie rien non plus.
J'ai testé, et c'est la deuxième option.

## Préconditions
Produit id=22, stock initial = 0, `available: true`, restaurant ouvert (pour isoler ce bug de BUG-06).

## Étapes de reproduction
1. Constater sur `GET /api/products/22` : `"stock": 0`.
2. Commander 2 unités : `POST /api/orders` avec `{"items":[{"product_id":22,"quantity":2}]}`.
3. Recontrôler le stock : `GET /api/products/22`.

## Résultat attendu
La commande devrait être refusée (stock insuffisant), et le stock ne devrait jamais passer sous 0.

## Résultat obtenu
- Commande créée avec succès (HTTP 201, commande #9, total 30,79 €).
- Stock du produit après commande : `"stock": -2`.

Le stock n'est décrémenté par aucune vérification de disponibilité réelle (seul le champ `available`,
indépendant du stock, est contrôlé).

## Preuve
Requête/réponse ci-dessus, exécutée le 21/09/2026. Stock confirmé négatif via
`GET /api/products/22` → `{"stock": -2, ...}`.

## Sévérité
Haute. C'est de la survente structurelle : à chaque commande passée sur un produit épuisé, le stock
s'enfonce un peu plus dans le négatif, sans qu'aucune alerte ne se déclenche nulle part.

## Priorité
Haute. Il faut vérifier `product.stock >= quantity` avant toute création de commande (web et API), et
idéalement rendre `available` dépendant de `stock > 0` plutôt que d'avoir deux champs indépendants qui
peuvent se contredire comme ici.
