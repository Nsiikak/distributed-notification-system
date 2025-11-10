# 📨 Distributed Notification System (NestJS Microservices)

A scalable, fault-tolerant **notification system** built with **NestJS microservices** that delivers **emails** and **push notifications** asynchronously using **RabbitMQ** as the message broker and **PostgreSQL + Redis** for data management and caching.

This project is designed for **Stage 4 Backend Task: Microservices & Message Queues**, focusing on **event-driven architecture**, **asynchronous communication**, and **resilient distributed systems**.

---

## 🚀 Core Architecture

This system is composed of five independent NestJS microservices that communicate asynchronously through **RabbitMQ** and synchronously via REST for internal lookups.

### 🧩 Services Overview

| Service | Description |
|----------|--------------|
| **API Gateway Service** | Entry point for all notification requests. Handles authentication, validation, and routes messages to queues. Tracks notification status and exposes REST APIs. |
| **User Service** | Manages user accounts, contact info (email, push tokens), and notification preferences. Handles login and permissions. Uses PostgreSQL. |
| **Email Service** | Listens to the `email.queue`, fills templates with variables, and sends emails via SMTP or SendGrid API. Handles delivery confirmations and bounces. |
| **Push Service** | Listens to the `push.queue`, sends mobile/web push notifications (via FCM, OneSignal, or Web Push), validates device tokens, and supports rich notifications. |
| **Template Service** | Manages notification templates and versions. Handles variable substitution and multi-language support. Uses PostgreSQL. |

---

## 🐇 Message Queue Setup

**Broker:** RabbitMQ  
**Exchange:** `notifications.direct`

Queues:
```

├── email.queue  → Email Service
├── push.queue   → Push Service
└── failed.queue → Dead Letter Queue

````

Each message contains metadata, request ID (for idempotency), and user/template references.

---

## 🏗️ Tech Stack

| Layer | Technology |
|--------|-------------|
| **Framework** | NestJS (Monorepo with microservices) |
| **Language** | TypeScript |
| **Message Broker** | RabbitMQ |
| **Databases** | PostgreSQL (Primary), Redis (Cache & Rate Limiting) |
| **Containerization** | Docker + Docker Compose |
| **CI/CD** | GitHub Actions |
| **API Documentation** | Swagger (OpenAPI Spec) |
| **Monitoring** | Health checks (`/health`) + Logging with correlation IDs |

---

## 🧱 System Design

**Key Concepts Implemented:**

- **Asynchronous Messaging:** RabbitMQ used for background message passing.
- **Circuit Breaker:** Prevents cascading failures when third-party APIs (SMTP/FCM) fail.
- **Retry & Backoff:** Exponential retries for transient failures; moves dead messages to `failed.queue`.
- **Service Discovery:** Services communicate via environment variables or internal DNS in Docker.
- **Idempotency:** Unique `request_id` ensures no duplicate notifications.
- **Health Checks:** `/health` endpoint in each service for uptime monitoring.
- **Distributed Logging:** Each notification request includes a `correlation_id` for end-to-end tracing.

---

## ⚙️ Setup & Installation

### 1. Clone the Repository
```bash
git clone https://github.com/<your-username>/distributed-notification-system.git
cd distributed-notification-system
````

### 2. Environment Setup

Create a `.env` file at the root directory:

```bash
RABBITMQ_URL=amqp://guest:guest@rabbitmq:5672
POSTGRES_URL=postgres://user:password@postgres:5432/notifications_db
REDIS_URL=redis://redis:6379
JWT_SECRET=supersecretkey
SMTP_HOST=smtp.gmail.com
SMTP_PORT=587
SMTP_USER=<your_email>
SMTP_PASS=<your_password>
```

Or copy from the example:

```bash
cp .env.example .env
```

### 3. Run with Docker

```bash
docker-compose up --build
```

This command starts:

* All microservices (API Gateway, User, Email, Push, Template)
* RabbitMQ (Broker + Management UI)
* PostgreSQL and Redis
* Swagger UI for each REST service

### 4. Access RabbitMQ Dashboard

Open: [http://localhost:15672](http://localhost:15672)
Default credentials: `guest / guest`

---

## 📦 Project Structure

```
distributed-notification-system/
│
├── apps/
│   ├── api-gateway/
│   ├── user-service/
│   ├── email-service/
│   ├── push-service/
│   └── template-service/
│
├── libs/
│   ├── shared/
│   │   ├── dto/
│   │   ├── interfaces/
│   │   ├── constants/
│   │   ├── utils/
│   │   └── decorators/
│
├── docker-compose.yml
├── .env.example
├── nest-cli.json
├── package.json
├── tsconfig.json
└── README.md
```

---

## 📡 API Endpoints

### Notification Request

**POST** `/api/v1/notifications/`

```json
{
  "notification_type": "email",
  "user_id": "uuid",
  "template_code": "welcome_email",
  "variables": {
    "name": "Nsikak",
    "link": "https://example.com"
  },
  "request_id": "req_12345",
  "priority": 1
}
```

### User Creation

**POST** `/api/v1/users/`

```json
{
  "name": "Nsikak",
  "email": "nsikak@example.com",
  "push_token": "device_token_xyz",
  "preferences": { "email": true, "push": false },
  "password": "strongpassword"
}
```

---

## 🔁 Response Format

```json
{
  "success": true,
  "data": { "notification_id": "abc123" },
  "message": "Notification queued successfully",
  "meta": {
    "total": 1,
    "limit": 10,
    "page": 1,
    "total_pages": 1,
    "has_next": false,
    "has_previous": false
  }
}
```

---

## 🧠 Learning Objectives

This project demonstrates:

* Microservices decomposition with NestJS
* Asynchronous event-driven messaging
* Distributed system fault tolerance
* Message retries & dead-letter handling
* Scalable architecture design
* DevOps integration with CI/CD pipelines

---

## 🧰 CI/CD Workflow (GitHub Actions)

The workflow automates:

* Code linting and testing
* Docker image build
* Push to Docker Hub
* SSH deployment to remote server

**File:** `.github/workflows/deploy.yml`

---

## 📊 Performance Targets

| Metric                | Target                       |
| --------------------- | ---------------------------- |
| Throughput            | 1,000+ notifications/min     |
| API Response Time     | < 100ms                      |
| Delivery Success Rate | ≥ 99.5%                      |
| Uptime                | ≥ 99%                        |
| Scalability           | Horizontal scaling supported |

---

## 🗺️ System Diagram

See `/diagrams/system-architecture.png` for a visual overview of:

* Service connections
* RabbitMQ queues
* Retry and failure flow
* Database relationships
* Scaling plan

---

## 👥 Contributors

Stage 4 Backend Team – Group of 4
**Institution:** HNG
**Project Lead:** Nsikak-Abasi Ebong
**Framework:** NestJS Microservices
**Deadline:** November 12th, 2025 (11:59 PM GMT+1)

---

## 🏁 License

This project is licensed under the **MIT License**.

---

