# BUG-04 : Un paiement en espèces inférieur au montant dû est accepté

## Contexte
Le mode de paiement "espèces" permet au client d'indiquer le montant remis (`cash_received`).
L'application doit
calculer la monnaie à rendre en s'assurant que le montant remis couvre au moins le total de la commande.

## Préconditions
Produit "Classic Burger" au panier, total attendu de la commande : 14,89 €.

## Étapes de reproduction
1. `POST /api/orders` avec `payment_method: "cash"` et `cash_received: 1`.

## Résultat attendu
La commande devrait être refusée (montant insuffisant), ou à défaut le système devrait demander un
complément avant de valider le paiement.

## Résultat obtenu
```json
{
  "cash_received": 1.0,
  "change_due": -13.89,
  "payment_method": "cash",
  "payment_status": "paid",
  "status": "confirmed",
  "total": 14.89
}
```
La commande est créée et marquée `payment_status: paid` malgré un rendu de monnaie négatif de -13,89 €,
ce qui est incohérent (on ne peut pas rendre une monnaie négative).

## Preuve
Requête/réponse ci-dessus, exécutée le 21/09/2026 (commande #3).

## Sévérité
Haute. En conditions réelles ça reviendrait à valider une commande sans que le client ait payé le bon
montant, impact financier direct, pas juste un détail d'affichage.

## Priorité
Haute. Il suffit d'ajouter une validation côté serveur qui rejette le paiement si
`cash_received < total`.
