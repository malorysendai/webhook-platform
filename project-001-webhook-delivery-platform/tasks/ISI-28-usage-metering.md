# Task: ISI-28 - Billing - Usage Metering Pipeline

**Project:** Webhook Delivery Platform  
**Phase:** 3  
**Issue:** ISI-28  
**Owner:** Sterling (Backend)  
**Status:** ⏳ Pending  
**Due:** 2026-03-23

---

## Description

Implement usage metering pipeline tracking billable events. Pricing: $0.10 per successful delivery. Foundation for billing integration.

---

## Requirements

### Metering

1. **Billable Events**
   - `delivery_success` - $0.10 each
   - Storage (future) - per GB-day
   - Retries, failed deliveries - not billable

2. **Schema** (already exists: usage_records)
   ```sql
   CREATE TABLE usage_records (
       id UUID PRIMARY KEY,
       tenant_id UUID,
       usage_type VARCHAR(50), -- delivery_success, event_ingested, etc.
       quantity INTEGER DEFAULT 1,
       event_id UUID,
       delivery_id UUID,
       period_hour TIMESTAMPTZ, -- Truncated to hour
       created_at TIMESTAMPTZ
   );
   ```

3. **Aggregation**
   - Hourly aggregation per tenant
   - Daily summary
   - Monthly billing period

4. **Background Job**
   - Aggregate hourly
   - Generate billing reports
   - Export to billing system

### Integration

1. **Billing Export API**
   ```
   GET /internal/billing/usage?tenant_id=&start_date=&end_date=
   Response:
   {
     "tenant_id": "...",
     "period": "2026-03",
     "deliveries_successful": 1500,
     "amount_due": 150.00,
     "currency": "USD"
   }
   ```

2. **Daily Export**
   - CSV/JSON to S3 or API push
   - Format agreed with Finance

---

## Acceptance Criteria

- [ ] Usage records created on successful delivery
- [ ] Hourly aggregation working
- [ ] Billing export API functional
- [ ] Daily export job running
- [ ] Metering accuracy 99.99%
- [ ] Dispute handling process

---

## Related

- Owner: Sterling
- Part of: Phase 3 Security & Billing
- Pricing: $0.10 per delivery
- Depends on: ISI-12 (delivery success tracking)
