# CamerTrust Lite — API

API FastAPI de detection de fraude Mobile Money. Recoit une transaction,
renvoie une prediction de fraude (via le modele fourni par E1), et expose
l'historique des transactions et des alertes pour le dashboard (E3).

## Demarrage rapide (local, sans Docker)

```bash
python -m venv venv
source venv/bin/activate  # Windows : venv\Scripts\activate
pip install -r requirements.txt

cp .env.example .env  # puis adapte les valeurs si besoin

uvicorn api.main:app --reload
```

API disponible sur http://localhost:8000
Documentation interactive (Swagger) : http://localhost:8000/docs

## Demarrage avec Docker Compose (API + PostgreSQL)

```bash
docker-compose up
```

## Lancer les tests

```bash
pytest tests/ -v --tb=short
```

## Test de charge (Locust)

```bash
locust -f tests/locustfile.py --host http://localhost:8000
# puis ouvrir http://localhost:8089
```

## Structure du projet

```
camertrust-api/
├── api/
│   ├── main.py         # endpoints FastAPI
│   ├── config.py       # variables d'environnement
│   ├── database.py     # connexion SQLAlchemy (SQLite/PostgreSQL)
│   ├── models.py       # tables ORM
│   ├── schemas.py      # schemas Pydantic (validation)
│   ├── security.py     # authentification JWT
│   └── ml.py            # chargement et inference du modele de E1
├── tests/
│   ├── test_api.py     # tests pytest
│   └── locustfile.py   # test de charge
├── data/models/         # pipeline_complet.pkl + model_info.json (fournis par E1)
├── Dockerfile
├── docker-compose.yml
├── render.yaml           # blueprint de deploiement Render.com
├── DEPLOYMENT.md         # guide de deploiement pas a pas
└── demo_api.sh            # script de demo via curl
```

## Endpoints

| Methode | Route            | Auth | Description                                  |
|---------|-------------------|------|-----------------------------------------------|
| GET     | `/health`         | non  | Verifie que l'API tourne                      |
| GET     | `/info`           | non  | Infos sur le modele charge                    |
| POST    | `/auth/token`     | non  | Obtenir un token JWT                          |
| POST    | `/predict`        | non  | Analyser une transaction                      |
| GET     | `/transactions`   | oui  | Liste paginee des transactions                |
| GET     | `/alerts`         | oui  | Transactions fraude, filtrables date/montant  |

Voir `DEPLOYMENT.md` pour deployer sur Render.com et obtenir une URL publique
stable a transmettre a E3.
