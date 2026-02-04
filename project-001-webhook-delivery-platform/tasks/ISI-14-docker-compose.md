# Task: ISI-14 - Docker Compose Local Dev Setup

**Project:** Webhook Delivery Platform  
**Phase:** 1  
**Issue:** ISI-14  
**Owner:** Sterling (Backend Engineer)  
**Status:** ✅ Complete  
**Due:** 2026-02-25

---

## Description

Create Docker Compose setup for local development including PostgreSQL 15, Redis 7, API server, and Worker with hot reload, migration scripts, and test data seeding.

---

## Requirements

### Services

1. **PostgreSQL 15**
   - Database: hooks_platform
   - User: hooks / hooks
   - Ports: 5432:5432
   - Persistent volume

2. **Redis 7**
   - Port: 6379:6379
   - Persistent volume
   - No auth (local dev)

3. **API Server**
   - Port: 8080:8080
   - Hot reload (Air)
   - Environment file support

4. **Worker**
   - Hot reload
   - Scalable (docker-compose up --scale worker=5)

### Features

- **Hot Reload:** Air for Go hot reloading
- **Migrations:** Automated on startup
- **Seeding:** Test data for development
- **Logs:** Colored, structured output
- **Networking:** Internal Docker network

---

## Implementation

**File:** `docker-compose.yml`

```yaml
version: '3.8'
services:
  postgres:
    image: postgres:15-alpine
    environment:
      POSTGRES_DB: hooks_platform
      POSTGRES_USER: hooks
      POSTGRES_PASSWORD: hooks
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data
      - ./migrations:/docker-entrypoint-initdb.d

  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"
    volumes:
      - redis_data:/data

  api:
    build:
      context: .
      dockerfile: Dockerfile.dev
    environment:
      ENV: development
      DB_HOST: postgres
      REDIS_HOST: redis
    ports:
      - "8080:8080"
    volumes:
      - .:/app
    depends_on:
      - postgres
      - redis

  worker:
    build:
      context: .
      dockerfile: Dockerfile.dev
    command: ["air", "-c", ".air.worker.toml"]
    environment:
      ENV: development
      DB_HOST: postgres
      REDIS_HOST: redis
    volumes:
      - .:/app
    depends_on:
      - postgres
      - redis
      - api

volumes:
  postgres_data:
  redis_data:
```

**Additional Files:**
- `Dockerfile.dev` - Development build with Air
- `.air.api.toml` - Air config for API
- `.air.worker.toml` - Air config for Worker
- `Makefile` - Common commands
- `scripts/migrate.sh` - Migration runner
- `scripts/seed.sql` - Test data

---

## Makefile Commands

```makefile
.PHONY: up down migrate seed dev-api dev-worker test

up:
	docker-compose up -d postgres redis

down:
	docker-compose down

migrate:
	psql postgres://hooks:hooks@localhost:5432/hooks_platform -f migrations/001_init_schema.sql

seed:
	psql postgres://hooks:hooks@localhost:5432/hooks_platform -f scripts/seed.sql

dev-api:
	air -c .air.api.toml

dev-worker:
	air -c .air.worker.toml

test:
	go test ./...
```

---

## Acceptance Criteria

- [x] `docker-compose up` starts all services
- [x] PostgreSQL migrations run automatically
- [x] API server hot reloads on code change
- [x] Worker hot reloads on code change
- [x] Services communicate on internal network
- [x] Data persists across restarts
- [x] Test data seeding script works
- [x] README documents setup process

---

## Quick Start

```bash
# 1. Clone and start
git clone <repo>
cd webhook-platform
docker-compose up -d postgres redis

# 2. Run migrations
make migrate

# 3. Seed test data
make seed

# 4. Start API (Terminal 1)
make dev-api

# 5. Start Worker (Terminal 2)
make dev-worker

# 6. Test
curl http://localhost:8080/health
```

---

## Notes

**Completed:** 2026-02-04  
**Dependencies:** None (self-contained)

**Design Decisions:**
- Air for hot reload (fast feedback)
- Separate toml configs for api/worker
- Volume mounts for live code updates
- Initdb.d for auto-migrations

**Portability:**
- Works on macOS, Linux, Windows (WSL2)
- No external dependencies
- Self-contained database

---

## Related

- Uses: All Phase 1 services
- Enables: Frontend development (Ray's dashboard)
- Replaces: Need for Krieger's infra in local dev
