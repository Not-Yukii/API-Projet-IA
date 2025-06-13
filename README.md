# API-Projet-IA

API FastAPI pour gérer le traitement des intéractions utilisateurs avec notre chatbot dans le cadre d'un projet de quatrième année.

L'API expose les points de terminaisons suivants :

- `POST /register` - Crée un nouvel utilisateur avec son `email` et son `mot de passe` et envoie un lien de vérification à l'utilisateur. Le mot de passe est hashé avant tout stockage dans la base de données.
- `POST /verify-email` - Vérification d'un utilisateur lorsque celui-ci à cliqué sur le lien envoyé par e-mail suite à son inscription.
- `POST /login` - Authentification avec l'`email` et le `mot de passe` pour recevoir un token d'authentification.
- `POST /logout` - Gère la déconnexion d'un utilisateur de notre application.
- `GET /conversations` - Liste les conversations pour l'utilisateur connecté (`Authorization: Bearer <token>`).
- `GET /chat/{id}` - Récupère tous les messages d'une conversation appartenant à l'utilisateur connecté (`Authorization: Bearer <token>`).
- `POST /send` - Envoie un message dans une conversation ou en commence une nouvelle pour l'utilisateur connecté (`Authorization: Bearer <token>`).
- `POST /delete_conversations/{id}` - Efface une conversation d'un utilisateur si celle-ci lui appartient à partir de l'identifiant de la conversation (`Authorization: Bearer <token>`).

Assurez-vous que la configuration de la base de données est fournie par des variables d'environnement.

## Variables d'environnement

Définissez les variables suivantes avant de lancer l'application :
- `DB_NAME`
- `DB_USER`
- `DB_PASSWORD`
- `DB_HOST`
- `SECRET_KEY`
- `SERPER_API_KEY`

Vous pouvez également définir `DATABASE_URL` pour remplacer la construction de l'URL de connexion à la base de données.

## Création de la base de données et enrichissement du RAG

Pour la mise en place de la base de données, veuillez exécuter le script suivant sur votre base de données PostgreSQL : 
_[https://github.com/Not-Yukii/API-Projet-IA/blob/main/database.sql](url)_

Pour enrichir le contexte global de l'IA avec les données situées dans le répertoire [https://github.com/Not-Yukii/API-Projet-IA/tree/main/data](url) placez vous à la racine de ce projet et exécutez la commande suivante :

```py 
./app/recherche_local.py --ingest
```

Une fois cette étape effectuée, exécutez la commande suivante sur votre base de données PostgreSQL : 

```sql
ALTER TABLE langchain_pg_embedding
  ADD COLUMN tsv tsvector
  GENERATED ALWAYS AS (to_tsvector('french', document)) STORED;
CREATE INDEX idx_chunks_tsv ON langchain_pg_embedding USING GIN(tsv);
```

## Lancer l'API

Installer les dépendances Python :

```bash
pip install -r requirements.txt
```

Lancer l'API avec `uvicorn` à la racine de ce projet :

```bash
uvicorn app.main:app --host 0.0.0.0 --port 8000 --ssl-keyfile=./Certifs_Projet4A/server.key --ssl-certfile=./Certifs_Projet4A/server.crt
```

ou

```bash
python -m uvicorn app.main:app --host 0.0.0.0 --port 8000 --ssl-keyfile=./Certifs_Projet4A/server.key --ssl-certfile=./Certifs_Projet4A/server.crt
```
