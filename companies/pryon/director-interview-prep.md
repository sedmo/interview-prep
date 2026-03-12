__PRYON__

Director of Engineering Interview Prep

Senior Software Engineer, Platform

Friday, March 13, 2026 | 3:30 PM EST | Hari Kathi, Director of Engineering

# Interview Overview

__Format: __Resume deep dive\. Hari will pull different parts of your resume and ask questions about technologies you've worked with\. Conversational but technical — expect probing follow\-ups on architectural decisions, trade\-offs, and lessons learned\.

__Duration: __Likely 45–60 minutes\.

__Stage: __Round 2 of 4\. After this: CoderPad, System Design/Architecture, and PR Review\.

__Goal: __Demonstrate depth across Go, Kubernetes, Terraform, and event\-driven systems\. Show you can articulate WHY you made decisions, not just WHAT you built\.

# What We Know About Pryon

__Detail__

__Information__

__Company__

Pryon — AI knowledge management platform \(enterprise \+ federal\)

__Stage__

Series B\. Founded by Alexa/Siri/Watson alumni \(Igor Jablokov\)

__Product__

Automated ML pipelines, large\-scale APIs, microservices for enterprise AI

__Team__

Platform squad: 5 engineers currently, hiring 2 more

__Stack__

Go, Kubernetes, Docker, Terraform, Istio \(service mesh\)

__Location__

Remote

__Comp__

$180–200K base \+ up to 20% bonus \(semiannual\) \+ stock options

__Interviewer__

Hari Kathi — Director of Engineering

# Stack Alignment — What Hari Will Probe

Hari's job is to validate that your resume maps to their platform needs\. Here's what to expect and how to respond\.

## Go \(Golang\)

__Their need: __Primary backend language for microservices and platform tooling\.

__Your experience: __Goroutine\-based concurrent pipelines \(Story 1\), K8s Controller in Go \(GreenOps\), telemetry service at IBM\.

__Be ready to discuss: __Goroutine patterns \(worker pools, channels, select\), backpressure mechanisms, error handling \(wrapping, sentinel errors\), connection pooling, and why Go over Python for specific workloads\.

__Lead with Story 1: __"I rewrote a Python migration pipeline in Go using a bounded worker pool\. The key insight was the backpressure mechanism — each worker measured DB round\-trip latency, and if P95 crossed 200ms, workers self\-throttled\. It went from 10\+ hours to under 3\."

## Kubernetes

__Their need: __Core orchestration layer for ML pipelines and microservices\.

__Your experience: __5\+ clusters at The Knot \(ArgoCD, Helm\), EKS at IBM, custom K8s Controller in Go \(GreenOps\)\.

__Be ready to discuss: __Cluster management at scale, GitOps workflows \(ArgoCD\), Helm chart design and versioning, resource requests/limits and right\-sizing, pod scheduling and affinity, RBAC, and cluster upgrades\.

__GreenOps angle: __"I built a custom K8s Controller in Go using client\-go that monitors pod CPU utilization via Prometheus metrics and automatically right\-sizes or hibernates underutilized pods\. It demonstrates my understanding of K8s internals — informers, work queues, reconciliation loops\."

## Terraform / Infrastructure as Code

__Their need: __Infrastructure provisioning and management across cloud environments\.

__Your experience: __Terraform across IBM Consulting \(entertainment \+ federal\), modular IaC at The Knot, AWS CDK for compliance constructs\.

__Be ready to discuss: __Module design patterns, state management \(remote state, locking\), provider versioning, handling secrets, plan/apply workflows in CI/CD, and drift detection\.

__Lead with Story 3: __"I built opinionated CDK constructs where compliance was baked in — encryption, tagging, access controls by default\. Teams could override with a break\-glass mechanism that auto\-triggered an audit alert\. We went from 60% to 100% compliance\."

## Istio / Service Mesh

__Their need: __Service mesh for inter\-service communication, traffic management, observability\.

__Your experience: __This is a growth area — no production Istio experience\. Be honest\.

__How to frame it: __"I haven't deployed Istio in production, but I understand the patterns it solves — mutual TLS for service\-to\-service auth, traffic splitting for canary deployments, circuit breaking, and observability via sidecar proxies\. At The Knot I implemented some of these patterns manually \(health checks, retry logic, circuit breakers\), so the concepts are familiar even if I haven't used the Istio control plane specifically\."

# Resume Areas Hari Will Likely Probe

A Director doing a resume deep dive focuses on architectural decisions and technical leadership\.

__Resume Area__

__Likely Question__

__Your Story / Answer__

__Go concurrent pipelines__

"Walk me through the goroutine\-based migration\."

Story 1\. Bounded worker pool, backpressure on P95, dead\-letter queue, dry\-run flag\.

__ECS vs Lambda decision__

"How did you decide between compute services?"

Story 2\. Lambda blew past DB max\_connections\. Hot path \(ECS\+PgBouncer\), cold path \(Lambda\)\.

__K8s cluster management__

"How did you manage 5\+ clusters?"

Story 7 \+ K8s details\. ArgoCD GitOps, Helm, unified CI/CD, failures down 30%\.

__Observability__

"How do you approach monitoring?"

Story 5\. IteratorAge, leading vs lagging indicators, tiered alerting, capacity model\.

__IaC / compliance__

"How did you handle compliance in IaC?"

Story 3\. Golden Path CDK constructs, break\-glass mechanism, opt\-in adoption\.

__Federal modernization__

"Tell me about the COBOL migration\."

Story 6\. 200 behavioral tests, SME partnerships, incremental translation\.

__CI/CD platform__

"How did you drive adoption?"

Story 7\. Listened first, extracted best implementations, opt\-in, turned blocker into contributor\.

__Database optimization__

"Tell me about DB performance work\."

Story 8\. PgBouncer \(transaction mode\), covering \+ partial indexes, read replica\. Connections: ~1000 to ~200\.

# Pryon\-Specific Angles to Weave In

Pryon builds automated ML pipelines for enterprise customers\. Connect your experience to their domain\.

- __ML Pipeline Infrastructure: __Your Kinesis streaming pipeline \(Story 5\) is directly analogous to ML data pipelines — high\-volume event processing, backpressure, observability\. Frame it this way\.
- __Enterprise \+ Federal: __Pryon serves enterprise and federal clients\. Your federal modernization \(Story 6\) and compliance tooling \(Story 3\) show you understand regulated environments\.
- __Platform Squad Mentality: __With 5 engineers hiring 2 more, they need someone who owns significant scope independently\. Story 7 shows you build for other engineers without mandating\.
- __Service Mesh Context: __Even without Istio production experience, show you understand the problems it solves: mTLS, traffic management, canary deployments, network\-layer observability\.
- __AI/LLM Awareness: __Your Scale AI work \(LLM evaluation, hallucination detection, BigQuery\) shows engineering\-side AI understanding\. Mention if conversation touches their product\.

# Questions for Hari

A Director interview is bidirectional\. These show you're evaluating them with the same rigor\.

- "Can you walk me through the platform squad's current architecture? What does the deployment pipeline look like from commit to production?"
- "What's the biggest infrastructure challenge the platform team is facing right now?"
- "How is Istio being used today — fully adopted, or still in progress?"
- "With the team at 5 hiring 2 more, what does ownership look like? Specific domains, or collective ownership?"
- "What does success look like for this role at 90 days?"
- "How does the platform squad interact with the ML engineering team?"

# Mindset Reminders

- __This is a conversation, not a quiz\. __When Hari pulls on a resume thread, tell the story — context, decision, trade\-off, outcome\.
- __Be honest about gaps\. __If he asks about Istio, don't bluff\. Say what you know, what's adjacent, and that you're excited to go deeper\. Transparency builds trust with Directors\.
- __Connect everything to platform impact\. __Don't just describe what you built — explain who it helped and how it changed how the team operated\.
- __Use your hooks\. __Your story bank has memorable opening lines\. They make your answers stick\.

# Compensation Note

You told Amanda your target is $200K total comp\. Their range is $180–200K base \+ up to 20% bonus \(semiannual\) \+ stock options\. At the high end: $200K base \+ $40K bonus = $240K cash \+ options\. Don't discuss comp with Hari — that's Amanda's conversation\. If he asks, deflect: "Amanda and I are aligned on the range — I'm focused on making sure the technical fit is right\."

__You know this stack\. You've lived these problems\. Trust your stories and let Hari see the engineer behind the resume\. Good luck Friday\.__

