# Task: ISI-19 - Dashboard Endpoint Management Page

**Project:** Webhook Delivery Platform  
**Phase:** 2  
**Issue:** ISI-19  
**Owner:** Ray (Frontend Engineer)  
**Status:** ⏳ Pending  
**Due:** 2026-03-04

---

## Description

Build React dashboard page for webhook endpoint CRUD operations. Customers can view, create, edit, and delete webhook endpoints with real-time status indicators.

---

## Requirements

### UI Components

1. **Endpoints List View**
   - Table with columns: Name, URL, Status, Circuit State, Created
   - Status badges (active, disabled)
   - Circuit state indicator (closed, open, half-open)
   - Quick actions: Edit, Delete, Test

2. **Create Endpoint Modal**
   - Form fields:
     - Name (required, 1-255 chars)
     - URL (required, must be https)
     - Secret (optional, for HMAC signatures)
     - Max Retries (default 3, range 0-10)
     - Description (optional)
   - Validation with error messages
   - Submit → POST /v1/endpoints

3. **Edit Endpoint Inline**
   - Inline editing or modal (consistent with create)
   - PUT /v1/endpoints/{id}
   - Partial updates supported

4. **Delete Endpoint**
   - Confirmation dialog
   - Soft delete (status → disabled)
   - DELETE /v1/endpoints/{id}

5. **Test Endpoint Button**
   - Send test event to endpoint
   - Show delivery result
   - Uses webhook testing tool (ISI-22)

### API Integration

```typescript
// GET /v1/endpoints
interface Endpoint {
  id: string;
  name: string;
  url: string;
  status: 'active' | 'disabled';
  circuit_state: 'closed' | 'open' | 'half-open';
  failure_count: number;
  max_retries: number;
  created_at: string;
}
```

### Design

- Clean, technical interface
- Responsive table (horizontal scroll on mobile)
- Loading states for async operations
- Error handling with toast notifications
- Empty state for new customers

---

## Technical Stack

- React 18+ with TypeScript
- TanStack Query (React Query) for data fetching
- Tailwind CSS for styling
- React Hook Form for forms
- Zod for validation

---

## Acceptance Criteria

- [ ] List displays endpoints from API
- [ ] Create endpoint works with validation
- [ ] Edit endpoint updates correctly
- [ ] Delete shows confirmation and soft-deletes
- [ ] Test button sends test event
- [ ] Loading states during API calls
- [ ] Error handling for failed requests
- [ ] Responsive on mobile devices

---

## Related

- Depends on: ISI-10 (API complete)
- Related to: ISI-20 (delivery logs), ISI-22 (testing tool)
- Part of: Phase 2 Customer Dashboard

---

## Notes

**Owner:** Ray  
**Backend Support:** Sterling (API already done)

**Design Mockups:**
*To be provided or use existing design system*

**Timeline:** Week 1 of Phase 2 (Days 1-2)
