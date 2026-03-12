# Betterment — Technical Round Notes

**Date:** March 11, 2026 (yesterday per recruiter wrap-up)
**Format:** Two back-to-back sessions (~2 hours total)

---

## Round 1: System Design — Echo (Sr. SRE, DevX) + Chakara (IE team, shadowing)

### Problem

Build a system that reliably creates Google Tasks from Google Calendar events.

**Constraints:**
- Stateless compute only (no database, no key-value store)
- Must create tasks within ~1 hour of calendar event
- No duplicate tasks for end users
- Full read/write access to both Tasks and Events APIs
- Calendar events do NOT have IDs

### Your Approach

- Event processor that hashes events for idempotency
- Proposed synthetic event IDs since calendar events don't have IDs (Echo liked this)
- Fingerprinting based on title + start/end time to detect changes vs new events
- Upsert logic: insert if no task ID exists, patch if fingerprint changed, skip if unchanged

### Key Probing Questions

- **Network partition handling:** Insert sent but no response back — you mentioned idempotency keys and reconciliation (right track, ran out of time to flesh out)
- **Insert vs upsert:** Echo asked about issues with insert — explored the at-least-once delivery problem

### Assessment

Concepts were sound but execution was abstract. Echo had to repeatedly ask you to use actual API calls from the prompt document. The back-and-forth on getting concrete took time. You identified this as "rocky" in the wrap-up call.

---

## Round 2: Pair Programming — Peter (SRE, DevX) + Kevin (SRE, IE)

### Exercise

EC2 CRUD operations using boto3 (Python) in CoderPad. Open book — Google/docs allowed, no AI.

### Part 0: Reformat Instance Output ✅

Changed output to return `{"instances": [list of instance IDs]}`. Some friction navigating boto3 response structure (Pascal case keys, metadata nesting) but completed.

### Part 1: Create N Instances ✅

Take input count, create that many EC2 instances using `run_instances`. Wired up AMI, security group, subnet, instance type. Completed.

### Part 2: Launch by Role with Tagging ✅

Create web and worker instances separately with tags. Resource type for tags was tricky (`"instance"`) — Peter and Kevin helped. Completed right at the buzzer.

### Behavioral Questions (brief, at start)

- **Camaraderie:** Round-robin reviews, living documentation culture
- **Encouraging opinions:** Smaller group settings, building rapport privately first before larger group discussions
- **Process change:** Platform engineering — became enabler instead of gatekeeper when devs were doing shadow IT

---

## Team Intel Gathered

- **Stack:** AWS, Kubernetes (most prod workloads), some Lambdas, Datadog, bespoke deployment system (Ruby on Rails), Terraform
- **Scale:** ~10–20 services total, not a microservices shop
- **On-call:** Shared rotation across both squads, rotates every couple months, not heavy
- **Current priorities:**
  - AI adoption governance and enablement
  - CI/CD optimization (more observability into deploys, not just speed)
  - Code ownership initiative
  - Exploring Bazel for builds
  - Software composition analysis tooling vendor review
- **Internal tooling:** "Coach" — internal abstraction on top of K8s for deployments, code ownership, golden paths (not a full IDP like Backstage/Cortex)
- **Culture:** Prescribed work over reactionary, sprint-based, agile. Not fighting fires.
