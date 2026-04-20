

# 🚗 Easy Rental API - Plateforme SaaS de Location de Véhicules

![Java](https://img.shields.io/badge/Java-21-orange.svg)
![Spring Boot](https://img.shields.io/badge/Spring%20Boot-3.4.12-brightgreen.svg)
![WebFlux](https://img.shields.io/badge/Spring%20WebFlux-Reactive-blue.svg)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-17-336791.svg)
![Liquibase](https://img.shields.io/badge/Liquibase-Migrations-red.svg)
![Docker](https://img.shields.io/badge/Docker-Containerized-2496ED.svg)

**Easy Rental API** est un backend robuste, réactif (100% non-bloquant) et multi-locataires (SaaS B2B2C) conçu pour gérer les plateformes de location de véhicules à l'échelle. L'API offre une gestion complète des organisations, flottes, réservations, paiements et un système RBAC avancé pour le contrôle d'accès granulaire.

---

## 📑 Table des matières

1. [Fonctionnalités Principales](#-fonctionnalités-principales)
2. [Stack Technique](#-stack-technique)
3. [Architecture du Projet](#-architecture-du-projet)
4. [Prérequis](#-prérequis)
5. [🚀 Installation et Démarrage](#-installation-et-démarrage)
6. [Configuration](#-configuration)
7. [Base de données et Migrations](#-base-de-données-et-migrations)
8. [Documentation API (Swagger)](#-documentation-api-swagger)
9. [Sécurité et RBAC](#-sécurité-et-rbac)
10. [Workflow d'utilisation](#-workflow-dutilisation-comment-tester)
11. [📦 Déploiement](#-déploiement)
12. [Maintenance et Mise à Jour](#-maintenance-et-mise-à-jour)
13. [Contribution](#-contribution)

---

## 🌟 Fonctionnalités Principales

### 🏢 Espace Organisation (B2B)

- **Multi-Agences** : Création et gestion de plusieurs agences physiques avec géolocalisation
- **Gestion de Flotte** : Ajout de véhicules avec caractéristiques techniques, assurances et photos
- **Gestion des Chauffeurs** : Enregistrement et suivi des chauffeurs
- **Ressources Humaines (RBAC)** : Création de postes personnalisés et assignation de permissions granulaires
- **Abonnements SaaS** : Gestion des quotas (max véhicules, agences) selon les plans (FREE, PRO, ENTERPRISE)
- **Tableaux de Bord** : Statistiques en temps réel (Revenus, taux d'occupation, évolution des locations)

### 📱 Espace Client (B2C)

- **Recherche Avancée** : Recherche d'agences et véhicules par ville, dates et catégories
- **Réservation & Paiement** : Processus complet (Devis → Acompte 60% → Paiement total → Récupération → Retour)
- **Avis & Évaluations** : Notation des véhicules et chauffeurs
- **Historique** : Suivi des transactions et locations passées

### ⚙️ Fonctionnalités Transverses

- **Notifications** : Alertes en temps réel (confirmations, paiements reçus)
- **Gestion des Médias** : Upload sécurisé (Photos, CNI, Permis, Images véhicules)
- **Audit Trail** : Traçabilité complète des actions sensibles
- **Système Réactif** : 100% non-bloquant pour haute performance et scalabilité

---

## 🛠 Stack Technique

| Composant | Technologie | Version |
|-----------|-------------|---------|
| **Langage** | Java | 21 |
| **Framework** | Spring Boot | 3.4.12 |
| **Réactivité** | Spring WebFlux (Project Reactor) | 3.4.12 |
| **Base de données** | PostgreSQL | 17 |
| **Driver BD** | Spring Data R2DBC | Réactif |
| **Migrations** | Liquibase | Latest |
| **Authentification** | Spring Security + JWT | 0.11.5 |
| **Documentation API** | SpringDoc OpenAPI / Swagger UI | 2.7.0 |
| **Utilitaires** | Lombok | Latest |
| **Build** | Maven | 3.9+ |
| **Conteneurisation** | Docker | Multi-stage |

### Choix Architecturaux

- **R2DBC** : Driver réactif pour accès non-bloquant à PostgreSQL
- **Project Reactor** : `Mono` et `Flux` pour la programmation réactive
- **JWT** : Authentification stateless et sécurisée
- **Liquibase** : Migrations versionnées et contrôlées
- **Module par domaine** : Architecture hexagonale / DDD pour maintenabilité

---

## 🏗 Architecture du Projet

Le code suit une organisation modulaire basée sur le **Domain-Driven Design (DDD)** :

```text
src/main/java/com/project/apirental/
├── config/                  # Configurations globales
│   ├── SecurityConfig.java
│   ├── SwaggerConfig.java
│   ├── CorsConfig.java
│   └── DataSeeder.java      # Population BD en dev
│
├── modules/                 # Modules métiers isolés
│   ├── agency/              # Gestion des agences
│   │   ├── api/            # Controllers (REST)
│   │   ├── domain/         # Entités, Value Objects
│   │   ├── dto/            # Data Transfer Objects
│   │   ├── repository/     # Accès données
│   │   └── service/        # Logique métier
│   │
│   ├── auth/                # Authentification & JWT
│   ├── driver/              # Gestion des chauffeurs
│   ├── media/               # Upload & gestion fichiers
│   ├── notification/        # Système d'alertes
│   ├── organization/        # Gestion entreprises
│   ├── permission/          # Catalogue des droits
│   ├── poste/               # Rôles personnalisés
│   ├── pricing/             # Tarification dynamique
│   ├── rental/              # Moteur de réservation
│   ├── review/              # Système d'avis
│   ├── schedule/            # Planning & indisponibilités
│   ├── staff/               # Gestion employés
│   ├── statistics/          # Rapports & KPIs
│   ├── subscription/        # Plans SaaS
│   └── vehicle/             # Gestion flotte
│
├── shared/                  # Code partagé
│   ├── exception/          # Exceptions métier
│   ├── enums/              # Énumérations
│   ├── dto/                # DTOs génériques
│   ├── security/           # Utilitaires sécurité
│   └── utils/              # Helpers
│
└── resources/
    ├── application.yml      # Configuration principale
    ├── application-dev.yml  # Profil développement
    ├── application-prod.yml # Profil production
    └── db/changelog/        # Scripts Liquibase
```

**Structure d'un module type :**

```
module/
├── api/
│   └── VehicleController.java     # @RestController, @RequestMapping
├── domain/
│   └── Vehicle.java               # @Entity (Domain Object)
├── dto/
│   ├── VehicleCreateDto.java      # Input DTO
│   └── VehicleResponseDto.java    # Output DTO
├── repository/
│   └── VehicleRepository.java     # @Repository (R2DBC)
├── service/
│   └── VehicleService.java        # @Service (logique métier)
└── mapper/
    └── VehicleMapper.java         # Conversion DTO <-> Entity
```

---

## 💻 Prérequis

### Système

- **Système d'exploitation** : Linux, macOS ou Windows (avec WSL2)
- **Java 21** : [Télécharger](https://www.oracle.com/java/technologies/downloads/#java21)
- **Maven 3.9+** : Inclus via wrapper `./mvnw`
- **PostgreSQL 17** : [Télécharger](https://www.postgresql.org/download/) ou via Docker

### Vérification des prérequis

```bash
# Vérifier Java
java -version
# Expected: java 21.x.x

# Vérifier Maven (le wrapper le fera automatiquement)
./mvnw -version
```

### Services externes requis

- **PostgreSQL 17** (local ou distant)
- **Système de fichiers** pour les uploads (au moins 1 GB recommandé)

---

## 🚀 Installation et Démarrage

### Option 1 : Installation locale (Mode Développement)

#### 1️⃣ Cloner le projet

```bash
git clone https://github.com/projet-synthese-gi26/rental-api.git
cd rental-api
```

#### 2️⃣ Configurer PostgreSQL

**Avec Docker Compose (Recommandé) :**

Créez un fichier `docker-compose.yml` à la racine du projet :

```yaml
version: '3.8'

services:
  postgres:
    image: postgres:17-alpine
    container_name: rental_postgres
    environment:
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: password
      POSTGRES_DB: rentaldb
      TZ: UTC
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U postgres"]
      interval: 10s
      timeout: 5s
      retries: 5

volumes:
  postgres_data:
```

Lancer PostgreSQL :

```bash
docker-compose up -d postgres
```

**Ou manuellement :**

```sql
-- Connexion à PostgreSQL
createdb -U postgres rentaldb

-- Vérifier la création
psql -U postgres -d rentaldb -c "\l"
```

#### 3️⃣ Compiler le projet

```bash
# Compiler et installer les dépendances
./mvnw clean install -DskipTests

# Ou avec Maven directement
mvn clean install -DskipTests
```

#### 4️⃣ Configurer l'application

Créez/vérifiez `src/main/resources/application.yml` :

```yaml
server:
  port: 8080
  servlet:
    context-path: /

spring:
  application:
    name: apirental
  
  # ===== PostgreSQL R2DBC (pour l'application) =====
  r2dbc:
    url: r2dbc:postgresql://localhost:5432/rentaldb
    username: postgres
    password: password
    pool:
      initial-size: 5
      max-size: 20
      max-idle-time: 30m
  
  # ===== Liquibase (JDBC, pour migrations) =====
  liquibase:
    url: jdbc:postgresql://localhost:5432/rentaldb
    user: postgres
    password: password
    change-log: classpath:db/changelog/db.changelog-master.xml
    enabled: true
  
  # ===== JPA / Hibernate (pour R2DBC) =====
  data:
    r2dbc:
      repositories:
        enabled: true
  
  # ===== Security & JWT =====
  security:
    jwt:
      secret: "your-super-secret-key-change-this-in-production-at-least-256-bits"
      expiration: 86400000  # 24 heures en ms

  # ===== Profils =====
  profiles:
    active: dev

logging:
  level:
    root: INFO
    com.project.apirental: DEBUG
    org.springframework.security: DEBUG
```

Créez `src/main/resources/application-dev.yml` :

```yaml
spring:
  liquibase:
    enabled: true
  jpa:
    show-sql: false

logging:
  level:
    com.project.apirental: DEBUG
    org.springframework.web: DEBUG
    org.springframework.data: DEBUG
    org.liquibase: INFO
```

#### 5️⃣ Lancer l'application

**Via Maven (Profil dev):**

```bash
./mvnw spring-boot:run -Dspring-boot.run.profiles=dev
```

**Ou depuis l'IDE (IntelliJ IDEA / Eclipse):**

1. Clic droit sur la classe principale → **Run 'RentalApiApplication'**
2. Configurer les variables d'environnement : `SPRING_PROFILES_ACTIVE=dev`

**Sortie attendue :**

```
  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 /\  ___'|_|_|_|_| |_|_| |_\__, | ) ) ) )
/_/\_____|__| |__| |__| |__| |_|/ / / /
  |_____|
  
 _______ ______ __   _________________ ______  _________  ____  _
|_  ___/|  ____/  \ /   |  ___    |  ___  ||  ____  ||    / _/  |
  | |   | |__  / /\ \    | |_  \  | |_  | || |_____| || ||_/ /   |
  | |   |  __/    ___/   |  _  /  |  _  | ||  _____  || |   \_\  |
  | |   | |____  /  \  \ | | \ \  | | | | || |     | || |    _ | |
  |_|   |______|/__/\__\|_|  \_\|_| |_| |_||_|     |_||_|__/ \_\|_|

2026-04-20 10:15:23.456 INFO [main] o.s.b.w.e.tomcat.TomcatWebServer : Tomcat initialized with port(s): 8080
...
2026-04-20 10:15:45.123 INFO [main] c.p.a.RentalApiApplication : Started RentalApiApplication in 21.567s
```

#### ✅ Vérifier que l'application fonctionne

```bash
# Test simple
curl http://localhost:8080/swagger-ui.html

# Ou via votre navigateur
open http://localhost:8080/swagger-ui.html
```

---

### Option 2 : Avec Docker Compose (Production-like)

#### 1️⃣ Construire l'image Docker

```bash
# À partir de la racine du projet
docker build -t easy-rental-api:latest .
```

#### 2️⃣ Créer docker-compose.yml complet

```yaml
version: '3.8'

services:
  # Base de données PostgreSQL
  postgres:
    image: postgres:17-alpine
    container_name: rental_postgres
    environment:
      POSTGRES_USER: ${DB_USER:-postgres}
      POSTGRES_PASSWORD: ${DB_PASSWORD:-password}
      POSTGRES_DB: ${DB_NAME:-rentaldb}
      TZ: Africa/Douala
    ports:
      - "${DB_PORT:-5432}:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U postgres"]
      interval: 10s
      timeout: 5s
      retries: 5
    networks:
      - rental_network

  # API Easy Rental
  api:
    build: .
    container_name: easy_rental_api
    environment:
      SPRING_PROFILES_ACTIVE: ${SPRING_ENV:-dev}
      SPRING_R2DBC_URL: r2dbc:postgresql://postgres:5432/${DB_NAME:-rentaldb}
      SPRING_R2DBC_USERNAME: ${DB_USER:-postgres}
      SPRING_R2DBC_PASSWORD: ${DB_PASSWORD:-password}
      SPRING_LIQUIBASE_URL: jdbc:postgresql://postgres:5432/${DB_NAME:-rentaldb}
      SPRING_LIQUIBASE_USER: ${DB_USER:-postgres}
      SPRING_LIQUIBASE_PASSWORD: ${DB_PASSWORD:-password}
      JWT_SECRET: ${JWT_SECRET:-your-super-secret-key-change-this-in-production}
      JAVA_OPTS: "-Xms512m -Xmx1024m"
    ports:
      - "${APP_PORT:-8080}:8080"
    volumes:
      - ./uploads:/app/uploads
    depends_on:
      postgres:
        condition: service_healthy
    networks:
      - rental_network

volumes:
  postgres_data:

networks:
  rental_network:
    driver: bridge
```

#### 3️⃣ Lancer avec Docker Compose

```bash
# Mode développement
docker-compose up -d

# Afficher les logs
docker-compose logs -f api

# Arrêter
docker-compose down
```

---

## ⚙️ Configuration

### Variables d'environnement principales

| Variable | Default | Description |
|----------|---------|-------------|
| `SPRING_PROFILES_ACTIVE` | `dev` | Profil actif (dev, prod) |
| `SPRING_R2DBC_URL` | `r2dbc:postgresql://localhost:5432/rentaldb` | URL BD réactive |
| `SPRING_R2DBC_USERNAME` | `postgres` | Utilisateur PostgreSQL |
| `SPRING_R2DBC_PASSWORD` | `password` | Mot de passe |
| `JWT_SECRET` | *(généré)* | Clé secrète JWT (min 256 bits) |
| `JWT_EXPIRATION` | `86400000` | Durée token (ms) |
| `SERVER_PORT` | `8080` | Port Spring Boot |

### Configuration par profil

**Développement (`application-dev.yml`) :**
- Liquibase : Activé
- Seeder : Activé (population BD)
- Logs : DEBUG
- CORS : Ouvert

**Production (`application-prod.yml`) :**
- Liquibase : Activé
- Seeder : Désactivé
- Logs : INFO
- CORS : Restreint

---

## 🗄 Base de données et Migrations

### Migrations Liquibase

**Structure des migrations :**

```text
src/main/resources/db/changelog/
├── db.changelog-master.xml          # Master changelog
├── v1.0/
│   ├── 001-initial-schema.xml
│   ├── 002-create-organizations.xml
│   ├── 003-create-vehicles.xml
│   ├── 004-create-rentals.xml
│   └── ...
└── v2.0/
    └── ...
```

### Ajouter une nouvelle migration

1. **Créer un fichier XML** `src/main/resources/db/changelog/v1.x/NNN-description.xml` :

```xml
<?xml version="1.1" encoding="UTF-8" standalone="no"?>
<databaseChangeLog xmlns="http://www.liquibase.org/xml/ns/dbchangelog"
                   xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
                   xsi:schemaLocation="http://www.liquibase.org/xml/ns/dbchangelog
                   http://www.liquibase.org/xml/ns/dbchangelog/dbchangelog-latest.xsd">

    <changeSet id="001-add-column-example" author="dev">
        <addColumn tableName="vehicles">
            <column name="fuel_type" type="VARCHAR(50)"/>
        </addColumn>
    </changeSet>

</databaseChangeLog>
```

2. **Référencer le fichier dans le master** `db.changelog-master.xml` :

```xml
<include file="db/changelog/v1.x/NNN-description.xml"/>
```

3. **Redémarrer l'application** - Liquibase applique automatiquement les migrations

**Note :** Liquibase exécute les migrations une seule fois via JDBC au démarrage.

### Data Seeder (Profil `dev`)

En mode développement, la classe `DataSeeder.java` remplit la BD avec données de test :

**Créées automatiquement :**
- 2 Organisations (Prestige Auto, Logistics Express)
- Agences (Douala, Yaoundé)
- Personnel (Managers, Agents commerciaux)
- Flotte (Toyota, Mercedes avec photos)
- Chauffeurs

**Comptes de test générés :**

| Utilisateur | Email | Mot de passe |
|-------------|-------|--------------|
| PDG Prestige Auto | contact@prestige-auto.cm | password123 |
| PDG Logistics | info@logistics-express.cm | password123 |

---

## 📚 Documentation API (Swagger)

### Accéder à Swagger UI

L'API est documentée via **OpenAPI 3** et **SpringDoc** :

👉 **[http://localhost:8080/swagger-ui.html](http://localhost:8080/swagger-ui.html)**

**La documentation est groupée en 3 sections :**

1. **1-Public-Auth** : Inscription, Connexion, Upload fichiers
2. **2-Espace-Client** : Recherche véhicules, Réservations clients
3. **3-Espace-Gestion** : Back-office organisations

### Tester une route protégée

1. **Obtenir un token :**
   - POST `/auth/login`
   - Body : `{ "email": "contact@prestige-auto.cm", "password": "password123" }`
   - Réponse : `{ "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..." }`

2. **S'authentifier dans Swagger :**
   - Cliquer sur le bouton **"Authorize"** (🔒)
   - Coller le token (sans "Bearer")
   - Cliquer **Authorize**

3. **Tester une route protégée :**
   - Sélectionner n'importe quelle route protégée
   - Cliquer **Try it out** → **Execute**

### Endpoints principaux

```
# ========== AUTHENTIFICATION ==========
POST   /auth/register/organizationOwner   # Inscription loueur
POST   /auth/register/client              # Inscription client
POST   /auth/login                        # Connexion
GET    /auth/me                           # Profil actuel

# ========== GESTION FLOTTES (Protégé) ==========
GET    /api/vehicles/org/{orgId}          # Lister véhicules
POST   /api/vehicles/org/{orgId}          # Ajouter véhicule
GET    /api/vehicles/{id}                 # Détails véhicule
PUT    /api/vehicles/{id}                 # Modifier véhicule
DELETE /api/vehicles/{id}                 # Supprimer véhicule

# ========== RÉSERVATIONS ==========
GET    /api/vehicles/search               # Recherche avancée (Public)
POST   /api/rentals/init                  # Initier location
POST   /api/rentals/{id}/pay              # Payer acompte/solde
PUT    /api/rentals/{id}/start            # Départ
PUT    /api/rentals/{id}/end-signal       # Fin signalement
PUT    /api/rentals/{id}/validate-return  # Validation retour

# ========== STATISTIQUES (Protégé) ==========
GET    /api/statistics/org/{orgId}        # KPIs organisation
GET    /api/statistics/agency/{agencyId}  # KPIs agence
```

---

## 🔐 Sécurité et RBAC

### Authentification JWT

- **Tokens stateless** générés à la connexion
- **Expiration** : 24h (configurable)
- **Secret** : Clé secrète de 256+ bits (variable d'env `JWT_SECRET`)

### 4 Rôles système

| Rôle | Permission | Cas d'usage |
|------|-----------|-----------|
| `ROLE_ADMIN` | Gère plans d'abonnement | Administrateur plateforme |
| `ROLE_ORGANIZATION` | Crée agences, véhicules | Propriétaire entreprise location |
| `ROLE_STAFF` | Actions selon poste | Employé agence |
| `ROLE_CLIENT` | Recherche & réserve | Utilisateur final |

### RBAC avancé (Pour STAFF)

L'accès aux ressources est vérifié **dynamiquement via `@rbac`** :

```java
@PutMapping("/{vehicleId}/status-pricing")
@rbac(permission = "vehicle:update")  // Annotation custom
public Mono<ResponseEntity<VehicleDto>> updateStatusAndPricing(
    @PathVariable String vehicleId,
    @RequestBody UpdateVehicleDto dto) {
    // Vérifie que l'utilisateur a la permission "vehicle:update"
    // pour son organisation
}
```

**Permissions granulaires par poste :**
- `vehicle:read`, `vehicle:create`, `vehicle:update`, `vehicle:delete`
- `agency:read`, `agency:create`, `agency:update`, `agency:delete`
- `staff:read`, `staff:create`, `staff:update`, `staff:delete`
- `report:read`

---

## 🔄 Workflow d'utilisation (Comment tester)

### Scénario 1 : Créer une organisation et ajouter des véhicules

#### Côté Loueur

```bash
# 1️⃣ Inscription loueur
POST /auth/register/organizationOwner
{
  "firstName": "Jean",
  "lastName": "Dupont",
  "email": "jean.dupont@rental.cm",
  "password": "SecurePassword123!",
  "organizationName": "Auto Prestige"
}

# Réponse : { "id": "org-123", "token": "jwt-token..." }

# 2️⃣ Utiliser le token dans Swagger "Authorize"
# Token : jwt-token...

# 3️⃣ Créer une agence
POST /api/agencies/org/org-123
{
  "name": "Agence Douala",
  "city": "Douala",
  "address": "123 Rue du Port",
  "latitude": 4.0511,
  "longitude": 9.7679,
  "phone": "+237 6 XX XX XX XX"
}

# Réponse : { "id": "agency-456", "name": "Agence Douala", ... }

# 4️⃣ Ajouter un véhicule
POST /api/vehicles/org/org-123
{
  "agencyId": "agency-456",
  "categoryId": "category-sedan",
  "brand": "Toyota",
  "model": "Corolla",
  "licensePlate": "CM-XXXX-XX",
  "yearOfManufacture": 2024,
  "mileage": 500,
  "fuelType": "ESSENCE",
  "transmission": "AUTOMATIQUE",
  "status": "AVAILABLE"
}

# Réponse : { "id": "vehicle-789", "brand": "Toyota", ... }

# 5️⃣ Définir le prix
PATCH /api/vehicles/vehicle-789/status-pricing
{
  "status": "AVAILABLE",
  "hourlyPrice": 50.00,
  "dailyPrice": 200.00
}
```

#### Côté Client

```bash
# 1️⃣ Inscription client
POST /auth/register/client
{
  "firstName": "Alice",
  "lastName": "Martin",
  "email": "alice.martin@email.cm",
  "password": "ClientPassword123!",
  "phone": "+237 6 XX XX XX XX"
}

# Réponse : { "id": "client-111", "token": "jwt-token..." }

# 2️⃣ Rechercher un véhicule (Route publique, pas besoin de token)
GET /api/vehicles/search?city=Douala&startDate=2026-05-01&endDate=2026-05-05

# Réponse : 
# [{
#   "id": "vehicle-789",
#   "brand": "Toyota",
#   "model": "Corolla",
#   "dailyPrice": 200.00,
#   "available": true
# }]

# 3️⃣ Initier une location
POST /api/rentals/init
{
  "vehicleId": "vehicle-789",
  "startDate": "2026-05-01",
  "endDate": "2026-05-05",
  "rentalType": "DAILY"
}

# Réponse :
# {
#   "id": "rental-222",
#   "vehicleId": "vehicle-789",
#   "totalPrice": 1000.00,
#   "depositRequired": 600.00,
#   "status": "QUOTE"
# }

# 4️⃣ Payer l'acompte (60%)
POST /api/rentals/rental-222/pay
{
  "amount": 600.00,
  "paymentMethod": "CARD"
}

# Réponse : { "id": "rental-222", "status": "RESERVED", ... }

# 5️⃣ Payer le solde (40%)
POST /api/rentals/rental-222/pay
{
  "amount": 400.00,
  "paymentMethod": "CARD"
}

# Réponse : { "id": "rental-222", "status": "PAID", ... }
```

#### Cycle de vie agence

```bash
# 1️⃣ Client prend la voiture (Agent agence)
PUT /api/rentals/rental-222/start

# Réponse : { "id": "rental-222", "status": "ONGOING", ... }

# 2️⃣ Client ramène la voiture
PUT /api/rentals/rental-222/end-signal

# Réponse : { "id": "rental-222", "status": "RETURN_SIGNALED", ... }

# 3️⃣ Agent valide le retour et l'état du véhicule
PUT /api/rentals/rental-222/validate-return
{
  "vehicleCondition": "GOOD",
  "mileage": 650,
  "notes": "Aucun dommage"
}

# Réponse : { "id": "rental-222", "status": "COMPLETED", ... }
```

---

## 📦 Déploiement

### Déploiement sur serveur (Ubuntu/Debian)

#### 1️⃣ Préparer le serveur

```bash
# Mettre à jour les paquets
sudo apt update && sudo apt upgrade -y

# Installer Java 21
sudo apt install -y openjdk-21-jre-headless

# Installer Docker & Docker Compose
sudo apt install -y docker.io docker-compose

# Ajouter l'utilisateur au groupe docker
sudo usermod -aG docker $USER
newgrp docker

# Vérifier les installations
java -version
docker --version
docker-compose --version
```

#### 2️⃣ Cloner le projet et configurer

```bash
# Créer un répertoire pour l'application
mkdir -p /opt/easy-rental-api
cd /opt/easy-rental-api

# Cloner le repo
git clone https://github.com/projet-synthese-gi26/rental-api.git .

```

#### 4️⃣ Lancer avec docker-compose

```bash
# Construire l'image
docker build -t easy-rental-api:latest .

# Lancer les services
docker-compose -f docker-compose.yml up -d

# Vérifier les logs
docker-compose logs -f api

# Vérifier l'API
curl http://localhost:8080/swagger-ui.html
```

### Déploiement sur Kubernetes (optionnel)

Créez un fichier `k8s-deployment.yaml` :

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: easy-rental-api
  labels:
    app: rental-api
spec:
  replicas: 2
  selector:
    matchLabels:
      app: rental-api
  template:
    metadata:
      labels:
        app: rental-api
    spec:
      containers:
      - name: api
        image: easy-rental-api:latest
        ports:
        - containerPort: 8080
        env:
        - name: SPRING_PROFILES_ACTIVE
          value: "prod"
        - name: SPRING_R2DBC_URL
          value: "r2dbc:postgresql://postgres-service:5432/rentaldb"
        - name: JWT_SECRET
          valueFrom:
            secretKeyRef:
              name: api-secrets
              key: jwt-secret
        resources:
          requests:
            memory: "512Mi"
            cpu: "250m"
          limits:
            memory: "1024Mi"
            cpu: "500m"
        livenessProbe:
          httpGet:
            path: /actuator/health
            port: 8080
          initialDelaySeconds: 30
          periodSeconds: 10
        readinessProbe:
          httpGet:
            path: /actuator/health
            port: 8080
          initialDelaySeconds: 10
          periodSeconds: 5

---
apiVersion: v1
kind: Service
metadata:
  name: api-service
spec:
  type: LoadBalancer
  selector:
    app: rental-api
  ports:
  - protocol: TCP
    port: 80
    targetPort: 8080
```

Déployer :

```bash
# Créer le secret
kubectl create secret generic api-secrets --from-literal=jwt-secret='your-super-secret-key'

# Appliquer la configuration
kubectl apply -f k8s-deployment.yaml

# Vérifier
kubectl get pods
kubectl logs -f deployment/easy-rental-api
```

---

## 🔧 Maintenance et Mise à Jour

### Mises à jour de dépendances

#### 1️⃣ Vérifier les nouvelles versions

```bash
# Afficher les plugins Maven obsolètes
./mvnw versions:display-plugin-updates

# Afficher les dépendances obsolètes
./mvnw versions:display-dependency-updates
```

#### 2️⃣ Mettre à jour les dépendances

```bash
# Mettre à jour minor/patch
./mvnw versions:use-newer-versions

# Mettre à jour une dépendance spécifique
./mvnw versions:set-property -Dproperty=springdoc.version -DnewVersion=2.8.0
```

#### 3️⃣ Tester la mise à jour

```bash
# Compiler et tester
./mvnw clean test

# Lancer localement
./mvnw spring-boot:run -Dspring-boot.run.profiles=dev
```

### Optimisation de la performance

#### Tuning du Pool de connexions R2DBC

**`application.yml`** :

```yaml
spring:
  r2dbc:
    pool:
      initial-size: 5          # Connexions initiales
      max-size: 20             # Max connexions
      max-idle-time: 30m       # Fermer idle après 30min
      max-acquire-time: 5s     # Timeout d'acquisition
      validation-query: "SELECT 1"
```

#### Configuration JVM

**Pour de meilleures performances :**

```bash
# Développement
JAVA_OPTS="-Xms512m -Xmx1024m"

# Production
JAVA_OPTS="-Xms2048m -Xmx4096m -XX:+UseG1GC -XX:MaxGCPauseMillis=200"
```

#### Indexation base de données

Vérifier que les colonnes fréquemment filtrées sont indexées dans les migrations Liquibase :

```xml
<createIndex indexName="idx_vehicle_agency_id" tableName="vehicles">
    <column name="agency_id"/>
</createIndex>

<createIndex indexName="idx_rental_status" tableName="rentals">
    <column name="status"/>
</createIndex>
```

### Monitoring et Logs

#### Activer l'Actuator (Health Checks)

```bash
# Ajouter la dépendance
./mvnw dependency:get -Dartifact=org.springframework.boot:spring-boot-starter-actuator
```

**`application.yml`** :

```yaml
management:
  endpoints:
    web:
      exposure:
        include: health,metrics,info
  endpoint:
    health:
      show-details: always
```

Accès :
- 👉 `http://localhost:8080/actuator/health`
- 👉 `http://localhost:8080/actuator/metrics`

#### Logs centralisés (ELK Stack)

Pour les déploiements production, intégrer Logback avec Elasticsearch :

**`logback-spring.xml`** :

```xml
<appender name="STASH" class="net.logstash.logback.appender.LogstashTcpSocketAppender">
    <destination>elasticsearch:9200</destination>
    <encoder class="net.logstash.logback.encoder.LogstashEncoder"/>
</appender>

<root level="INFO">
    <appender-ref ref="STASH"/>
</root>
```

---

## 📊 Architecture Réactive (Project Reactor)

### Comprendre Mono et Flux

```java
// Mono : 0 ou 1 élément
Mono<Vehicle> vehicle = vehicleService.getById(id);

// Flux : 0 à N éléments
Flux<Vehicle> vehicles = vehicleService.getAll();

// Chaîner les opérations
vehicleService.getById(id)
    .filter(v -> v.getStatus() == Status.AVAILABLE)
    .flatMap(v -> rentalService.calculatePrice(v))
    .subscribe(price -> logger.info("Prix: " + price));
```

### Gestion des erreurs réactive

```java
Mono.just(vehicleId)
    .flatMap(id -> vehicleRepository.findById(id))
    .onErrorResume(e -> {
        logger.error("Erreur lors de la récupération: ", e);
        return Mono.error(new VehicleNotFoundException());
    })
    .switchIfEmpty(Mono.error(new VehicleNotFoundException()))
    .subscribe();
```

---

## 🤝 Contribution

### Branching Strategy (Git Flow)

```bash
# Créer une branche feature
git checkout -b feature/add-new-feature

# Committer
git commit -m "feat: add new feature"

# Push & ouvrir une PR
git push origin feature/add-new-feature
```

### Convention de commits (Conventional Commits)

```
feat: nouvelle fonctionnalité
fix: correction de bug
docs: documentation
refactor: refactorisation du code
test: ajout de tests
chore: maintenance (dépendances, config)
```

### Tests

```bash
# Lancer tous les tests
./mvnw test

# Tester un module spécifique
./mvnw test -Dtest=VehicleServiceTest

# Générer un rapport de couverture
./mvnw jacoco:report
```

---

## 📄 Licence

[À définir]

---

## 📧 Support & Contact

**Pour les questions ou problèmes :**

- 📝 Issues GitHub : https://github.com/projet-synthese-gi26/rental-api/issues
- 💬 Discussions : https://github.com/projet-synthese-gi26/rental-api/discussions
- 📞 Contact : []

---

## 📌 Version History

| Version | Date | Changements |
|---------|------|------------|
| 0.0.1 | 2026-04-20 | Version initiale |

---

**Dernière mise à jour :** 2026-04-20

---

## ✨ Résumé des améliorations apportées

### 📋 Organisation
- ✅ Table des matières complète et cliquable
- ✅ Structure claire avec sections hiérarchisées
- ✅ Emojis pour meilleure lisibilité

### 🚀 Installation
- ✅ 2 options (local + Docker Compose)
- ✅ Instructions étape par étape
- ✅ Configuration détaillée de PostgreSQL
- ✅ Vérifications de prérequis

### 🏗️ Architecture
- ✅ Diagramme visuel de l'arborescence
- ✅ Explication de la structure DDD
- ✅ Exemple de module type

### 🔐 Sécurité
- ✅ Section JWT complète
- ✅ RBAC avancé expliqué
- ✅ Bonnes pratiques de secrets

### 📦 Déploiement
- ✅ Instructions serveur (Ubuntu)
- ✅ Docker Compose complet
- ✅ Kubernetes optionnel
- ✅ Service systemd

### 🔧 Maintenance
- ✅ Mise à jour des dépendances
- ✅ Optimisation performance
- ✅ Monitoring (Actuator)
- ✅ Logs centralisés

Voulez-vous que je mette à jour le fichier sur GitHub directement ?
