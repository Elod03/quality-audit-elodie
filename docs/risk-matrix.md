# Matrice des risques : QuickOrder V3.1

Avant même de toucher à l'appli, j'ai listé les endroits où je m'attendais à trouver des problèmes, en
me basant sur ce que je connais des applis de e-commerce/livraison (c'est souvent le paiement, les
coupons et les contrôles d'accès qui posent problème). Ça m'a servi à décider où mettre le plus de cas
de test.

Échelle : Probabilité (1=faible, 3=élevée) × Impact (1=mineur, 3=critique). Score = P × I.

| ID | Zone fonctionnelle | Risque | Probabilité | Impact | Score | Priorité test |
|---|---|---|---|---|---|---|
| R1 | Coupons | Mauvais calcul de réduction / plafond non respecté | 3 | 3 | 9 | Très haute |
| R2 | Commandes | Accès à une commande d'un autre utilisateur (IDOR) | 2 | 3 | 6 | Haute |
| R3 | Admin | Endpoint admin accessible à un compte client | 2 | 3 | 6 | Haute |
| R4 | Panier | Règle mono-restaurant contournable | 2 | 2 | 4 | Moyenne |
| R5 | Commandes | Annulation possible sur un statut qui ne devrait pas le permettre | 2 | 2 | 4 | Moyenne |
| R6 | Panier/Commande | Quantité hors bornes (0, négative, >20) acceptée | 2 | 2 | 4 | Moyenne |
| R7 | Auth | Mot de passe faible accepté / pas de contrôle de format email | 2 | 2 | 4 | Moyenne |
| R8 | Coupons | Coupon expiré accepté malgré tout | 1 | 3 | 3 | Moyenne |
| R9 | Produits | Produit indisponible commandable quand même | 1 | 2 | 2 | Basse |
| R10 | Avis | Note hors échelle (négative, >5) acceptée | 1 | 1 | 1 | Basse |
| R11 | UI | Incohérence d'affichage entre montant panier et total réel | 2 | 1 | 2 | Basse |
| R12 | API | Messages d'erreur incohérents entre endpoints (codes HTTP) | 2 | 1 | 2 | Basse |

## Ce que j'en retiens

Les scores les plus élevés se concentrent sur les **coupons**, le **contrôle d'accès** (IDOR/admin) et le
**panier/commande**, logique, ce sont les zones avec le plus de règles métier, donc le plus de façons
de se tromper, et un impact réel (argent ou confidentialité) si ça casse. Sans surprise, c'est là que j'ai
trouvé le plus de bugs une fois la campagne lancée (voir `execution-report.md`) : les scores les plus
hauts de cette matrice se sont plutôt bien vérifiés dans les faits.
