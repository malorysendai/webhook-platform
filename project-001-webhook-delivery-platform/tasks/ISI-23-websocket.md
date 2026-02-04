# Task: ISI-23 - Dashboard WebSocket Real-time Updates

**Project:** Webhook Delivery Platform  
**Phase:** 2  
**Issue:** ISI-23  
**Owner:** Ray (Frontend) + Sterling (Backend)  
**Status:** ⏳ Pending  
**Due:** 2026-03-11

---

## Description

Implement WebSocket support for real-time dashboard updates. Backend provides WebSocket endpoint, frontend maintains connection with auto-reconnect.

---

## Requirements

### Backend (Sterling)

1. **WebSocket Endpoint**
   - `WS /v1/ws`
   - Authentication via query param or header
   - Tenant isolation

2. **Events to Publish**
   - `delivery:updated` - When delivery status changes
   - `event:created` - When new event ingested
   - `endpoint:updated` - When endpoint circuit state changes

3. **Message Format**
   ```json
   {
     "type": "delivery:updated",
     "tenant_id": "...",
     "data": {
       "delivery_id": "...",
       "status": "delivered",
       "timestamp": "..."
     }
   }
   ```

### Frontend (Ray)

1. **WebSocket Client**
   - Connection management
   - Auto-reconnect with backoff
   - Heartbeat/ping-pong
   - Error handling

2. **State Integration**
   - TanStack Query cache updates
   - Real-time table updates
   - Toast notifications for important events

3. **Subscription Management**
   - Subscribe to events relevant to current page
   - Unsubscribe on page change
   - Efficient reconnection

---

## Technical Stack

**Backend:**
- gorilla/websocket or native Go websockets
- Redis Pub/Sub for multi-instance sync

**Frontend:**
- Native WebSocket API
- Reconnecting-websocket library
- React context for connection state

---

## Acceptance Criteria

- [ ] Backend WebSocket endpoint accepts connections
- [ ] Tenant isolation enforced
- [ ] Frontend connects and receives events
- [ ] Auto-reconnect works after disconnect
- [ ] Delivery updates reflect in real-time
- [ ] Endpoint circuit state updates live
- [ ] Graceful handling of connection errors

---

## Related

- Backend: Sterling
- Frontend: Ray
- Used by: ISI-20 (logs), ISI-22 (testing tool)
- Part of: Phase 2 Customer Dashboard

---

## Notes

**Split Task:**
- Sterling: Backend WebSocket endpoint
- Ray: Frontend WebSocket client

**Timeline:** Week 2 of Phase 2 (Days 4-5)

**Resources:**
- Backend needs Redis Pub/Sub for scaling
- Frontend needs reconnection logic
