# Phase 1: Core Infrastructure

**Project:** Webhook Delivery Platform  
**Phase:** 1 of 4  
**Duration:** 3 weeks (2026-02-04 → 2026-02-25)  
**Status:** 🟢 In Progress (ahead of schedule)

---

## Goal
Working delivery pipeline with REST API, async workers, and local development environment.

---

## Deliverables

| Task | Issue | Owner | Status |
|------|-------|-------|--------|
| PostgreSQL schema design | ISI-13 | Sterling | ✅ Complete |
| REST API for endpoint CRUD | ISI-10 | Sterling | ✅ Complete |
| Event ingestion endpoint | ISI-11 | Sterling | ✅ Complete |
| Delivery workers with retry logic | ISI-12 | Sterling | ✅ Complete |
| Docker compose local dev setup | ISI-14 | Sterling | ✅ Complete |
| K8s/PostgreSQL/Redis infrastructure | ISI-15 | Krieger | ⏳ In Progress |

---

## Technical Stack

- **Language:** Go 1.22+
- **Database:** PostgreSQL 15+
- **Queue:** Redis 7+ (sorted sets)
- **API:** REST with Chi router
- **Workers:** Go routines with Redis-backed queue

---

## Schema Overview

### Tables Created
1. `tenants` - Multi-tenant organization records
2. `endpoints` - Webhook endpoint configurations with circuit breaker state
3. `events` - Webhook events received from customers
4. `deliveries` - Individual delivery attempts to endpoints
5. `dead_letter` - Failed deliveries that exceeded retry limits
6. `delivery_attempts` - Audit log of all delivery attempts
7. `api_keys` - Tenant API key management
8. `usage_records` - Foundation for billing metering

---

## API Endpoints

### Endpoints CRUD
- `POST /v1/endpoints` - Create endpoint
- `GET /v1/endpoints` - List endpoints
- `GET /v1/endpoints/{id}` - Get endpoint
- `PUT /v1/endpoints/{id}` - Update endpoint
- `DELETE /v1/endpoints/{id}` - Delete endpoint (soft)

### Events
- `POST /v1/events` - Ingest event
- `GET /v1/events` - List events
- `GET /v1/events/{id}` - Get event

### Health
- `GET /health` - Health check
- `GET /ready` - Readiness probe

---

## Worker Features

### Retry Strategy
| Attempt | Delay | When |
|---------|-------|------|
| 1 | Immediate | First failure |
| 2 | 5 minutes | Second failure |
| 3 | 30 minutes | Third failure |
| DLQ | Never | Max retries exceeded |

### Circuit Breaker
- Opens after 5 consecutive failures
- Stays open for 5 minutes
- Half-open state allows test requests
- Closes after 3 successful deliveries

### Signature Algorithm
- HMAC-SHA256 (Stripe-style)
- Header: `X-Signature: sha256=<hex>`
- Payload signed with endpoint secret

---

## Local Development

```bash
# Start infrastructure
docker-compose up -d postgres redis

# Run migrations
make migrate

# Seed test data
make seed

# Run API server (hot reload)
make dev-api

# Run workers (hot reload)
make dev-worker
```

---

## Testing

### Unit Tests
```bash
make test
```

### Integration Tests
```bash
make test-integration
```

### Manual Testing
```bash
# Create endpoint
curl -X POST http://localhost:8080/v1/endpoints \
  -H "Content-Type: application/json" \
  -H "X-Tenant-ID: your-tenant-id" \
  -d '{"name":"Test","url":"https://httpbin.org/post","secret":"webhook-secret-123"}'

# Send event
curl -X POST http://localhost:8080/v1/events \
  -H "Content-Type: application/json" \
  -H "X-Tenant-ID: your-tenant-id" \
  -d '{"event_type":"user.created","payload":{"user_id":"123","email":"test@example.com"}}'
```

---

## Dependencies

| Dependency | From | Status |
|------------|------|--------|
| PostgreSQL cluster | Krieger | ⏳ Pending |
| Redis cluster | Krieger | ⏳ Pending |
| K8s namespace | Krieger | ⏳ Pending |

---

## Risks & Mitigations

| Risk | Status | Mitigation |
|------|--------|------------|
| Queue ordering | ✅ Resolved | Redis sorted sets guarantee ordering |
| Worker scaling | ✅ Ready | HPA configured, can scale to N workers |
| Circuit breaker false positives | ✅ Resolved | Half-open state allows recovery testing |

---

## Completion Criteria

- [x] API responds to all endpoint CRUD operations
- [x] Events are queued and processed asynchronously
- [x] Workers deliver to endpoints with retries
- [x] Circuit breakers open/close correctly
- [x] Failed deliveries go to dead letter queue
- [x] Docker compose runs full stack locally
- [x] Tests pass (unit + integration)

---

## Handoff to Phase 2

**Ready for:**
- Ray (Frontend) to build dashboard against working API
- Integration with real customer endpoints
- Load testing preparation

**API Base URL:** `http://localhost:8080/v1` (local)  
**Documentation:** See `internal/api/` for handler implementations

---

## Notes

**Sterling's Notes:**
- All Phase 1 features implemented in Go
- Redis queue using sorted sets for pending + retry queues
- HMAC-SHA256 signatures ready for Phase 3 security hardening
- Usage metering table ready for Phase 3 billing integration
- Docker compose includes hot reload for rapid development

**Blockers:** None. Awaiting Krieger's infrastructure for production deployment.
