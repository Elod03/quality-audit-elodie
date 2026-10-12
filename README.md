# Audit qualité : QuickOrder V3.1

Ce dépôt contient notre travail d'audit QA pour le cours **Tests et qualité des solutions numériques**,
fait en groupe par **Elodie** et **Marina**.
L'idée du TP : QuickOrder est prêt à être mis en ligne, et on doit se mettre dans la peau d'une équipe QA
qui vérifie si c'est vraiment le cas avant de donner le feu vert.

Spoiler : non, pas encore (voir `execution-report.md`).

## Ce qu'il y a dedans

- `docs/test-strategy.md` : ce qu'on a décidé de tester (et pas tester), et pourquoi
- `docs/risk-matrix.md` : les zones qui nous semblaient les plus dangereuses avant de commencer, et ce
  que la campagne en a confirmé
- `docs/test-cases.xlsx` : nos 44 cas de test (nominaux, limites, erreurs, règles métier), avec qui les a
  exécutés et où trouver la preuve
- `docs/execution-report.md` : les résultats une fois tout exécuté, et notre avis Go/No-Go
- `docs/bug-reports/` : les 12 bugs reproduits, avec preuve à chaque fois
- `docs/preuves/` : les captures d'écran (navigateur, DevTools et Swagger)
- `docs/user-feedback.md` : les tests utilisateurs (2 participantes, 3 scénarios) et ce qu'on en retient
- `docs/improvement-plan.md` : ce qu'on proposerait de corriger en premier

## L'appli testée

QuickOrder V3.1 (Flask / Flask-SQLAlchemy / SQLite), lancée en local via Docker
(`docker compose up --build`) ou Python (`python seed.py` puis `python run.py`) sur
`http://127.0.0.1:5000`, API sur `/api`, Swagger sur `/docs/`.

Comme demandé dans le brief, on n'a touché à aucune ligne du code source de QuickOrder pendant la
campagne : tout ce qui est décrit ici vient de l'usage réel de l'appli (navigateur, Swagger, API, requêtes
en base pour vérifier certains états).
