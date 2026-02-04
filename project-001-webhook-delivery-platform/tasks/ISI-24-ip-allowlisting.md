# Task: ISI-24 - Security - IP Allowlisting

**Project:** Webhook Delivery Platform  
**Phase:** 3  
**Issue:** ISI-24  
**Owner:** Barry (Security) + Sterling (Backend)  
**Status:** ⏳ Pending  
**Due:** 2026-03-18

---

## Description

Implement IP allowlisting for webhook endpoints, allowing customers to restrict deliveries to specific IP ranges for enhanced security.

---

## Requirements

### Backend

1. **Schema Update**
   - `endpoints.allowed_ips` array already exists in schema
   - Support CIDR notation (e.g., `192.168.1.0/24`)
   - Support single IPs (e.g., `10.0.0.1`)

2. **IP Validation Middleware**
   - Check source IP on delivery attempt
   - If allowlist exists, verify IP is in range
   - If not in range: mark failed, "IP not allowed"
   - No retry (permanent failure)

3. **API Endpoints**
   ```
   PUT /v1/endpoints/{id}/allowed-ips
   Body: { "allowed_ips": ["192.168.1.0/24", "10.0.0.5"] }
   ```

### Frontend

1. **IP Management UI**
   - Textarea or tag input for IPs
   - CIDR validation
   - "My IP" button (auto-add current IP)
   - IP format validation

2. **Display**
   - Show allowed IPs on endpoint detail
   - Warning if empty (open to all)

---

## IP Validation Logic

```go
func isIPAllowed(sourceIP string, allowedList []string) bool {
    if len(allowedList) == 0 {
        return true // No restriction
    }
    
    for _, allowed := range allowedList {
        if isCIDR(allowed) {
            if ipInCIDR(sourceIP, allowed) {
                return true
            }
        } else {
            if sourceIP == allowed {
                return true
            }
        }
    }
    return false
}
```

---

## Acceptance Criteria

- [ ] API accepts CIDR and single IPs
- [ ] Validation middleware blocks non-allowed IPs
- [ ] Frontend IP management works
- [ ] "My IP" button adds current IP
- [ ] Proper error messages for blocked IPs
- [ ] Empty allowlist = allow all (backward compatible)

---

## Related

- Owner: Barry (spec), Sterling (implementation)
- Part of: Phase 3 Security & Billing
- Security: Medium priority
