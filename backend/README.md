# Backend – Spring Boot API 

This directory contains the **Spring Boot backend**.  
It exposes a REST API for managing **courses and lessons** and is fully containerized.

---

## Tech Stack

- Java 17
- Spring Boot
- H2 (in-memory DB)
- Maven
- Docker

---

## API Overview

Base path:
/api/courses


### Example Endpoints

- `GET /api/courses`
- `POST /api/courses`
- `PUT /api/courses/{id}`
- `DELETE /api/courses/{id}`

---

## Docker

### Dockerfile Highlights

- Multi-stage build
- Maven build stage
- Lightweight JRE runtime image

### Build locally

```bash
docker build -t course-api 
```

### Run locally

```bash
docker run -p 8080:8080 course-api
```

---

## CORS Configuration

CORS is configured using environment variables:

```java
@CrossOrigin(origins = "${APP_CORS_ORIGINS:*}")
```

This allows:
- Local development
- Docker Compose networking
- Production deployment behind Nginx

---

## Validation

The API uses custom enum validation to ensure data consistency.
Example: Category enum:

```java
FRONT_END("Front-end"),
BACK_END("Back-end")
```
Invalid values are rejected with HTTP 400.

---

## Testing the API

```bash
curl -i http://localhost:8080/api/courses
```

---

## Notes

- Designed to run behind a reverse proxy
- Stateless container
- Production-ready configuration


