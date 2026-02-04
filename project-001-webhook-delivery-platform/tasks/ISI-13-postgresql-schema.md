# Task: ISI-13 - PostgreSQL Schema Design

**Project:** Webhook Delivery Platform  
**Phase:** 1  
**Issue:** ISI-13  
**Owner:** Sterling (Backend Engineer)  
**Status:** ✅ Complete  
**Due:** 2026-02-11

---

## Description

Design and implement the PostgreSQL database schema for the webhook delivery platform. Schema must support multi-tenancy, event storage, delivery tracking, circuit breakers, dead letter queue, and billing metering.

---

## Requirements

### Tables Required

1. **tenants** - Multi-tenant organization records
   - UUID primary key
   - Name, slug, status, tier
   - Rate limiting configuration
   - Timestamps

2. **api_keys** - Tenant API key management
   - Hashed key storage
   - Expiration and revocation tracking
   - Last used timestamp

3. **endpoints** - Webhook endpoint configurations
   - URL, status, secret for signatures
   - Circuit breaker state and failure count
   - Retry configuration (max retries, backoff type)
   - IP allowlisting array
   - Soft delete support

4. **events** - Webhook events received
   - JSONB payload storage
   - Idempotency key support
   - Source tracking
   - Payload size for billing

5. **deliveries** - Individual delivery attempts
   - Event and endpoint foreign keys
   - Status tracking (pending, delivering, delivered, failed, expired)
   - Attempt count and scheduling
   - Request/response logging
   - Signature tracking
   - Duration metrics

6. **dead_letter** - Failed deliveries exceeding retries
   - Original delivery reference
   - Failure reason categorization
   - Replay tracking

7. **delivery_attempts** - Audit log of all attempts
   - Full request/response details
   - Retry decisions
   - Performance metrics

8. **usage_records** - Billing metering foundation
   - Event type and quantity
   - Hourly aggregation support

### Technical Requirements

- UUID primary keys with uuid-ossp extension
- Foreign key constraints with CASCADE where appropriate
- Indexes for all query patterns:
  - Tenant-scoped queries
  - Status-based filtering
  - Time-based queries
  - Idempotency lookups
- Updated_at triggers for all mutable tables
- Soft delete pattern (deleted_at timestamp)
- JSONB for flexible metadata storage

---

## Implementation

**File:** `migrations/001_init_schema.sql`

```sql
-- Migration 001: Initial schema for webhook delivery platform
-- See full implementation in repository
```

**Key Design Decisions:**
1. Used UUIDs for all primary keys (scalability, security)
2. Soft deletes for data recovery
3. JSONB for payload and metadata (flexibility)
4. Circuit breaker state stored in endpoints table
5. Separate dead_letter table for query performance
6. Comprehensive audit trail in delivery_attempts

---

## Acceptance Criteria

- [x] All 8 tables created with proper constraints
- [x] Indexes added for query performance
- [x] Triggers for updated_at columns
- [x] Foreign key relationships established
- [x] Migration runs successfully
- [x] Schema supports all Phase 1-4 features

---

## Notes

**Completed:** 2026-02-04  
**Commit:** `5fa4b3f` and subsequent  
**Reviewed by:** Sterling (self-reviewed, pattern standard)

This schema was designed to support:
- Multi-tenancy from day one
- Circuit breaker pattern per endpoint
- Comprehensive audit logging
- Billing metering without schema changes
- Event replay capability

---

## Related

- Phase 1: Core Infrastructure
- Task ISI-10: REST API (depends on this schema)
- Task ISI-12: Delivery Workers (depends on deliveries table)
