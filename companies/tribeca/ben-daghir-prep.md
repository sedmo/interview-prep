__INTERVIEW PREP GUIDE__

Tribeca Startup \(Stealth\) — Staff Backend Engineer

Interview with Ben Daghir \(Founding Backend Engineer\) — 30 Minutes

Recommended Date: Thursday March 19 or Friday March 20, 2026

# __Intel from Merritt \(Your Cheat Sheet\)__

Merritt gave you incredibly specific guidance\. Treat this as your roadmap for the conversation\.

## __What Got You the Interview__

*"He liked your profile specifically because of your platform level thinking and the breadth you bring across the stack\. That’s what separated you from other candidates so make sure you lead with that\."*

Translation: Don’t go deep on one technology\. Show Ben you can own the WHOLE backend platform — from data layer to deployment to observability\. Breadth \+ ownership is the signal\.

## __Exactly What to Highlight__

*"Your Kafka work, Kubernetes cluster management, the monolith decomposition at IBM, the real time work with Go and Kinesis and the GreenOps controller are all going to resonate with him\."*

__Topic Merritt Flagged__

__Your Story / Experience__

__Prep Status__

Kafka work

Kinesis at Disney \(similar patterns: partitioning, consumers, ordering\)

⚠️ Review Kafka vs Kinesis

K8s cluster management

5\+ clusters at The Knot, EKS at IBM

✅ Strong — refresh details

Monolith decomposition at IBM

COBOL migration / service extraction

⚠️ Clarify which project

Real\-time Go \+ Kinesis

Disney data pipeline — Story 1

✅ Strong

GreenOps controller

On resume but NOT completed

❌ MUST PREP — see below

Event\-driven architecture

ECS vs Lambda \(Story 2\), Kinesis pipeline

✅ Strong

Observability \(Prometheus/Grafana\)

Story 5 \+ observability work across roles

⚠️ Refresh specifics

## __Key Instruction from Merritt__

*"Connect those dots for him, don’t assume he’ll make that connection on his own\."*

Ben is quiet and technical\. He won’t ask leading questions to help you connect your experience to their needs\. YOU have to do that work\. For every story, explicitly say: “This maps to what you’re building because\.\.\.”

# __⚠️ GreenOps Controller — Critical Prep Item__

__WARNING: This is on your resume but you never completed it\. If Ben asks about it, you MUST have a coherent answer\. Do NOT get caught off guard\.__

## __What to Say__

Be honest but frame it as in\-progress design work, not a shipped product\. Here’s how to talk about it:

*"The GreenOps controller was a Kubernetes controller I was designing to automate resource right\-sizing based on actual utilization metrics\. The idea was to watch pod resource usage via Prometheus, compare it against requests/limits, and automatically generate right\-sizing recommendations — or in auto mode, patch the deployments directly\. I built out the initial design and some of the core logic, but the project got deprioritized when my engagement shifted\. It’s something I plan to finish as a side project because the problem is real — most teams over\-provision by 40\-60% and don’t have a feedback loop\."*

## __Be Ready to Discuss__

- How a K8s controller works \(watch loop, reconciliation, CRDs\)
- How you’d pull metrics from Prometheus API to inform right\-sizing
- The difference between recommendations vs auto\-patching \(safety\)
- VPA \(Vertical Pod Autoscaler\) as prior art and how yours differs
- Why this matters for cost optimization at scale

# __The Big Question: Architect a Social Platform Backend__

*"If he asks you something like how would you architect a high traffic social platform backend from scratch just be ready with a clean answer\. Event driven architecture, data modeling, caching, reliability\."*

## __Your Framework \(5 minutes, clean and structured\)__

__1\. Start with the domain model: __Users, content \(streams, posts, clips\), follows/subscriptions, payments, notifications\. Identify the core entities and their relationships\. Call out that a creator platform has asymmetric traffic patterns — a few creators generate most of the content, millions of viewers consume it\.

__2\. Event\-driven backbone: __Use an event bus \(Kafka/MSK\) as the central nervous system\. Every meaningful action produces an event: stream started, payment processed, user followed\. Downstream services consume events asynchronously\. This decouples services and lets you scale consumers independently\.

__3\. Data modeling: __PostgreSQL/Aurora for transactional data \(users, payments, subscriptions\)\. Redis for hot data — session state, real\-time viewer counts, leaderboards, rate limiting\. ClickHouse or similar OLAP store for analytics \(watch time, engagement metrics\)\. Separate read and write paths early — CQRS pattern\.

__4\. Caching strategy: __Multi\-layer: CDN for static assets and stream thumbnails, Redis for API\-level caching \(user profiles, content metadata\), application\-level caching for computed values\. Cache invalidation via events — when a creator updates their profile, publish an event that invalidates the relevant cache keys\.

__5\. Reliability: __Circuit breakers between services\. Graceful degradation — if the recommendation service is down, show chronological content instead\. Health checks and readiness probes in K8s\. Rate limiting at the API gateway\. Retry with exponential backoff for async operations\. Dead letter queues for failed event processing\.

__6\. Observability from day one: __Structured logging, distributed tracing \(OpenTelemetry\), metrics \(Prometheus \+ Grafana\)\. SLOs on critical paths: stream start latency < 2s, payment processing < 5s, API p99 < 200ms\. Alert on burn rate, not just thresholds\.

Connect it back: “This is basically the architecture I’d bring to what you’re building\. Event\-driven core, K8s for orchestration, strong observability, and clear ownership boundaries between services\.”

# __Your 60\-Second Pitch__

*“I’m a senior platform and backend engineer with about 7 years across the full infrastructure stack\. What I think sets me apart is that I don’t just write services — I own the platform they run on\. At The Knot, I managed 5\+ K8s clusters and built the shared CI/CD infrastructure that 10 teams adopted\. At IBM, I built real\-time data pipelines in Go with Kinesis, did monolith decomposition work, and led cloud migrations for enterprise clients\. I’ve also done AI\-adjacent work at Scale AI evaluating frontier models\. The through\-line is platform\-level ownership: from the event\-driven architecture down to the deployment pipeline and observability stack\. That’s exactly what a founding backend engineer role needs — someone who can see and own the whole picture\.”*

# __Action Items Before the Interview__

__Complete these before your scheduled interview with Ben\.__

1. __GreenOps Controller: __Write a 1\-page design doc covering: what it does, how the K8s controller pattern works, what metrics it reads from Prometheus, and how it generates recommendations\. Even if the code isn’t done, having a clear design narrative makes it credible\. Consider actually building a minimal version \(watch loop \+ Prometheus query\) to have something real to show\. Estimated time: 2–3 hours\.
2. __Kafka deep dive: __Review Kafka core concepts: topics, partitions, consumer groups, offsets, exactly\-once semantics\. Compare to Kinesis \(which you know\)\. Be able to discuss: when to use Kafka vs SQS vs Kinesis, partition key design, consumer lag monitoring\. Merritt specifically called out Kafka\. Estimated time: 1–2 hours\.
3. __Monolith decomposition story: __Clarify which IBM project this refers to on your resume\. If it’s the COBOL migration \(Story 6\), prepare a version that emphasizes the architectural decomposition angle: how you broke a monolithic COBOL system into discrete services, what the migration strategy was, how you maintained parity\. If it’s a different project, refresh the details\. Estimated time: 30 min\.
4. __Prometheus/Grafana refresh: __Review: PromQL basics \(rate, histogram\_quantile, aggregation\), Grafana dashboard design, alerting rules, SLO\-based alerting \(burn rate\)\. Be ready to describe a dashboard you’ve built and what metrics you tracked\. Estimated time: 1 hour\.
5. __K8s cluster management stories: __Refresh The Knot details: how many clusters, what workloads ran on them, how you handled upgrades, node scaling, networking \(Istio?\), and cost management\. Be specific with numbers\. Estimated time: 30 min\.
6. __Social platform architecture answer: __Practice delivering the architecture framework above in under 5 minutes\. Do it out loud at least twice\. Time yourself\. Make sure it flows naturally, not like a memorized script\. Estimated time: 30 min\.

# __During the Interview — 30\-Minute Game Plan__

__Minutes 0–2: __Quick intro\. Lead with your pitch — platform\-level ownership, breadth across the stack\. Don’t wait for him to ask\. Remember, he’s quiet\.

__Minutes 2–15: __He’ll likely ask about your experience or go straight to architecture\. Either way, connect every answer back to their needs: “This is directly relevant to building a creator platform because\.\.\.” Hit the topics Merritt flagged naturally\.

__Minutes 15–25: __If he asks the architecture question, deliver your framework cleanly\. If not, and the conversation is going deep on your experience, that’s fine too — let him lead\.

__Minutes 25–30: __Your questions for him\. Keep them technical and genuine:

- “What’s the current state of the backend? How many services, what’s the data layer look like?”
- “What’s the hardest infrastructure problem you’ve hit so far?”
- “How are you thinking about real\-time features — WebSockets, SSE, or something else?”
- “What does the team look like today and what’s the hiring plan?”

# __Things to Avoid__

- __Don’t undersell yourself: __This is a Staff role at $450K\. Own your experience confidently\. You’re not interviewing up — you’re interviewing as a peer\.
- __Don’t go frontend: __If he asks about React/Next\.js, be honest: “My depth is backend and infrastructure\. I can work across the stack when needed, but my highest value is owning the platform layer\.”
- __Don’t get caught on GreenOps: __If you haven’t prepped it, don’t bring it up\. But if HE brings it up, you need a clean answer\. See above\.
- __Don’t be passive: __Ben is quiet\. If you wait for him to drive, there will be awkward silences\. Come in with energy and lead the conversation\.

