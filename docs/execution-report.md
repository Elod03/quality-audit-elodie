# Rapport d’exécution de la campagne : QuickOrder V3.1

## Comment les tests ont été réalisés

La campagne a utilisé le navigateur, Swagger, l’API, les DevTools et quelques vérifications dans la base SQLite.
La base de test a été réinitialisée lorsque cela était nécessaire.

## Résultats globaux

| Statut | Nombre | Pourcentage |
|---|---:|---:|
| Pass | 16 | 67 % |
| Fail | 8 | 33 % |
| Blocked | 0 | 0 % |
| Skipped | 0 | 0 % |
| **Total** | **24** | **100 %** |

## Résultats par catégorie

| Catégorie | Cas | Pass | Fail |
|---|---:|---:|---:|
| Nominaux | 6 | 6 | 0 |
| Limites | 6 | 6 | 0 |
| Erreurs | 6 | 3 | 3 |
| Règles métier | 6 | 1 | 5 |

Les parcours classiques et les limites de quantité fonctionnent. Les échecs concernent surtout les règles métier,
le paiement, le stock et la sécurité.

## Bugs confirmés

- **BUG-01** : le coupon ATLAS15 applique 10 % au lieu de 15 %.
- **BUG-03** : l’API permet d’agir sur le compte d’Alice sans authentification personnelle.
- **BUG-04** : un paiement en espèces insuffisant est accepté.
- **BUG-05** : un paiement PayPal crée deux commandes.
- **BUG-06** : une commande est possible chez un restaurant fermé.
- **BUG-07** : un produit hors stock peut être commandé et le stock devient négatif.
- **BUG-10** : une commande peut être confirmée avec une adresse vide.
- **BUG-11** : le minimum de commande n’est pas appliqué.

## Décision finale

**No-Go.** L’application ne doit pas être mise en production avant la correction des problèmes liés à la sécurité,
au paiement, au stock, à l’adresse de livraison et aux règles de commande.

Une nouvelle campagne de 24 tests devra être exécutée après les corrections.
