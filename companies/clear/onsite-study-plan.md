__CLEAR__

Onsite Interview Study Plan

Stephan Edmonson  |  Senior Software & Platform Engineer

Interview Date: Wednesday, March 18, 2026

Location: 85 Tenth Avenue, 9th Floor, New York, NY 10011

__Arrive by: 11:15 AM EDT__

__TIME__

__INTERVIEW ROUND__

__INTERVIEWER__

__FORMAT__

11:30–12:30

__Infrastructure System Design__

James Forcier \(SDE IV\)

Whiteboard

12:30–1:30

__Python Live Coding__

Alexis Gabriel \(SDE III\)

CodeSignal

1:45–2:15

__Cross\-Functional Interview__

Patrick Carroll \(Sr TPM\)

Behavioral

2:15–2:45

__Engineering Leadership__

Julien Eid \(Dir, Eng\)

Behavioral \(Zoom\)

# Priority Ranking by Impact

Not all rounds carry equal weight\. Here’s how to allocate your prep time based on the typical weighting and your strengths\.

__\#__

__ROUND__

__PRIORITY__

__PREP TIME__

__WHY THIS RANKING__

__1__

__System Design__

__HIGHEST__

__40%__

60 min with an SDE IV\. Longest round, most senior technical interviewer\. This is the technical signal they’ll weigh heaviest\.

__2__

__Python Live Coding__

__HIGH__

__25%__

60 min, pragmatic coding \(not LeetCode\)\. Your tech screen went well, so this is about reinforcing that signal\. Focus on clean code and communication\.

__3__

__Cross\-Functional__

__MEDIUM__

__20%__

30 min with a Sr TPM\. Your story bank is strong here\. Focus on rehearsing delivery and picking the right story for each prompt\.

__4__

__Engineering Leadership__

__MEDIUM__

__15%__

30 min with a Director\. This is about senior\-level presence: mentoring, ownership, learning from failure\. Your stories already cover this well\.

# Round 1: Infrastructure System Design

11:30 AM – 12:30 PM  |  James Forcier \(SDE IV\)  |  Whiteboard \+ Lucidchart

## What They’re Looking For

- Architecting Distributed Systems — inter\-service communication, trade\-offs between sync/async, choosing distributed components
- Security — auth/authz, encryption at rest and in transit, common attack vectors \(especially relevant for CLEAR’s identity verification platform\)
- Resilience — fault tolerance, graceful degradation, replication strategies, circuit breakers
- Scaling & Performance — horizontal vs vertical, load balancing, caching layers, database sharding
- Operations — metrics, logs, traces, alerting, deployment strategies \(canary, blue\-green\)
- Data Storage — relational vs NoSQL, object storage, transactions, data modeling decisions

## Your 6\-Step Framework \(Memorize This\)

Use this structure for every system design problem\. It keeps you organized and ensures you hit every assessment criterion\.

__STEP__

__PHASE__

__TIME__

__WHAT TO DO__

__1__

__Clarify__

5 min

Ask clarifying questions\. Define functional vs non\-functional requirements\. Nail down scale \(users, requests/sec, data volume\)\. Agree on scope\.

__2__

__Estimate__

5 min

Back\-of\-envelope: storage needs, QPS, bandwidth\. This shows you think about constraints before designing\. Mention latency and cost targets\.

__3__

__High\-Level Design__

15 min

Draw the big boxes: clients, API gateway, services, databases, caches, queues\. Show data flow with arrows\. Name your APIs\. This is where you spend the most time\.

__4__

__Deep Dive__

20 min

Pick 2–3 interesting components and go deep\. Database schema, caching strategy, async processing\. The interviewer may steer you — follow their lead\. Discuss trade\-offs explicitly\.

__5__

__Resilience & Ops__

10 min

How does it handle failures? What do you monitor? How do you deploy safely? This is where you stand out — your Story 5 \(Observability\) experience is gold here\.

__6__

__Scale & Evolve__

5 min

How would this scale 10x? 100x? What would you add next? This shows strategic thinking\.

## CLEAR\-Specific Design Angles to Prepare

CLEAR is an identity verification and biometric security company\. Their systems likely deal with:

- High\-throughput identity verification \(airports, stadiums, healthcare\) — think low\-latency, high\-availability
- Biometric data storage and matching — encryption, compliance \(BIPA, GDPR\), secure pipelines
- Event\-driven architectures — verification events, partner integrations \(TSA, Greenhouse\)
- Multi\-region resilience — identity checks can’t go down; think about failover, data replication, and degraded mode

## Practice Problems to Work Through

1. __Design an identity verification system __— a user approaches a kiosk, scans biometrics, and gets verified in under 3 seconds\. Consider edge cases: what if the biometric service is down? How do you handle false positives?
2. __Design a real\-time event processing pipeline __— very relevant given your Kinesis/observability experience\. Show how you’d handle backpressure, exactly like your Story 5\.
3. __Design a multi\-tenant API platform __— CLEAR partners with airports, stadiums, healthcare\. Think rate limiting, tenant isolation, API versioning\.
4. __Design a notification/alerting system __— think fan\-out, delivery guarantees, multiple channels \(push, SMS, email\)\.

## Tips & Tricks for This Round

- __Lead with trade\-offs, not solutions\. __Don’t just say “use Redis\.” Say “I’d add a caching layer here — Redis for low\-latency reads, with a TTL of X because of Y\. The trade\-off is cache invalidation complexity\.”
- __Weave in your real experience\. __When discussing connection pooling, casually mention “I actually ran into this exact problem with Lambda scaling…” \(Story 2\)\. It shows you’re not just book\-smart\.
- __Don’t skip observability\. __CLEAR explicitly assesses “Designing for Operations\.” Mention what you’d monitor, how you’d alert, what dashboards you’d build\. This is your wheelhouse \(Story 5\)\.
- __Practice with Lucidchart\. __They mentioned Lucidchart specifically\. Spend 30 min getting comfortable drawing boxes, arrows, and labels quickly\. You don’t want tool fumbling eating into thinking time\.
- __Security is a first\-class concern at CLEAR\. __Always mention encryption \(at rest \+ in transit\), auth/authz patterns, and data compliance\. For a biometric company, this isn’t an afterthought — it’s core\.

# Round 2: Python Live Coding

12:30 PM – 1:30 PM  |  Alexis Gabriel \(SDE III\)  |  CodeSignal

## What They’re Looking For

- Coding Fundamentals — strong Python fluency, clean readable code, good variable naming, proper use of built\-in data structures
- Quality & Performance — complete working solution first, then optimize\. Mention time/space complexity verbally
- Design Approach — break down the problem before coding\. Discuss edge cases and trade\-offs out loud
- Communication — read the full problem first\. Ask clarifying questions\. Think out loud throughout

## Based on Your Tech Screen: What to Expect

Your tech screen was pragmatic — building features on a todo app with undo/redo using a stack and a list\. Expect the onsite to follow a similar pattern: a practical, real\-world problem where you implement methods on a class or module\. Not LeetCode, but you still need solid fundamentals\.

### Key Python Patterns to Drill

__PATTERN__

__WHY IT MATTERS FOR THIS INTERVIEW__

__Stack / Queue__

Already tested on undo/redo\. Be ready for variations: browser history, transaction logs, command pattern implementations\.

__Dict as index/cache__

Fast lookups by key\. Think: user registry, config store, in\-memory cache\. Know defaultdict, Counter, OrderedDict\.

__Class design / OOP__

Pragmatic interviews often ask you to build a class with methods\. Know \_\_init\_\_, @property, clean method signatures\.

__List comprehensions__

Shows Python fluency\. Use them for filtering and transformation, but don’t over\-nest — readability matters\.

__Error handling__

try/except with specific exceptions\. Validate inputs early\. A senior engineer handles edge cases without being asked\.

__Generators / iterators__

Shows you think about memory\. yield for streaming data, iter\(\) for custom traversal\.

## Your Coding Playbook \(5\-Step Process\)

1. __Read the FULL problem __\(2 min\)\. Don’t start coding after reading half\. Read examples and constraints carefully\.
2. __Restate and clarify __\(3 min\)\. “So the input is X and I need to return Y\. What about edge case Z?” This is free signal\.
3. __Outline your approach __\(3 min\)\. Explain your plan in plain English before writing code\. “I’ll use a dict to index by ID, then iterate through\.\.\.”
4. __Code the solution __\(35–40 min\)\. Write clean, working code\. Use good variable names\. Add brief comments for non\-obvious logic\. Think out loud\.
5. __Test and optimize __\(10 min\)\. Walk through an example\. Discuss complexity\. Mention optimizations even if you don’t implement them\.

## Tips & Tricks

- __Get comfortable in CodeSignal\. __Run the Python learning path they recommended\. Familiarize yourself with the IDE, running tests, and autocomplete behavior so you’re not fighting the tool\.
- __Working code first, then refine\. __Don’t try to write the perfect solution on the first pass\. Get something running, then improve\. A working brute\-force beats an incomplete optimal solution\.
- __Narrate your thinking\. __Silence is your enemy\. Even when stuck, say “I’m considering whether to use X or Y because\.\.\.” They’re assessing your thought process, not just the output\.
- __Handle edge cases proactively\. __Empty inputs, None values, duplicate keys, single\-element lists\. Mention these before the interviewer asks\. At senior level, this is expected\.

# Round 3: Cross\-Functional Interview

1:45 PM – 2:15 PM  |  Patrick Carroll \(Senior TPM\)  |  Behavioral

## What They’re Looking For

- Communication & Collaboration — building relationships across teams, shared vision, working with PMs
- Ambiguity & Action — cutting through noise, making strong decisions, pushing things forward
- Resilience — navigating disagreements with grace, giving/receiving feedback, big\-picture thinking
- Accountability & Ownership — learning from mistakes, owning outcomes
- Human\-Centered — bringing people along as you learn and grow

## Story Mapping: Your Best Stories for Each Theme

__LIKELY QUESTION THEME__

__PRIMARY STORY__

__BACKUP STORY__

__Cross\-team collaboration__

Story 6: COBOL Translation \(SME partnerships\)

Story 7: Platform Adoption \(10\+ teams\)

__Working with ambiguity__

Story 6: COBOL \(no docs, unclear requirements\)

Story 5: Observability \(diagnosing invisible lag\)

__Disagreement / pushback__

Story 7: Platform Adoption \(resistant team lead\)

Story 3: Golden Path \(devs routing around policy\)

__Failure / learning__

Story 4/2: Lambda incident \(over\-indexed on velocity\)

Story 1: Go Refactor \(first attempt crashed DB\)

__Project ownership / results__

Story 5: Observability \(end\-to\-end ownership\)

Story 1: Go Refactor \(70% perf improvement\)

__PM partnership__

Story 7: Platform Adoption \(listening before building\)

Story 6: COBOL \(cross\-functional translation\)

## STAR Method Reminders

They explicitly mention STAR\. For a 30\-minute round, you’ll likely get 3–4 questions\. Keep each answer to 3–4 minutes max\.

- __Situation __\(30 sec\): Set the scene quickly\. “At \[company\], I was on a team that\.\.\.” Don’t spend 2 minutes on context\.
- __Task __\(15 sec\): What was YOUR specific responsibility? Distinguish what you owned vs\. what the team owned\.
- __Action __\(90 sec\): This is the meat\. Be specific: “I built\.\.\.” not “We decided\.\.\.” Use your hooks to make it memorable\.
- __Result __\(30 sec\): Quantify\. “70% reduction in execution time\.” “Zero connection limit incidents after that\.” “100% feature parity\.”

## Tips & Tricks

- __Use your hooks\. __Your story bank has great opening lines\. “I had to fix a system that was too slow, but my first attempt actually crashed the database because it was too fast\.” That’s memorable\. Use them\.
- __This is a PM asking\. __Patrick is a Senior TPM, not an engineer\. Lean into collaboration language: “I partnered with\.\.\.” “We aligned on\.\.\.” “I translated the technical constraint into business impact\.”
- __Connect to business impact\. __They assess this explicitly\. Every story should end with how it moved the needle for the organization, not just the technical metric\.
- __Don’t be afraid to show vulnerability\. __They want “learning from mistakes\.” Your Lambda failure story is perfect because it shows self\-awareness and a concrete change in behavior\.

# Round 4: Engineering Leadership

2:15 PM – 2:45 PM  |  Julien Eid \(Director, Engineering\)  |  Behavioral \(Zoom\)

## What They’re Looking For

This is a Director\-level engineer assessing your senior engineering presence\. They want to know:

- How you lead and mentor people around you \(without necessarily being a manager\)
- How you learn from mistakes and take ownership / accountability
- How you operate as a senior\-level engineer on your teams — influence, decision\-making, technical judgment
- Your overall engineering philosophy and growth mindset

## Story Mapping for Leadership Themes

__LEADERSHIP THEME__

__PRIMARY STORY__

__KEY ANGLE TO EMPHASIZE__

__Mentoring / growing others__

Story 7: Platform Adoption

Turned a resistant team lead into a contributor by giving them ownership of the design

__Learning from failure__

Story 4/2: Lambda Incident

Changed your permanent approach: now always model 10x/100x scenarios before choosing architecture

__Ownership & accountability__

Story 5: Observability Win

Took ownership of a system nobody asked you to fix\. Built capacity model proactively\.

__Technical decision\-making__

Story 2: ECS vs Lambda

Data\-driven cost analysis, documented ADR for future engineers

__Influence without authority__

Story 3: Golden Path

“Enablers beat gatekeepers\.” Made the right thing the easy thing\.

__Raising the bar__

Story 3: Golden Path

Went from 60% to 100% compliance by changing the system, not policing people

## Tips & Tricks

This is a Director asking, so calibrate your answers to show senior\-level thinking\. Here’s how:

1. __Zoom out\. __Don’t just tell the technical story — explain how your work connected to team/org goals\. A Director thinks about impact, not implementation\.
2. __Show multiplier thinking\. __Senior engineers don’t just write good code — they make everyone around them better\. Highlight how your work \(Golden Path, CI/CD library\) made OTHER people more productive\.
3. __Own failures without hedging\. __“I over\-indexed on velocity” is great because it’s honest and shows judgment maturity\. Don’t blame circumstances or other people\.
4. __Have a question ready for Julien\. __Ask about engineering culture, how they invest in developer productivity, or what the biggest technical challenge is right now\. Shows you’re evaluating them too\.
5. __This is on Zoom\. __Camera on, good lighting, quiet background\. Keep notes nearby but don’t read from them\. Eye contact with the camera, not the screen\.

# 13\-Day Study Plan \(March 5 – March 17\)

You have 13 days until interview day\. Here’s a day\-by\-day breakdown weighted by priority\. Aim for 1\.5–2 hours of focused prep per day\.

__DAY__

__DATE__

__FOCUS__

__1__

Thu Mar 5

__System Design Foundations\. __Review your 6\-step framework\. Practice drawing an architecture diagram in Lucidchart for 30 min\. Read about CLEAR’s product \(clear\.me\) to understand their domain\.

__2__

Fri Mar 6

__System Design Practice \#1\. __Design an identity verification system end\-to\-end\. Time yourself: 45 min\. Focus on clarifying questions, high\-level design, and security \(encryption, biometric data handling\)\.

__3__

Sat Mar 7

__Python Coding \+ CodeSignal\. __Start the CodeSignal Python learning path\. Focus on class design, stack/queue patterns, and dict\-based solutions\. Build a simple feature: implement 3 methods on a class\.

__4__

Sun Mar 8

__System Design Practice \#2\. __Design a real\-time event processing pipeline\. Lean into your Kinesis experience \(Story 5\)\. Practice discussing backpressure, monitoring, and IteratorAge\-style leading indicators\.

__5__

Mon Mar 9

__Behavioral Stories: Cross\-Functional\. __Rehearse Stories 6 and 7 out loud using STAR\. Time each at 3–4 min\. Practice the hooks\. Record yourself and listen back\.

__6__

Tue Mar 10

__System Design Deep Dive: Data & Security\. __Study database trade\-offs \(SQL vs NoSQL, sharding, replication\)\. Review encryption patterns \(at rest, in transit, envelope encryption\)\. Know auth patterns: OAuth2, JWT, API keys\.

__7__

Wed Mar 11

__Python Coding Practice\. __Continue CodeSignal path\. Practice 2–3 pragmatic problems\. Focus on clean class design, error handling, and talking through your approach out loud\.

__8__

Thu Mar 12

__System Design Practice \#3\. __Design a multi\-tenant API platform with rate limiting and tenant isolation\. Practice scaling discussion: what happens at 10x, 100x? Discuss deployment strategies \(canary, blue\-green\)\.

__9__

Fri Mar 13

__Behavioral Stories: Leadership\. __Rehearse Stories 2/4 \(failure\), 3 \(Golden Path\), and 5 \(Observability\) with leadership framing\. For each, practice answering: “How did this make your team better?”

__10__

Sat Mar 14

__Full System Design Mock\. __Set a 55\-minute timer\. Pick a problem you haven’t done\. Go through all 6 steps\. Use Lucidchart\. Grade yourself: did you hit clarify, estimate, design, deep dive, resilience, scale?

__11__

Sun Mar 15

__Python Coding: Final Drills\. __Do 2–3 timed CodeSignal problems \(45 min each\)\. Focus on completion \+ narrating your approach\. Review any patterns that tripped you up\.

__12__

Mon Mar 16

__Full Behavioral Rehearsal\. __Do a mock run of both behavioral rounds back\-to\-back \(45 min\)\. Have a friend ask random questions from the story mapping tables\. Practice transitioning between stories naturally\.

__13__

Tue Mar 17

__Light Review \+ Logistics\. __Skim your story bank one final time\. Review the 6\-step framework\. Check Zoom link works for Round 4\. Plan your route to 85 Tenth Ave\. Pick your outfit\. Get a good night’s sleep\.

# Day\-Of Game Plan — March 18

__TIME__

__ACTION__

__10:30 AM__

Leave for 85 Tenth Ave\. Buffer for transit delays\. Grab water and a snack\.

__11:00 AM__

Arrive at building\. Check in with lobby security\. Take elevator to 9th floor\.

__11:15 AM__

Greeted by recruiter\. Get settled\. Quick bathroom break\. Deep breaths\. Review your 6\-step framework on your phone\.

__11:30–12:30__

__SYSTEM DESIGN with James Forcier\. Lead with clarifying questions\. Draw clean diagrams\. Don’t skip observability or security\.__

__12:30–1:30__

__PYTHON CODING with Alexis Gabriel\. Read the full problem\. Restate requirements\. Code clean, talk through it, test at end\.__

__1:30–1:45__

BREAK\. Eat a snack\. Drink water\. Don’t review notes — just reset\. The technical rounds are done\.

__1:45–2:15__

__CROSS\-FUNCTIONAL with Patrick Carroll\. Use your hooks\. STAR format\. Connect to business impact\. Be specific\.__

__2:15–2:45__

__LEADERSHIP with Julien Eid \(Zoom\)\. Camera on\. Show senior\-level thinking: mentoring, multiplier impact, ownership\. Have a question ready\.__

# Final Reminders

__You’re already strong\. __You passed the tech screen\. They’re investing a full afternoon with four people\. They want you to succeed\.

__Your story bank is a weapon\. __You have 7 well\-structured stories with hooks, probing answers, and quantified results\. Most candidates wing it\. You won’t\.

__Security is your thread\. __CLEAR is a security/identity company\. Weave security thinking into every answer — system design \(encryption, auth\), coding \(input validation, error handling\), and behavioral \(the Golden Path story\)\.

__Be collaborative, not performative\. __Every round description mentions collaboration\. They want a teammate, not a solo hero\. “We” language for team wins, “I” language for your specific contributions\.

