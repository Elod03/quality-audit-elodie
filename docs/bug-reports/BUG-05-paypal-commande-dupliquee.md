# BUG-05 : Un paiement PayPal crée deux commandes identiques

## Contexte
Celui-là, je ne l'ai vu qu'en comparant des compteurs avant/après, rien dans la réponse de l'API ne le
laissait deviner. Lors du passage d'une commande avec le mode de paiement PayPal, une seconde commande
strictement identique (mêmes articles, même montant) est créée silencieusement, sans que le client n'en
soit informé ni que l'API n'en fasse mention dans sa réponse.

## Préconditions
Panier valide (1x Classic Burger).

## Étapes de reproduction
1. Relever le nombre total de commandes du compte : `GET /api/orders` → `count`.
2. Passer une commande avec `payment_method: "paypal"` via `POST /api/orders`.
3. Relever à nouveau le nombre total de commandes.

## Résultat attendu
Une seule commande créée : le compteur doit augmenter de 1.

## Résultat obtenu
- Avant : `"count": 3`
- Réponse de la requête POST : une seule commande visible dans la réponse (id=4).
- Après : `"count": 5` (soit +2, alors qu'une seule commande a été demandée et qu'une seule est renvoyée
  au client).

Une commande fantôme (id=5, non retournée par l'API) a été créée en parallèle, avec le même panier, le
même montant, et le même décrément de stock produit appliqué deux fois.

## Preuve
Séquence exécutée le 21/09/2026 : count 3 → POST paypal (réponse : 1 commande id=4) → count 5.

## Sévérité
Je le classe en critique : double facturation potentielle côté paiement réel, double décrément de stock
(ça aggrave aussi BUG-07), et une commande "invisible" qui existe sur le compte du client sans qu'il le
sache.

## Priorité
Très haute. Il faut corriger la logique de création de commande pour PayPal afin qu'elle ne crée qu'un
seul enregistrement.
