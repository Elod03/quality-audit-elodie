# Stratégie de test : QuickOrder V3.1

## Contexte

QuickOrder est une application pédagogique de commande de repas (Flask / Flask-SQLAlchemy / SQLite). Le
brief demande de l'auditer avant une éventuelle mise en production, sans modifier le code pendant la
campagne initiale. Nous nous sommes donc appuyées sur ce que l'application expose réellement : le site,
l'API et quelques vérifications en base de données.

Environnement utilisé pour toute la campagne :
- Application : `http://127.0.0.1:5000` (lancée via Docker Compose)
- API REST : `http://127.0.0.1:5000/api`, clé `X-API-Key: quickorder-demo-key`
- Swagger UI : `http://127.0.0.1:5000/docs/`
- Comptes fournis : `alice@example.com` / `bob@example.com` (clients), `admin@quickorder.local` (admin)

## Ce que nous testons, et ce que nous ne testons pas

Nous nous sommes concentrées sur le fonctionnel et les règles métier, qui couvrent les risques principaux
de l'application (paiement, panier, coupons). Nous avons laissé de côté :
- la charge/performance (pas pertinent sur une appli de démo avec 6 restaurants),
- la compatibilité multi-navigateurs poussée,
- un audit de sécurité complet (pentest, fuzzing), même si nous relevons quelques problèmes de contrôle d'accès
  en testant normalement,
- Docker/déploiement en tant que tel.

Dans le périmètre : inscription/connexion, recherche et filtres restaurants, panier (y compris la règle
mono-restaurant), coupons, checkout et paiement simulé, suivi et annulation de commande, avis, espace
admin, et l'API dans son ensemble.

## Les parcours qui comptent le plus

| # | Parcours | Pourquoi nous y faisons attention |
|---|---|---|
| P1 | Inscription → connexion | Sans ce parcours, les autres fonctionnalités ne sont pas testables |
| P2 | Recherche/filtre restaurant | Premier contact avec l'application, ce parcours doit être fiable |
| P3 | Panier mono-restaurant | Règle métier simple, mais facile à contourner si elle est mal codée |
| P4 | Coupon appliqué au checkout | Beaucoup de contraintes (minimum, plafond, expiration) = beaucoup de façons de se planter |
| P5 | Checkout → paiement → commande créée | Cœur transactionnel, avec un risque financier direct en cas d'erreur |
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
- Tests exploratoires autour des zones jugées risquées

## Priorisation

Détaillée dans `risk-matrix.md`. Les coupons, le contrôle d'accès et la logique panier/commande ont reçu
le plus de cas de test, car une erreur dans ces zones peut avoir un impact sur l'argent ou la
confidentialité.

## Quand est-ce que j'arrête

Nous arrêtons la campagne lorsque les 24 cas minimum sont exécutés et
documentés, que les bugs bloquants sont reproduits proprement et qu'un avis Go/No-Go peut être justifié
par des preuves.
