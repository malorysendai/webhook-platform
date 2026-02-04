# Task: ISI-30 - Production - Grafana Monitoring Dashboards

**Project:** Webhook Delivery Platform  
**Phase:** 4  
**Issue:** ISI-30  
**Owner:** Krieger (Platform)  
**Status:** ⏳ Pending  
**Due:** 2026-03-28

---

## Description

Create Grafana dashboards for monitoring the webhook platform: platform overview, endpoint health, and infrastructure metrics.

---

## Requirements

### Dashboards

1. **Platform Overview**
   - Events ingested per second
   - Deliveries per second (success/failure)
   - Queue depth (pending + retry)
   - Worker utilization
   - API request rate and latency
   - Delivery success rate percentage

2. **Endpoint Health**
   - Success rate by endpoint (top 10)
   - Circuit breaker states (count open/closed)
   - Average delivery latency by endpoint
   - Top failing endpoints
   - Endpoint status distribution

3. **Infrastructure**
   - PostgreSQL: connections, query time, transactions
   - Redis: memory usage, hit rate, operations/sec
   - K8s: pod CPU, memory, restart count
   - Network: I/O, error rate

### Metrics to Collect

**Application Metrics:**
- `hooks_events_ingested_total` (counter)
- `hooks_deliveries_total` (counter, label: status)
- `hooks_delivery_duration_seconds` (histogram)
- `hooks_queue_depth` (gauge, label: queue_type)
- `hooks_circuit_breaker_state` (gauge, label: endpoint_id)

**Infrastructure Metrics:**
- Standard node_exporter
- postgres_exporter
- redis_exporter

### Alert Thresholds

| Metric | Warning | Critical |
|--------|---------|----------|
| Delivery success rate | <99% | <95% |
| P95 delivery latency | >5s | >10s |
| Queue depth | >10k | >50k |
| Worker CPU | >80% | >95% |
| PostgreSQL connections | >80% | >95% |

---

## Acceptance Criteria

- [ ] Platform Overview dashboard created
- [ ] Endpoint Health dashboard created
- [ ] Infrastructure dashboard created
- [ ] All metrics collecting correctly
- [ ] Alert thresholds configured
- [ ] Dashboards tested with load

---

## Related

- Owner: Krieger
- Part of: Phase 4 Production Hardening
- Tooling: Prometheus + Grafana
