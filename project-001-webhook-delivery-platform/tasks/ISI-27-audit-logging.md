# Task: ISI-27 - Security - Audit Logging

**Project:** Webhook Delivery Platform  
**Phase:** 3  
**Issue:** ISI-27  
**Owner:** Sterling (Backend)  
**Status:** ⏳ Pending  
**Due:** 2026-03-19

---

## Description

Implement comprehensive audit logging for all admin actions, configuration changes, and security events. 90-day retention.

---

## Requirements

### Audit Events

1. **Logged Actions**
   - Endpoint CRUD operations
   - API key rotations/revocations
   - Configuration changes
   - DLQ replays
   - Failed authentication attempts

2. **Audit Log Schema**
   ```sql
   CREATE TABLE audit_logs (
       id UUID PRIMARY KEY,
       tenant_id UUID,
       user_id TEXT,          -- External auth user ID
       action VARCHAR(50),    -- create, update, delete, rotate, etc.
       resource_type VARCHAR(50), -- endpoint, api_key, event
       resource_id UUID,
       old_values JSONB,
       new_values JSONB,
       ip_address INET,
       user_agent TEXT,
       created_at TIMESTAMPTZ
   );
   ```

3. **Retention**
   - 90 days in hot storage
   - Archive to cold storage after 90 days
   - Compliance: 1 year total retention

### API

```
GET /v1/audit-logs?resource_type=&action=&start_date=&end_date=
```

### Frontend

- Audit log viewer in dashboard
- Filter by date, action, resource
- Export to CSV

---

## Acceptance Criteria

- [ ] All admin actions logged
- [ ] Old/new values captured for changes
- [ ] 90-day retention configured
- [ ] API for querying logs
- [ ] Frontend audit viewer
- [ ] Export functionality

---

## Related

- Owner: Sterling
- Part of: Phase 3 Security & Billing
- Compliance: SOC 2, GDPR
