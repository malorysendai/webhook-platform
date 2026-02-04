# Task: ISI-22 - Dashboard Webhook Testing Tool

**Project:** Webhook Delivery Platform  
**Phase:** 2  
**Issue:** ISI-22  
**Owner:** Ray (Frontend Engineer)  
**Status:** ⏳ Pending  
**Due:** 2026-03-10

---

## Description

Build integrated webhook testing tool allowing customers to send test events to their endpoints and see real-time delivery results.

---

## Requirements

### UI Components

1. **Test Tool Page/Modal**
   - Endpoint selector dropdown (list of user's endpoints)
   - JSON payload editor
     - Syntax highlighting
     - Format validation
     - Prettify/compact toggle
     - Sample payloads (user.created, order.placed, etc.)
   - "Send Test" button

2. **Results Panel**
   - Real-time delivery status
   - Request details sent
   - Response received:
     - Status code (color coded)
     - Response body (formatted)
     - Response headers
     - Duration (ms)
   - Success/failure indicator

3. **History**
   - Recent test events
   - Quick re-run button
   - Clear history option

### API Integration

Uses existing event ingestion API:

```typescript
// POST /v1/events (existing)
POST /v1/events
Body: {
  event_type: "test.webhook",
  payload: { /* user JSON */ },
  idempotency_key?: string
}

// Then poll or WebSocket for delivery status
```

### Design

- Split pane: editor on left, results on right
- JSON editor with validation
- Real-time updates via WebSocket
- Copy-to-clipboard for payloads

---

## Acceptance Criteria

- [ ] Endpoint selector populated from API
- [ ] JSON editor with syntax validation
- [ ] Send test event works
- [ ] Results show request/response
- [ ] Real-time delivery status
- [ ] History of recent tests
- [ ] Accessible from endpoints page (test button)

---

## Related

- Depends on: ISI-11 (event ingestion), ISI-23 (WebSocket)
- Reuses: Event ingestion API
- Part of: Phase 2 Customer Dashboard

---

## Notes

**Owner:** Ray  
**Backend:** Uses existing APIs

**Timeline:** Week 2 of Phase 2 (Days 3-4)
