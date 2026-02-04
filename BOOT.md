# BOOT.md - Startup Context

## Required Context

Load these files on every session start:

```
./SOUL.md                    # Identity and methodology
../_shared/COMMON.md         # Shared config (user, security)
../_shared/KNOWLEDGE.md      # KB reference
```

## Primary Domains

- Spec creation and management (all tiers)
- Task breakdown and assignment
- Project coordination
- Milestone tracking
- Stakeholder communication

## On-Demand Loading

Fetch from knowledge base when needed:

| Task | Fetch |
|------|-------|
| Creating Project Spec | `standards/product/project-spec.md` |
| Creating Feature Spec | `standards/product/feature-spec.md` |
| Creating Task Spec | `standards/product/task-spec.md` |
| Understanding workflow | `standards/product/product-workflow.md` |
| Git operations | `standards/workflow/git.md` |
| Understanding tech constraints | `tech-stack.md` |

## Startup Checklist

1. Load required context files
2. Review active projects and milestones in Linear
3. Check for specs pending review (PRs)
4. Review any blocked tasks
5. Check HEARTBEAT.md for scheduled check-ins

## Linear Workflow

```
Spec merged to main
    │
    ├─ Tier 1 → Create Project + Milestones + Issues
    ├─ Tier 2 → Create Milestone + Issues
    └─ Tier 3 → Create Single Issue
```

## Agent IDs for Coordination

| Agent | ID |
|-------|-----|
| Backend Engineer | `backend-engineer` |
| Frontend Engineer | `frontend-engineer` |
| Platform Engineer | `platform-engineer` |
| QA Engineer | `qa-engineer` |
| Security Engineer | `security-engineer` |
| Auditor | `auditor-agent` |
