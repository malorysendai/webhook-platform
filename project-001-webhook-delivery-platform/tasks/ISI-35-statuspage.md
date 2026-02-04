# Task: ISI-35 - Production - Status Page Integration

**Project:** Webhook Delivery Platform  
**Phase:** 4  
**Issue:** ISI-35  
**Owner:** Krieger (Platform)  
**Status:** ⏳ Pending  
**Due:** 2026-04-01

---

## Description

Set up customer-facing status page showing platform health. Integrate with PagerDuty for automatic incident creation.

---

## Requirements

### Status Page Provider

**Option A: Atlassian Statuspage**
- Custom domain: status.hooks.sendai.co.zw
- Components: API, Workers, Dashboard
- Incident management
- Subscriber notifications

**Option B: UptimeRobot**
- Simpler, cheaper
- Basic status page
- Less customization

### Components

1. **Webhook Ingestion API**
   - Health: /health endpoint check
   - Latency: <200ms P95

2. **Delivery Workers**
   - Queue processing
   - Worker availability

3. **Dashboard**
   - Static assets
   - API connectivity

### Automation

1. **PagerDuty → Statuspage**
   - Critical alert → Incident created
   - Resolved → Incident closed
   - Automatic component status update

2. **Manual Incidents**
   - Scheduled maintenance
   - Partial outages
   - Customer communication

### Notifications

- Email subscribers
- Slack webhook
- RSS feed
- Twitter (optional)

---

## Acceptance Criteria

- [ ] Status page live at custom domain
- [ ] All 3 components monitored
- [ ] PagerDuty integration working
- [ ] Incident auto-creation tested
- [ ] Subscriber notifications work
- [ ] Linked from dashboard footer

---

## Related

- Owner: Krieger
- Part of: Phase 4 Production Hardening
- Depends on: ISI-31 (PagerDuty)
- Cost: ~$29/month (Statuspage) or free (UptimeRobot)
