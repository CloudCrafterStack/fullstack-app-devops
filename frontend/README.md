# Frontend – Angular + Nginx 

This directory contains the Angular frontend.
The application is built as a production bundle and served using Nginx inside a Docker container.

---

## Tech Stack

- Angular
- TypeScript
- Nginx
- Docker (multi-stage build)

---

## Application Role

The frontend provides a user interface for:

- Listing courses
- Creating and editing courses
- Managing lessons
- Communicating with the backend REST API

All API calls are made using relative paths to allow easy deployment across environments.

---

## Docker Architecture

The frontend Docker image uses a multi-stage build:

Build stage:
- Node.js installs dependencies
- Angular builds a production-ready bundle

Runtime stage:
- Nginx serves static files
- Nginx proxies API requests to the backend container

This keeps the final image lightweight and production-ready.

---

## API Communication

All API requests are sent to:

/api/*

This avoids hardcoded backend URLs and allows the same frontend image to run:

- Locally
- In Docker Compose
- On AWS EC2
- Behind reverse proxies

---

## Nginx Reverse Proxy

Nginx is configured to:

- Serve the Angular application
- Forward /api requests to the backend container

This design:
- Eliminates CORS issues in production
- Keeps frontend and backend on the same origin

---

## Local Development

For local development without Docker, an Angular proxy configuration can be used:

/api → http://localhost:8080

This file is only used in development and is not included in the production Docker image.

---

## Build and Run Locally with Docker

Build the frontend image:

```bash
docker build -t course-web
```

Run the container:

```bash
docker run -p 4200:80 course-web
```

Then open in browser:

http://localhost:4200

