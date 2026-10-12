# BUG-03 : L'API permet de lire, créer et annuler des commandes sans aucune authentification (usurpation du compte Alice)

## Contexte
L'analyse de l'API montre que tous les endpoints `/api/*` exigent
uniquement l'en-tête `X-API-Key: quickorder-demo-key`, une clé **statique et documentée publiquement**
dans le README du projet, sans aucun rapport avec la session ou le compte d'un utilisateur connecté.
En pratique, lorsque aucune session utilisateur n'est fournie, l'API
agit silencieusement **au nom du compte client "Alice" par défaut**, sans jamais demander email ou mot
de passe.

## Préconditions
- Aucun compte, aucun mot de passe, aucune session : uniquement la clé publique `quickorder-demo-key`.

## Étapes de reproduction
1. Lister les commandes existantes du compte par défaut : `GET /api/orders` (clé API seule).
2. Créer une nouvelle commande sur ce même compte : `POST /api/orders` avec un panier valide.
3. Annuler une commande existante de ce compte : `POST /api/orders/{id}/cancel`.
Aucune de ces requêtes ne contient d'identifiant de session, de token utilisateur ou de mot de passe.

## Résultat attendu
Sans preuve d'identité (login, session, token propre à l'utilisateur), ces opérations devraient être
refusées (401/403), ou à défaut l'API ne devrait pas être en mesure d'agir "en tant qu'Alice" sans qu'Alice
se soit authentifiée elle-même.

## Résultat obtenu
- `GET /api/orders` (aucune session) renvoie directement l'historique réel d'Alice (1 commande initiale).
- `POST /api/orders` crée avec succès une commande n°2 sur le compte d'Alice.
- `POST /api/orders/2/cancel` annule cette commande avec succès, statut passé à `cancelled`.

Toutes ces actions ont réussi avec pour seul justificatif la clé de démonstration publique, identique
pour tous les utilisateurs et documentée en clair dans le README.

## Preuve
Séquence exécutée le 21/09/2026 :
```
GET /api/orders           → {"count":1, items:[{"id":1,...}]}
POST /api/orders {...}    → 201 {"id":2,"status":"confirmed",...}
POST /api/orders/2/cancel → 200 {"id":2,"status":"cancelled",...}
```
Aucune de ces 3 requêtes ne contenait de cookie de session ni d'identifiant lié à Alice.

## Sévérité
Critique. Le contrôle d'accès est défaillant : toute personne connaissant la clé publique (documentée
dans le dépôt lui-même) peut
consulter, créer ou annuler des commandes pour un vrai compte client, sans jamais prouver qui il est.

## Priorité
Très haute, à corriger avant toute mise en prod. L'API doit exiger une authentification propre à chaque
utilisateur (token ou session liée au compte) ; la clé `X-API-Key` ne devrait servir qu'à identifier
l'application appelante, pas l'utilisateur final derrière.
