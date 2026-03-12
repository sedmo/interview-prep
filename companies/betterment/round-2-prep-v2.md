__BETTERMENT — ROUND 2__

Sr\. Site Reliability Engineer — Developer Experience

__Tuesday, March 10, 2026 | 12:30 – 2:45 PM EDT__

Updated March 9, 2026 — Hello Interview Framework \+ Evaluation Criteria

# __Interview Schedule__

__TIME__

__SESSION__

__INTERVIEWERS__

__EVALUATING__

__12:30–1:30__

System Design \(LucidChart\)

Chikara Takahashi, Ecco L’nu

Collaborating w/ Infra Enablement \+ Empathy, EQ, Creativity

__1:30–2:30__

Pairing Exercise \(CoderPad, Python\)

Kevin Lu, Peter Marks

Fosters Inclusion \+ Leading Change

__2:30–2:45__

Recruiter Wrap\-Up

Ollie Lutton

Debrief \+ next steps

# __What the Evaluation Criteria Tell You__

The session titles reveal exactly what Betterment is assessing beyond technical skill\. This is critical intelligence\.

__System Design: "Collaborating with Infra Enablement"__

Betterment’s SRE org has two squads: Infrastructure Enablement \(AWS, IaC, multi\-region\) and Developer Experience \(your squad\)\. This session is testing whether you can design a system that acknowledges and collaborates with the Infra Enablement team\. In practice, this means:

- Reference the Infra Enablement squad naturally: “This is where I’d partner with the Infra Enablement team on the AWS provisioning\.\.\.”
- Don’t design in a vacuum — acknowledge shared ownership\. “The deployment pipeline is DX\-owned, but the underlying EKS clusters would be Infra Enablement’s domain\.”
- Ask the interviewers: “How does your team typically split ownership between DX and Infra Enablement for something like this?”

__System Design: "Empathy, Emotional Intelligence, & Creativity"__

They’re watching HOW you design, not just WHAT you design:

- Think out loud and invite input: “What do you think — is a queue overkill here, or does it give us the decoupling we need?”
- Show empathy for the developer user: “If I’m an engineer on a product team, what’s my experience with this system? How do I debug when something goes wrong?”
- Be creative — don’t just give textbook answers\. Connect to real experience and propose novel approaches\.

__Pairing: "Fosters Inclusion & Leading Change"__

This isn’t just “can you code” — it’s “can you pair effectively and bring others along?”

- Treat the interviewer as a true partner\. Ask their opinion, incorporate their suggestions\.
- When you make a decision, explain your reasoning so they can follow: “I’m choosing a dict here because we need O\(1\) lookups by service name\.”
- If you disagree with a suggestion, engage constructively: “That’s an interesting approach\. My instinct is X because of Y, but I can see how Z would work too\. What do you think?”

# __System Design Framework \(Hello Interview\)__

Use this framework for the 60\-minute session\. It’s the delivery structure from Hello Interview that you’ve been practicing with\.

__STEP__

__TIME__

__WHAT TO DO__

__1\. Requirements__

~5 min

Functional reqs \(3 “Users should be able to\.\.\.” statements\)\. Non\-functional reqs \(3–5: CAP, latency, scalability, durability\)\. Skip capacity estimation unless it directly influences design\.

__2\. Core Entities__

~2 min

List the core nouns/resources\. Keep it short — you’ll add fields later when you reach the data layer\.

__3\. API / System Interface__

~5 min

Define the contract\. Default to REST\. Name your endpoints, methods, and key request/response shapes\. Derive user from auth token, not request body\.

__4\. High\-Level Design__

~15 min

Draw boxes and arrows on LucidChart\. Build up sequentially — go endpoint by endpoint\. Show data flow\. Add schema fields next to your database\. Stay simple — resist adding complexity too early\. Note areas for deep dives verbally\.

__5\. Deep Dives__

~10 min

Harden the design: ensure non\-functional reqs are met, address bottlenecks, handle edge cases\. Lead proactively but give the interviewer room to probe\. Update your diagram as you iterate\.

__Key principle: __Don’t over\-engineer in the high\-level design\. Get a working system first, then iterate in deep dives\. The most common failure mode is never delivering a complete system because you went too deep too early\.

# __Practice Prompts \(SRE/DX Flavored\)__

Given the DX role and the “Collaborating with Infra Enablement” evaluation, the prompt will likely involve developer tooling, CI/CD, or observability\. Here are the most likely prompts with notes on how to connect them to Betterment’s context\.

__Prompt 1: Design a CI/CD Pipeline System__

"Design a CI/CD system for an engineering organization with 150\+ engineers, a large monorepo, and multiple deployment targets\."

- __Why likely: __This is literally the DX squad’s core domain\. Andrew said build times went from 1 hour to under 10 minutes\.
- __Your edge: __Story 7 — shared CI/CD library across 10\+ teams at The Knot\. 40% duplicate code eliminated\.
- __Infra Enablement angle: __“The DX squad owns the pipeline logic and developer experience\. Infra Enablement owns the underlying compute and deployment targets \(EKS clusters, environments\)\.”

__Prompt 2: Design a Monitoring and Alerting Platform__

"Design an observability platform for a microservices architecture\."

- __Why likely: __SRE core competency\. Betterment uses Datadog\.
- __Your edge: __Story 5 — IteratorAge, tiered alerting, leading vs\. lagging indicators\.
- __Fintech angle: __“In financial services, I’d define SLOs on transaction success rate and latency, with error budgets that gate deployments\.”

__Prompt 3: Design an Internal Developer Platform__

"Design a system where engineering teams can provision and manage their own services with guardrails\."

- __Why likely: __Andrew mentioned they have an IDP and are trying not to over\-build it\.
- __Your edge: __Story 3 — Golden Path CDK constructs with break\-glass mechanism\.
- __Collaboration angle: __“The IDP is the interface between DX and Infra Enablement — DX builds the self\-service layer, Infra Enablement provides the underlying primitives\.”

# __Intel from Round 1 \(Weave In Naturally\)__

- Build times: ~1 hour \+ 25% flake rate → under 10 min \+ 90%\+ success rate
- Hot fixes deploy in ~15 minutes
- Two SRE squads: Infrastructure Enablement \(AWS, IaC, multi\-region\) and Developer Experience \(CI/CD, tooling\)
- Shared on\-call rotation every 8–9 weeks
- AI adoption is ground\-up, leadership encouraging
- IDP exists but they’re careful not to over\-build \(build vs\. buy awareness\)
- Monorepo architecture
- Stack: AWS, CircleCI, Datadog, Ruby on Rails, Go

# __Session 2: Pairing Exercise \(1:30–2:30 PM\)__

__Evaluating: __Fosters Inclusion & Leading Change

Format: Pair with Kevin Lu and Peter Marks on CoderPad\. Python\. 60 minutes\. AI tools allowed with guardrails \(share screen, specific components only, don’t paste requirements\)\.

## __Your Pairing Playbook__

1. __Read the full problem __\(2 min\)\. Don’t start coding after reading half\.
2. __Ask clarifying questions __\(3 min\)\. “Can I assume the input is always well\-formed? What should happen on empty input?”
3. __State your approach __\(3 min\)\. “I’m thinking I’d break this into three parts: parsing, processing, output\. Does that sound reasonable?” — this is the “Leading Change” moment\.
4. __Code cleanly __\(40 min\)\. Meaningful names, helper functions, test incrementally\. Talk as you type\.
5. __Wrap up __\(10 min\)\. Walk through a test case\. Mention edge cases\. Discuss complexity\.

__Fostering Inclusion During Pairing__

- Ask for input: “What do you think about this approach?” or “Would you handle this differently?”
- When they suggest something, engage with it — don’t dismiss or ignore\.
- If you get stuck, say so openly: “I’m stuck on how to handle X — let me think for a second\.” Vulnerability is inclusion\.
- Credit their input: “That’s a good point about the edge case — let me add that\.”

## __AI Usage Strategy__

Their rules: share screen, use for specific components only, don’t paste requirements\. You MAY describe an algorithm in plain English or pseudocode\.

How to use it well: After you’ve explained your approach and started coding, use AI for targeted helpers\. Narrate: “I’m going to ask the AI for the parsing logic for this specific piece\.” Transparency shows judgment\.

## __Likely Problem Types__

- Log parsing/analysis — Counter, defaultdict, regex
- Metrics aggregation — P50/P99 computation, rolling averages
- Config/YAML processing — yaml library, dict manipulation
- Health check / monitoring — requests, retry logic
- CLI tool — argparse basics
- Data pipeline — generators, file I/O, filtering

## __Python Quick Reference__

from collections import defaultdict, Counter, deque

sorted\(data, key=lambda x: x\['timestamp'\]\)

import re; match = re\.search\(r'pattern', line\)

with open\('file\.log'\) as f: for line in f: \.\.\.

from datetime import datetime, timedelta

assert compute\_p99\(values\) == expected

# __Session 3: Recruiter Wrap\-Up \(2:30–2:45 PM\)__

Ollie Lutton\. 15 minutes\. Debrief and logistics\.

__Questions to Ask__

- “What does the final round look like — the 2\.5\-hour meet\-the\-team session?”
- “Is there anything specific I should prepare for the final round?”
- “What’s the timeline you’re working with for filling this role?”
- “What do people enjoy most about working on the DX squad?”

__If Compensation Comes Up__

*“The posted range works for me — I’m more focused right now on making sure the technical fit is strong on both sides\.”*

Posted range: $175K–$205K \+ equity \+ bonus\. Don’t negotiate yet\.

# __Stories to Weave In__

- __CI/CD decisions → __Story 7 \(Platform Adoption\)\. “At The Knot we had 10\+ teams with their own pipelines\. I got adoption by listening first, not mandating\.”
- __Observability → __Story 5 \(IteratorAge\)\. “We had a system passing all health checks but silently delivering stale data\.”
- __Scaling incidents → __Story 2 \(Lambda connection exhaustion\)\. “I learned to model ‘what happens at 10x’ before picking a compute service\.”
- __Compliance guardrails → __Story 3 \(Golden Path\)\. “I made the secure path the easy path\.”

# __If SRE Depth Comes Up__

*“My background is more platform engineering than pure SRE, and I want to be transparent about that\. I’ve done the adjacent work — Kubernetes in production, observability from scratch, incident response, runbooks — but I haven’t been in a formal SRE role with on\-call rotation and SLO culture\. That’s part of why this role appeals to me: the DX mandate is a natural extension of what I’ve done, and it gives me the chance to deepen the reliability discipline in a domain where it genuinely matters\.”*

# __Final Reminders__

- Think out loud\. Silence while drawing is a missed opportunity\.
- Ask, don’t assume\. It shows collaboration, not weakness\.
- Connect to Betterment’s context: “In financial services, I’d lean toward stronger consistency here\.”
- Manage time\. Flag transitions: “I want to make sure we get to deep dives — should I keep going here or move on?”
- Resist over\-engineering\. Get a working system, then iterate\.
- Embody their values: Play to Win, Make an Impact, Build Better Together, Simplify\.

__You know the material\. Trust the framework, think out loud, and treat the interviewers like teammates\. Good luck tomorrow\.__

