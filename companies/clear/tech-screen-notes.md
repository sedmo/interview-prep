# CLEAR — Tech Screen Notes

**Date:** March 4, 2026
**Interviewer:** Fernando (Senior Engineer, Cloud Infrastructure team, ~1.5 years at CLEAR)
**Platform:** CodeSignal
**Format:** 5 min intro → 45 min coding → 10 min Q&A

---

## Coding Problem

**To-do app** with two lists: backlog and today's items. Implement `add`, `undo`, and `redo` methods.

- **Add:** Find task in backlog, move to today's list, record action for undo, clear redo history
- **Undo:** Pop from undo stack, remove from today's list, insert back into backlog at original index, push to redo stack
- **Redo:** Pop from redo stack, find in backlog, move back to today's list, push to undo stack

**Result:** All tests passed. Hit submit, all green.

### Complexity Discussion

- **Time:** O(N) for add/redo (linear scan of backlog), O(1) for undo
- **Space:** O(N) for today's list and stacks
- **Improvement:** Extract helper functions for duplicated traversal/move logic

## Team Intel — Cloud Infrastructure

### Stack
- **Cloud:** AWS (everything possible)
- **Containers:** EKS
- **Service Mesh:** Istio (new, major org-wide rollout — biggest current initiative)
- **IaC:** Pulumi (migrating from Terraform)
- **Observability:** Datadog (migrated from Splunk). Observability team owns Datadog, infra owns deploying agents and writing metrics code
- **GitOps:** Argo (trying to move away from UI heaviness where possible, no formal replacement yet)
- **Helm:** Yes
- **FedRAMP environments:** CloudWatch (Datadog wasn't previously FedRAMP approved)

### Physical Infrastructure
- Huge fleet of physical devices in airports
- Fernando's work: automating device configuration (was done by hand → now YAML files through IaC)
- Separate team manages on-prem airport Windows computers
- Interface with TSA systems (can be slow/bureaucratic)

### Team Culture
- Very green-field heavy — constantly building new systems
- Legacy work exists but in service of modernization (clearing old stuff to build new)
- On-call for owned infrastructure (RDS, etc.)
- Cross-functional impact radius — infra changes affect entire org

### Current Challenges
- Service mesh rollout (impacting every service at CLEAR)
- Coordinating with all teams for smooth integration
- Legacy service porting to the mesh
- Designing for non-technical airport staff (physical device UX)
- Scope constantly expanding

## Next Steps

Multi-round single-day on-site interview in NYC (scheduled for next week). Fernando said recruiting follows up quickly.

## Key Notes

- Fernando confirmed this role is likely for his Cloud Infrastructure team
- MTLS already implemented between select services (precursor to full mesh)
- Government environments have additional compliance constraints
- You signed NDA — got full exposure to stack details
