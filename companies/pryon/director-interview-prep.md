# PRYON Director of Engineering Interview Prep

**Senior Software Engineer, Platform**
**Friday, March 13, 2026 | 3:30 PM EST | Hari Kathi, Director of Engineering**

---

## Interview Overview

**Format:** Resume deep dive. Hari will pull different parts of your resume and ask questions about technologies you've worked with. Conversational but technical — expect probing follow-ups on architectural decisions, trade-offs, and lessons learned.

**Duration:** Likely 45–60 minutes.

**Stage:** Round 2 of 4. After this: CoderPad, System Design/Architecture, and PR Review.

**Goal:** Demonstrate depth across Go, Kubernetes, Terraform, and event-driven systems. Show you can articulate WHY you made decisions, not just WHAT you built.

---

## What We Know About Pryon

| Detail | Information |
|---|---|
| Company | Pryon — AI knowledge management platform (enterprise + federal) |
| Stage | Series B. Founded by Alexa/Siri/Watson alumni (Igor Jablokov) |
| Product | Automated ML pipelines, large-scale APIs, microservices for enterprise AI |
| Team | Platform squad: 5 engineers currently, hiring 2 more |
| Stack | Go, Kubernetes, Docker, Terraform, Istio (service mesh) |
| Location | Remote |
| Comp | $180–200K base + up to 20% bonus (semiannual) + stock options |
| Interviewer | Hari Kathi — Director of Engineering |

---

## The Opener: "Tell Me About Yourself"

Keep it under 90 seconds. Structure: present → past → future.

"I'm a Senior Software and Platform Engineer with about 8 years of experience, most recently at IBM Consulting where I worked on two large engagements — a real-time data pipeline for an entertainment client and a mainframe-to-cloud modernization for a federal agency. Before that I was at The Knot Worldwide, where I owned the Kubernetes platform and CI/CD infrastructure for 10+ engineering teams. The thread across all of it is building platform tooling that makes other engineers faster — whether that's a self-regulating Go pipeline, GitOps-managed clusters, or shared CI/CD libraries. I'm drawn to Pryon because you're doing that same kind of work — platform infrastructure for ML pipelines — at a stage where the engineering decisions really shape the product."

---

## "Why Are You Looking to Leave?" / "Why Pryon?"

"IBM Consulting gave me great exposure to complex systems across industries, but I'm ready to go deeper on a single platform rather than cycling across client engagements. Pryon is compelling because you're building ML infrastructure at a scale where the platform decisions genuinely matter — and the stack aligns with where I've done my best work."

---

## Stack Alignment — What Hari Will Probe

Hari's job is to validate that your resume maps to their platform needs. Here's what to expect and how to respond.

### Go (Golang)

**Their need:** Primary backend language for microservices and platform tooling.

**Your experience:** Goroutine-based concurrent pipelines (Story 1, Phase 1), K8s Controller in Go (GreenOps), telemetry service at IBM.

**Be ready to discuss:** Goroutine patterns (worker pools, channels, select), backpressure mechanisms, error handling (wrapping, sentinel errors), connection pooling, and why Go over Python for specific workloads.

**Lead with Story 1 (Phase 1 — The Go Rewrite):** "I rewrote a Python migration pipeline in Go using a bounded worker pool. The key insight was the backpressure mechanism — each worker measured DB round-trip latency, and if P95 crossed 200ms, workers self-throttled. It went from 10+ hours to under 3."

### Kubernetes

**Their need:** Core orchestration layer for ML pipelines and microservices.

**Your experience:** 5+ clusters at The Knot (ArgoCD, Helm), EKS at IBM, custom K8s Controller in Go (GreenOps).

**Be ready to discuss:** Cluster management at scale, GitOps workflows (ArgoCD), Helm chart design and versioning, resource requests/limits and right-sizing, pod scheduling and affinity, RBAC, and cluster upgrades.

**GreenOps angle:** "I built a custom K8s Controller in Go using client-go that monitors pod CPU utilization via Prometheus metrics and automatically right-sizes or hibernates underutilized pods. It demonstrates my understanding of K8s internals — informers, work queues, reconciliation loops."

### Terraform / Infrastructure as Code

**Their need:** Infrastructure provisioning and management across cloud environments.

**Your experience:** Terraform across IBM Consulting (entertainment + federal), modular IaC at The Knot, AWS CDK for compliance constructs.

**Be ready to discuss:** Module design patterns, state management (remote state, locking), provider versioning, handling secrets, plan/apply workflows in CI/CD, and drift detection.

**Lead with Story 4 (Part 1 — Infrastructure Golden Path):** "I built opinionated CDK constructs where compliance was baked in — encryption, tagging, access controls by default. Teams could override with a break-glass mechanism that auto-triggered an audit alert. We went from 60% to 100% compliance."

### Istio / Service Mesh

**Their need:** Service mesh for inter-service communication, traffic management, observability.

**Your experience:** This is a growth area — no production Istio experience. Be honest.

**How to frame it:** "I haven't deployed Istio in production, but I understand the patterns it solves — mutual TLS for service-to-service auth, traffic splitting for canary deployments, circuit breaking, and observability via sidecar proxies. At The Knot I implemented some of these patterns manually (health checks, retry logic, circuit breakers), so the concepts are familiar even if I haven't used the Istio control plane specifically."

---

## Resume Areas Hari Will Likely Probe

A Director doing a resume deep dive focuses on architectural decisions and technical leadership.

| Resume Area | Likely Question | Your Story / Answer |
|---|---|---|
| Go concurrent pipelines | "Walk me through the goroutine-based migration." | **Story 1, Phase 1.** Bounded worker pool, backpressure on P95, dead-letter queue, dry-run flag. |
| ECS vs Lambda decision | "How did you decide between compute services?" | **Story 2.** Lambda blew past DB max_connections. Hot path (ECS+PgBouncer), cold path (Lambda). |
| K8s cluster management | "How did you manage 5+ clusters?" | **Story 5.** ArgoCD GitOps for cluster config, Helm, unified monitoring, deployment failures down 30%. |
| Observability | "How do you approach monitoring?" | **Story 1, Phase 2.** IteratorAge, leading vs lagging indicators, tiered alerting, capacity model. |
| IaC / compliance | "How did you handle compliance in IaC?" | **Story 4, Part 1.** Golden Path CDK constructs, break-glass mechanism, opt-in adoption. |
| Federal modernization | "Tell me about the COBOL migration." | **Story 3.** 200 behavioral tests, SME partnerships, incremental translation. |
| CI/CD platform | "How did you drive adoption?" | **Story 4, Part 2.** Listened first, extracted best implementations, opt-in, turned blocker into contributor. |
| Database optimization | "Tell me about DB performance work." | **Story 1, Phase 3.** PgBouncer (transaction mode), covering + partial indexes, read replica. Connections: ~1000 to ~200. |

---

## Pryon-Specific Angles to Weave In

Pryon builds automated ML pipelines for enterprise customers. Connect your experience to their domain.

**ML Pipeline Infrastructure:** Your Kinesis streaming pipeline (Story 1, Phase 2) is directly analogous to ML data pipelines — high-volume event processing, backpressure, observability. Frame it this way.

**Enterprise + Federal:** Pryon serves enterprise and federal clients. Your federal modernization (Story 3) and compliance tooling (Story 4, Part 1) show you understand regulated environments.

**Platform Squad Mentality:** With 5 engineers hiring 2 more, they need someone who owns significant scope independently. Story 4 (Part 2) shows you build for other engineers without mandating.

**Service Mesh Context:** Even without Istio production experience, show you understand the problems it solves: mTLS, traffic management, canary deployments, network-layer observability.

**AI/LLM Awareness:** Your Scale AI work (LLM evaluation, hallucination detection, BigQuery) shows engineering-side AI understanding. Mention if conversation touches their product.

---

## Questions for Hari

A Director interview is bidirectional. These show you're evaluating them with the same rigor.

- "Can you walk me through the platform squad's current architecture? What does the deployment pipeline look like from commit to production?"
- "What's the biggest infrastructure challenge the platform team is facing right now?"
- "How is Istio being used today — fully adopted, or still in progress?"
- "With the team at 5 hiring 2 more, what does ownership look like? Specific domains, or collective ownership?"
- "What does success look like for this role at 90 days?"
- "How does the platform squad interact with the ML engineering team?"

---

## Mindset Reminders

**This is a conversation, not a quiz.** When Hari pulls on a resume thread, tell the story — context, decision, trade-off, outcome.

**Be honest about gaps.** If he asks about Istio, don't bluff. Say what you know, what's adjacent, and that you're excited to go deeper. Transparency builds trust with Directors.

**Connect everything to platform impact.** Don't just describe what you built — explain who it helped and how it changed how the team operated.

**Use your hooks.** Your story bank has memorable opening lines. They make your answers stick.

---

## Compensation Note

You told Amanda your target is $200K total comp. Their range is $180–200K base + up to 20% bonus (semiannual) + stock options. At the high end: $200K base + $40K bonus = $240K cash + options. Don't discuss comp with Hari — that's Amanda's conversation. If he asks, deflect: "Amanda and I are aligned on the range — I'm focused on making sure the technical fit is right."

---

*You know this stack. You've lived these problems. Trust your stories and let Hari see the engineer behind the resume. Good luck Friday.*
