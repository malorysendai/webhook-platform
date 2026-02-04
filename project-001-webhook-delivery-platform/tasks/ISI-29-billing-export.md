# Task: ISI-29 - Billing - Data Export API

**Project:** Webhook Delivery Platform  
**Phase:** 3  
**Issue:** ISI-29  
**Owner:** Sterling (Backend)  
**Status:** ⏳ Pending  
**Due:** 2026-03-24

---

## Description

Build billing data export system for finance team integration. Daily exports with fallback to manual billing if integration delays.

---

## Requirements

### Export API

1. **Internal API**
   ```
   GET /internal/billing/export?date=2026-03-15
   Authorization: Bearer {billing_service_token}
   
   Response:
   {
     "date": "2026-03-15",
     "tenants": [
       {
         "tenant_id": "uuid",
         "tenant_name": "Acme Corp",
         "tier": "pro",
         "events_ingested": 5000,
         "deliveries_successful": 4500,
         "deliveries_failed": 500,
         "billable_amount": 450.00,
         "currency": "USD"
       }
     ]
   }
   ```

2. **Export Formats**
   - JSON (API)
   - CSV (S3/manual)

3. **Scheduling**
   - Daily at 00:00 UTC
   - Backfill capability
   - Retry on failure

### S3 Export

1. **File Structure**
   ```
   s3://billing-exports/
     2026/
       03/
         15/
           webhook-platform-2026-03-15.json
           webhook-platform-2026-03-15.csv
   ```

2. **Notification**
   - S3 event → Lambda → Billing system
   - Or polling by billing system

### Manual Fallback

- Dashboard export button (emergency)
- Finance can manually download
- Documented process

---

## Acceptance Criteria

- [ ] Export API returns correct data
- [ ] Daily scheduled export
- [ ] S3 file format correct
- [ ] Backfill capability
- [ ] Manual export button
- [ ] Finance team validated format

---

## Related

- Owner: Sterling
- Depends on: ISI-28 (usage metering)
- Part of: Phase 3 Security & Billing
- Stakeholder: Finance team
