# Task: ISI-21 - Dashboard Event Replay Interface

**Project:** Webhook Delivery Platform  
**Phase:** 2  
**Issue:** ISI-21  
**Owner:** Ray (Frontend Engineer)  
**Status:** ⏳ Pending  
**Due:** 2026-03-09

---

## Description

Build event replay functionality allowing customers to re-send events from their event log or from the dead letter queue.

---

## Requirements

### UI Components

1. **Event Replay from Event Log**
   - In Events page, add "Replay" button per event
   - Confirmation dialog
   - Show replay status
   - Creates new delivery attempts

2. **Dead Letter Queue (DLQ) Page**
   - List failed deliveries
   - Columns: Event, Endpoint, Failure Reason, Failed At
   - Actions: Replay, View Details
   - Bulk selection for batch replay

3. **Replay Confirmation**
   - Show event preview
   - Select target endpoints (if different from original)
   - Confirm replay action

4. **Replay Status**
   - Toast notification on replay trigger
   - Link to new delivery in logs
   - Real-time status via WebSocket

### API Integration

```typescript
// Replay from event
POST /v1/events/{id}/replay
Body: {
  endpoint_ids?: string[] // Optional: replay to specific endpoints
}

// Replay from DLQ
POST /v1/dead-letter/{id}/replay

// List DLQ
GET /v1/dead-letter?limit=20&offset=0

interface DeadLetterEntry {
  id: string;
  event_id: string;
  endpoint_id: string;
  reason: 'max_retries_exceeded' | 'circuit_open' | 'expired';
  final_error?: string;
  created_at: string;
}
```

**Note:** Backend needs to add these endpoints.

---

## Acceptance Criteria

- [ ] Replay button on event log entries
- [ ] DLQ page lists failed deliveries
- [ ] Single replay works
- [ ] Bulk replay from DLQ
- [ ] Replay status feedback
- [ ] Error handling for failed replays

---

## Related

- Depends on: ISI-11 (event ingestion), ISI-20 (delivery logs)
- Needs Backend: Replay endpoints (Sterling to add)
- Part of: Phase 2 Customer Dashboard

---

## Notes

**Owner:** Ray  
**Backend Needs:**
- POST /v1/events/{id}/replay
- POST /v1/dead-letter/{id}/replay
- GET /v1/dead-letter

**Timeline:** Week 2 of Phase 2 (Days 1-2)
