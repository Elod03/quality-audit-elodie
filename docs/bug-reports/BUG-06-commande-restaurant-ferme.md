# BUG-06 : Il est possible de commander auprès d'un restaurant fermé

## Contexte
En listant les restaurants via l'API, j'ai repéré "Night Sushi" (id=6), marqué `is_open: false` ("Fermé
aujourd'hui"), clairement un restaurant de test prévu pour ce genre de cas. Logiquement, un restaurant
fermé ne peut pas préparer ni livrer de commande, donc j'ai voulu voir ce qui se passait si on essayait
quand même.

## Préconditions
Restaurant Night Sushi fermé, produit "Late Sushi Box" (id=24) disponible et en stock (100), donc sans
lien avec un problème de stock.

## Étapes de reproduction
1. Vérifier l'état du restaurant : `GET /api/restaurants/6` → `is_open: false`.
2. Passer commande sur ce restaurant : `POST /api/orders` avec `{"items":[{"product_id":24,"quantity":1}]}`.

## Résultat attendu
La commande devrait être refusée (ex. 409) tant que le restaurant est fermé.

## Résultat obtenu
```json
{
  "id": 8,
  "restaurant_id": 6,
  "status": "confirmed",
  "payment_status": "paid",
  "total": 16.99
}
```
Commande créée avec succès (HTTP 201) malgré le restaurant fermé.

## Preuve
Requête/réponse ci-dessus, exécutée le 21/09/2026 (commande #8).

Même constat dans le navigateur (22/09/2026, Marina) : le badge « FERMÉ » est affiché, et pourtant
l'ajout au panier puis la commande passent :
![Restaurant fermé, ajout au panier accepté](../preuves/ui-restaurant-ferme-ajout-panier.png)

## Sévérité
Haute. Une commande peut être acceptée et payée alors que littéralement personne ne peut la préparer ni
la livrer. Impact direct sur l'expérience client et sur l'exploitation côté restaurant.

## Priorité
Haute. Il manque juste une vérification `restaurant.is_open` avant la création de la commande, à
ajouter côté web et côté API.
