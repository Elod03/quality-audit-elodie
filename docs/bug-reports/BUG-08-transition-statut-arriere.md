# BUG-08 : L'administrateur peut faire revenir une commande à un statut antérieur

## Contexte
En regardant l'espace admin, j'ai vu que le statut d'une commande se change via un simple menu déroulant
parmi `confirmed, preparing, out_for_delivery, delivered, cancelled`, sans aucune règle de transition
visible. Je me suis demandé si on pouvait revenir en arrière (ex. repasser une commande "livrée" à
"confirmée"), ce qui n'a normalement aucun sens.

## Préconditions
- Session administrateur (`admin@quickorder.local`).
- Commande #1 au statut `delivered`.

## Étapes de reproduction
1. Vérifier le statut initial : `GET /api/orders/1` → `"status": "delivered"`.
2. En tant qu'admin, appeler `POST /admin/orders/1/status` avec `status=confirmed`.
3. Revérifier le statut.

## Résultat attendu
La transition `delivered → confirmed` devrait être refusée (retour en arrière incohérent).

## Résultat obtenu
Statut modifié avec succès : `"status": "confirmed"` après l'action admin (redirection 302 avec message
"Statut mis à jour.").

## Preuve
Séquence exécutée le 21/09/2026 : statut avant = `delivered`, action admin `status=confirmed` acceptée
(HTTP 302, flash succès), statut après = `confirmed`.

## Sévérité
Moyenne. Pas de risque financier direct, mais ça casse la fiabilité du suivi de commande côté client et
peut fausser les statistiques d'exploitation si un statut "livré" redevient "en attente" sans raison.

## Priorité
Moyenne. Je proposerais une machine à états explicite qui limite les transitions autorisées, avec
idéalement un historique des changements de statut pour garder une trace.
