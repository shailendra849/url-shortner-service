<div align="center">

# ⚡ URL Shortener

**A production-ready POC built with Spring Boot, MongoDB & Redis.**  
Shorten long URLs, set expiration times, and redirect at speed.

[![Java](https://img.shields.io/badge/Java-21-orange?style=flat-square&logo=openjdk)](https://openjdk.org/)
[![Spring Boot](https://img.shields.io/badge/Spring_Boot-3.x-brightgreen?style=flat-square&logo=springboot)](https://spring.io/projects/spring-boot)
[![MongoDB](https://img.shields.io/badge/MongoDB-latest-green?style=flat-square&logo=mongodb)](https://www.mongodb.com/)
[![Redis](https://img.shields.io/badge/Redis-cache-red?style=flat-square&logo=redis)](https://redis.io/)
[![Docker](https://img.shields.io/badge/Docker-Compose_+_Swarm-blue?style=flat-square&logo=docker)](https://www.docker.com/)
[![License](https://img.shields.io/badge/License-MIT-purple?style=flat-square)](LICENSE)
```
https://example.com/very/long/path/to/some/page  →  localhost:8080/abc123
```

</div>

---

## 🏗 Architecture
```
Client → Spring Boot API → Redis Cache → MongoDB
```

Incoming requests hit the Spring Boot layer, which checks **Redis** for a cached mapping before falling back to **MongoDB** — keeping redirects fast on repeat lookups.

---

## 📦 Stack

| Technology | Role |
|---|---|
| **Java 21** | Core runtime |
| **Spring Boot** | Web layer + Data access |
| **MongoDB** | Persistent URL storage |
| **Redis** | In-memory redirect cache |
| **Docker** | Compose & Swarm deployment |

---

## 🔌 API Reference

### `POST /shorten` — Create a short URL

**Request body:**
```json
{
  "url": "https://example.com",
  "expiresAt": "2025-05-21T11:28:00"  // optional
}
```

**Response `200 OK`:**
```json
{
  "shortUrl": "http://localhost:8080/abc123",
  "expiresAt": "2025-05-21T11:28:00"
}
```

---

### `GET /{id}` — Redirect to original URL

Resolves a short code and issues a `301 Redirect` to the original destination.  
Returns `404` if the link is expired or not found.
```bash
curl -L http://localhost:8080/abc123
# → 301 → https://example.com
```

---

## 🚀 Running the App

### 🐳 Docker Compose (recommended for local dev)
```bash
docker compose --profile production up --build -d
```

App available at `http://localhost:8080`.

---

### ⚙️ Docker Swarm (for multi-node / production)

**1. Initialize the swarm**
```bash
docker swarm init
```

**2. Create Docker secrets**
```bash
echo 8081       | docker secret create APP_PORT -
echo mongo      | docker secret create MONGO_HOST -
echo 27017      | docker secret create MONGO_PORT -
echo root       | docker secret create MONGO_USER -
echo password   | docker secret create MONGO_PASSWORD -
echo url_shortener | docker secret create MONG0_DB -
echo cache      | docker secret create REDIS_HOST -
echo 6379       | docker secret create REDIS_PORT -
```

**3. Deploy the stack**
```bash
docker stack deploy -c docker-swarm.yml url_shortener
```

---

## 📂 Project Structure
```
url-shortener/
├── src/main/java/
│   ├── controller/     # REST endpoints
│   ├── service/        # Business logic + cache layer
│   ├── model/          # URL document entity
│   └── repository/     # MongoDB repository
├── docker-compose.yml
├── docker-swarm.yml
└── README.md
```

---

## 🤝 Contributing

Pull requests are welcome! For major changes, open an issue first to discuss what you'd like to change.

---

<div align="center">
  <sub>Built for learning · MIT License</sub>
</div>
