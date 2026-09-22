# Rapport d'exécution de la campagne : QuickOrder V3.1

## Comment j'ai testé

- Date : 21-22/09/2026
- Environnement : instance locale lancée via `docker compose up --build`, base réinitialisée par
  `seed.py` au démarrage.
- Méthode : j'ai commencé par lire le code source pour repérer les zones à risque (ça m'a fait gagner
  du temps plutôt que de tester au hasard), puis j'ai exécuté chaque cas pour de vrai contre l'API et les
  pages web (curl + requêtes directes), avec quelques vérifications en base SQLite quand l'info n'était
  pas visible depuis l'interface (ex : stock réel d'un produit).
- Je n'ai pas eu de navigateur piloté sous la main pour cette partie de la campagne (pas de
  captures/vidéos), donc les parcours UI ont surtout été vérifiés via le HTML rendu et l'API qui est
  derrière, ce qui couvre la grosse majorité de la logique métier, mais pas le rendu visuel pur. Une
  repasse manuelle en navigateur reste utile pour affiner le rendu visuel, mais elle n'était pas
  bloquante pour cette campagne.

## Résultats globaux

| Statut | Nombre | % |
|---|---|---|
| Pass | 17 | 63 % |
| Fail | 10 | 37 % |
| Blocked | 0 | 0 % |
| Skipped | 0 | 0 % |
| **Total** | **27** | 100 % |

## Par catégorie

| Catégorie | Cas | Pass | Fail |
|---|---|---|---|
| Nominaux | 6 | 5 | 1 |
| Limites | 6 | 6 | 0 |
| Erreurs | 6 | 3 | 3 |
| Règles métier | 9 | 3 | 6 |

Ce qui me frappe en regardant ce tableau : les cas *Limites* passent tous, et la plupart des *Nominaux*
aussi, les validations de base (bornes de quantité, recherche, filtres, inscription) sont plutôt bien
faites. Les échecs se concentrent presque tous sur les règles métier transactionnelles (paiement, stock,
statut, coupon) et sur le contrôle d'accès. C'est exactement ce que ma matrice de risques prévoyait avant
que je commence (R1, R2/R3, R6 dans `risk-matrix.md`), plutôt satisfaisant de voir que l'analyse de
risque initiale tenait la route.

## Ce qui m'inquiète le plus

1. **Le contrôle d'accès sur l'API** (BUG-03), c'est celui qui m'a le plus surpris. N'importe qui avec
   la clé publique documentée dans le README peut agir sur le compte réel d'Alice sans jamais se
   connecter. Aucun mot de passe demandé, aucune session.
2. **La duplication PayPal** (BUG-05), une commande PayPal en crée deux en silence. Facile à louper si
   on ne compare pas le nombre de commandes avant/après.
3. **La survente** (BUG-06, BUG-07), restaurant fermé ou produit à stock 0, ça passe quand même, et le
   stock finit négatif.
4. **Le paiement espèces non vérifié** (BUG-04), un client pourrait payer 1€ pour une commande à 15€.
5. **Des promesses non tenues** (BUG-01, BUG-02), le coupon ATLAS15 annonce -15% mais applique -10%, et
   le filtre "frais de livraison max" fait l'inverse de ce qu'il promet.
6. **Des données pas fiables** (BUG-08, BUG-09), un statut de commande peut revenir en arrière, un avis
   peut être posté sans avoir jamais commandé.
7. **Une commande confirmée sans adresse valide** (BUG-10), trouvé en préparant les tests utilisateurs :
   l'adresse créée automatiquement à l'inscription est vide, et rien n'empêche de payer avec.

## Mon avis : Go / No-Go

**No-Go** en l'état.

Aucun de ces bugs ne rend l'appli inutilisable, on peut toujours commander, payer, suivre sa commande.
Mais plusieurs touchent directement à l'argent (paiement espèces, doublon PayPal, survente, commande sans
adresse livrable) ou à la sécurité (accès à un vrai compte client sans authentification), et ça, je ne le
laisserais pas passer en prod. Je recommande une nouvelle campagne ciblée sur BUG-01 à BUG-10 une fois les
corrections faites, avant de redonner un avis.
