# TP 14 : Conteneurisation Spring Boot avec Docker

## Description

Ce projet illustre la conteneurisation d’une application Spring Boot avec Docker et Docker Compose, incluant une base de données MySQL.

## Objectifs

* Créer une image Docker à partir d’un projet Spring Boot
* Exécuter et gérer des conteneurs
* Configurer les variables d’environnement
* Déployer MySQL dans un conteneur séparé
* Orchestrer l’application et la base avec Docker Compose

## Technologies

* Java 17
* Spring Boot 3.2.5
* Docker & Docker Compose
* MySQL 8.0
* Maven

## Dépendances Spring Boot

* Spring Web
* Spring Data JPA
* MySQL Driver
* Lombok

---

## Étape 1 : Préparation du projet

### Configuration Spring Boot

Créé via [Spring Initializr](https://start.spring.io) :

* Group : `ma.ens`
* Artifact : `springdocker`
* Java 17

#### application.properties

```properties
spring.datasource.url=jdbc:mysql://localhost:3306/demo_db?createDatabaseIfNotExist=true
spring.datasource.username=root
spring.datasource.password=1234
spring.jpa.hibernate.ddl-auto=update
server.port=8080
```

### Compilation

```bash
mvn clean package
```

---

## Étape 2 : Dockerfile

```dockerfile
FROM eclipse-temurin:17-jdk-jammy
WORKDIR /app
COPY target/springdocker-0.0.1-SNAPSHOT.jar app.jar
EXPOSE 8080
ENTRYPOINT ["java", "-jar", "app.jar"]
```

---

## Étape 3 : Construire et exécuter l’image

```bash
docker build -t ens/springdocker:1.0 .
docker run -d -p 8080:8080 --name spring-app ens/springdocker:1.0
docker logs -f spring-app
docker stop spring-app
docker rm spring-app
```

---

## Étape 4 : Docker Compose avec MySQL

```yaml
version: '3.8'
services:
  mysql-db:
    image: mysql:8.0
    container_name: mysql-db
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: 1234
      MYSQL_DATABASE: demo_db
    ports:
      - "3306:3306"
    volumes:
      - mysql-data:/var/lib/mysql

  spring-app:
    image: ens/springdocker:1.0
    container_name: spring-app
    restart: always
    depends_on:
      - mysql-db
    environment:
      - SPRING_DATASOURCE_URL=jdbc:mysql://mysql-db:3306/demo_db?createDatabaseIfNotExist=true
      - SPRING_DATASOURCE_USERNAME=root
      - SPRING_DATASOURCE_PASSWORD=1234
      - SPRING_JPA_HIBERNATE_DDL_AUTO=update
    ports:
      - "8080:8080"

volumes:
  mysql-data:
```

### Commandes Docker Compose

```bash
docker-compose up -d
docker-compose logs -f
docker-compose down
```

---

## Étape 5 : Extensions possibles

* Ajouter phpMyAdmin pour gérer MySQL
* Publier l’image sur Docker Hub
* Intégrer dans un pipeline CI/CD (Jenkins, GitHub Actions…)

---

## Compétences acquises

* Création d’images Docker et exécution de conteneurs
* Gestion des logs et ports
* Configuration via variables d’environnement
* Orchestration multi-conteneurs avec Docker Compose
* Persistance des données MySQL via volumes

---

## le Test de notre travail

-<img width="986" height="227" alt="1" src="https://github.com/user-attachments/assets/685cf7a4-2661-4a8b-bc9f-f1a1fbd6d06c" />

-<img width="1770" height="301" alt="2" src="https://github.com/user-attachments/assets/c34df35c-fae7-4535-ab13-225c22bdf865" />
