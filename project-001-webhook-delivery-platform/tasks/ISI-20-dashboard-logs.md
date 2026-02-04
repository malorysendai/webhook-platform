# Task: ISI-20 - Dashboard Delivery Logs Page

**Project:** Webhook Delivery Platform  
**Phase:** 2  
**Issue:** ISI-20  
**Owner:** Ray (Frontend Engineer)  
**Status:** ⏳ Pending  
**Due:** 2026-03-06

---

## Description

Build delivery logs page with filtering, search, and detailed request/response viewing. Show real-time delivery status updates via WebSocket.

---

## Requirements

### UI Components

1. **Delivery Logs List**
   - Table with columns: Time, Endpoint, Event Type, Status, Duration
   - Status badges: delivered, failed, pending, expired
   - Color coding: green (success), red (failed), yellow (pending)
   - Pagination (20 items per page default)

2. **Filters**
   - Endpoint dropdown (all endpoints)
   - Status multi-select
   - Date range picker (last 24h, 7 days, custom)
   - Event type filter

3. **Delivery Detail View**
   - Modal or slide-out panel
   - Request details:
     - Payload (formatted JSON)
     - Headers
     - Timestamp
   - Response details:
     - Status code
     - Response body (truncated if large)
     - Response headers
     - Duration (ms)
   - Delivery attempts timeline

4. **Real-time Updates**
   - WebSocket connection for live updates
   - New deliveries appear without refresh
   - Status changes update in real-time

### API Integration

```typescript
// GET /v1/deliveries?limit=20&offset=0&endpoint_id=&status=
interface Delivery {
  id: string;
  event_id: string;
  endpoint_id: string;
  status: 'pending' | 'delivering' | 'delivered' | 'failed' | 'expired';
  attempt_count: number;
  scheduled_at: string;
  delivered_at?: string;
  response_status?: number;
  duration_ms?: number;
  error_message?: string;
}

// GET /v1/deliveries/{id}/attempts
interface DeliveryAttempt {
  attempt_number: number;
  attempted_at: string;
  success: boolean;
  response_status?: number;
  duration_ms?: number;
  error?: string;
}
```

**Note:** Backend needs to add these endpoints (Sterling task).

---

## WebSocket Events

```typescript
// Client receives
interface DeliveryUpdatedEvent {
  type: 'delivery:updated';
  delivery_id: string;
  status: string;
  timestamp: string;
}
```

---

## Acceptance Criteria

- [ ] List deliveries with pagination
- [ ] Filters work (endpoint, status, date)
- [ ] Detail view shows full request/response
- [ ] Real-time updates via WebSocket
- [ ] Delivery attempts timeline visible
- [ ] Responsive design
- [ ] Empty states handled

---

## Related

- Depends on: ISI-10 (API pattern), ISI-23 (WebSocket)
- Needs Backend: Delivery endpoints (Sterling to add)
- Part of: Phase 2 Customer Dashboard

---

## Notes

**Owner:** Ray  
**Backend Needs:**
- GET /v1/deliveries
- GET /v1/deliveries/{id}
- GET /v1/deliveries/{id}/attempts
- WebSocket events

**Timeline:** Week 1 of Phase 2 (Days 3-5)
