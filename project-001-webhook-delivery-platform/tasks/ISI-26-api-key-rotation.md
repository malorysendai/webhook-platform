# Task: ISI-26 - Security - API Key Rotation

**Project:** Webhook Delivery Platform  
**Phase:** 3  
**Issue:** ISI-26  
**Owner:** Sterling (Backend) + Ray (Frontend)  
**Status:** ⏳ Pending  
**Due:** 2026-03-17

---

## Description

Implement API key rotation mechanism allowing customers to generate new keys with grace period, revoke keys, and track key usage.

---

## Requirements

### Backend

1. **Key Schema** (already exists)
   - `api_keys` table with expires_at, revoked_at, last_used_at

2. **Rotation Flow**
   - Generate new key
   - Old key valid for 24 hours (grace period)
   - Show new key only once
   - Track which key was used per request

3. **Revocation**
   - Immediate revocation option
   - No grace period
   - Audit logged

4. **API Endpoints**
   ```
   POST /v1/api-keys              # Create new key
   DELETE /v1/api-keys/{id}       # Revoke key
   GET /v1/api-keys               # List keys (masked)
   ```

### Frontend

1. **API Keys Page**
   - List keys (masked, e.g., `hooks_****_abcd`)
   - Created date, last used, expires
   - "Rotate" button (shows new key once)
   - "Revoke" button with confirmation

2. **New Key Modal**
   - Show full key (copy button)
   - Warning: "Store this now - won't be shown again"
   - Grace period explanation

---

## Acceptance Criteria

- [ ] Create new key generates unique key
- [ ] Old key works for 24h grace period
- [ ] New key shown only once
- [ ] Revocation immediate
- [ ] Usage tracking (last_used_at)
- [ ] Frontend key management works
- [ ] Audit logs for rotations

---

## Related

- Backend: Sterling
- Frontend: Ray
- Part of: Phase 3 Security & Billing
- Security: High priority
