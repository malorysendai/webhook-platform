# Project Spec: Webhook Delivery Platform

**Project ID:** 001  
**Status:** Draft → In Progress  
**Tier:** 1 (Multi-phase, multi-sprint initiative)  
**Branch:** `project-001-webhook-delivery-platform`  
**Created:** 2026-02-04  
**Author:** Malory Archer (Product Manager)

---

## Executive Summary

Build a customer-facing webhook delivery platform from scratch. The service will allow external customers to register webhook endpoints, receive event deliveries with guaranteed reliability, and manage their webhook configurations through a dashboard. This is a revenue-generating product with SLA commitments.

---

## Goals

1. **Reliable Delivery**: 99.9% successful delivery rate with automatic retries
2. **Customer Self-Service**: Full dashboard for endpoint management and log access
3. **Enterprise Security**: Signature verification, IP allowlisting, encryption options
4. **Revenue Ready**: Billing integration foundation for metered usage
5. **Production Grade**: Monitoring, alerting, and operational runbooks

---

## Success Criteria

| Metric | Target |
|--------|--------|
| Delivery Success Rate | ≥ 99.9% |
| P95 Latency | < 2s (to customer endpoint) |
| Event Replay Availability | 7 days |
| API Uptime | 99.95% |
| Customer Dashboard Load | < 3s |

---

## In Scope

### Core Infrastructure
- Multi-tenant webhook ingestion API (REST + authenticated)
- Delivery engine with exponential backoff and circuit breakers
- Event persistence with replay capabilities
- Dead letter queue for failed deliveries
- Rate limiting per customer/endpoint

### Security & Compliance
- HMAC-SHA256 webhook signatures
- IP allowlisting per endpoint
- Optional payload encryption (AES-256)
- API key management with rotation
- Audit logging for all operations

### Customer Experience
- Dashboard (React/Vue) for endpoint management
- Real-time delivery logs with filtering
- Event replay interface
- Webhook testing tool (send test events)
- Email alerts for endpoint failures

### Billing Foundation
- Usage metering (events delivered, retries, storage)
- Billing data export to billing system
- Tier limits enforcement (free/pro/enterprise)

### Operations
- Monitoring dashboards (Grafana)
- Alerting rules (PagerDuty/Slack)
- Operational runbooks
- Customer-facing status page integration

---

## Out of Scope (v1)

- Webhook payload transformation/filtering (Phase 2)
- Third-party integrations (Zapier, Make, etc.) (Phase 2)
- Advanced analytics/reports (Phase 2)
- Multi-region deployment (Phase 3)
- WebSocket streaming (Future)

---

## Architecture Overview

```
┌─────────────────┐     ┌──────────────────┐     ┌─────────────────┐
│  Customer API   │────▶│  Ingestion API   │────▶│  Event Store    │
│  (Your Product) │     │  (Our Platform)  │     │  (PostgreSQL)   │
└─────────────────┘     └──────────────────┘     └────────┬────────┘
                                                            │
                              ┌─────────────────────────────┘
                              ▼
                    ┌──────────────────┐
                    │  Delivery Queue  │
                    │  (Redis/RabbitMQ)│
                    └────────┬─────────┘
                             │
              ┌──────────────┼──────────────┐
              ▼              ▼              ▼
       ┌──────────┐   ┌──────────┐   ┌──────────┐
       │ Worker 1 │   │ Worker 2 │   │ Worker N │
       └────┬─────┘   └────┬─────┘   └────┬─────┘
            │              │              │
            └──────────────┼──────────────┘
                           ▼
                    ┌──────────────┐
                    │ Customer     │
                    │ Webhook URL  │
                    └──────────────┘
```

---

## Phases

| Phase | Focus | Duration | Dependencies |
|-------|-------|----------|--------------|
| Phase 1 | Core Infrastructure | 3 weeks | Krieger: K8s cluster, Sterling: API design |
| Phase 2 | Customer Dashboard | 2 weeks | Ray: UI components, Sterling: API completion |
| Phase 3 | Security & Billing | 2 weeks | Barry: Security review, Krieger: Billing infra |
| Phase 4 | Production Hardening | 1 week | Lana: Load testing, all teams |

---

## Phase Details

### Phase 1: Core Infrastructure (Weeks 1-3)

**Goal**: Working delivery pipeline with API and workers

**Deliverables**:
- [ ] REST API for endpoint CRUD (POST/GET/PUT/DELETE /v1/endpoints)
- [ ] Event ingestion endpoint (POST /v1/events)
- [ ] Async delivery workers with retry logic
- [ ] PostgreSQL schema (tenants, endpoints, events, deliveries)
- [ ] Redis queue setup
- [ ] Docker compose for local development
- [ ] Unit + integration tests

**Risks**:
- Queue ordering guarantees — confirm Redis Streams vs RabbitMQ **Answer** Redis
- Worker scaling — need K8s HPA configured

**Owner**: Sterling (Backend Lead)  
**Support**: Krieger (Platform)

---

### Phase 2: Customer Dashboard (Weeks 4-5)

**Goal**: Customer-facing UI for self-service

**Deliverables**:
- [ ] Dashboard: Endpoint management (create, edit, delete)
- [ ] Dashboard: Delivery logs with search/filter
- [ ] Dashboard: Event replay interface
- [ ] Dashboard: API key management
- [ ] Webhook testing tool (send test payload)
- [ ] Responsive design (mobile support)
- [ ] Authentication integration (SSO or email)

**Risks**:
- Auth system dependency — confirm if we use existing auth or new **Answer** Auth will be delegated elsewhere skip
- Real-time logs — WebSocket vs polling decision needed **Answer** websocket

**Owner**: Ray (Frontend Lead)  
**Support**: Sterling (API integration)

---

### Phase 3: Security & Billing (Weeks 6-7)

**Goal**: Enterprise-ready security and billing foundation

**Deliverables**:
- [ ] HMAC-SHA256 signature generation on all webhooks
- [ ] IP allowlisting per endpoint
- [ ] Optional payload encryption
- [ ] API key rotation mechanism
- [ ] Audit logging (all admin actions)
- [ ] Usage metering pipeline
- [ ] Billing data export API
- [ ] Tier limit enforcement

**Risks**:
- Encryption performance — benchmark before committing
- Billing system integration — need API docs from finance team

**Owner**: Barry (Security) + Sterling  
**Support**: Krieger (Billing infra)

---

### Phase 4: Production Hardening (Week 8)

**Goal**: Production deployment with monitoring and docs

**Deliverables**:
- [ ] Grafana dashboards (delivery metrics, error rates, latency)
- [ ] PagerDuty alerting rules
- [ ] Load testing (Lana: 10k events/sec target)
- [ ] Runbooks: incident response, rollback procedures
- [ ] Customer-facing documentation
- [ ] API reference docs (Swagger/OpenAPI)
- [ ] Status page integration
- [ ] Production deployment with blue/green

**Risks**:
- Load testing reveals bottlenecks — buffer time for fixes
- SSL/TLS cert management — confirm with Krieger

**Owner**: Krieger (Platform Lead)  
**Support**: All teams

---

## Dependencies

| Dependency | From | Need By | Status |
|------------|------|---------|--------|
| K8s cluster provisioned | Krieger | Week 1 | ⏳ Pending |
| PostgreSQL 15+ cluster | Krieger | Week 1 | ⏳ Pending |
| Redis 7+ cluster | Krieger | Week 1 | ⏳ Pending |
| Billing system API docs | Finance | Week 5 | ⏳ Pending |
| Auth system decision | Rick | Week 3 | ⏳ Pending |
| Domain + SSL certs | Krieger | Week 7 | ⏳ Pending |
| Load testing env | Lana | Week 7 | ⏳ Pending |

---

## Open Questions

1. **Authentication**: Use existing company auth or build new customer auth system? **Answer** We will delegate auth so for now skip it
2. **Domain**: What domain will host this? (hooks.scapelabs.com? webhook.scapelabs.com?) **Answer** hooks.sendai.co.zw
3. **Pricing Tiers**: What are the free/pro/enterprise limits? **Answer** Flat rate per delivery $0.10
4. **Regions**: Single region (US-East) or multi-region from day 1? **Answer** Single , we want to slowly progress
5. **Signature Version**: Standard (Stripe-style) or custom format? **Answer** Stripe style

---

## Team Assignments

| Agent | Role | Responsibilities |
|-------|------|------------------|
| **Sterling** | Backend Lead | API design, delivery workers, database, billing pipeline |
| **Ray** | Frontend Lead | Dashboard UI, customer-facing components, testing tools |
| **Krieger** | Platform Lead | Infrastructure, K8s, monitoring, deployment, security hardening |
| **Barry** | Security | Security review, encryption, signatures, audit logging |
| **Lana** | QA | Load testing, integration tests, delivery verification |
| **Malory** | PM | Spec coordination, task assignment, progress tracking, stakeholder updates |

---

## Risks & Mitigations

| Risk | Impact | Probability | Mitigation |
|------|--------|-------------|------------|
| Queue bottlenecks at scale | High | Medium | Load test early (Week 6); have Kafka as backup option |
| Customer endpoint failures spike | Medium | High | Circuit breakers + backoff; monitoring alerts |
| Security vulnerability found | Critical | Low | Security review in Phase 3; bounty program |
| Billing integration delays | Medium | Medium | Build export API first; manual billing fallback |
| Auth system decision delayed | Medium | Medium | Decision needed by Week 3 or block Phase 2 |

---

## Timeline Summary

```
Week:  1    2    3    4    5    6    7    8
       ├────┴────┴────┤
       │   Phase 1     │
       │ Infrastructure│
                      ├────┴────┤
                      │ Phase 2 │
                      │   UI    │
                                ├────┴────┤
                                │ Phase 3 │
                                │Security │
                                │ Billing │
                                          ├────┤
                                          │P4  │
                                          │Prod│
```

**Target Launch**: Week 8 (2026-04-01)

---

## Next Steps

1. ✅ Create project branch and spec (Malory)
2. ⏳ Schedule kickoff meeting with all agents (Malory)
3. ⏳ Resolve open questions (Rick + Malory)
4. ⏳ Create Linear project + milestones (Malory)
5. ⏳ Assign Phase 1 tasks to Sterling (Malory)
6. ⏳ Krieger provisions infrastructure (Krieger)

---

## Changelog

| Date | Author | Change |
|------|--------|--------|
| 2026-02-04 | Malory | Initial spec created |

---

## Approvals

| Role | Name | Status | Date |
|------|------|--------|------|
| Product Manager | Malory Archer | ✅ Approved | 2026-02-04 |
| Engineering Lead | Sterling | ✅ Approved | |
| Platform Lead | Krieger | ✅ Approved | |
| Security | Barry | ✅ Approved | |
| Stakeholder | Rick | ✅ Approved | |
