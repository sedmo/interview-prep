# Stephan Edmonson — Interview Pipeline Context
**Last Updated:** March 6, 2026

---

## About Stephan

- **Title:** Senior Software & Platform Engineer
- **Experience:** ~7 years
- **Core strengths:** Backend engineering (Go, Python, Node/TypeScript), infrastructure/platform engineering (Kubernetes, Terraform, AWS), CI/CD pipeline design, observability, event-driven architecture
- **Background trajectory:** IT/networking → full-stack intern (Microsoft) → Salesforce engineer (IBM) → DevOps engineer (IBM) → Platform Engineer (The Knot Worldwide) → ML engineering (Scale AI, short contract) → Senior Platform & DevOps Engineer (IBM Consulting)
- **Bilingual:** English / Spanish
- **Location:** NYC area
- **AWS Certified:** December 2025
- **Personal project:** PocketMechanic — AI-powered app integrating LLMs to parse vehicle diagnostic data
- **Interests:** Personal finance, AI tooling, T-shaped engineering

---

## Resumes

Stephan uses different resumes for different roles:
- **Master Resume** — broader, covers full background including AI (used for OpenLane)
- **Platform Engineer Resume** — focused on infra/SRE/DevOps experience (used for Betterment)

---

## Interview Story Bank (7 Stories)

### Story 1: The Go Refactor — Performance & Safety
**Best for:** Complex technical challenge, system performance improvement, rewrite/refactor
**Hook:** "I had to fix a system that was too slow, but my first attempt actually crashed the database because it was too fast."
**Summary:** At Disney engagement, inherited a Python data migration pipeline (10+ hours sequential). Rewrote in Go with bounded worker pool (50 concurrent workers). Key innovation: backpressure mechanism measuring round-trip latency — if P95 crossed 200ms, workers self-throttled. Added dead-letter queue, dry-run flag, staging validation.
**Result:** 10+ hours → under 3 hours (~70% reduction). Zero data corruption.

### Story 2: ECS vs. Lambda — Architecture & Cost
**Best for:** Architectural trade-off, production incident, failure story, cost optimization
**Hook:** "I realized that Serverless isn't always 'No Ops' — sometimes it's 'Database Killer.'"
**Summary:** Built event-driven pipeline on Lambda. First production spike: ~1,000 concurrent Lambda executions each opened DB connections, blew past RDS max_connections, cascading failures. Fix: categorized workloads — hot path (DB-heavy) moved to ECS Fargate with PgBouncer connection pooling; cold path stayed on Lambda. Added CloudWatch alarms on connections, reserved concurrency limits, wrote ADR.
**As failure story:** "Over-indexed on developer velocity, underestimated blast radius of serverless scaling against stateful resources."
**Result:** Production stabilized. Monthly compute costs down ~15%.

### Story 3: The Golden Path — Compliance & Influence
**Best for:** Compliance/security vs. speed, internal tools, making the right thing the easy thing
**Hook:** "If you try to be a 'gatekeeper,' developers just route around you. You have to be an enabler."
**Summary:** At federal client + The Knot, teams deploying infra manually — shadow IT everywhere. Built opinionated CDK constructs with compliance baked in (encryption, tagging, access controls by default). Break-glass mechanism: teams could override defaults, but doing so auto-triggered P1 audit alert. Didn't mandate — went to friendly teams first, let adoption spread organically.
**Result:** 60% → ~100% compliance. Deployment time for new services down ~20%.

### Story 4: The Failure Reframe
Uses Story 2 reframed for failure-specific questions. Key learning: "Every architectural choice has a blast radius. Now I always draw a table: what happens at 10x traffic?"

### Story 5: The Observability Win — Proactive Engineering
**Best for:** Reliability, improving a system you didn't build, preventing outages, SRE questions
**Hook:** "We had a real-time pipeline that passed every health check — green across the board — but was silently delivering stale data."
**Summary:** Kinesis streaming pipeline at Disney. IteratorAge sitting at 45 minutes during morning peaks — all health checks green but dashboards stale. Existing monitoring measured failures (lagging indicators), not lag (leading indicators). Fix: Splunk dashboard correlating IteratorAge + IncomingRecords + processing duration. Tiered alerting (60s warning → 300s Slack → 900s page). Batch writes cut processing time in half. Tuned shard count.
**Result:** Peak IteratorAge from 45 min → under 30 seconds. Zero stale data incidents next quarter.

### Story 6: The COBOL Translation — Ambiguity & Collaboration
**Best for:** Working with ambiguity, unclear requirements, cross-functional collaboration, learning outside expertise
**Hook:** "I was asked to migrate business logic from a 30-year-old mainframe to AWS Lambda. Nobody could fully explain what the code did."
**Summary:** Federal modernization — COBOL to serverless. Requirements doc from 1994, variable names like WS-TEMP-AMT-3. Approach: built relationships with two COBOL devs (15+ years maintaining system), screen-shared for hours walking through code. Built behavioral test harness (200 test cases) BEFORE writing any new code. Incremental translation with continuous validation. Maintained translation log mapping every COBOL paragraph to Python/TS equivalent.
**Key human element:** Earned trust by being transparent about what he didn't know, showing failing tests, giving credit.
**Result:** 100% feature parity. First comprehensive documentation the system ever had. Approach became template for subsequent migrations.

### Story 7: The Platform Adoption — Influence Without Authority
**Best for:** Driving adoption, influencing without authority, buy-in from skeptics, building for developers
**Hook:** "I had to get 10+ teams to adopt a shared pipeline library without mandating it."
**Summary:** At The Knot, each team had their own CI/CD pipeline — ~40% duplicated code. Three categories of pushback: "we're special," "it already works," and unspoken "don't replace my work." Approach: spent first 2 weeks listening (not coding), asked each team 3 questions. Extracted best implementations from across teams rather than designing from scratch. Made adoption opt-in and incremental. Picked 2 influential teams for initial rollout, let them evangelize. Handled holdouts by giving them review authority (turned a blocker into a contributor).
**Result:** 8/10 teams adopted within 2 quarters. ~40% duplicate pipeline code eliminated. Cross-cutting security patch: one PR instead of 10. Deployment failure rate down 30%.

### Story-to-Question Quick Reference
| Question | Primary | Backup |
|---|---|---|
| Complex technical challenge | Story 1 | Story 5 |
| Architectural trade-off | Story 2 | Story 1 |
| Failure / mistake | Story 2 | Story 1 |
| Compliance / security | Story 3 | Story 7 |
| Ambiguity / unclear requirements | Story 6 | Story 5 |
| Influence without authority | Story 7 | Story 3 |
| Improving reliability / SRE | Story 5 | Story 2 |
| Cross-functional collaboration | Story 6 | Story 7 |
| Cost optimization | Story 2 | Story 5 |
| Proactive engineering | Story 5 | Story 3 |

---

## Active Interview Pipeline

### 1. Betterment — Sr. Site Reliability Engineer (Developer Experience)
**Status:** Round 2 scheduled for March 10, 2026
**Comp:** $175K–$205K + equity + bonus (NYC, hybrid 4 days/week)
**Resume used:** Platform Engineer Resume

**Round 1 (Completed March 5):** 30-min HM intro with Andrew Allred (Director of Developer Platform & Security). Went well. Used Story 7 (CI/CD adoption). Andrew clarified the role is more DevX/platform than traditional SRE. He was warm, said "very thoughtful," mentioned they'll be in touch.

**Key intel from Andrew:**
- Build times went from ~1 hour + 25% flake rate → under 10 min + 90%+ success
- Hot fixes deploy in ~15 minutes
- SRE has two squads: Infrastructure Enablement (AWS, IaC, multi-region) and Developer Experience (CI/CD, tooling)
- Shared on-call rotation every 8-9 weeks
- AI adoption is ground-up, leadership encouraging, team has role in standardizing
- They have an IDP but trying not to over-build it
- Monorepo architecture
- Stack: AWS, CircleCI, Datadog, Ruby on Rails, Go

**Round 2 (March 10):**
- 12:30–1:30 PM: Systems Design with Ecco L'nu, Chikara Takahashi (LucidChart, no code)
- 1:30–2:30 PM: Pairing Exercise with Peter Marks, Kevin Lu (CoderPad, Python chosen)
- 2:30–2:45 PM: Recruiter Wrap-Up with Ollie Lutton
- AI tools allowed with guardrails (share screen, specific components only, can't paste requirements)

**Final Round (if advanced):** 2.5 hours, meet the team

**Honest assessment of fit:**
- Strong alignment: AWS, K8s, CI/CD, Go, observability, developer empathy
- Gap: Not deep SRE — more platform engineering adjacent. No formal on-call/SLO culture experience. No direct Datadog or CircleCI experience (but used analogous tools). No Ruby on Rails.
- Framing: "My background is more platform engineering than deep SRE. The DX mandate is a natural extension of what I've done, and it lets me deepen the reliability discipline."

**Interview analysis (Round 1):**
- Story 7 delivery was strong
- T-shaped engineer comment about AI resonated
- AI workflows answer was defensible (compliance framing was contextually smart for fintech)
- Build vs. buy question didn't have a real example — Andrew let it go gracefully
- Time ran out before all questions could be asked

---

### 2. OpenLane — AI-First Software Engineer
**Status:** Coding challenge assigned, due March 11, 2026
**Resume used:** Master Resume

**Round 1 (Completed March 5):** Technical Q&A with Nicholas (Engineering Manager, Uruguay). ~45 min. B+ interview.

**What the role is:**
- Small team (3-4 engineers) being built from scratch
- Experiment with AI tools in software development while building features
- US marketplace is a Ruby on Rails + React application
- Big 2026 project: integration with a new enterprise stack (microservices migration)
- Currently trunk-based development, deploy daily to production, manual + automated QA
- Teams distributed: Uruguay, Brazil, US, Philippines, India

**Why they reached out:** Bilingual abilities + AI experience (Scale AI)

**Interview highlights:**
- AI workflow answer was strongest moment (spec-driven development, TDD with AI, multiple models/agents)
- COBOL story (Story 6) landed well for cross-functional collaboration
- Scale AI mention (o3 evaluation, hallucination flagging) added credibility
- Nicholas gave positive feedback: appreciated IBM work, stakeholder management, COBOL project
- Staging environment complexity observation showed technical depth

**Areas that were weaker:**
- Bad AI output example got muddled (pivoted to consultants lowering code coverage — tangential)

**Coding challenge details:**
- Ruby on Rails project (Stephan has NO Rails experience)
- Private GitHub repo, username SEDMO
- Due: close of business Wednesday, March 11
- Debrief session will test Rails knowledge
- Additional features/requirements are a bonus
- This is essentially a test of using AI tools to be productive in unfamiliar codebase

**Next step after challenge:** Technical interview discussing the code, focused on AI tools used, challenges found, way of work (not Rails-specific questions)

---

### 3. Tribeca Startup (Stealth) — Staff Backend Engineer
**Status:** Recruiter call with Merritt Sparks (SDL Staffing) — scheduling
**Comp:** $450K+ (structure TBD — likely base + equity + bonus)
**Location:** In-person, Tribeca, NYC

**What we know:**
- Backed by founder of EasyGo/Kick/Stake (Ed Craven)
- High-scale content creator platform, live streaming, payments, real-time systems
- No NSFW, focused on top-tier creators
- Building from scratch, 5+ years runway
- Originally posted as Senior, upgraded to Staff/Principal
- Stack: TypeScript, Node.js (NestJS), React/Next.js, Kubernetes (EKS), Kafka/MSK, PostgreSQL/Aurora, Redis, ClickHouse

**Stack alignment:**
- Strong: Kubernetes (5+ clusters), event-driven architecture, persistent services over serverless, PostgreSQL/Aurora, Node.js/TypeScript
- Gaps: NestJS (new but learnable), React/Next.js (not primary strength), Kafka (used Kinesis — similar patterns), consumer-scale traffic (enterprise/operational experience, not social media scale)

**Key questions to clarify:** How much frontend is expected? Is this truly Staff Backend or fullstack? Company name? Equity structure? Team size?

---

### 4. Clear — Onsite Interview
**Status:** Discussed in a separate Cowork task. Details not in this conversation.

### 5. CrowdStrike — Early Stage
**Status:** Very early. Wants to speed up.

### 6. Coinbase — Early Stage
**Status:** Very early. Wants to speed up.

---

## General Notes

- Stephan wants to ramp up on **system design** interview prep broadly
- He has a strong story bank that's well-organized with hooks, probing Q&A, and transition tips between stories
- His honest self-assessment: stronger on platform/backend than pure SRE, stronger on infrastructure than frontend, not afraid to be transparent about gaps
- He learns from interview feedback and pushes back thoughtfully on analysis (e.g., the AI workflows answer at Betterment was more defensible than initially assessed given compliance context)
- Significant time pressure: Betterment Round 2 and OpenLane challenge both land on March 10-11

---

## Prep Documents Created

1. **Interview_Prep_March_5.docx** — OpenLane + Betterment Round 1 combined prep
2. **Betterment_Round_2_Prep.docx** — Systems Design framework, pairing playbook, Python refreshers
3. **Tribeca_Startup_Recruiter_Prep.docx** — Stack alignment, questions for recruiter, decision framework
