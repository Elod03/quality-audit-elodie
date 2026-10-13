# Plan d’amélioration : QuickOrder V3.1

Les améliorations sont limitées aux six actions prioritaires demandées dans la consigne.

| # | Amélioration | Bug concerné | Priorité |
|---:|---|---|---|
| 1 | Ajouter une authentification propre à chaque utilisateur de l’API. | BUG-03 | Très haute |
| 2 | Vérifier le stock réel avant d’accepter une commande. | BUG-07 | Haute |
| 3 | Bloquer les commandes lorsque le restaurant est fermé. | BUG-06 | Haute |
| 4 | Empêcher PayPal de créer deux commandes pour un seul paiement. | BUG-05 | Très haute |
| 5 | Refuser un paiement en espèces inférieur au total. | BUG-04 | Haute |
| 6 | Vérifier l’adresse complète et le minimum de commande avant confirmation. | BUG-10, BUG-11 | Haute |

Ces corrections concernent les risques les plus importants : sécurité, argent, stock et livraison.
