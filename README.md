# 🎓 Campus-Event

![Build Status](https://img.shields.io/badge/build-passing-brightgreen?style=flat-square)
![Java](https://img.shields.io/badge/Java-17-007396?style=flat-square&logo=java)
![Spring Boot](https://img.shields.io/badge/Spring%20Boot-3.x-6DB33F?style=flat-square&logo=springboot)
![License](https://img.shields.io/badge/license-MIT-blue?style=flat-square)
![Version](https://img.shields.io/badge/version-0.0.1--SNAPSHOT-orange?style=flat-square)
![Microservices](https://img.shields.io/badge/architecture-microservices-purple?style=flat-square)

> **Plateforme de gestion d'évènements universitaires** — Permettre aux associations étudiantes de créer des événements et aux étudiants de réserver leurs tickets en ligne, le tout en un seul endroit.

---

## Table des matières

- [Description](#-description--contexte)
- [Architecture](#-architecture)
- [Prérequis](#-prérequis)
- [Installation](#-installation)
- [Utilisation & API](#-utilisation--exemples-dapi)
- [Toolchain du projet](#-toolchain-du-projet)
- [Guide de contribution](#-guide-de-contribution)
- [Licence](#-licence)

---

## Description & Contexte

**Campus-Event** est une application web en architecture **microservices** développée pour l'université. Elle permet aux **associations étudiantes** de publier et gérer des événements (conférences, tournois, soirées culturelles) et aux **étudiants** de consulter ces événements et de réserver leurs tickets directement en ligne, sans file d'attente physique.

Le projet adopte une approche **Agile/Scrum** avec une toolchain professionnelle intégrant Notion, Trello, GitHub et Slack pour simuler les conditions réelles d'une squad de développement.

---

## Architecture

Le projet est découpé en **4 microservices Spring Boot indépendants**, chacun responsable d'un domaine métier précis :

```
campusevent/
├── AuthService/          → Authentification & gestion des tokens JWT
├── UserService/          → Gestion des profils utilisateurs
├── EventService/         → Création et gestion des événements
├── ReservationService/   → Réservations et tickets
└── db/
    └── schema.sql        → Schéma de la base de données
```

| Service              | Port   | Rôle                                      | Build  |
|----------------------|--------|-------------------------------------------|--------|
| `AuthService`        | `8081` | Authentification, JWT                     | Maven  |
| `UserService`        | `8082` | CRUD utilisateurs, profils                | Maven  |
| `EventService`       | `8083` | CRUD événements, catégories               | Maven  |
| `ReservationService` | `8084` | Réservations, génération de tickets       | Gradle |

---

## Prérequis

Assurez-vous d'avoir les outils suivants installés **avant** de lancer le projet :

| Outil        | Version minimale | Lien                                      |
|--------------|-----------------|-------------------------------------------|
| Java (JDK)   | 17+             | https://adoptium.net                      |
| Maven        | 3.8+            | https://maven.apache.org                  |
| Gradle       | 9.4+            | https://gradle.org                        |
| MySQL        | 8.0+            | https://dev.mysql.com                     |
| Git          | 2.x             | https://git-scm.com                       |

Vérifiez vos versions :

```bash
java -version
mvn -version
gradle -version
mysql --version
git --version
```

---

##  Installation

### 1. Cloner le dépôt

```bash
git clone [https://github.com/<votre-organisation>/campusevent.git](https://github.com/Dingboy03/campus_event.git)
cd campusevent
```

### 2. Configurer la base de données

```bash
# Se connecter à MySQL
mysql -u root -p

# Exécuter le schéma SQL
mysql> source db/schema.sql;
```

### 3. Configurer les variables d'environnement

Dans chaque service, éditez le fichier `src/main/resources/application.properties` :

```properties
# Exemple pour UserService
spring.datasource.url=jdbc:mysql://localhost:3306/campusevent
spring.datasource.username=root
spring.datasource.password=VotreMotDePasse

server.port=8082
```

> Ne commitez **jamais** vos mots de passe en clair. Utilisez des variables d'environnement ou un fichier `.env` (déjà présent dans `.gitignore`).

### 4. Lancer les services

Lancez chaque service dans un terminal séparé :

```bash
# AuthService (Maven)
cd AuthService
./mvnw spring-boot:run

# UserService (Maven)
cd UserService
./mvnw spring-boot:run

# EventService (Maven)
cd EventService
./mvnw spring-boot:run

# ReservationService (Gradle)
cd ReservationService
./gradlew bootRun
```

---

## 🔌 Utilisation & Exemples d'API

### Authentification — `AuthService` (port 8081)

```bash
# Connexion et récupération du token JWT
curl -X POST http://localhost:8081/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email": "etudiant@univ.bf", "password": "motdepasse"}'
```

### Événements — `EventService` (port 8083)

```bash
# Lister tous les événements
curl -X GET http://localhost:8083/api/events \
  -H "Authorization: Bearer <TOKEN_JWT>"

# Créer un événement
curl -X POST http://localhost:8083/api/events \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer <TOKEN_JWT>" \
  -d '{
    "titre": "Hackathon de fin d'\''année",
    "description": "48h de code non-stop !",
    "date": "2025-06-15",
    "lieu": "Amphi A",
    "capacite": 200
  }'
```

### Réservations — `ReservationService` (port 8084)

```bash
# Réserver un ticket
curl -X POST http://localhost:8084/api/reservations \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer <TOKEN_JWT>" \
  -d '{"eventId": 1, "userId": 42}'
```

---

## Toolchain du projet

Ce projet s'appuie sur **4 outils interconnectés** qui forment notre système nerveux de développement :

| Outil      | Usage                                           | Lien                      |
|------------|-------------------------------------------------|---------------------------|
| **Notion** | Documentation technique & cahier des charges    | `[Lien Notion]`           |
| **Trello** | Kanban Agile (Backlog → Terminé)                | `[Lien Trello]`           |
| **GitHub** | Versioning du code source                       | `[Lien GitHub]`           |
| **Slack**  | Communication & alertes automatisées GitHub     | `[Lien Slack]`            |

### Intégrations actives

- **GitHub ↔ Trello** : Chaque commit référence sa carte Trello (`[Trello-Card-#N]`)
- **GitHub ↔ Slack** : Chaque `git push` déclenche une alerte automatique dans `#alertes-github`

### Convention de commit

```bash
# Format obligatoire
git commit -m "type: description courte [Trello-Card-#N]"

# Exemples
git commit -m "feat: ajout de la route POST /api/events [Trello-Card-#4]"
git commit -m "fix: correction de la validation JWT [Trello-Card-#7]"
git commit -m "docs: mise à jour du README [Trello-Card-#2]"
```

### Branches

```
main              → Code stable, validé
develop           → Intégration continue
feature/api-event → Développement d'une fonctionnalité
fix/bug-jwt       → Correction d'un bug
```

---

## Guide de contribution

1. **Assignez-vous une carte** sur le tableau Trello et déplacez-la dans `En Cours`
2. **Créez une branche** depuis `develop` :
   ```bash
   git checkout develop
   git pull origin develop
   git checkout -b feature/nom-de-la-fonctionnalite
   ```
3. **Développez & committez** en respectant la convention de nommage
4. **Poussez** votre branche :
   ```bash
   git push origin feature/nom-de-la-fonctionnalite
   ```
5. **Ouvrez une Pull Request** vers `develop` sur GitHub
6. **Déplacez votre carte** Trello dans `Revue de Code`
7. Après validation par au moins **1 coéquipier**, la PR est mergée et la carte passe à `Terminé`

> Pour plus de détails, consultez la page **"Processus de contribution"** sur Notion.

---

## Licence

Ce projet est distribué sous licence **MIT**.

```
MIT License — Copyright (c) 2025 Équipe Campus-Event

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software [...] subject to the following conditions: The above copyright
notice and this permission notice shall be included in all copies.
```

Voir le fichier [LICENSE](./LICENSE) pour le texte complet.

---

<div align="center">
  <sub>Développé avec ☕ par l'équipe Campus-Event • Université 2024–2025</sub>
</div>