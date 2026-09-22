# BUG-02 : Le filtre "frais de livraison maximum" retourne l'inverse de ce qui est demandé

## Contexte
En testant les filtres de la page d'accueil un par un (recherche, catégorie, note, livraison), celui du
frais de livraison m'a paru bizarre dès le premier essai. La page propose un filtre "livraison max X€"
(`GET /?max_delivery=X`), et l'intention est évidemment d'afficher les restaurants dont les frais de
livraison sont **inférieurs ou égaux** à X.

## Préconditions
Aucune (page publique, pas de connexion nécessaire).

## Étapes de reproduction
1. Ouvrir `http://127.0.0.1:5000/?max_delivery=2.50`.
2. Relever les restaurants retournés et leurs frais de livraison.

## Résultat attendu
Seuls les restaurants avec frais de livraison ≤ 2,50 € apparaissent (ex : Green Kitchen 1,99 €,
Pizza Roma 2,49 €).

## Résultat obtenu
Les restaurants retournés ont tous des frais de livraison **≥** 2,50 € :
- Couscous Atlas : 2,99 €
- Tokyo Bowl : 3,49 €
- Burger Factory : 2,99 €
- Night Sushi : 2,99 €

Green Kitchen (1,99 €) et Pizza Roma (2,49 €), qui respectent pourtant le critère demandé, sont exclus.

## Preuve
```
curl "http://127.0.0.1:5000/?max_delivery=2.50"
```
→ fragments HTML retournés le 21/09/2026 : "Couscous Atlas 2.99 €", "Tokyo Bowl 3.49 €",
"Burger Factory 2.99 €", "Night Sushi 2.99 €" (aucun restaurant ≤ 2,50 € affiché).

## Sévérité
Moyenne. Pas de risque sécurité ou financier, mais la fonctionnalité fait littéralement l'inverse de ce
qu'elle promet, ce qui est pire qu'une fonctionnalité absente.

## Priorité
Haute quand même : le correctif est un simple changement d'opérateur de comparaison dans la requête SQL
(`>=` → `<=`), donc autant le faire tout de suite.
