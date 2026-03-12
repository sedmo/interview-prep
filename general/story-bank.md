__INTERVIEW STORY BANK__

Deep Dives, Alternatives & Quick\-Reference Guide

Stephan Edmonson | Senior Software & Platform Engineer

Last Updated: March 9, 2026 | 8 Stories

# __Story 1: The Go Refactor — Performance & Safety__

## __Best Prompts__

- Tell me about a complex technical challenge\.
- Describe a time you improved system performance\.
- Tell me about a time you had to rewrite or refactor a system\.

__THE HOOK: __*“I had to fix a system that was too slow, but my first attempt actually crashed the database because it was too fast\.”*

## __Situation / Context__

At the entertainment client \(Disney\), I inherited a data migration pipeline that synced staffing and scheduling data between systems\. The existing Python script ran sequentially, hitting an API or database row\-by\-row\. During peak periods it was taking 10\+ hours, which meant the operational dashboards were stale by the time people needed them in the morning\.

## __The Discovery__

*I didn’t just guess it was I/O\-bound\. I instrumented the script with timing logs and saw that 85–90% of wall\-clock time was spent waiting on network round\-trips to the database\. CPU utilization was sitting at maybe 5%\. The script was essentially a very expensive sleep loop\.*

## __The First Attempt \(The Failure Within the Success\)__

*My first instinct was to just throw goroutines at it\. I spun up an unbounded number of workers and let them rip\. It was incredibly fast — for about 90 seconds\. Then our RDS instance hit max\_connections, latency spiked to seconds, and the whole thing started throwing deadlocks\. I had essentially built an accidental load tester\.*

## __The Real Fix__

I implemented a bounded worker pool pattern — 50 concurrent workers pulling from a shared channel\. But the key insight was the backpressure mechanism:

*Each worker measured round\-trip latency on every DB call\. If the P95 crossed 200ms — which I picked based on the baseline healthy latency being around 40ms — the worker would sleep for a back\-off interval before pulling the next job\. This created a self\-regulating system\. When the DB was healthy, we ran at full throughput\. When it started struggling, we automatically eased off\.*

## __Proactive Safeguards__

- __Dead\-letter queue: __Each failed row got pushed to a separate Go channel for retry\. After 3 retries, it got logged to a file for manual review\. Zero data loss\.
- __Dry\-run flag: __Added a \-\-dry\-run flag that did everything except the final write, so we could validate transformation logic independently\.
- __Staging validation: __Ran against a staging replica first with synthetic load before touching production\.

## __Probing Questions & Answers__

__Why Go over fixing the Python?__

*We considered asyncio, but the team was already standardizing on Go for new services\. It also gave us better memory efficiency — the Python script was loading entire result sets into memory, while the Go version streamed rows\.*

__How did you test it?__

*I ran it against a staging replica with synthetic load\. I also used the \-\-dry\-run flag to validate transformation logic independently of the write path\.*

## __Result__

Execution time went from 10\+ hours to under 3 hours — roughly 70% reduction\. More importantly, we ran it dozens of times with zero data corruption and zero database incidents\.

# __Story 2: ECS vs\. Lambda — Architecture & Cost__

## __Best Prompts__

- Tell me about an architectural trade\-off or pivot\.
- Describe a production incident and how you resolved it\.
- Tell me about a time you failed\.
- How do you approach cost optimization?

__THE HOOK: __*“I realized that Serverless isn’t always ‘No Ops’ — sometimes it’s ‘Database Killer\.’”*

## __Situation / Context__

When I joined the engagement, we were building a new event\-driven pipeline to process real\-time staffing data\. We didn’t have historical traffic patterns yet, so Lambda was the natural starting point — pay per invocation, no capacity planning needed, fast to ship\.

## __The Incident__

*It worked perfectly in dev and staging\. Then we hit our first real production spike — a mass schedule change event that triggered thousands of downstream recalculations\. Lambda scaled exactly like it’s supposed to: horizontally, fast, and without asking permission\. Within minutes we had ~1,000 concurrent executions, and every single one opened its own database connection\. RDS Aurora has a max connections limit based on instance memory, and we blew right past it\. Queries started queuing, timeouts cascaded, and the dashboard went dark\.*

## __The Diagnosis__

*The root cause wasn’t Lambda itself — it was the interaction between Lambda’s scaling model and a stateful resource \(the database\)\. Lambda scales connections linearly with concurrency\. ECS with a fixed task count gives you a predictable connection ceiling\.*

## __The Fix__

I didn’t move everything off Lambda\. I categorized workloads into two tiers:

- __Hot path \(high\-frequency, DB\-heavy\): __Moved to ECS Fargate with a fixed task count and connection pooling via PgBouncer\.
- __Cold path \(infrequent, event\-processing\): __Stayed on Lambda because it didn’t touch the database directly\.

## __Proactive Safeguards__

- __CloudWatch alarms: __Set on DatabaseConnections as a percentage of max\. At 70% it alerted\. At 85% it paged\.
- __Reserved Concurrency: __Set limits on remaining Lambda functions so they couldn’t runaway\-scale\.
- __ADR: __Documented the whole decision so the next engineer wouldn’t re\-learn this lesson\.

## __Cost Analysis \(If Asked\)__

*I built a simple spreadsheet model comparing Lambda cost \(invocations × duration × memory\) vs\. Fargate cost \(vCPU\-hours \+ memory\-hours\) at different traffic levels\. Below ~50 req/sec, Lambda won\. Above that, Fargate was cheaper because you’re not paying per\-invocation overhead\. Our hot path was consistently above that threshold\.*

## __As a Failure Story__

__The Fail: __“I over\-indexed on Developer Velocity by choosing Lambda and underestimated the impact on the Database Connection limit\.”

__The Redemption: __“I learned that every architectural choice has a blast radius\. Now I always draw a table: ‘what happens at 10x traffic?’ For Lambda, 10x traffic meant 10x DB connections\. For ECS, 10x traffic meant queuing at the load balancer\. That five\-minute exercise would have saved us the incident\.”

## __Result__

Production stabilized immediately\. Monthly compute costs dropped about 15% because the ECS tasks ran more efficiently than thousands of short\-lived Lambdas for that workload\. We never hit a connection limit again\.

# __Story 3: The Golden Path — Compliance & Influence__

## __Best Prompts__

- How do you handle compliance/security vs\. speed?
- Tell me about building internal tools\.
- How do you make the “right thing” the “easy thing”?

__THE HOOK: __*“I found that if you try to be a ‘gatekeeper,’ developers just route around you\. You have to be an enabler\.”*

## __Situation / Context__

At the federal client \(and paralleled at The Knot\), I noticed teams were deploying infrastructure manually or with one\-off scripts\. The result was “Shadow IT” — S3 buckets without encryption, security groups with overly broad rules, no tagging standards\. It wasn’t malicious; people were just trying to ship fast and the “right way” was too slow\.

## __Why the Obvious Fix Doesn’t Work__

*My first instinct was to write a policy doc and send it to every team\. But I’d seen that movie before — nobody reads the doc, and you become the person who says ‘no’ to everything\. Developers route around gatekeepers\.*

## __The Real Fix__

I built a library of opinionated CDK constructs — pre\-built infrastructure components where compliance was baked in\. If you used our S3 construct, encryption was on by default, versioning was on, public access was blocked, and it was tagged correctly\. You couldn’t accidentally create an insecure bucket because the secure configuration was the path of least resistance\.

## __The “Break Glass” Mechanism__

*I knew there’d be legitimate edge cases\. So I built an escape hatch: any team could override a default, but doing so required an explicit flag — something like allowPublicAccess: true — and that flag automatically triggered a P1 audit alert to the security team\. It didn’t block them\. It just made the risky choice visible and accountable\.*

## __The Adoption Strategy__

*I didn’t mandate it\. I went to two friendly teams, helped them migrate, showed the time savings in their sprint retros, and let word spread\. Within a quarter, most teams had adopted it voluntarily because it was genuinely faster than doing it themselves\.*

## __Result__

We went from maybe 60% compliance on encryption and tagging to effectively 100%\. Deployment time for new services dropped about 20% because teams weren’t reinventing the wheel\. And security stopped being a bottleneck because they only had to review the shared library, not every individual deployment\.

# __Story 4: The Failure Reframe__

## __Best Prompts__

- Tell me about a time you failed\.
- Describe a mistake and what you learned from it\.

This story uses Story 2 \(ECS vs\. Lambda\) as its foundation but reframes the angle for failure\-specific questions\.

## __The Failure__

*I over\-indexed on Developer Velocity by choosing Lambda and underestimated the impact on the Database Connection limit\. The system worked beautifully in dev and staging, but I didn’t model what would happen when Lambda scaled to 1,000 concurrent executions against a database with a finite connection pool\.*

## __What I Learned__

*Every architectural choice has a blast radius\. Serverless compute scales infinitely, but the stateful resources downstream do not\. I learned to always ask: ‘What happens at 10x? What about 100x?’*

## __How I Apply It Now__

*On my next project, before picking a compute service, I literally drew a table: ‘What happens at 10x traffic?’ For Lambda, 10x traffic meant 10x DB connections\. For ECS, 10x traffic meant queuing at the load balancer\. That five\-minute exercise would have saved us the entire incident\.*

I also now build capacity\-aware alerting from day one — CloudWatch alarms on DatabaseConnections as a percentage of max, not just error rates\.

# __Story 5: The Observability Win — Proactive Engineering__

## __Best Prompts__

- How do you approach reliability?
- Tell me about improving a system you didn’t build\.
- How do you know something is wrong before users complain?
- Describe a time you prevented an outage\.

__THE HOOK: __*“We had a real\-time pipeline that passed every health check — green across the board — but was silently delivering stale data\. The dashboards looked fine until someone realized the numbers were 45 minutes old\.”*

## __Situation / Context__

At the entertainment client, I’d built a Kinesis\-based streaming pipeline that ingested high\-volume staffing and scheduling events and powered operational dashboards\. The system had basic monitoring — process\-level health checks, Lambda error rates, a CloudWatch alarm on DLQ depth\. By traditional standards, it looked healthy\.

## __The Discovery__

*One morning a business stakeholder flagged that the headcount numbers on the dashboard didn’t match what they were seeing on the ground\. I checked the pipeline — no errors, no failures, everything was running\. But when I dug into the Kinesis metrics, I found IteratorAge was sitting at around 2,700 seconds — 45 minutes\. The pipeline wasn’t broken; it was drowning\.*

## __Why Existing Monitoring Missed It__

*Our alerts were built around failure — error rates, dead\-letter queue depth, process crashes\. But this wasn’t a failure\. Every record was being processed successfully, just late\. We were monitoring lagging indicators \(did something break?\) when we needed leading indicators \(is something falling behind?\)\. It’s the difference between checking if your car engine seized versus checking if the temperature gauge is climbing\.*

## __The Diagnosis__

I pulled the IteratorAge metric over the past two weeks and saw a pattern\. During off\-peak hours, the consumer kept up fine — IteratorAge near zero\. But during the morning scheduling rush, around 7–9 AM, event volume would spike 5–8x and the consumer would fall behind\. It would spend the next few hours catching up, but by then the damage was done — the morning dashboards, the most business\-critical ones, were stale\.

## __The Fix \(Three Parts\)__

__Part 1: Instrumentation__

I built a Splunk dashboard that correlated three metrics side by side: Kinesis IteratorAge, IncomingRecords, and consumer processing duration per batch\. Before this, those metrics existed in isolation — nobody had connected them\.

__Part 2: Tiered Alerting__

- __60 seconds: __Warning log — consumer starting to fall behind \(“yellow light”\)\.
- __300 seconds \(5 min\): __Slack alert to the team\.
- __900 seconds \(15 min\): __Page\. Set below the 10\-minute business staleness threshold to give time to respond\.

__Part 3: Performance Fix__

The consumer was processing records one at a time within each batch\. I refactored it to do batch writes — accumulating records and writing them in batches of 100\. This alone cut per\-batch processing time roughly in half\. I also tuned the Kinesis shard count and consumer parallelism to match peak ingestion rate\.

## __Proactive Safeguards__

- __Capacity model: __A document stating: at X events/second, we need Y shards and Z consumer instances\. When business planned expansion, I proactively scaled before traffic arrived\.
- __Weekly operational review: __Added IteratorAge to the team’s regular review cadence as a leading health indicator\.

## __Probing Questions & Answers__

__How did you decide on shard count?__

*Each Kinesis shard supports up to 1MB/sec or 1,000 records/sec of input\. I measured our peak ingestion rate, added a 30% buffer, and divided\. We went from 2 shards to 5\.*

__Did you consider alternatives to Kinesis?__

*Briefly\. SQS would have given us automatic scaling on the consumer side, but we needed ordered processing within certain partition keys — schedule changes for the same location had to be processed in order\. Kinesis gave us that with partition keys; SQS doesn’t guarantee ordering in standard queues\.*

__How did you validate the fix?__

*I replayed a day’s worth of production events through the staging pipeline and watched IteratorAge\. During the simulated morning peak, it spiked to about 15 seconds and recovered within a minute\. Before the fix, that same volume caused 45 minutes of lag\.*

## __Result__

IteratorAge during peak hours dropped from 45 minutes to under 30 seconds\. Zero incidents related to stale dashboard data over the next quarter\. MTTR for pipeline issues dropped significantly because we understood what was happening inside the system, not just whether it was running\.

# __Story 6: The COBOL Translation — Ambiguity & Collaboration__

## __Best Prompts__

- Tell me about working with ambiguity\.
- How do you handle unclear requirements?
- Describe a time you had to learn something outside your expertise\.
- Tell me about a cross\-functional collaboration\.
- How do you ensure quality when migrating systems?

__THE HOOK: __*“I was asked to migrate business logic from a 30\-year\-old mainframe to AWS Lambda\. The catch was that nobody — not even the people who maintained it — could fully explain what the code did\.”*

## __Situation / Context__

On the federal government engagement, I was part of a modernization effort moving legacy mainframe workloads to cloud\-native serverless architecture\. These were decades\-old COBOL programs that handled critical government processes\. The mandate was 100% feature parity — no simplifying, no skipping edge cases, no reimagining the workflow\.

## __The Problem__

*The first thing I did was ask for the requirements document\. There wasn’t one — or rather, there was one from 1994 that bore almost no resemblance to the current code\. Over 30 years, the system had been patched, extended, and modified by dozens of people, and the documentation never kept up\. The COBOL source was the only source of truth, and it was tens of thousands of lines with variable names like WS\-TEMP\-AMT\-3 and no comments\.*

## __The Approach__

__Step 1: Build Relationships with the SMEs__

*There were two COBOL developers who’d been maintaining this system for 15\+ years\. I had to sit with them — sometimes literally screen\-sharing for hours — and walk through the code paragraph by paragraph\. I’d ask: ‘What business scenario triggers this branch?’ and ‘What happens if this field is null?’ They’d often say ‘That never happens,’ and I’d say ‘But what does the code do if it does?’ Those edge cases turned out to be critical\.*

__Step 2: Write Behavioral Tests Against the Existing System__

Before writing a single line of Python or TypeScript, I built a test harness\. I worked with the SMEs to identify representative input scenarios and ran them through the existing mainframe system to capture expected outputs\. This gave me a behavioral contract: for input X, the correct output is Y\.

*I ended up with about 200 test cases\. Some of them exposed behaviors even the SMEs didn’t know about — ‘Oh, I didn’t realize it rounded that way for negative amounts\. That must have been added in 2008\.’*

__Step 3: Incremental Translation with Continuous Validation__

I broke the program into logical units — roughly corresponding to COBOL paragraphs — and translated one at a time, running the test suite after each piece\. When a test failed, I went back to the SMEs with a specific question\.

__Step 4: The Translation Log__

I maintained a running document that mapped every COBOL paragraph to its Python/TypeScript equivalent, along with decision notes\. This became the living spec that never existed before\.

## __The Human Challenge__

*The trickiest part wasn’t technical — it was trust\. The COBOL developers were understandably protective of a system they’d maintained for years\. I earned their trust by being transparent about what I didn’t know, by showing them failing tests \(not just passing ones\), and by giving them credit when their knowledge caught bugs I would have shipped\. By the end, they were actively helping me write test cases\.*

## __Probing Questions & Answers__

__How did you handle data format differences?__

*COBOL uses fixed\-width fields with packed decimal and signed overpunch formats\. I wrote utility functions to convert between COBOL data representations and Python/TypeScript native types, and tested those converters exhaustively because a rounding difference at that layer would cascade through every calculation\.*

__What about mainframe\-specific constructs?__

*CICS screen handling became API endpoints \(screens became a frontend consuming REST APIs\)\. JCL job chains became Step Functions orchestrations with EventBridge scheduling\.*

__How long did this take?__

*The first workflow took about 6 weeks because we were building the methodology\. The second took 3 weeks\. By the fourth, we had it down to about 10 days per workflow because the patterns were established\.*

## __Result__

100% feature parity — every one of the 200\+ behavioral tests passed against the new serverless implementation\. We also produced the first comprehensive documentation this system had ever had\. The approach became the template for subsequent migration waves\.

# __Story 7: The Platform Adoption — Influence Without Authority__

## __Best Prompts__

- How do you drive adoption?
- Tell me about influencing without authority\.
- Describe a time you had to get buy\-in from skeptical stakeholders\.
- How do you handle disagreements with other engineers?
- Tell me about building something for other developers\.

__THE HOOK: __*“I was asked to build a shared CI/CD platform for 10\+ engineering teams\. The problem wasn’t building it — the problem was that every team had their own pipeline, their own opinions, and zero incentive to change\.”*

## __Situation / Context__

At The Knot Worldwide, the engineering organization had grown organically\. Each team had built their own CI/CD pipeline\. Roughly 40% of pipeline code was duplicated across teams\. When a security patch needed to go into the build process, someone had to touch 10\+ separate pipeline definitions\. Things got missed\. Deployments broke in inconsistent ways\.

## __The Initial Resistance__

When I proposed a shared CI/CD library, I got three categories of pushback:

- __The “we’re special” objection: __“Our service has unique deployment requirements, a shared pipeline can’t handle that\.”
- __The “stability” objection: __“Our pipeline works fine, why would I risk breaking it by adopting something new?”
- __The unspoken objection: __“I built this pipeline and I don’t want someone else’s code replacing mine\.”

## __The Approach__

__Step 1: Listen Before Building__

*I spent the first two weeks not writing code\. I talked to at least one engineer on every team and asked three questions: ‘Walk me through your deployment process\. What’s the most annoying part? What would you never want to change?’*

__Step 2: Extract, Don’t Impose__

Instead of designing a “perfect” pipeline from scratch, I took the best implementation of each common step from across the teams\.

*When I showed a team the shared library, I could say ‘This Docker build step is actually based on what Team X built — you might recognize the caching strategy\.’ It wasn’t my pipeline replacing theirs; it was the best of everyone’s work, curated\.*

__Step 3: Make It Opt\-In and Incremental__

Teams could adopt one step at a time — replace just their Docker build step with the shared one and keep everything else\. This lowered both risk and effort\.

__Step 4: Win Two, Let Them Sell for You__

*I picked two teams for initial adoption — one vocal about pipeline pain, one influential within the org\. Nothing I could have said would have been as persuasive as another engineer saying ‘Yeah, we switched and it just works, plus we deleted 200 lines of pipeline YAML\.’*

## __The Compromises__

A few teams had genuinely unique needs\. I didn’t force them into the shared model\. I made sure the shared library was modular enough that they could use applicable pieces and keep their custom deployment logic\. Purity wasn’t the goal; reducing overall maintenance burden was\.

## __Proactive Safeguards__

- __Semantic versioning: __Teams pinned to a major version\. Bug fixes shipped without breaking anyone\.
- __Integration tests: __The shared library had its own CI/CD pipeline testing against a sample application on every PR\.
- __Adoption dashboard: __Simple dashboard showing which teams used which version — enabled proactive outreach for security updates\.

## __Probing Questions & Answers__

__How did you handle a team that flat\-out refused?__

*One team lead was strongly opposed\. Instead of pushing, I asked him to be a reviewer on the shared library\. Giving him influence over the design turned him from a blocker into a contributor\. He ended up adding the Helm deployment step that several other teams adopted\.*

__What was non\-negotiable?__

*Security scanning\. I was flexible on almost everything, but vulnerability scanning in the build pipeline was a requirement, not an option\. I framed it as ‘this is a company policy, not my preference’ and made sure the shared step made it trivially easy to comply\.*

## __Result__

Within two quarters, 8 of 10 teams had adopted at least the core shared steps\. We eliminated about 40% of duplicated pipeline code\. When a critical vulnerability required updating our base Docker image, I made one PR and every adopting team got the fix on their next build\. Deployment failure rate dropped 30%\.

# __Story 8: The Database Rescue — Connection Management & Query Performance__

## __Best Prompts__

- Tell me about your experience with database optimization\.
- Describe a time you diagnosed and fixed a database performance issue\.
- Have you worked with connection pooling or database scaling?
- Tell me about a production incident involving a database\.
- How do you approach performance tuning?

__THE HOOK: __*“We had a system where the database was the victim, not the villain\. Every component looked healthy in isolation, but the way they interacted with the database turned a perfectly good Aurora instance into a bottleneck that brought down real\-time dashboards\.”*

## __Situation / Context__

At the entertainment client, we were running a real\-time staffing and scheduling pipeline on AWS\. The pipeline ingested events via Kinesis, processed them through Lambda functions and ECS services, and wrote results to an RDS Aurora PostgreSQL cluster\. The dashboards that operational teams relied on every morning were powered by reads from this same Aurora instance\.

The system had been built quickly — Lambda was chosen for the processing tier because it was fast to ship\. It worked fine in dev, staging, and even the first few weeks of production\.

## __The Discovery__

*The first sign wasn’t an error — it was latency\. Query response times that had been stable at 20–40ms started creeping up to 200ms, then 500ms, then timeouts\. But only during peak hours\. Off\-peak, everything was fine\.*

I pulled Aurora performance metrics from CloudWatch and found the smoking gun: DatabaseConnections was spiking to near the max\_connections limit during the morning scheduling rush\. Each Lambda invocation opened its own connection\. During a mass schedule change, ~1,000 concurrent Lambda executions each held a connection\. Aurora’s max\_connections on our db\.r5\.large was about 1,000\. We were hitting it\.

The cascading effect: connections saturated → new queries queued → latency increased → Lambda functions ran longer → more concurrent executions → more connections\. A feedback loop\.

## __The Diagnosis \(What I Actually Investigated\)__

__Step 1: __Pulled CloudWatch metrics for DatabaseConnections, ReadLatency, WriteLatency, and CPUUtilization over two weeks\. Correlated the connection spike with morning traffic\.

__Step 2: __Ran pg\_stat\_activity queries against Aurora\. Found hundreds of idle connections from Lambda functions that had finished processing but whose connections hadn’t been released \(Lambda’s execution context reuse keeps TCP connections alive\)\.

__Step 3: __Checked query execution plans for the heaviest queries\. Found two queries doing sequential scans on a table that had grown from 100K to 2M rows over six months\. Missing indexes\.

## __The Fix \(Three Parts\)__

__Part 1: Connection Pooling with PgBouncer__

Introduced PgBouncer between the application tier and Aurora\. Instead of each Lambda/ECS task opening a direct connection, they connected to PgBouncer, which maintained a fixed pool and multiplexed application connections\.

- __Transaction\-mode pooling __\(connections returned after each transaction, not session close\) — critical because Lambda’s execution context reuse would still hoard connections in session mode\.
- __Pool size set to 200 __\(well below Aurora’s ~1,000 max\), leaving headroom for monitoring, migrations, and admin access\.
- __Deployed as a sidecar on ECS Fargate tasks, __not standalone, to avoid a single point of failure\.

__Part 2: Index Optimization__

- A covering index on \(location\_id, schedule\_date\) for the staffing lookup query — eliminated a sequential scan on 2M rows\. Query time from ~800ms to ~15ms\.
- A partial index on \(status = ‘active’\) for headcount aggregation — since 70% of rows were historical/inactive, this index was a fraction of the full table size\.

Validated both in staging with EXPLAIN ANALYZE before and after\. The covering index turned a Seq Scan into an Index Only Scan — Aurora didn’t even need to touch the heap\.

__Part 3: Workload Separation__

Moved dashboard read queries to an Aurora Read Replica\. Write path \(event processing\) hit the primary; read path \(dashboards\) hit the replica\. Separated connection pressure and meant dashboard availability was no longer coupled to write\-path load\.

## __Proactive Safeguards__

- __CloudWatch alarms: __DatabaseConnections as percentage of max\. 70% → warning\. 85% → page\.
- __Connection monitoring dashboard: __Grafana panel showing active vs\. idle vs\. waiting connections, broken down by source \(Lambda vs\. ECS vs\. admin\)\.
- __Slow query logging: __Enabled pg\_stat\_statements and set log\_min\_duration\_statement to 100ms\. Weekly report of top\-10 slowest queries\.

## __Probing Questions & Answers__

__Why PgBouncer over RDS Proxy?__

*We evaluated both\. RDS Proxy is AWS\-managed, which is appealing, but at the time it had higher tail latency for transaction\-mode pooling and limited configuration options\. PgBouncer gave us more control over pool sizing, timeout behavior, and monitoring\. For a system where we were already managing ECS tasks, adding PgBouncer as a sidecar wasn’t much additional operational burden\.*

__How did you identify which queries needed indexing?__

*I used pg\_stat\_statements to find queries with the highest total\_time\. The top two accounted for about 60% of total database load\. Then EXPLAIN ANALYZE on each to understand the execution plan\. Both were doing sequential scans where an index would allow index\-only scans\.*

__What about read replica lag?__

*Aurora replica lag is typically under 20ms\. We monitored AuroraReplicaLag in CloudWatch with an alarm at 1 second\. In practice, it stayed under 50ms — well within tolerance for dashboard data already processed through a streaming pipeline\.*

__How did you test PgBouncer safely?__

*Deployed to staging first, ran the same synthetic load test used for the Go refactor — replaying a day’s production events\. Connection count dropped from ~800 peak to ~180 with PgBouncer\. Throughput stayed the same\. Rolled out to production during off\-peak with a rollback plan: revert the ECS task definition to the version without PgBouncer sidecar\.*

## __Result__

Database connections during peak dropped from ~800–1,000 \(near max\) to a stable ~180–200\. Dashboard query latency from 500ms\+ \(with timeouts\) to under 20ms for indexed queries\. Zero connection exhaustion incidents after the fix\. Read replica separation meant dashboard availability was decoupled from write\-path load\.

# __Quick Reference: Story → Question Mapping__

Use this table to quickly identify which story to lead with for common interview questions\.

__INTERVIEW QUESTION__

__PRIMARY STORY__

__BACKUP STORY__

__Complex technical challenge__

Story 1 \(Go Refactor\)

Story 5 \(Observability\)

__Architectural trade\-off__

Story 2 \(ECS vs\. Lambda\)

Story 1 \(Go vs\. Python\)

__Failure / mistake__

Story 2 \(Lambda incident\)

Story 1 \(first attempt crash\)

__Compliance / security__

Story 3 \(Golden Path\)

Story 7 \(non\-negotiable scanning\)

__Ambiguity / unclear reqs__

Story 6 \(COBOL Translation\)

Story 5 \(diagnosing lag\)

__Influence without authority__

Story 7 \(Platform Adoption\)

Story 3 \(Golden Path adoption\)

__Improving reliability / SRE__

Story 5 \(Observability\)

Story 2 \(post\-incident safeguards\)

__Cross\-functional collaboration__

Story 6 \(COBOL SMEs\)

Story 7 \(resistant team lead\)

__Cost optimization__

Story 2 \(ECS vs\. Lambda\)

Story 5 \(shard right\-sizing\)

__Proactive engineering__

Story 5 \(capacity model\)

Story 3 \(break\-glass mechanism\)

__Database optimization__

Story 8 \(DB Rescue\)

Story 1 \(Go Refactor — DB latency\)

__Production incident \(DB\)__

Story 8 \(connection exhaustion\)

Story 2 \(Lambda → ECS pivot\)

__Performance tuning__

Story 8 \(indexes \+ pooling\)

Story 1 \(goroutines \+ backpressure\)

## __Transition Tips__

Interviewers often ask follow\-ups that steer you from one story into another\. Having natural bridges ready makes you sound like someone with a coherent engineering philosophy, not just a collection of anecdotes\.

- __Story 1 → Story 5: __“You mentioned monitoring in the Go refactor\. How do you approach observability more broadly?”
- __Story 2 → Story 3: __“You mentioned Lambda guardrails\. How do you prevent teams from making risky infra choices?”
- __Story 5 → Story 2: __“You mentioned IteratorAge\. Have you dealt with other scaling incidents?”
- __Story 6 → Story 7: __“You mentioned building trust with COBOL devs\. How do you drive adoption with skeptical engineers?”
- __Story 2 → Story 8: __“You mentioned the Lambda connection exhaustion\. Can you tell me more about how you actually fixed the database side?”
- __Story 1 → Story 8: __“You mentioned measuring database round\-trip latency\. Did you do other database optimization work?”
- __Story 5 → Story 8: __“You mentioned batch writes cutting processing time in half\. What else did you do on the database side?”

