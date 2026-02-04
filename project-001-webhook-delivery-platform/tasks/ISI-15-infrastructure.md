# Task: ISI-15 - K8s Cluster Provisioning

**Project:** Webhook Delivery Platform  
**Phase:** 1  
**Issue:** ISI-15  
**Owner:** Krieger (Platform Engineer)  
**Status:** ⏳ In Progress  
**Due:** 2026-02-11

---

## Description

Provision production infrastructure: K8s cluster (EKS/GKE), managed PostgreSQL 15+, managed Redis 7+, domain SSL certificates, and namespace setup.

---

## Requirements

### Infrastructure Components

1. **Kubernetes Cluster**
   - Provider: EKS (AWS) or GKE (GCP)
   - Version: 1.28+
   - Nodes: 3+ worker nodes (t3.medium or equivalent)
   - Region: us-east-1 (single region for v1)

2. **PostgreSQL 15+**
   - Provider: RDS (AWS) or Cloud SQL (GCP)
   - Instance: db.t3.medium or equivalent
   - Storage: 100GB (expandable)
   - Backup: Daily snapshots, 7-day retention
   - Multi-AZ: Enabled for production

3. **Redis 7+**
   - Provider: ElastiCache (AWS) or Memorystore (GCP)
   - Node type: cache.t3.micro or equivalent
   - Cluster mode: Enabled (for scaling)
   - Persistence: AOF enabled

4. **Domain & SSL**
   - Domain: hooks.sendai.co.zw
   - SSL: Let's Encrypt or commercial cert
   - DNS: A record to load balancer
   - Auto-renewal: Configured

5. **K8s Resources**
   - Namespace: webhook-platform
   - Service accounts with RBAC
   - ConfigMaps for configuration
   - Secrets for credentials
   - HPA for worker auto-scaling

---

## Deliverables

### 1. Terraform/Infra-as-Code

```
infrastructure/
├── terraform/
│   ├── main.tf          # Provider config
│   ├── vpc.tf           # Networking
│   ├── eks.tf           # K8s cluster
│   ├── rds.tf           # PostgreSQL
│   ├── elasticache.tf   # Redis
│   └── outputs.tf       # Connection strings
└── k8s/
    ├── namespace.yaml
    ├── deployment-api.yaml
    ├── deployment-worker.yaml
    ├── service.yaml
    ├── ingress.yaml
    └── hpa.yaml
```

### 2. Connection Strings for Sterling

| Service | Connection String |
|---------|-------------------|
| PostgreSQL | `postgres://user:pass@host:5432/hooks_platform` |
| Redis | `redis://host:6379` |

### 3. K8s Deployment Files

- **API Deployment**: 3 replicas, resource limits
- **Worker Deployment**: 5 replicas (scalable), HPA
- **Ingress**: TLS termination, path routing
- **HPA**: Scale workers 5-20 based on queue depth

---

## Configuration

### Environment Variables for Sterling

```yaml
# ConfigMap
ENV: production
SERVER_PORT: "8080"
DB_HOST: "hooks-platform.xxx.us-east-1.rds.amazonaws.com"
DB_PORT: "5432"
DB_NAME: "hooks_platform"
REDIS_HOST: "hooks-platform.xxx.cache.amazonaws.com"
REDIS_PORT: "6379"
QUEUE_STREAM_NAME: "events:stream"
WORKER_COUNT: "5"

# Secrets (base64 encoded)
DB_USER: "hooks"
DB_PASSWORD: "<generated>"
REDIS_PASSWORD: "<if applicable>"
```

---

## Timeline

| Day | Task |
|-----|------|
| 1-2 | VPC, subnets, security groups |
| 3 | EKS/GKE cluster provisioning |
| 4 | RDS/Cloud SQL PostgreSQL |
| 5 | ElastiCache/Memorystore Redis |
| 6 | K8s namespace, deployments |
| 7 | Domain, SSL, ingress |

---

## Acceptance Criteria

- [ ] K8s cluster running and accessible
- [ ] PostgreSQL accessible from K8s
- [ ] Redis accessible from K8s
- [ ] Domain DNS resolves to ingress
- [ ] SSL certificate valid
- [ ] API pods running (3 replicas)
- [ ] Worker pods running (5+ replicas)
- [ ] HPA configured and working
- [ ] Logs accessible (CloudWatch/Stackdriver)
- [ ] Sterling can deploy code

---

## Dependencies

| Dependency | From | Status |
|------------|------|--------|
| Domain ownership | Rick | ✅ Confirmed (sendai.co.zw) |
| Cloud provider access | Rick | ⏳ Pending |
| Container registry | Krieger | ⏳ To create |
| SSL cert decision | Malory | ⏳ Let's Encrypt default |

---

## Notes

**Started:** 2026-02-04  
**Expected Complete:** 2026-02-11

**Design Decisions:**
- AWS preferred (EKS, RDS, ElastiCache)
- Terraform for IaC
- ArgoCD for GitOps deployment (optional Phase 4)
- Single region (us-east-1) for v1

**Cost Estimates (monthly):**
- EKS cluster: ~$75
- RDS PostgreSQL: ~$50
- ElastiCache Redis: ~$15
- EC2 nodes: ~$150
- **Total: ~$290/month**

---

## Security

- VPC isolated from public internet
- PostgreSQL in private subnet
- Redis in private subnet
- API ingress only on 443/TLS
- Secrets in K8s Secrets (not env vars in repo)
- Network policies between pods

---

## Related

- Blocks: Phase 4 production deployment
- Supports: All backend services
- Handoff to: Lana (load testing)
