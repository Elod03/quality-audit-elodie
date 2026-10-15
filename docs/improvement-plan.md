# Plan d’amélioration : QuickOrder V3.1

Les améliorations sont classées de la plus urgente à la moins urgente. La priorité tient compte de la gravité
du problème (sécurité, argent, livraison) et du fait qu’il soit visible ou non par le client.

| # | Amélioration | Origine | Priorité | Justification |
|---:|---|---|---|---|
| 1 | Exiger une authentification propre à chaque utilisateur sur l’API. | BUG-02 | Très haute | Avec la seule clé publique, n’importe qui peut lire, créer ou annuler les commandes d’Alice. C’est le problème le plus grave de la campagne. |
| 2 | Ne créer qu’une seule commande par paiement PayPal. | BUG-04 | Très haute | Chaque paiement PayPal crée une commande en double invisible pour le client : double facturation possible et stock décrémenté deux fois. |
| 3 | Refuser un paiement en espèces inférieur au total. | BUG-03 | Haute | Une commande payée 1 € pour 14,89 € est marquée « paid » : perte d’argent directe. La correction est une simple vérification côté serveur. |
| 4 | Vérifier le stock avant d’accepter une commande. | BUG-06 | Haute | Un produit affiché « HORS STOCK » reste commandable et le stock devient négatif : le restaurant reçoit des commandes qu’il ne peut pas servir. |
| 5 | Bloquer les commandes quand le restaurant est fermé. | BUG-05 | Haute | Une commande est acceptée et payée chez Night Sushi alors que le restaurant affiche « Fermé » : elle ne pourra être ni préparée ni livrée. |
| 6 | Exiger une adresse complète au checkout. | BUG-07 | Haute | Un nouveau compte peut commander avec l’adresse « Domicile » vide créée à l’inscription : la commande est impossible à livrer. |
| 7 | Appliquer le minimum de commande du restaurant. | BUG-08 | Haute | Le minimum est affiché mais jamais vérifié (commande à 3,50 € pour un minimum de 20 €) : la règle annoncée au client n’est pas respectée. |
| 8 | Aligner le coupon ATLAS15 sur la promotion annoncée et afficher la réduction appliquée. | BUG-01, tests utilisateurs (S3) | Moyenne | Le coupon donne 10 % au lieu des 15 % annoncés, et aucune des deux participantes ne l’a remarqué : le client paie plus que prévu sans le savoir. |
| 9 | Rendre les filtres de recherche plus visibles. | Tests utilisateurs (S1) | Basse | Une participante n’a pas trouvé les filtres et a parcouru les restaurants un par un. Gêne réelle, mais sans risque financier. |

Les sept premières corrections bloquent la mise en production (voir la décision No-Go dans
`execution-report.md`). Les deux dernières améliorent l’expérience client et peuvent suivre.
