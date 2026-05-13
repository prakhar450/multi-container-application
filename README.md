# Multi-Container Fibonacci Calculator

A distributed Fibonacci calculator demonstrating multi-container Docker architecture with a React frontend, Express API, PostgreSQL for persistence, Redis for pub/sub, and Nginx as a reverse proxy.

## Architecture

```
                    ┌─────────┐
                    │  Nginx  │ :3050
                    └────┬────┘
                   /           \
          ┌───────┐           ┌───────┐
          │ React │ :3000     │Express│ :5000
          │Client │           │  API  │
          └───────┘           └───┬───┘
                              /       \
                    ┌────────┐       ┌───────┐
                    │Postgres│       │ Redis │
                    └────────┘       └───┬───┘
                                         │
                                    ┌────────┐
                                    │ Worker │
                                    └────────┘
```

**How it works:**
1. User submits a Fibonacci index via the React frontend
2. Express API stores the index in PostgreSQL (history) and publishes to Redis
3. Worker subscribes to Redis, calculates fib(n), and stores the result back in Redis
4. Frontend displays both submitted indexes and calculated values

## Tech Stack

| Service | Technology |
|---------|-----------|
| Client | React 16, React Router, Axios |
| API Server | Express, pg, redis, cors |
| Worker | Node.js, Redis subscriber |
| Database | PostgreSQL |
| Cache/PubSub | Redis |
| Proxy | Nginx |
| Orchestration | Docker Compose |

## Getting Started

### Prerequisites

- Docker & Docker Compose

### Run

```bash
docker-compose up --build
```

The application will be available at `http://localhost:3050`.

### API Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| `GET` | `/api/values/all` | All submitted indexes (from PostgreSQL) |
| `GET` | `/api/values/current` | Calculated Fibonacci values (from Redis) |
| `POST` | `/api/values` | Submit a new index (max 40) |

## Project Structure

```
client/              # React frontend
  src/
    Fib.js           # Main calculator component
    OtherPage.js     # Secondary page
server/              # Express API
  index.js           # Routes + PostgreSQL/Redis connections
  keys.js            # Environment configuration
worker/              # Fibonacci calculator
  index.js           # Redis subscriber + fib computation
nginx/               # Reverse proxy
  default.conf       # Routing rules
docker-compose.yml   # Service orchestration
```
