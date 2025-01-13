# Compte Rendu : Containerisation et Déploiement d'une Application Frontend et Backend

## Objectif
L'objectif de ce TP est de réaliser la containerisation et le déploiement d'un exemple basique d'application frontend et backend.

## Composants et Objectifs
| Composant          | Objectif          | Statut       |
|--------------------|-------------------|--------------|
| Backend            | Containerisation  | Réalisé      |
| Backend            | Déploiement       | Réalisé      |
| Frontend           | Containerisation  | Réalisé      |
| Frontend           | Déploiement       | Réalisé      |
| Ensemble Front/Back| Communication     | Réalisé      |

---

## Étapes Réalisées

### 1. Containerisation du Backend
Le backend a été conteneurisé en utilisant le fichier `Dockerfile` suivant, situé dans le répertoire `backend` :

```dockerfile
FROM node:18

WORKDIR /app

COPY package*.json ./

RUN npm install

COPY . .

EXPOSE 5000

CMD ["node", "server.js"]
```


### 2. Containerisation du Frontend
Le frontend a également été conteneurisé avec le fichier `Dockerfile` suivant, situé dans le répertoire `frontend` :

```dockerfile
FROM node:18

WORKDIR /app

COPY package*.json ./

RUN npm install

COPY . .

RUN npm run build

FROM nginx:alpine
COPY --from=0 /app/build /usr/share/nginx/html

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
```

### 3. Communication entre Frontend et Backend
Le frontend a été configuré pour communiquer avec le backend à l'aide du nom de service Docker. Dans le fichier `frontend/src/App.js` (ou équivalent), l'URL du backend a été configurée comme suit :

```js
const serverPort = 3001; 
const serverURL = `http://backend:${serverPort}/`;
```

Cela permet au frontend de consommer l'API exposée par le backend, en s'assurant que les deux conteneurs peuvent interagir.

### 4. Orchestration avec Docker Compose
Un fichier `docker-compose.yml` a été créé à la racine du projet pour coordonner les deux conteneurs :

```yaml
version: '3'
services:
  backend:
    build: ./backend
    ports:
      - "3001:3001"
  frontend:
    build: ./frontend
    ports:
      - "3000:80"
    depends_on:
      - backend
```
Ce fichier gère le lancement des conteneurs pour le frontend et le backend et expose les services sur leurs ports respectifs.

### 5. Déploiement
Pour déployer l'application, les commandes suivantes ont été exécutées :

```bash
docker-compose build
docker-compose up
```

Les conteneurs sont alors disponibles :
- Frontend : http://localhost:3000
- Backend : http://localhost:3001