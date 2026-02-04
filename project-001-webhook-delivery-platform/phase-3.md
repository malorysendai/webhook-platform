# Phase 3: Security & Billing

**Project:** Webhook Delivery Platform  
**Phase:** 3 of 4  
**Duration:** 2 weeks (2026-03-12 → 2026-03-25)  
**Status:** ⏳ Pending

---

## Goal
Enterprise-ready security features and billing integration foundation.

---

## Deliverables

| Task | Issue | Owner | Status |
|------|-------|-------|--------|
| HMAC-SHA256 webhook signatures | ISI-17 | Barry + Sterling | ⏳ Pending |
| IP allowlisting | - | Barry | ⏳ Pending |
| Optional payload encryption | - | Barry | ⏳ Pending |
| API key rotation | - | Sterling | ⏳ Pending |
| Audit logging | - | Sterling | ⏳ Pending |
| Usage metering | ISI-18 | Sterling | ⏳ Pending |
| Billing data export | - | Sterling | ⏳ Pending |

---

## Technical Stack

- **Signatures:** HMAC-SHA256 (Stripe-style)
- **Encryption:** AES-256-GCM
- **Audit:** PostgreSQL audit table
- **Metering:** Background job aggregating usage_records

---

## Security Features

### 1. HMAC-SHA256 Signatures (ISI-17)

**Status:** Partially implemented in Phase 1

**Requirements:**
- All webhooks include `X-Signature` header
- Format: `sha256=<hex_hmac>`
- Signed payload: `timestamp.payload`
- Timestamp tolerance: ±5 minutes

**Implementation:**
```
Signature = HMAC-SHA256(secret, timestamp + "." + json_payload)
Header: X-Signature: sha256=<hex_signature>
Header: X-Timestamp: <unix_ms>
```

**Verification (customer-side):**
```python
import hmac
import hashlib

expected = hmac.new(secret, f"{timestamp}.{payload}", hashlib.sha256).hexdigest()
assert hmac.compare_digest(signature, expected)
```

---

### 2. IP Allowlisting

**Endpoint-level configuration:**
- `allowed_ips` array in endpoints table
- If empty, allow all
- If populated, only deliver from listed IPs

**UI:**
- CIDR support (e.g., `192.168.1.0/24`)
- IP validation
- "My IP" button to add current IP

---

### 3. Payload Encryption (Optional)

**Algorithm:** AES-256-GCM

**Use Cases:**
- Healthcare data (HIPAA)
- Financial data (PCI)
- PII-heavy payloads

**Configuration:**
- Per-endpoint opt-in
- Key management via API or dashboard

**Performance:**
- Benchmark before enabling
- Async encryption to avoid blocking

---

### 4. API Key Rotation

**Features:**
- Generate new key (old key still valid for 24h)
- Revoke key immediately
- Key expiration dates
- Last used tracking

**UI:**
- "Rotate Key" button (shows new key once)
- "Revoke" with confirmation
- Key usage statistics

---

### 5. Audit Logging

**Logged Events:**
- Endpoint CRUD operations
- API key rotations
- Configuration changes
- Manual replays from DLQ

**Retention:** 90 days

**Schema:**
```sql
CREATE TABLE audit_logs (
    id UUID PRIMARY KEY,
    tenant_id UUID,
    user_id TEXT, -- External auth user ID
    action VARCHAR(50),
    resource_type VARCHAR(50),
    resource_id UUID,
    old_values JSONB,
    new_values JSONB,
    ip_address INET,
    user_agent TEXT,
    created_at TIMESTAMPTZ
);
```

---

## Billing Integration

### Usage Metering (ISI-18)

**Pricing:** Flat rate $0.10 per successful delivery

**Metered Events:**
1. `event_ingested` - Event received
2. `delivery_success` - Successful delivery (billable)
3. `delivery_retry` - Retry attempt (not billable)
4. `storage_gb_day` - Event storage (future)

**Aggregation:**
- Hourly aggregation
- Daily export to billing system

**Export API:**
```
GET /v1/billing/usage?start_date=&end_date=
Authorization: Bearer {billing_service_token}

Response:
{
  "tenant_id": "...",
  "period": "2026-03-01",
  "deliveries_successful": 1500,
  "amount_due": 150.00,
  "currency": "USD"
}
```

---

### Billing System Integration

**Approach:**
1. Daily export job
2. CSV/JSON to S3 or API push
3. Billing system consumes and generates invoices

**Data Format:**
```json
{
  "date": "2026-03-15",
  "tenant_id": "uuid",
  "tenant_name": "Acme Corp",
  "tier": "pro",
  "events_ingested": 5000,
  "deliveries_successful": 4500,
  "deliveries_failed": 500,
  "billable_amount": 450.00
}
```

---

## New API Endpoints

### Security
```
GET /v1/endpoints/{id}/signature/verify
POST /v1/endpoints/{id}/secret/regenerate

GET /v1/audit-logs?resource_type=&action=&start_date=&end_date=
```

### Billing (Internal)
```
GET /internal/billing/usage?tenant_id=&start_date=&end_date=
GET /internal/billing/export?date=
```

---

## Dependencies

| Dependency | From | Need By | Status |
|------------|------|---------|--------|
| Phase 2 dashboard | Ray | Week 1 | ⏳ Pending |
| Encryption performance benchmark | Barry | Week 1 | ⏳ Pending |
| Billing system API docs | Finance | Week 1 | ⏳ Pending |
| Security review sign-off | Barry | Week 2 | ⏳ Pending |
| Audit log storage sizing | Krieger | Week 1 | ⏳ Pending |

---

## Testing

### Security Testing
- Signature verification unit tests
- IP allowlisting integration tests
- Encryption/decryption round-trip tests
- Audit log completeness verification

### Billing Testing
- Metering accuracy tests
- Edge cases (duplicate events, retries)
- Export format validation

### Penetration Testing
- To be scheduled by Barry

---

## Compliance

### Standards
- SOC 2 Type II (target)
- GDPR (data retention, deletion)
- HIPAA (if healthcare customers)

### Documentation
- Security whitepaper
- API security guide
- Customer integration docs

---

## Completion Criteria

- [ ] All webhooks include valid signatures
- [ ] IP allowlisting enforced
- [ ] Audit logs capture all admin actions
- [ ] Usage metering accurate to 99.99%
- [ ] Billing export validated by finance
- [ ] Security review passed
- [ ] Penetration test report clean

---

## Handoff to Phase 4

**Ready for:**
- Production deployment
- Load testing at scale
- Customer onboarding

---

## Risks

| Risk | Probability | Impact | Mitigation |
|------|-------------|--------|------------|
| Encryption performance | Medium | High | Benchmark early, make optional |
| Billing integration delays | Medium | Medium | Build export API first, manual fallback |
| Security review findings | Low | High | Buffer time for fixes |

---

## Timeline

```
Week 1:
  Day 1-2: Signatures hardening, IP allowlisting
  Day 3-4: Encryption, API key rotation
  Day 5: Audit logging

Week 2:
  Day 1-2: Usage metering, billing export
  Day 3: Integration testing
  Day 4: Security review
  Day 5: Fixes, documentation
```
