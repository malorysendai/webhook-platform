# Task: ISI-31 - Production - PagerDuty Alerting Rules

**Project:** Webhook Delivery Platform  
**Phase:** 4  
**Issue:** ISI-31  
**Owner:** Krieger (Platform)  
**Status:** ⏳ Pending  
**Due:** 2026-03-29

---

## Description

Configure PagerDuty alerting with critical and warning levels. Integrate with Grafana alerts for operational notifications.

---

## Requirements

### Critical Alerts (Page Immediately)

- **Delivery success rate <95%** for 5 minutes
  - Impact: Platform not meeting SLA
  - Escalation: Page on-call engineer

- **API completely down** (no 200s for 2 minutes)
  - Impact: Customers can't send events
  - Escalation: Page + notify stakeholders

- **PostgreSQL connection exhaustion**
  - Impact: Can't process events
  - Escalation: Page + consider failover

- **Redis unavailable**
  - Impact: Queue broken, deliveries stuck
  - Escalation: Page immediately

### Warning Alerts (Slack Notification)

- **Delivery success rate <99%** for 10 minutes
  - Action: Investigate endpoint failures

- **Queue depth >10k** for 15 minutes
  - Action: Scale workers or investigate

- **Worker CPU >80%** for 10 minutes
  - Action: Consider HPA scaling

- **Circuit breakers open** for >10 endpoints
  - Action: Check for widespread customer outage

- **Disk usage >85%** on any node
  - Action: Clean up or expand

### Configuration

```yaml
# PagerDuty Service Integration
service: webhook-platform
integration_key: ${PAGERDUTY_INTEGRATION_KEY}

# Alert Routing
severity_critical:
  - pagerduty
  - slack #incidents
  
severity_warning:
  - slack #alerts
```

### On-Call Rotation

- Primary: Krieger (Week 1)
- Secondary: Sterling (Week 2)
- Escalation: Rick

---

## Acceptance Criteria

- [ ] Critical alerts page PagerDuty
- [ ] Warning alerts go to Slack
- [ ] Alert routing tested
- [ ] On-call rotation configured
- [ ] Escalation policy defined
- [ ] Runbooks linked in alerts

---

## Related

- Owner: Krieger
- Part of: Phase 4 Production Hardening
- Depends on: ISI-30 (Grafana dashboards)
