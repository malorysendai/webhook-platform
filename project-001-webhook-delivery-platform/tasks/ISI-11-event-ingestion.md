# Task: ISI-11 - Event Ingestion Endpoint

**Project:** Webhook Delivery Platform  
**Phase:** 1  
**Issue:** ISI-11  
**Owner:** Sterling (Backend Engineer)  
**Status:** ✅ Complete  
**Due:** 2026-02-20

---

## Description

Implement event ingestion API that receives webhook events from customers, validates them, queues for async processing, and returns immediately with event ID.

---

## Requirements

### API Endpoint

| Method | Path | Description |
|--------|------|-------------|
| POST | /v1/events | Ingest new event |
| GET | /v1/events | List events |
| GET | /v1/events/{id} | Get specific event |

### Request/Response Format

**Ingest Event:**
```json
POST /v1/events
Headers: 
  Content-Type: application/json
  X-Tenant-ID: {tenant_uuid}

{
  "event_type": "user.created",
  "payload": {
    "user_id": "123",
    "email": "user@example.com",
    "plan": "pro"
  },
  "idempotency_key": "unique-key-123"
}

Response 201:
{
  "success": true,
  "data": {
    "id": "event-uuid",
    "event_type": "user.created",
    "payload": { ... },
    "status": "processing",
    "created_at": "2026-02-04T18:00:00Z"
  }
}
```

### Validation Rules

- `event_type`: Required, 1-100 characters
- `payload`: Required, valid JSON object
- `idempotency_key`: Optional, max 255 characters

### Idempotency

- Duplicate idempotency keys return existing event
- Dedup window: 24 hours (configurable)
- Same tenant, same key = same event

### Async Processing

1. Receive request
2. Validate payload
3. Store event in database
4. Create delivery records for active endpoints
5. Queue deliveries to Redis
6. Return event ID immediately
7. Workers process deliveries asynchronously

### Rate Limiting (Foundation)

- Per-tenant rate limit structure
- Full enforcement deferred to Phase 3
- Basic logging in place

---

## Implementation

**Files:**
- `internal/api/handlers/event_handler.go` - HTTP handlers
- `internal/queue/redis_queue.go` - Queue operations

**Key Components:**

1. **EventHandler.Create**
   - Payload validation
   - Idempotency check
   - Endpoint lookup
   - Delivery creation
   - Queue publishing

2. **RedisQueue**
   - Sorted sets for priority queue
   - Delayed scheduling support
   - Batch operations

3. **Processing Flow**
   ```
   Request → Validate → Store Event
     ↓
   Lookup Endpoints
     ↓
   Create Deliveries
     ↓
   Queue to Redis
     ↓
   Return Event ID
   ```

---

## Acceptance Criteria

- [x] POST /v1/events accepts and stores events
- [x] Returns event ID immediately (async)
- [x] Creates deliveries for all active endpoints
- [x] Queues to Redis for worker processing
- [x] Idempotency key deduplication works
- [x] GET endpoints for listing/retrieving events
- [x] Tenant isolation enforced

---

## Testing

**Manual Test:**
```bash
# Create endpoint first
curl -X POST http://localhost:8080/v1/endpoints ...

# Send event
curl -X POST http://localhost:8080/v1/events \
  -H "Content-Type: application/json" \
  -H "X-Tenant-ID: test-tenant" \
  -d '{
    "event_type": "test.event",
    "payload": {"test": true},
    "idempotency_key": "test-001"
  }'

# Duplicate (should return same event)
curl -X POST ... -d '{..., "idempotency_key": "test-001"}'
```

---

## Notes

**Completed:** 2026-02-04  
**Dependencies:** ISI-13 (schema), ISI-10 (API pattern)

**Performance Considerations:**
- JSON payload stored as JSONB (indexed)
- Payload size tracked for billing
- Bulk endpoint lookup
- Batch Redis operations

**Design Decisions:**
- Immediate response, async processing
- Idempotency prevents duplicate charges
- Redis queue decouples ingestion from delivery

---

## Related

- Used by: ISI-12 (workers consume these queues)
- Related to: Phase 3 billing (usage_records created)
- Frontend: Dashboard "testing tool" uses this
