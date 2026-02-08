# 🏗️ CITYMATE - Infrastructure Docker

Ce dépôt contient toute la configuration Docker pour orchestrer les **3 micro-services** et **3 bases de données** du projet CityMate.

---

## 📋 TABLE DES MATIÈRES

1. [Prérequis](#prérequis)
2. [Installation](#installation)
3. [Configuration](#configuration)
4. [Démarrage du projet](#démarrage-du-projet)
5. [Commandes Docker utiles](#commandes-docker-utiles)
6. [Architecture](#architecture)
7. [Bases de données](#bases-de-données)
8. [Troubleshooting](#troubleshooting)

---

## 🔧 PRÉREQUIS

Avant de commencer, assurez-vous d'avoir installé :

- ✅ **Docker** : [Télécharger Docker Desktop](https://www.docker.com/products/docker-desktop)
- ✅ **Docker Compose** : Inclus avec Docker Desktop
- ✅ **Git** : [Télécharger Git](https://git-scm.com/downloads)

Vérifier les installations :
```bash
docker --version
docker-compose --version
git --version
```

---

## 📥 INSTALLATION

### Étape 1 : Cloner TOUS les dépôts du projet

Créez un dossier parent pour le projet :

```bash
mkdir citymate-workspace
cd citymate-workspace
```

Clonez les 5 dépôts :

```bash
# Infrastructure (Docker)
git clone https://github.com/VOTRE-EQUIPE/citymate-infrastructure.git

# USER API
git clone https://github.com/VOTRE-EQUIPE/citymate-user-api.git

# CITY API
git clone https://github.com/VOTRE-EQUIPE/citymate-city-api.git

# COMMUNITY API
git clone https://github.com/VOTRE-EQUIPE/citymate-community-api.git

# Mobile App
git clone https://github.com/VOTRE-EQUIPE/citymate-mobile.git
```

Votre structure doit ressembler à ça :

```
citymate-workspace/
├── citymate-infrastructure/   ← Vous êtes ici
├── citymate-user-api/
├── citymate-city-api/
├── citymate-community-api/
└── citymate-mobile/
```

---

## ⚙️ CONFIGURATION

### Étape 2 : Configurer les variables d'environnement

Allez dans le dossier infrastructure :

```bash
cd citymate-infrastructure
```

Copiez le fichier `.env.example` en `.env` :

```bash
cp .env.example .env
```

Éditez le fichier `.env` avec vos propres mots de passe :

```bash
nano .env   # ou vim .env, ou ouvrez avec un éditeur de texte
```

**⚠️ IMPORTANT** : Ne JAMAIS commiter le fichier `.env` sur Git (il est déjà dans `.gitignore`).

---

## 🚀 DÉMARRAGE DU PROJET

### Étape 3 : Lancer tous les services avec Docker Compose

Depuis le dossier `citymate-infrastructure`, lancez :

```bash
docker-compose up -d
```

Cette commande va :
1. ✅ Créer les 3 bases de données PostgreSQL
2. ✅ Initialiser les tables avec les scripts SQL
3. ✅ Builder les 3 APIs Spring Boot
4. ✅ Démarrer tous les services

**Temps d'attente** : 2-5 minutes la première fois (téléchargement des images Docker).

### Étape 4 : Vérifier que tout fonctionne

Vérifier les services en cours :

```bash
docker-compose ps
```

Vous devriez voir 6 services :
- ✅ `citymate-user-db` (PostgreSQL)
- ✅ `citymate-city-db` (PostgreSQL avec PostGIS)
- ✅ `citymate-community-db` (PostgreSQL)
- ✅ `citymate-user-api` (Spring Boot)
- ✅ `citymate-city-api` (Spring Boot)
- ✅ `citymate-community-api` (Spring Boot)

### Étape 5 : Tester les APIs

Ouvrez votre navigateur ou utilisez `curl` :

```bash
# USER API - Health check
curl http://localhost:8081/health

# CITY API - Health check
curl http://localhost:8082/health

# COMMUNITY API - Health check
curl http://localhost:8083/health
```

Vous pouvez aussi tester avec **Postman** ou **Insomnia**.

---

## 🐳 COMMANDES DOCKER UTILES

### Démarrer les services
```bash
docker-compose up -d
```

### Arrêter les services
```bash
docker-compose down
```

### Voir les logs
```bash
# Tous les logs
docker-compose logs -f

# Logs d'un service spécifique
docker-compose logs -f user-api
docker-compose logs -f city-api
docker-compose logs -f community-api
```

### Rebuilder les services après modification du code
```bash
# Rebuilder toutes les APIs
docker-compose up -d --build

# Rebuilder une seule API
docker-compose up -d --build user-api
```

### Supprimer TOUT (services + volumes + données)
```bash
docker-compose down -v
```

⚠️ **Attention** : Cette commande supprime aussi les données des bases de données !

### Se connecter à une base de données

```bash
# USER DB
docker exec -it citymate-user-db psql -U citymate_user -d user_db

# CITY DB
docker exec -it citymate-city-db psql -U citymate_city -d city_db

# COMMUNITY DB
docker exec -it citymate-community-db psql -U citymate_community -d community_db
```

### Voir les conteneurs actifs
```bash
docker ps
```

### Supprimer les conteneurs arrêtés
```bash
docker container prune
```

---

## 🏛️ ARCHITECTURE

```
┌─────────────────────────────────────────────────────┐
│           CITYMATE - Architecture Globale           │
└─────────────────────────────────────────────────────┘

┌─────────────┐      ┌─────────────┐      ┌─────────────┐
│  USER API   │      │  CITY API   │      │ COMMUNITY   │
│  Port 8081  │      │  Port 8082  │      │  API 8083   │
└──────┬──────┘      └──────┬──────┘      └──────┬──────┘
       │                    │                     │
       ↓                    ↓                     ↓
┌─────────────┐      ┌─────────────┐      ┌─────────────┐
│  user_db    │      │  city_db    │      │ community_db│
│  Port 5432  │      │  Port 5433  │      │  Port 5434  │
└─────────────┘      └─────────────┘      └─────────────┘
```

---

## 💾 BASES DE DONNÉES

### USER DB (Port 5432)
- **Tables** : users, roles, user_roles, user_interests, checklist_templates, user_checklist_items
- **Fonctionnalités** : Authentification, profils, checklist personnalisée

### CITY DB (Port 5433)
- **Extension** : PostGIS (géolocalisation)
- **Tables** : pois, poi_reviews, events, event_registrations, deals, deal_saves
- **Fonctionnalités** : Points d'intérêt, événements, bons plans

### COMMUNITY DB (Port 5434)
- **Tables** : forum_categories, forum_discussions, forum_replies, forum_reactions, notifications, user_tokens
- **Fonctionnalités** : Forum, notifications push

---

## 🐛 TROUBLESHOOTING

### Problème : Port déjà utilisé

**Erreur** : `port is already allocated`

**Solution** :
```bash
# Vérifier quel service utilise le port
lsof -i :8081   # macOS/Linux
netstat -ano | findstr :8081   # Windows

# Modifier les ports dans docker-compose.yml ou arrêter le service conflictuel
```

### Problème : Les bases de données ne démarrent pas

**Solution** :
```bash
# Supprimer les volumes et recréer
docker-compose down -v
docker-compose up -d
```

### Problème : Erreur de connexion entre API et DB

**Solution** :
```bash
# Vérifier que les bases de données sont "healthy"
docker-compose ps

# Si "unhealthy", vérifier les logs
docker-compose logs user-db
docker-compose logs city-db
docker-compose logs community-db
```

### Problème : API ne démarre pas

**Solution** :
```bash
# Voir les logs de l'API
docker-compose logs user-api

# Rebuilder l'API
docker-compose up -d --build user-api
```

### Problème : "Cannot find Dockerfile"

**Solution** :
Assurez-vous que vous avez bien cloné TOUS les dépôts au même niveau dans `citymate-workspace`.

---

## 📞 SUPPORT

Si vous rencontrez un problème :

1. 📖 Consultez d'abord cette documentation
2. 🔍 Vérifiez les logs : `docker-compose logs -f`
3. 💬 Contactez le Tech Lead (Personne 1)
4. 🐛 Ouvrez une issue sur GitHub

---

## 👥 ÉQUIPE

| Rôle | Responsable | Dépôt |
|------|-------------|-------|
| Tech Lead + USER API | Personne 1 | `citymate-user-api` |
| CITY API | Personne 2 | `citymate-city-api` |
| COMMUNITY API | Personne 3 | `citymate-community-api` |
| Frontend Mobile | Personne 4 | `citymate-mobile` |
| Infrastructure | Personne 1 | `citymate-infrastructure` |

---

## 📅 TIMELINE

- **Semaine 1** : Setup + Authentification
- **Semaine 2** : Fonctionnalités principales
- **Semaine 3** : Fonctionnalités avancées
- **Semaine 4** : Finalisation + intégration
- **Semaine 5** : Tests + Présentation (14 Janvier 2026)

---

## 📝 LICENCE

Projet académique - Université de Bretagne Occidentale  
Master 2 TIIL-A - 2025/2026

---

🚀 **Bon développement à toute l'équipe CityMate !**
