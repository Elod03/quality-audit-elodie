# Tests utilisateurs : QuickOrder V3.1

Séances réalisées le 12/10/2026 avec 2 participantes, sur 3 scénarios.

## Objectif

La campagne de test (`execution-report.md`) recense les dysfonctionnements. Les tests utilisateurs
permettent de vérifier lesquels sont visibles ou bloquants pour une personne qui découvre l'application.

## Protocole

- **Participants** : 2 personnes extérieures au projet, qui découvrent QuickOrder. P2 est une camarade de
  classe, différente des membres du binôme.
- **Environnement** : QuickOrder lancé en local (`http://127.0.0.1:5000`), base remise à zéro avec
  `python seed.py` avant chaque participant, pour que tout le monde parte du même état.
- **Durée** : environ 5 minutes par personne (3 scénarios).
- **Consigne donnée au participant** : « Je teste l'appli, pas toi. Il n'y a pas de mauvaise réponse.
  Pense à voix haute : dis ce que tu cherches et ce qui te surprend. »
- **Règles pour l'observatrice** : lire chaque scénario à voix haute, ne pas montrer où cliquer et ne pas
  aider. Au-delà de 2 minutes de blocage, noter « échec » et passer au scénario suivant. Noter les
  phrases exactes entre guillemets, au moment où elles sont dites.

## Participants

| ID | Profil (âge, études/métier) | Habitude des applis de livraison | Appareil / navigateur | Date |
|---|---|---|---|---|
| P1 | Kavisha, 21 ans | parfois | ordinateur (appli lancée en local) | 12/10/2026 |
| P2 | Camarade de classe, âge non renseigné | souvent | ordinateur (appli lancée en local) | 12/10/2026 |

## Scénarios

### S1 : Trouver une livraison pas chère

> « Tu veux te faire livrer sans payer cher la livraison. Trouve un restaurant dont la livraison coûte
> **moins de 2,50 €** et ajoute un plat à ton panier. »

- **Réussite** : le participant choisit un restaurant à 2,50 € de livraison ou moins (Green Kitchen
  1,99 €, Pizza Roma 2,49 €) et ajoute un plat.
- **À observer** : utilise-t-il le filtre « livraison max » ? Remarque-t-il que les résultats affichent des
  livraisons plus chères (lien avec BUG-02) ?

### S2 : Première commande avec un nouveau compte

> « Tu n'as pas encore de compte. Crée-toi un compte et **commande un repas jusqu'au bout**, livré chez
> toi. »

- **Réussite** : compte créé, commande confirmée **avec une adresse complète**.
- **À observer** : remarque-t-il l'adresse « Domicile — , » vide proposée au checkout (BUG-10) ? Pense-t-il
  à en ajouter une ? Remarque-t-il le minimum de commande (BUG-11) ?

### S3 : Utiliser un code promo

> « Chez **Couscous Atlas**, on t'a dit que le code **ATLAS15** donne -15 %. Commande un repas d'au
> moins 25 € avec ce code et vérifie que tu as bien ta réduction. »

- **Réussite** : le participant applique le code et vérifie le montant de la réduction.
- **À observer** : remarque-t-il que la réduction est de 10 % et non de 15 % (BUG-01) ? Comment réagit-il ?

## Résultats par scénario

| Scénario | Participant | Résultat | Ce qui s'est passé |
|---|---|---|---|
| S1 | P1 | avec difficulté | A eu du mal à trouver comment filtrer les restaurants par frais de livraison : le filtre ne lui a pas paru visible. |
| S1 | P2 | réussi | N'a pas utilisé le filtre : a ouvert le détail de chaque restaurant un par un jusqu'à en trouver un avec une livraison à moins de 2,50 €. |
| S2 | P1 | avec difficulté | Compte créé sans problème. En voulant commander chez Couscous Atlas, n'a pas pu ajouter les plats car son panier contenait déjà un produit d'un autre restaurant (message « Un panier ne peut contenir que des produits du même restaurant. »). |
| S2 | P2 | avec difficulté | Compte créé. A pu ajouter au panier des produits affichés hors stock, ce qu'elle a jugé déroutant pour un client. |
| S3 | P1 | réussi selon la participante | A ajouté des plats de Couscous Atlas et appliqué ATLAS15 ; a jugé que « tout a bien fonctionné ». Elle n'a pas relevé que la réduction appliquée est de 10 % et non de 15 % (BUG-01). |
| S3 | P2 | réussi selon la participante | A appliqué ATLAS15 au paiement sans relever de problème. Comme P1, elle n'a pas remarqué que la réduction est de 10 % et non de 15 % (BUG-01). |

## Problèmes relevés

Chaque bloc correspond à un problème observé. La **fréquence** indique le nombre de participantes
concernées. L'**impact** est classé comme bloquant (scénario non terminé), gênant (scénario terminé avec
difficulté) ou mineur (problème remarqué sans gêne importante).

### UF-01 : Le filtre des frais de livraison est difficile à trouver

| Champ | Contenu |
|---|---|
| Profil | P1 (21 ans, commande parfois en ligne), P2 (commande souvent en ligne) |
| Contexte | S1, page d'accueil : chercher un restaurant dont la livraison coûte moins de 2,50 € |
| Problème | Aucune des deux participantes ne s'est servie naturellement du filtre « livraison max ». P1 n'a pas repéré facilement comment filtrer. P2 a contourné le filtre en ouvrant chaque restaurant un par un. Les filtres et leurs libellés ne ressortent pas assez sur la page. |
| Fréquence | 2 / 2 |
| Impact | Gênant pour P1 (terminé avec difficulté) ; P2 a réussi, mais en passant plus de temps |
| Verbatim | P1 : « Difficile de trouver la livraison à moins de 2,50 €. Les textes ne sont pas assez attirants, je ne comprends pas. Ils doivent être plus mis en avant. »<br>P2 : « J'ai cherché dans les détails de chaque resto, puis j'ai directement trouvé un resto où la livraison coûte moins de 2,50 €. » |
| Hypothèse d'amélioration | Rendre les filtres plus visibles, avec un libellé explicite (« Frais de livraison maximum ») et une application immédiate, sans bouton « Rechercher ». Le filtre étant en plus inversé (BUG-02), un client qui le trouve obtient des résultats faux. |
| Priorité | Haute |
| Lien | BUG-02, amélioration #7 et #18 |

### UF-02 : Les règles de mot de passe paraissent trop faibles

| Champ | Contenu |
|---|---|
| Profil | P1 |
| Contexte | S2, création de compte |
| Problème | Le compte se crée avec un mot de passe simple : seule une longueur de 8 caractères est exigée, sans caractère spécial ni chiffre. La participante l'a vécu comme un manque de sécurité. |
| Fréquence | 1 / 2 |
| Impact | Mineur sur le parcours (pas bloquant), mais nuit à la confiance |
| Verbatim | « J'ai réussi à créer le compte trop facilement. Il y a zéro restriction sur les mots de passe, avec des caractères spéciaux par exemple : là il n'y en avait pas, donc on peut se faire pirater rapidement. » |
| Hypothèse d'amélioration | Exiger un mot de passe plus robuste (au moins un chiffre et un caractère spécial) et afficher les règles et un indicateur de robustesse sous le champ. |
| Priorité | Moyenne |
| Lien | Risque R7 (`risk-matrix.md`), amélioration #19 |

### UF-03 : La règle « un seul restaurant par panier » bloque sans proposer de solution

| Champ | Contenu |
|---|---|
| Profil | P1 |
| Contexte | S2, ajout de plats de Couscous Atlas alors que le panier contenait déjà un produit d'un autre restaurant (ajouté en S1) |
| Problème | L'ajout est refusé avec le message « Un panier ne peut contenir que des produits du même restaurant. », sans rien proposer d'autre. La participante a dû comprendre seule qu'il fallait vider son panier. La règle est voulue (TC-B01 Pass), mais elle est mal expliquée au client. |
| Fréquence | 1 / 2 |
| Impact | Gênant (scénario terminé avec difficulté) |
| Verbatim | « Pour commander chez Couscous Atlas, je n'arrivais pas à mettre les articles dans le panier quand il y avait des articles d'autres restos dedans. C'est pas pratique, on devrait pouvoir créer plusieurs paniers pour plusieurs restos. » |
| Hypothèse d'amélioration | Garder la règle (une commande = une livraison = un restaurant), mais proposer directement dans le message « Vider le panier et ajouter ce plat », et rappeler le restaurant du panier en cours. |
| Priorité | Moyenne |
| Lien | TC-B01, amélioration #20 |

### UF-04 : L'écart de réduction du code ATLAS15 passe inaperçu

| Champ | Contenu |
|---|---|
| Profil | P1, P2 |
| Contexte | S3, checkout chez Couscous Atlas avec le code ATLAS15 annoncé à -15 % |
| Problème | Les deux participantes ont appliqué le code et jugé que tout fonctionnait, sans remarquer que la réduction réelle est de 10 % (BUG-01). Le bug est invisible pour le client, qui paie plus que ce qu'on lui a promis sans s'en rendre compte. |
| Fréquence | 2 / 2 |
| Impact | Aucun ressenti sur le moment, mais client lésé sans le savoir (risque de réclamation s'il s'en aperçoit plus tard) |
| Verbatim | P1 : « J'ai ajouté les articles du resto puis j'ai utilisé le code ATLAS15, et tout a bien fonctionné. »<br>P2 : « Aucun problème, j'ai pu appliquer la réduction lors du paiement dans ma commande. » |
| Hypothèse d'amélioration | Corriger le taux du coupon (ou le texte de la promotion), et afficher le pourcentage appliqué à côté de la réduction au checkout (« ATLAS15 : -10 % ») pour que l'écart se voie. |
| Priorité | Haute |
| Lien | BUG-01, amélioration #8 |

### UF-05 : Des produits hors stock peuvent être ajoutés au panier

| Champ | Contenu |
|---|---|
| Profil | P2 |
| Contexte | S2, ajout de plats au panier pendant la première commande |
| Problème | Des produits affichés « HORS STOCK » restent ajoutables au panier. Le client ne sait plus si son plat sera vraiment disponible. |
| Fréquence | 1 / 2 |
| Impact | Gênant (scénario terminé avec difficulté) |
| Verbatim | « J'ai créé le compte, mais il y a eu pas mal de bugs : par exemple, je pouvais ajouter des articles hors stock dans le panier. Donc c'est difficile pour l'utilisateur. » |
| Hypothèse d'amélioration | Désactiver le bouton « Ajouter » sur les produits hors stock et vérifier le stock côté serveur. |
| Priorité | Haute |
| Lien | BUG-07, TC-M08, amélioration #2 et #14 |

## Ce qu'on en retient

- **Le problème le plus important est invisible** : les 2 participantes ont validé le code ATLAS15 sans voir que
  la réduction est de 10 % au lieu de 15 % (UF-04). Les tests utilisateurs confirment donc que BUG-01 ne
  sera pas signalé par les clients : il faut le corriger plutôt qu'attendre des réclamations.
- **Les filtres sont difficiles à trouver** : aucune des deux n'a trouvé naturellement le filtre de frais de
  livraison (UF-01). Combiné au filtre inversé (BUG-02), ça rend la recherche par prix de livraison
  inutilisable en pratique.
- **Les autres problèmes** (mots de passe, panier mono-restaurant, produits hors stock) n'ont touché
  qu'une participante chacun. Ils rejoignent des bugs ou des risques déjà relevés pendant la campagne.
- Ces résultats ont fait remonter l'amélioration #8 (coupon) et ajouter #19 et #20 au plan
  d'amélioration.

## Limites

- Seulement 2 participantes, donc les fréquences sont indicatives.
- Les deux participantes commandent déjà en ligne (parfois / souvent) : un public moins à l'aise avec le
  numérique rencontrerait sans doute plus de difficultés.
- Les phrases sont reprises des notes prises pendant les séances, avec seulement l'orthographe corrigée.
