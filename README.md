# dunning-core

Async FastAPI microservice for subscription payment recovery (dunning). It ingests payment-gateway webhooks, detects failed subscription charges and schedules smart retries to recover revenue.

## Overview

`dunning-core` is gateway-agnostic: every payment provider sits behind a port (interface) in the domain layer, and each provider is an adapter in the infrastructure layer. Stripe already offers built-in retries; this service targets teams that need custom retry strategies and a single recovery pipeline across several gateways.

Status: Sprint 0, under active development. Nothing below the "Planned Features" heading is implemented yet.

## Planned Features

- Webhook ingestion with signature verification and idempotency
- Gateways: Stripe, Paddle, Lemon Squeezy
- Failed-payment detection and decline classification (soft/hard)
- Configurable retry strategies (backoff, jitter, decline category)
- REST API for subscriptions, invoices, recovery stats and manual retries

## Tech Stack

- Python 3.12, managed with [uv](https://docs.astral.sh/uv/)
- FastAPI, Uvicorn, Pydantic v2 (strict typing)
- SQLAlchemy 2.0 (async) with asyncpg, Alembic migrations
- PostgreSQL (required from day one)
- Pytest, pytest-asyncio, httpx, Ruff, mypy

## Architecture

Clean Architecture; dependencies point inwards (presentation, infrastructure -> application -> domain).

```text
src/dunning_core/
├── domain/            entities, value objects, ports, domain services
│   ├── entities/
│   ├── value_objects/
│   ├── ports/
│   └── services/
├── application/       use cases and DTOs
│   ├── use_cases/
│   └── dto/
├── infrastructure/    persistence, gateway adapters, configuration
│   ├── persistence/
│   ├── gateways/
│   └── config/
└── presentation/      HTTP layer
    └── api/
        ├── routes/
        └── schemas/
tests/
├── unit/
└── integration/
```

## Getting Started

```bash
uv sync
uv run pytest
```

PostgreSQL is required for integration tests and runtime. Connection setup will be documented in Sprint 0.

## Roadmap

- S0 Foundations: tooling, settings, Postgres via Docker, CI
- S1 Domain: value objects, entities, ports
- S2 Persistence: async SQLAlchemy, Alembic, repositories
- S3 Webhooks: normalized events, signature verification, gateway adapters
- S4 Failure detection: decline classification, subscription state machine
- S5 Retry engine: strategies, worker, gateway execution
- S6 REST API: queries, stats, manual retry, API key auth
- S7 Hardening: logging, health checks, rate limiting, Docker image
