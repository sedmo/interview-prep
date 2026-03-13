# Interview Story Bank

**Deep Dives, Alternatives & Quick-Reference Guide**

Stephan Edmonson | Senior Software & Platform Engineer

Last Updated: March 11, 2026 | 5 Stories

---

## Story 1: The Real-Time Pipeline — Go, Observability & Database Mastery

### Best Prompts

- Tell me about a complex technical challenge.
- Describe a time you improved system performance.
- Tell me about a time you had to rewrite or refactor a system.
- How do you approach reliability?
- Tell me about improving a system you didn't build.
- How do you know something is wrong before users complain?
- Tell me about your experience with database optimization.
- Have you worked with connection pooling or database scaling?

**THE HOOK:** *"I inherited a pipeline that was too slow, my first fix crashed the database because it was too fast, and then I discovered the monitoring was blind to all of it."*

### Situation / Context

At the entertainment client (Disney), I was responsible for a real-time staffing and scheduling pipeline. It ingested high-volume events via Kinesis, processed them through Lambda and ECS services, and wrote to an RDS Aurora PostgreSQL cluster that powered operational dashboards used every morning. The system had three interconnected problems that I tackled in sequence — each fix revealed the next layer.

### Phase 1: The Concurrency Optimization

The existing Go data migration service ran sequentially — hitting an API or database row-by-row. During peak periods it took 10+ hours, leaving dashboards stale by morning. I ran Go's built-in profiler (pprof) and confirmed that 85–90% of wall-clock time was spent waiting on network round-trips. CPU was sitting at about 5%. The service was essentially a very expensive sleep loop.

**The First Attempt (The Failure):** My instinct was to throw goroutines at it. I spun up an unbounded number of workers and let them rip. It was incredibly fast — for about 90 seconds. Then our RDS instance hit max_connections, latency spiked, and the whole thing threw deadlocks. I'd built an accidental load tester.

**The Real Fix:** I implemented a bounded worker pool — 50 concurrent workers pulling from a shared channel with a backpressure mechanism. Each worker measured round-trip latency on every DB call. If P95 crossed 200ms (baseline healthy was ~40ms), the worker would sleep for a back-off interval before pulling the next job. This created a self-regulating system: full throughput when the DB was healthy, automatic easing when it struggled. I also added a dead-letter queue channel for failed rows (retry 3x, then log for manual review), a --dry-run flag for validation, and staged rollout against a replica first.

**Result:** Execution time went from 10+ hours to under 3 hours — roughly 70% reduction. Zero data corruption across dozens of runs.

### Phase 2: The Observability Gap

After stabilizing the pipeline, a business stakeholder flagged that dashboard numbers didn't match ground truth. I checked everything — no errors, no failures, green across the board. But when I dug into Kinesis metrics, IteratorAge was sitting at about 2,700 seconds — 45 minutes. The pipeline wasn't broken; it was drowning.

**Why Existing Monitoring Missed It:** Our alerts were built around failure — error rates, dead-letter queue depth, process crashes. But this wasn't a failure. Every record was processed successfully, just late. We were monitoring lagging indicators (did something break?) when we needed leading indicators (is something falling behind?). It's the difference between checking if your car engine seized versus watching the temperature gauge climb.

**The Diagnosis:** I pulled IteratorAge over two weeks and found a pattern. During morning scheduling rushes (7–9 AM), event volume spiked 5–8x and the consumer fell behind. It would catch up by afternoon, but the morning dashboards — the most critical ones — were always stale.

**The Fix:** Three parts. First, I built a Splunk dashboard correlating Kinesis IteratorAge, IncomingRecords, and consumer processing duration — metrics that existed in isolation but nobody had connected. Second, I implemented tiered alerting: 60 seconds = warning log, 5 minutes = Slack alert, 15 minutes = page (set below the 10-minute business staleness threshold). Third, I refactored the consumer from single-record processing to batch writes (batches of 100), cutting per-batch time roughly in half, and tuned shard count and consumer parallelism for peak ingestion.

**Result:** IteratorAge during peak dropped from 45 minutes to under 30 seconds. Zero stale-dashboard incidents for the next quarter.

### Phase 3: The Database Rescue

With the pipeline running fast and properly monitored, the next bottleneck emerged: Aurora itself. Query response times that had been 20–40ms started creeping to 500ms+ during peak. Investigation revealed DatabaseConnections spiking near max_connections (~1,000 on our db.r5.large), driven by Lambda's scaling model — each invocation held its own connection, and during spikes we'd hit ~1,000 concurrent executions.

**The Cascading Effect:** Connections saturated → new queries queued → latency increased → Lambda functions ran longer → more concurrent executions → more connections. A feedback loop.

**The Diagnosis:** I ran pg_stat_activity queries and found hundreds of idle connections from Lambda's execution context reuse. Then pg_stat_statements revealed two queries doing sequential scans on a table that had grown from 100K to 2M rows — missing indexes.

**The Fix (Three Parts):**

1. **PgBouncer for connection pooling** — deployed as an ECS Fargate sidecar in transaction-mode (connections returned after each transaction, not session close). Pool size set to 200, well below Aurora's max, leaving headroom for monitoring and admin. We chose PgBouncer over RDS Proxy for better control over pool sizing, timeout behavior, and lower tail latency in transaction mode.

2. **Index optimization** — a covering index on (location_id, schedule_date) eliminated a sequential scan on 2M rows, dropping query time from ~800ms to ~15ms. A partial index on (status = 'active') covered headcount aggregation efficiently since 70% of rows were historical. Both validated with EXPLAIN ANALYZE in staging — the covering index turned a Seq Scan into an Index Only Scan.

3. **Workload separation** — moved dashboard reads to an Aurora Read Replica. Write path hit the primary; read path hit the replica. Dashboard availability was no longer coupled to write-path load.

**Safeguards:** CloudWatch alarms on DatabaseConnections (70% = warning, 85% = page), Grafana dashboard showing active/idle/waiting connections by source, slow query logging via pg_stat_statements at 100ms threshold with weekly top-10 reports.

**Result:** Peak connections dropped from ~1,000 to ~200. Dashboard query latency from 500ms+ to under 20ms. Zero connection exhaustion incidents after the fix.

### Probing Questions

**Why restructure instead of scaling horizontally?** Adding more instances would have multiplied the database connection problem. The bottleneck was I/O wait within a single process, not CPU — so the fix was concurrency within the existing service, not more copies of the same sequential code.

**How did you decide on shard count?** Each Kinesis shard supports 1MB/sec or 1,000 records/sec. I measured peak ingestion, added 30% buffer, and divided. Went from 2 to 5 shards.

**Why not SQS instead of Kinesis?** We needed ordered processing within partition keys — schedule changes for the same location had to be processed in order. Kinesis partition keys gave us that; standard SQS doesn't guarantee ordering.

**What about read replica lag?** Aurora replica lag is typically under 20ms. We alarmed at 1 second. In practice it stayed under 50ms — well within tolerance for data already processed through a streaming pipeline.

---

## Story 2: The Architecture Pivot — Serverless vs. Containers

### Best Prompts

- Tell me about an architectural trade-off or pivot.
- Describe a production incident and how you resolved it.
- Tell me about a time you failed.
- How do you approach cost optimization?

**THE HOOK:** *"I learned that Serverless isn't always 'No Ops' — sometimes it's 'Database Killer.'"*

### Situation / Context

When I joined the Disney engagement, we were building a new event-driven pipeline to process real-time staffing data. Without historical traffic patterns, Lambda was the natural starting point — pay per invocation, no capacity planning, fast to ship.

### The Incident

It worked perfectly in dev and staging. Then we hit a production spike — a mass schedule change that triggered thousands of recalculations. Lambda scaled exactly as designed: horizontally, fast, without permission. Within minutes we had ~1,000 concurrent executions, each opening its own database connection. RDS Aurora's max_connections limit based on instance memory — we blew past it. Queries queued, timeouts cascaded, dashboards went dark.

### The Diagnosis

The root cause wasn't Lambda — it was the interaction between Lambda's scaling model and a stateful resource. Lambda scales connections linearly with concurrency. ECS with a fixed task count gives you a predictable connection ceiling.

### The Fix

I didn't move everything off Lambda. I categorized workloads into two tiers:

- **Hot path (high-frequency, DB-heavy):** Moved to ECS Fargate with a fixed task count and connection pooling.
- **Cold path (infrequent, event-processing):** Stayed on Lambda because it didn't touch the database directly.

**Safeguards:** CloudWatch alarms on DatabaseConnections as percentage of max (70% alert, 85% page). Reserved Concurrency limits on remaining Lambda functions. ADR documenting the whole decision.

### Cost Analysis (If Asked)

I built a spreadsheet comparing Lambda cost (invocations × duration × memory) vs. Fargate cost (vCPU-hours + memory-hours) at different traffic levels. Below ~50 req/sec, Lambda wins. Above that, Fargate is cheaper because you're not paying per-invocation overhead. Our hot path was consistently above that threshold.

### As a Failure Story

**The Fail:** "I over-indexed on developer velocity by choosing Lambda and underestimated the impact on the database connection limit."

**The Redemption:** "I learned that every architectural choice has a blast radius. Now I always draw a table: 'What happens at 10x traffic?' For Lambda, 10x traffic meant 10x DB connections. For ECS, 10x traffic meant queuing at the load balancer. That five-minute exercise would have saved us the incident."

**What I Apply Now:** Before picking a compute service, I model the interaction between compute scaling and every stateful downstream resource. I also build capacity-aware alerting from day one — alarms on connection count as a percentage of max, not just error rates.

### Result

Production stabilized immediately. Monthly compute costs dropped about 15% because ECS tasks ran more efficiently than thousands of short-lived Lambdas for that workload. We never hit a connection limit again.

---

## Story 3: The COBOL Modernization — Ambiguity & Collaboration

### Best Prompts

- Tell me about working with ambiguity.
- How do you handle unclear requirements?
- Describe a time you had to learn something outside your expertise.
- Tell me about a cross-functional collaboration.
- How do you ensure quality when migrating systems?

**THE HOOK:** *"I was asked to migrate business logic from a 30-year-old mainframe to AWS Lambda. The catch was that nobody — not even the people who maintained it — could fully explain what the code did."*

### Situation / Context

On the federal government engagement, I was part of a modernization effort moving legacy mainframe workloads to cloud-native serverless architecture. These were decades-old COBOL programs handling critical government processes. The mandate was 100% feature parity — no simplifying, no skipping edge cases, no reimagining the workflow.

### The Problem

I asked for the requirements document. There wasn't one — or rather, there was one from 1994 that bore no resemblance to the current code. Over 30 years, the system had been patched by dozens of people and documentation never kept up. The COBOL source was the only truth — tens of thousands of lines with variable names like WS-TEMP-AMT-3 and no comments.

### The Approach

**Step 1: Build Relationships with the SMEs.** Two COBOL developers had maintained this system for 15+ years. I sat with them — sometimes screen-sharing for hours — walking through code paragraph by paragraph. I'd ask "What business scenario triggers this branch?" and "What happens if this field is null?" They'd say "That never happens," and I'd press: "But what does the code do if it does?" Those edge cases turned out to be critical.

**Step 2: Write Behavioral Tests Against the Existing System.** Before writing a single line of Python or TypeScript, I built a test harness. I worked with the SMEs to capture representative input/output pairs from the live mainframe. This gave me a behavioral contract: for input X, the correct output is Y. I ended up with about 200 test cases. Some exposed behaviors even the SMEs didn't know about — "Oh, I didn't realize it rounded that way for negative amounts. That must have been added in 2008."

**Step 3: Incremental Translation with Continuous Validation.** I broke the program into logical units — roughly corresponding to COBOL paragraphs — and translated one at a time, running the full test suite after each piece. When a test failed, I went back to the SMEs with a specific question, not a vague "how does this work?"

**Step 4: The Translation Log.** I maintained a running document mapping every COBOL paragraph to its Python/TypeScript equivalent with decision notes. This became the living spec that never existed before.

### The Human Challenge

The trickiest part wasn't technical — it was trust. The COBOL developers were understandably protective. I earned their trust by being transparent about what I didn't know, by showing them failing tests (not just passing ones), and by giving them credit when their knowledge caught bugs I would have shipped. By the end, they were actively writing test cases with me.

### Probing Questions

**How did you handle data format differences?** COBOL uses fixed-width fields with packed decimal and signed overpunch formats. I wrote utility functions for conversion and tested them exhaustively because a rounding difference at that layer cascades through every calculation.

**What about mainframe-specific constructs?** CICS screen handling became API endpoints (screens became a frontend consuming REST APIs). JCL job chains became Step Functions orchestrations with EventBridge scheduling.

**How long did this take?** The first workflow took about 6 weeks because we were building the methodology. The second took 3 weeks. By the fourth, we had it down to about 10 days per workflow because the patterns were established.

### Result

100% feature parity — every one of the 200+ behavioral tests passed against the serverless implementation. We also produced the first comprehensive documentation this system had ever had. The approach became the template for subsequent migration waves.

---

## Story 4: The Golden Path — Platform Engineering & Developer Experience

### Best Prompts

- How do you handle compliance/security vs. speed?
- Tell me about building internal tools.
- How do you drive adoption?
- Tell me about influencing without authority.
- Describe a time you had to get buy-in from skeptical stakeholders.
- How do you make the "right thing" the "easy thing"?

**THE HOOK:** *"If you try to be a gatekeeper, developers route around you. I learned to be an enabler — twice, at two different companies, in two different ways."*

### Situation / Context

I encountered the same fundamental problem at both the federal client and The Knot Worldwide: teams were doing infrastructure and CI/CD their own way, leading to inconsistency, duplication, and risk. At the federal client, it was "Shadow IT" — S3 buckets without encryption, security groups with broad rules, no tagging. At The Knot, it was CI/CD fragmentation — each of 10+ teams had their own pipeline, ~40% of pipeline code was duplicated, and security patches had to be applied to each one individually.

### Part 1: Infrastructure Golden Path (Federal Client)

**Why the Obvious Fix Doesn't Work:** My first instinct was a policy doc. But nobody reads the doc, and you become the person who says "no." Developers route around gatekeepers.

**The Real Fix:** I built a library of opinionated CDK constructs — pre-built infrastructure components where compliance was baked in. Use our S3 construct and encryption is on, versioning is on, public access is blocked, tagging is correct. You couldn't accidentally create an insecure bucket because the secure configuration was the path of least resistance.

**The "Break Glass" Mechanism:** I knew there'd be edge cases. Any team could override a default with an explicit flag (like `allowPublicAccess: true`), but doing so automatically triggered a P1 audit alert to the security team. It didn't block them — it just made the risky choice visible and accountable.

### Part 2: CI/CD Platform (The Knot Worldwide)

When I proposed a shared CI/CD library, I got three flavors of pushback: "Our service has unique requirements," "Our pipeline works, why risk it," and the unspoken "I built this and don't want it replaced."

**Step 1: Listen Before Building.** I spent two weeks not writing code. I talked to engineers on every team asking three questions: "Walk me through your deployment. What's most annoying? What would you never want to change?"

**Step 2: Extract, Don't Impose.** Instead of a "perfect" pipeline from scratch, I took the best implementation of each common step from across teams. When I showed a team the shared library, I could say "This Docker build step is based on what Team X built." It wasn't my pipeline replacing theirs; it was the best of everyone's work, curated.

**Step 3: Opt-In and Incremental.** Teams could adopt one step at a time — replace just their Docker build with the shared one and keep everything else.

**Step 4: Win Two, Let Them Sell.** I picked two teams — one vocal about pipeline pain, one influential in the org — and helped them migrate. Nothing I could say would be as persuasive as another engineer telling peers "We switched and deleted 200 lines of pipeline YAML."

**Handling the Holdout:** One team lead was strongly opposed. Instead of pushing, I asked him to be a reviewer on the shared library. Giving him influence over the design turned him from a blocker into a contributor. He ended up adding the Helm deployment step that several other teams adopted.

### The Common Philosophy

Both projects followed the same playbook: make the right thing easier than the wrong thing, don't mandate — demonstrate value, make it incremental so adoption has low risk, and build in escape hatches so people don't feel trapped.

### Non-Negotiables

Security scanning was never optional. I framed it as company policy, not personal preference, and made the shared tooling make compliance trivially easy.

### Results

**Federal:** Encryption and tagging compliance went from ~60% to effectively 100%. Deployment time for new services dropped about 20%.

**The Knot:** 8 of 10 teams adopted core shared steps within two quarters. 40% reduction in duplicated pipeline code. When a critical vulnerability required updating the base Docker image, one PR fixed it for every adopting team. Deployment failure rate dropped 30%.

---

## Story 5: The Kubernetes Platform — Reliability & Operations at Scale

### Best Prompts

- Tell me about your experience with Kubernetes in production.
- How do you approach platform reliability?
- Describe a time you improved operational maturity.
- How do you balance speed and stability?
- Tell me about maintaining a platform that other teams depend on.

**THE HOOK:** *"I managed Kubernetes clusters that 10+ engineering teams depended on for every deployment. The hardest part wasn't the technology — it was making sure nobody noticed the platform at all."*

### Situation / Context

At The Knot Worldwide, I managed 5+ Kubernetes clusters that served as the deployment target for the entire engineering organization. Teams deployed their services through GitOps workflows — they'd merge a PR, and ArgoCD would sync the desired state to the cluster. When it worked, it was invisible. When it didn't, every team was blocked.

When I joined, the platform had growing pains. Deployment failures were inconsistent — the same manifest would succeed in one cluster and fail in another. Incident resolution was slow because there was no standard monitoring and no runbooks. Teams were losing trust in the platform.

### The Diagnosis

The inconsistency came from configuration drift between clusters. Each had been set up slightly differently over time — different Helm chart versions, different resource quotas, different admission controller configurations. ArgoCD was syncing application state, but the cluster-level configuration wasn't managed with the same rigor.

### The Fix (Three Parts)

**Part 1: GitOps for Everything.** I extended the ArgoCD GitOps model beyond applications to cover cluster configuration itself — resource quotas, network policies, admission controllers, and Helm chart versions all managed as code in Git. Every cluster pulled from the same source of truth. Drift became impossible because ArgoCD would automatically reconcile.

**Part 2: Observability Standards.** I established monitoring standards using Prometheus and Grafana. This meant standardized dashboards that every team could use: pod health, resource utilization, deployment frequency, and error rates. The key insight was making these dashboards self-service — teams could see their own services without asking the platform team for help.

I also set up alerting tiers: Prometheus alerts for infrastructure-level issues (node pressure, persistent volume claims, certificate expiration), and application-level alerts that teams owned themselves using the same Prometheus stack.

**Part 3: Runbooks and Operational Maturity.** I authored production runbooks for every common failure mode: pod crash loops, stuck deployments, node not-ready conditions, certificate rotations, cluster upgrades. Each runbook followed a consistent format: symptoms, diagnosis steps, resolution, and escalation path. These weren't write-once documents — I updated them after every incident based on what we actually did versus what the runbook said.

### The Cultural Shift

The runbooks had a secondary effect I didn't expect. Because they were clear and accessible, on-call engineers who weren't deeply familiar with Kubernetes could handle common issues confidently. This distributed operational knowledge instead of concentrating it in a few people. On-call stopped being "hope it's not a cluster issue" and became a manageable rotation.

### Probing Questions

**How did you handle cluster upgrades?** We maintained staging clusters that mirrored production. I'd upgrade staging first, let it soak for a week with real traffic patterns, then upgrade production during low-traffic windows. The GitOps model meant we could quickly identify any manifest incompatibilities with the new version.

**What about resource management?** I implemented resource quotas per namespace (one per team) with requests and limits. This prevented any single team's runaway pod from starving others. I also built a weekly utilization report that helped teams right-size their requests — most were over-provisioned because the defaults were too generous.

**How did you handle a major incident?** A node failure during peak traffic took down pods for three teams simultaneously. The runbook had us cordon the node, verify pod rescheduling, and check PodDisruptionBudgets. Because we had proper anti-affinity rules and PDBs, pods redistributed within 2 minutes. Teams saw brief latency spikes but no downtime. Post-incident, I added a Prometheus alert on node conditions to catch failing nodes before they went fully not-ready.

### Result

Deployment failures dropped 30% after standardizing cluster configuration. Incident resolution times improved 40% with runbooks. The platform maintained 99.9% availability. Most importantly, engineering teams stopped thinking about the platform at all — which is exactly what good platform engineering should achieve.

---

## Quick Reference: Story → Question Mapping

| Interview Question | Primary Story | Backup Story |
|---|---|---|
| Complex technical challenge | Story 1 (Real-Time Pipeline) | Story 3 (COBOL) |
| Improved system performance | Story 1 (Go rewrite + DB optimization) | Story 2 (ECS cost) |
| Architectural trade-off | Story 2 (Lambda vs ECS) | Story 1 (sequential vs concurrent) |
| Failure / mistake | Story 2 (Lambda incident) | Story 1 (unbounded goroutines) |
| Compliance / security | Story 4 (Golden Path CDK) | Story 5 (cluster policies) |
| Ambiguity / unclear reqs | Story 3 (COBOL Translation) | Story 1 (diagnosing lag) |
| Influence without authority | Story 4 (Platform Adoption) | Story 3 (earning COBOL devs' trust) |
| Improving reliability / SRE | Story 1 (Observability) | Story 5 (Kubernetes platform) |
| Cross-functional collaboration | Story 3 (COBOL SMEs) | Story 4 (resistant team lead) |
| Cost optimization | Story 2 (ECS vs Lambda) | Story 1 (shard right-sizing) |
| Proactive engineering | Story 1 (capacity model + tiered alerts) | Story 4 (break-glass mechanism) |
| Database optimization | Story 1 (PgBouncer + indexes) | Story 2 (connection ceiling) |
| Production incident | Story 2 (Lambda scaling) | Story 1 (Go goroutines crash) |
| Kubernetes / platform | Story 5 (K8s at Scale) | Story 4 (CI/CD platform) |
| Building for other developers | Story 4 (Golden Path) | Story 5 (self-service dashboards) |
| Operational maturity | Story 5 (runbooks + monitoring) | Story 1 (weekly query reviews) |

## Transition Tips

Interviewers often ask follow-ups that steer you between stories. Having natural bridges makes you sound like someone with a coherent engineering philosophy.

- **Story 1 → Story 2:** "You mentioned the concurrency optimization hitting connection limits. Did you deal with that at a broader architectural level?" → leads into the Lambda vs ECS pivot.
- **Story 2 → Story 1:** "After the ECS migration, what else did you optimize?" → leads into the observability and database work.
- **Story 1 → Story 5:** "You built observability for the pipeline. How did you think about observability at the platform level?" → leads into Prometheus/Grafana at The Knot.
- **Story 3 → Story 4:** "You mentioned building trust with the COBOL devs. How do you drive adoption with skeptical engineers more broadly?" → leads into the platform adoption story.
- **Story 4 → Story 5:** "You built shared CI/CD at The Knot. What was the deployment target?" → leads into the Kubernetes platform story.
- **Story 5 → Story 4:** "You mentioned teams deploying through GitOps. How did you get them to adopt that workflow?" → loops back to the adoption strategy.
