# Task: ISI-12 - Delivery Workers with Retry Logic

**Project:** Webhook Delivery Platform  
**Phase:** 1  
**Issue:** ISI-12  
**Owner:** Sterling (Backend Engineer)  
**Status:** ✅ Complete  
**Due:** 2026-02-23

---

## Description

Implement asynchronous delivery workers that consume from Redis queue, execute HTTP POST requests to customer endpoints with exponential backoff retry, circuit breakers, and dead letter queue handling.

---

## Requirements

### Worker Features

1. **Queue Consumption**
   - Poll Redis for pending deliveries
   - Claim deliveries with atomic operations
   - Process in configurable batch sizes
   - Heartbeat monitoring

2. **HTTP Delivery**
   - POST to customer endpoint URLs
   - 30-second timeout per request
   - HMAC-SHA256 signature generation
   - Request/response logging

3. **Retry Strategy (Exponential Backoff)**

| Attempt | Delay | Condition |
|---------|-------|-----------|
| 1 | Immediate | First failure |
| 2 | 5 minutes | Second failure |
| 3 | 30 minutes | Third failure |
| DLQ | Never | Max retries exceeded |

4. **Circuit Breaker**
   - Opens after 5 consecutive failures
   - Stays open for 5 minutes
   - Half-open state allows test delivery
   - Closes after 3 successful deliveries

5. **Dead Letter Queue**
   - Max retries exceeded
   - Circuit permanently open
   - Endpoint disabled/deleted
   - Event expired (>7 days)

6. **Signature Generation**
   - HMAC-SHA256 (Stripe-style)
   - Header: `X-Signature: sha256=<hex>`
   - Payload + timestamp signed

---

## Implementation

**File:** `internal/worker/delivery_worker.go`

**Key Components:**

1. **DeliveryWorker**
   - Worker pool with goroutines
   - Configurable poll interval
   - Graceful shutdown handling

2. **Delivery Execution**
   ```go
   func (w *Worker) executeDelivery(ctx, delivery, endpoint, event) 
       → (success, statusCode, body, duration, error)
   ```

3. **Retry Logic**
   ```go
   func (w *Worker) shouldRetry(attemptCount, statusCode) bool
   func (w *Worker) getRetryDelay(attemptCount) time.Duration
   ```

4. **Circuit Breaker State Machine**
   ```
   CLOSED → (5 failures) → OPEN → (5 min) → HALF_OPEN → (3 success) → CLOSED
   ```

5. **Dead Letter Queue**
   - Separate DLQ table in PostgreSQL
   - Reason categorization
   - Replay support (Phase 2)

---

## Configuration

```env
# Worker settings
WORKER_COUNT=5
WORKER_POLL_INTERVAL=1s
WORKER_SHUTDOWN_TIMEOUT=30s

# Retry settings
RETRY_MAX_ATTEMPTS=3
RETRY_BACKOFF=exponential

# Circuit breaker
CIRCUIT_FAILURE_THRESHOLD=5
CIRCUIT_OPEN_DURATION=5m
```

---

## Acceptance Criteria

- [x] Workers consume from Redis queue
- [x] HTTP POST with 30s timeout
- [x] Exponential backoff retry (0, 5min, 30min)
- [x] Circuit breaker opens/closes correctly
- [x] Failed deliveries go to DLQ
- [x] HMAC-SHA256 signatures generated
- [x] Delivery attempts logged
- [x] Worker pool scales horizontally
- [x] Graceful shutdown handling

---

## Testing

**Manual Test:**
```bash
# Start infrastructure
docker-compose up -d postgres redis

# Start worker
make dev-worker

# Send event (creates deliveries)
curl -X POST http://localhost:8080/v1/events ...

# Watch worker logs
# Verify delivery attempt
# Check circuit state on failures
```

**Integration Tests:**
- Delivery success path
- Retry with backoff
- Circuit breaker activation
- DLQ on max retries

---

## Performance

- Workers process in parallel (configurable count)
- Redis operations are O(log N)
- Database writes batched where possible
- HTTP client connection pooling

---

## Notes

**Completed:** 2026-02-04  
**Dependencies:** ISI-11 (queue), ISI-13 (schema)

**Design Decisions:**
- Go goroutines for concurrency
- Redis sorted sets for reliable queue
- Circuit breaker per endpoint
- Separate DLQ table (not just status)

**Monitoring Points:**
- Worker throughput (deliveries/sec)
- Queue depth
- Circuit breaker states
- Retry rates

---

## Related

- Runs with: cmd/worker/main.go
- Monitors: Endpoints, Deliveries, Queue
- Updates: Circuit states, Delivery status, DLQ
