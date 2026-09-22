# Audit qualité : QuickOrder V3.1

Ce dépôt contient mon travail d'audit QA pour le cours **Tests et qualité des solutions numériques**.
L'idée du TP : QuickOrder est prêt à être mis en ligne, et je dois me mettre dans la peau d'une équipe QA
qui vérifie si c'est vraiment le cas avant de donner le feu vert.

Spoiler : non, pas encore (voir `execution-report.md`).

## Ce qu'il y a dedans

- `docs/test-strategy.md` : ce que j'ai décidé de tester (et pas tester), et pourquoi
- `docs/risk-matrix.md` : les zones qui me semblaient les plus dangereuses avant même de commencer
- `docs/test-cases.xlsx` : mes 27 cas de test (nominaux, limites, erreurs, règles métier)
- `docs/execution-report.md` : les résultats une fois tout exécuté, et mon avis Go/No-Go
- `docs/bug-reports/` : les 10 bugs que j'ai réussi à reproduire, avec preuve à chaque fois
- `docs/improvement-plan.md` : ce que je proposerais de corriger en premier

## L'appli testée

QuickOrder V3.1 (Flask / Flask-SQLAlchemy / SQLite), lancée en local via Docker
(`docker compose up --build`) sur `http://127.0.0.1:5000`, API sur `/api`, Swagger sur `/docs/`.

Comme demandé dans le brief, je n'ai touché à aucune ligne du code source de QuickOrder pendant la
campagne : tout ce qui est décrit ici vient de l'usage réel de l'appli (navigateur, API, requêtes en base
pour vérifier certains états).
