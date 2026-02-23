# 🏗️ ARCHITECTURE CITYMATE

## 📊 Vue d'ensemble
```
┌─────────────────────────────────────┐
│   Application Mobile (Kotlin)       │
│   Se connecte à l'API Gateway       │
└───────────────┬─────────────────────┘
                │
                ↓
    ┌───────────────────────────┐
    │     API GATEWAY           │
    │       Port 8090           │
    │                           │
    │  - Routing                │
    │  - CORS                   │
    └───────┬───────┬───────┬───┘
            │       │       │
    ┌───────┘       │       └────────┐
    ↓               ↓                ↓
┌────────┐     ┌────────┐     ┌──────────┐
│ USER   │     │ CITY   │     │COMMUNITY │
│ API    │     │ API    │     │   API    │
│ 8081   │     │ 8082   │     │   8083   │
└───┬────┘     └───┬────┘     └────┬─────┘
    │              │                │
    ↓              ↓                ↓
┌────────┐     ┌────────┐     ┌──────────┐
│user_db │     │city_db │     │community │
│ 5432   │     │ 5433   │     │  _db     │
└────────┘     └────────┘     │  5434    │
                               └──────────┘
```

## 🚀 Démarrage

### Prérequis
- Docker
- Docker Compose

### Lancer tous les services
```bash
cd citymate-infrastructure
docker-compose up -d
```

### Vérifier les services
```bash
docker-compose ps
```

### Voir les logs
```bash
docker-compose logs -f
```

### Arrêter
```bash
docker-compose down
```

## 🔗 URLs

| Service | URL | Description |
|---------|-----|-------------|
| **API Gateway** | http://localhost:8090 | Point d'entrée unique |
| **USER API** | http://localhost:8081 | Authentification (interne) |
| **CITY API** | http://localhost:8082 | POIs, Events (à venir) |
| **COMMUNITY API** | http://localhost:8083 | Forum (à venir) |
| **PostgreSQL User** | localhost:5432 | Base USER |
| **PostgreSQL City** | localhost:5433 | Base CITY |
| **PostgreSQL Community** | localhost:5434 | Base COMMUNITY |

## 📝 Endpoints disponibles

### Via API Gateway (Port 8090)

#### Authentification
- `POST /api/v1/auth/register` - Créer un compte
- `POST /api/v1/auth/login` - Se connecter

#### Utilisateurs (JWT requis)
- `GET /api/v1/users/me` - Mon profil
- `GET /api/v1/users/{username}` - Profil public

## 🔐 Authentification

Le système utilise **JWT (JSON Web Tokens)**.

### Flow d'authentification

1. **Register/Login** → Reçoit un token JWT
2. **Requêtes suivantes** → Header `Authorization: Bearer <token>`
3. **Token valide 24h**

### Exemple
```bash
# 1. Register
curl -X POST http://localhost:8090/api/v1/auth/register \
  -H "Content-Type: application/json" \
  -d '{"username":"alice","email":"alice@test.com","password":"password123"}'

# Réponse : {"token":"eyJ...","type":"Bearer","username":"alice"}

# 2. Utiliser le token
curl http://localhost:8090/api/v1/users/me \
  -H "Authorization: Bearer eyJ..."
```

## 🧪 Tests

### Collection Postman

Importer : `docs/CityMate_API.postman_collection.json`

### Test rapide
```bash
# Health check Gateway
curl http://localhost:8090/actuator/health

# Register
curl -X POST http://localhost:8090/api/v1/auth/register \
  -H "Content-Type: application/json" \
  -d '{"username":"test","email":"test@test.com","password":"password123","firstName":"Test","lastName":"User","profileType":"STUDENT"}'
```

## 👥 Équipe

| Membre | Rôle | API |
|--------|------|-----|
| BRAHIM | Tech Lead | USER API + API Gateway |
| Membre 2 | Développeur | CITY API |
| Membre 3 | Développeur | COMMUNITY API |
| Membre 4 | Développeur | Frontend Mobile |

## 📅 Présentation
