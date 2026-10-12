# Stratégie de test : QuickOrder V3.1

## Contexte

QuickOrder est une appli pédagogique de commande de repas (Flask / Flask-SQLAlchemy / SQLite). Le brief
me demande de l'auditer avant une hypothétique mise en prod, sans toucher au code pendant la campagne
initiale, donc je ne peux jouer qu'avec ce que l'appli expose réellement : le site, l'API, et un peu de
base de données pour vérifier certains résultats.

Environnement utilisé pour toute la campagne :
- Application : `http://127.0.0.1:5000` (lancée via Docker Compose)
- API REST : `http://127.0.0.1:5000/api`, clé `X-API-Key: quickorder-demo-key`
- Swagger UI : `http://127.0.0.1:5000/docs/`
- Comptes fournis : `alice@example.com` / `bob@example.com` (clients), `admin@quickorder.local` (admin)

## Ce que je teste, et ce que je ne teste pas

Je me concentre sur le fonctionnel et les règles métier, parce que c'est là que se cache la plupart des
bugs sur ce genre d'appli (paiement, panier, coupons...). Je laisse de côté :
- la charge/performance (pas pertinent sur une appli de démo avec 6 restaurants),
- la compatibilité multi-navigateurs poussée,
- un vrai audit sécurité (pentest, fuzzing), même si je relève quelques problèmes de contrôle d'accès
  en testant normalement,
- Docker/déploiement en tant que tel.

Dans le périmètre : inscription/connexion, recherche et filtres restaurants, panier (y compris la règle
mono-restaurant), coupons, checkout et paiement simulé, suivi et annulation de commande, avis, espace
admin, et l'API dans son ensemble.

## Les parcours qui comptent le plus

| # | Parcours | Pourquoi j'y fais attention |
|---|---|---|
| P1 | Inscription → connexion | Sans ça, rien d'autre n'est testable |
| P2 | Recherche/filtre restaurant | Premier contact avec l'appli, doit être fiable |
| P3 | Panier mono-restaurant | Règle métier simple mais facile à contourner si mal codée |
| P4 | Coupon appliqué au checkout | Beaucoup de contraintes (minimum, plafond, expiration) = beaucoup de façons de se planter |
| P5 | Checkout → paiement → commande créée | Le cœur transactionnel, l'endroit où une erreur coûte de l'argent |
| P6 | Suivi et annulation | Impacte la confiance client si le statut ment |
| P7 | Accès aux commandes d'un autre utilisateur | Risque de sécurité classique (IDOR) |
| P8 | Séparation client / admin | Risque de sécurité si un client accède aux fonctions admin |
| P9 | Avis et notes | Doivent rester fiables puisqu'affichés publiquement |

## Types de tests

- Fonctionnel sur l'UI (navigation, formulaires)
- Fonctionnel sur l'API (Swagger + requêtes directes)
- Tests aux limites (bornes de quantité, montants, dates)
- Tests d'erreur (données invalides ou manquantes)
- Tests de règles métier (coupons, mono-restaurant, statuts)
- Contrôle d'accès (IDOR, élévation de privilège)
- Un peu d'exploratoire autour des zones que je sentais risquées

## Priorisation

Détaillée dans `risk-matrix.md`. En résumé : coupons, contrôle d'accès et logique panier/commande sont
les zones où j'ai mis le plus de cas de test, parce que ce sont celles où une erreur a le plus
d'impact (argent ou confidentialité).

## Quand est-ce que j'arrête

Quand les 44 cas (27 d'Elodie, 17 de Marina) sont exécutés et documentés, que tous les bugs bloquants
trouvés sont reproduits proprement, et qu'on a un avis Go/No-Go justifiable avec des preuves plutôt que
"je pense que".
