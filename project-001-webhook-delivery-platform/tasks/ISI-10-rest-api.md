# Task: ISI-10 - REST API for Endpoint CRUD

**Project:** Webhook Delivery Platform  
**Phase:** 1  
**Issue:** ISI-10  
**Owner:** Sterling (Backend Engineer)  
**Status:** ✅ Complete  
**Due:** 2026-02-18

---

## Description

Implement REST API for webhook endpoint management. Support creating, reading, updating, and deleting webhook endpoints with multi-tenant isolation, validation, and rate limiting foundation.

---

## Requirements

### Endpoints

| Method | Path | Description |
|--------|------|-------------|
| POST | /v1/endpoints | Create new endpoint |
| GET | /v1/endpoints | List all endpoints |
| GET | /v1/endpoints/{id} | Get specific endpoint |
| PUT | /v1/endpoints/{id} | Update endpoint |
| DELETE | /v1/endpoints/{id} | Soft delete endpoint |

### Request/Response Format

**Create Endpoint:**
```json
POST /v1/endpoints
Headers: X-Tenant-ID: {tenant_uuid}

{
  "name": "Production Webhook",
  "url": "https://api.customer.com/webhooks",
  "secret": "webhook-secret-123",
  "max_retries": 3,
  "description": "Main production endpoint"
}

Response 201:
{
  "success": true,
  "data": {
    "id": "uuid",
    "name": "Production Webhook",
    "url": "https://api.customer.com/webhooks",
    "status": "active",
    "circuit_state": "closed",
    "failure_count": 0,
    "max_retries": 3,
    "created_at": "2026-02-04T18:00:00Z"
  }
}
```

### Validation Rules

- `name`: Required, 1-255 characters
- `url`: Required, valid URL format (http/https)
- `secret`: Optional, 16-255 characters
- `max_retries`: Optional, 0-10, default 3
- `description`: Optional, max 1000 characters

### Multi-tenancy

- Tenant ID extracted from `X-Tenant-ID` header
- All queries scoped to tenant_id
- No cross-tenant data leakage

### Rate Limiting Foundation

- Structure in place for per-tenant limits
- Full implementation deferred to Phase 3

---

## Implementation

**Files:**
- `internal/api/handlers/endpoint_handler.go` - HTTP handlers
- `internal/api/middleware.go` - Tenant auth middleware
- `internal/api/response.go` - Standardized responses

**Key Components:**

1. **EndpointHandler**
   - CRUD operations
   - Validation
   - Response formatting

2. **Middleware**
   - TenantAuthMiddleware - extracts X-Tenant-ID
   - RequestIDMiddleware - tracing
   - RecoveryMiddleware - panic recovery
   - CORSMiddleware - web dashboard support

3. **Response Format**
   - Consistent JSON structure
   - Success/error wrapping
   - HTTP status codes

---

## Acceptance Criteria

- [x] All 5 CRUD endpoints functional
- [x] Multi-tenant isolation working
- [x] Validation returns proper errors
- [x] Soft delete (not hard delete)
- [x] Circuit breaker state returned
- [x] CORS enabled for dashboard
- [x] Response format standardized

---

## Testing

**Manual Test:**
```bash
curl -X POST http://localhost:8080/v1/endpoints \
  -H "Content-Type: application/json" \
  -H "X-Tenant-ID: test-tenant-uuid" \
  -d '{"name":"Test","url":"https://httpbin.org/post"}'
```

**Integration Tests:**
- Create → Get → Update → Delete flow
- Validation error cases
- Tenant isolation verification

---

## Notes

**Completed:** 2026-02-04  
**Tech Stack:** Go, Chi router, PostgreSQL

**Design Decisions:**
- Chi router for clean routing
- Repository pattern for database access
- Middleware chain for cross-cutting concerns
- Delegated auth pattern (X-Tenant-ID header)

**Security:**
- Secrets never returned in API responses
- Tenant isolation enforced at DB level
- Validation before DB operations

---

## Related

- Depends on: ISI-13 (PostgreSQL schema)
- Related to: ISI-11 (event ingestion uses same pattern)
- Used by: Phase 2 dashboard
