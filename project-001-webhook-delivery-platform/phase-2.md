# Phase 2: Customer Dashboard

**Project:** Webhook Delivery Platform  
**Phase:** 2 of 4  
**Duration:** 2 weeks (2026-02-26 → 2026-03-11)  
**Status:** ⏳ Pending (ready to start)

---

## Goal
Customer-facing React dashboard for webhook management, delivery logs, and event replay.

---

## Deliverables

| Task | Issue | Owner | Status |
|------|-------|-------|--------|
| Dashboard: Endpoint management | ISI-16 | Ray | ⏳ Pending |
| Dashboard: Delivery logs | - | Ray | ⏳ Pending |
| Dashboard: Event replay | - | Ray | ⏳ Pending |
| Dashboard: API key management | - | Ray | ⏳ Pending |
| Webhook testing tool | - | Ray | ⏳ Pending |
| WebSocket for real-time logs | - | Ray | ⏳ Pending |

---

## Technical Stack

- **Framework:** React 18+ with TypeScript
- **Styling:** Tailwind CSS or Material-UI
- **State:** React Query + Zustand
- **WebSocket:** Socket.io or native WebSocket
- **Build:** Vite

---

## Dashboard Pages

### 1. Endpoints Page
**Route:** `/endpoints`

**Features:**
- List all endpoints (name, URL, status, circuit state)
- Create endpoint modal (name, URL, secret, max retries)
- Edit endpoint inline
- Delete endpoint with confirmation
- Test endpoint button (send test event)
- View endpoint details (circuit state, failure count)

**APIs Needed:**
- `GET /v1/endpoints`
- `POST /v1/endpoints`
- `PUT /v1/endpoints/{id}`
- `DELETE /v1/endpoints/{id}`

---

### 2. Events Page
**Route:** `/events`

**Features:**
- List recent events (type, timestamp, status)
- Filter by event type, date range, status
- View event payload
- View delivery attempts per event
- Replay event button

**APIs Needed:**
- `GET /v1/events`
- `GET /v1/events/{id}`
- `POST /v1/events` (for replay)

---

### 3. Deliveries Page
**Route:** `/deliveries`

**Features:**
- List delivery attempts (endpoint, status, timestamp)
- Filter by endpoint, status, date range
- View delivery details (request, response, duration)
- Real-time updates via WebSocket

**APIs Needed:**
- `GET /v1/deliveries` (needs to be added)
- `GET /v1/deliveries/{id}` (needs to be added)

---

### 4. Dead Letter Queue Page
**Route:** `/dead-letter`

**Features:**
- List failed deliveries in DLQ
- View failure reason
- Replay from DLQ button
- Bulk replay options

**APIs Needed:**
- `GET /v1/dead-letter` (needs to be added)
- `POST /v1/dead-letter/{id}/replay` (needs to be added)

---

### 5. API Keys Page
**Route:** `/api-keys`

**Features:**
- List API keys
- Create new key (show secret once)
- Revoke key
- View last used timestamp

**APIs Needed:**
- `GET /v1/api-keys` (needs to be added)
- `POST /v1/api-keys` (needs to be added)
- `DELETE /v1/api-keys/{id}` (needs to be added)

---

### 6. Testing Tool
**Route:** `/test`

**Features:**
- Select endpoint from dropdown
- Compose test payload (JSON editor)
- Send test event
- View delivery response in real-time

---

## WebSocket Events

### Client → Server
- `subscribe:deliveries` - Subscribe to delivery updates
- `subscribe:events` - Subscribe to new events

### Server → Client
- `delivery:updated` - Delivery status changed
- `event:created` - New event received
- `endpoint:updated` - Endpoint circuit state changed

---

## Backend APIs to Add

### Deliveries
```
GET /v1/deliveries?limit=&offset=&endpoint_id=&status=
GET /v1/deliveries/{id}
GET /v1/deliveries/{id}/attempts
```

### Dead Letter
```
GET /v1/dead-letter?limit=&offset=
POST /v1/dead-letter/{id}/replay
```

### API Keys
```
GET /v1/api-keys
POST /v1/api-keys
DELETE /v1/api-keys/{id}
```

### WebSocket
```
WS /v1/ws
```

---

## Authentication

**Decision:** Delegated external auth
- Dashboard uses external auth provider
- API calls include `X-Tenant-ID` header
- No auth token handling in Phase 2

---

## Design Mockups

*To be provided by design team or Ray*

Key principles:
- Clean, technical interface
- Real-time indicators (WebSocket status)
- Clear status badges (delivered, failed, pending)
- JSON viewers for payloads
- Responsive for mobile admin access

---

## Testing

### Frontend Tests
```bash
npm run test
npm run test:e2e
```

### Integration with Backend
```bash
# Start backend
docker-compose up api worker

# Start frontend dev server
npm run dev
```

---

## Dependencies

| Dependency | From | Need By | Status |
|------------|------|---------|--------|
| Phase 1 backend APIs | Sterling | Week 1 | ✅ Ready |
| Design mockups | Design/PM | Week 1 | ⏳ Pending |
| WebSocket backend support | Sterling | Week 1 | ⏳ Pending |
| Domain SSL | Krieger | Week 2 | ⏳ Pending |

---

## Completion Criteria

- [ ] Dashboard lists endpoints with real data
- [ ] Can create/edit/delete endpoints
- [ ] Events page shows real events
- [ ] Delivery logs display with filtering
- [ ] WebSocket shows real-time updates
- [ ] Testing tool sends events and shows responses
- [ ] Mobile-responsive layout

---

## Handoff to Phase 3

**Ready for:**
- Security hardening (signatures, encryption)
- Billing integration
- Production deployment prep

---

## Notes

**Ray's Notes:**
*To be added*

**Dependencies on Sterling:**
- Additional API endpoints for deliveries/attempts
- WebSocket endpoint for real-time updates
- CORS configuration for dashboard domain

---

## Timeline

```
Week 1:
  Day 1-2: Setup React project, auth integration
  Day 3-4: Endpoints page (CRUD)
  Day 5: Events page

Week 2:
  Day 1-2: Deliveries page with WebSocket
  Day 3: DLQ and replay
  Day 4: Testing tool + API keys
  Day 5: Polish, testing, docs
```
