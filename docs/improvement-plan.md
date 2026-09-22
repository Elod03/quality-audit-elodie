# Plan d'amélioration : QuickOrder V3.1

Voici ce que je proposerais de corriger, classé par priorité. Je me suis basée sur les 10 bugs trouvés
pendant la campagne (`bug-reports/`), en essayant d'estimer à chaque fois si le correctif est simple ou
demande plus de travail.

| # | Amélioration | Justification | Priorité | Effort estimé |
|---|---|---|---|---|
| 1 | Exiger une authentification propre à l'utilisateur pour toute action sur son compte via l'API (et non une clé statique partagée) | Corrige BUG-03 : n'importe qui peut aujourd'hui lire/créer/annuler des commandes d'un compte réel sans identifiant | Très haute | Moyen (introduire un token utilisateur en plus de la clé applicative) |
| 2 | Vérifier le stock réel avant toute création de commande (web et API), et rendre `available` dépendant de `stock > 0` | Corrige BUG-07 : le stock devient négatif, aucune protection contre la survente | Haute | Faible (ajout d'une vérification avant `add_order_items`) |
| 3 | Bloquer la création de commande si `restaurant.is_open` est faux | Corrige BUG-06 : des commandes sont acceptées sur des restaurants fermés, impossibles à honorer | Haute | Faible |
| 4 | Corriger la logique de paiement PayPal pour ne créer qu'une seule commande | Corrige BUG-05 : double commande silencieuse, double décrément de stock, risque de double facturation | Très haute | Faible (bug de duplication dans `checkout()`/`create_order_api()`) |
| 5 | Valider que `cash_received >= total` avant de confirmer un paiement en espèces | Corrige BUG-04 : une commande peut être validée avec un paiement manifestement insuffisant | Haute | Faible |
| 6 | Introduire une machine à états explicite pour le statut de commande (transitions autorisées uniquement dans le sens `confirmed → preparing → out_for_delivery → delivered`, plus `cancelled` depuis les premiers statuts) | Corrige BUG-08 : un statut peut revenir en arrière sans contrôle, cassant la confiance dans le suivi de commande | Moyenne | Moyen |
| 7 | Corriger l'opérateur de comparaison du filtre "frais de livraison maximum" (`<=` au lieu de `>=`) | Corrige BUG-02 : le filtre retourne l'exact inverse de ce qui est demandé | Haute | Très faible |
| 8 | Aligner le taux de réduction réel des coupons sur le texte promotionnel affiché (ex. ATLAS15) et ajouter une vérification automatisée coupon ↔ promo_text | Corrige BUG-01 : promesse commerciale non tenue, risque de réclamation client | Moyenne | Faible |
| 9 | Restreindre le dépôt d'avis aux utilisateurs ayant au moins une commande livrée dans le restaurant concerné | Corrige BUG-09 : des avis peuvent être publiés sans expérience réelle, fragilisant la fiabilité des notes | Moyenne | Faible |
| 10 | Étendre le filtre de recherche restaurant/produit à la description (pas seulement au nom) | Améliore la pertinence de la recherche, relevé lors de l'analyse de code (`main.py`, `api.py`) | Basse | Faible |
| 11 | Ajouter une limitation de tentatives de connexion (rate-limiting / lockout) | Réduit le risque de force brute sur les comptes, absent aujourd'hui sur `/login` | Moyenne | Moyen |
| 12 | Exiger une adresse complète (rue, code postal, ville) avant de pouvoir confirmer une commande | Corrige BUG-10 : l'adresse créée automatiquement à l'inscription est vide et n'importe quelle commande peut être payée sans adresse livrable | Haute | Faible (appliquer la même validation qu'`profile.add_address` au moment du checkout) |

## Ce que je ferais en premier

Si je devais choisir les corrections à faire avant tout le reste, ce serait #1, #4, #2, #3, #5 et #12 :
elles touchent toutes au cœur transactionnel (authentification, paiement, stock, adresse de livraison),
et ce sont aussi celles qui ont le plus petit rapport effort/impact : la plupart se corrigent en quelques
lignes. Les améliorations #6 à #9 viennent ensuite, elles concernent plutôt la fiabilité des données
affichées (statut, avis, promotions). #10 et #11 sont plus du confort, pas urgentes mais pas inutiles
non plus.
