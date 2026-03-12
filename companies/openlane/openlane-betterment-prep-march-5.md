__Interview Prep Guide__

Thursday, March 5, 2026

OpenLane  •  Betterment

# __OPENLANE  —  AI\-First Software Engineer__

__Format: __Technical Q&A — no live coding\. Engineering Manager Nico Medeiros \+ 1–2 engineers

__Resume: __Master Resume

__Why they reached out: __Bilingual abilities \+ AI engineering experience \(Scale AI\)

__Their AI focus: __Internal developer tooling \(not at\-scale model training like Scale AI\)

__Stage: __Initial technical screen

## __Your Elevator Pitch__

*"I’m a platform engineer with a software background — about 7 years of experience\. Most recently I was a Senior Platform & DevOps Engineer at IBM Consulting, where I led engagements for a major entertainment company and federal government agencies\. What connects all of that work is that I sit at the intersection of infrastructure and software — I’m as comfortable writing Go microservices as I am designing the CI/CD pipeline and cloud architecture that deploys them\. Increasingly, that’s included integrating AI tooling into developer workflows, which is actually what drew me to OpenLane’s focus\."*

## __Q1: How are you using AI tools in your current dev environment?__

*Lead with your Scale AI credibility, then connect to day\-to\-day tooling\.*

### __Full Answer__

"There are two layers to how I use AI in my dev environment\. The first is workflow augmentation — I use tools like Claude and Copilot throughout my development cycle\. For code generation, it’s less about having AI write features for me and more about using it as a force multiplier for the tedious parts: boilerplate, writing unit test scaffolding, generating Terraform module documentation, and first\-pass code reviews before I open a PR\. I’ve found that framing prompts well — giving the model context about the system architecture, the constraints, the edge cases — dramatically changes the quality of the output\. Bad prompts give you code you have to throw away\. Good prompts give you code you have to review\.

The second layer is more structural\. At Scale AI, I was on the engineering side of AI evaluation — I built Python\-based frameworks that systematically tested LLM outputs, flagging hallucinations and logic errors at scale\. That work gave me a different perspective: I understand not just how to use these models, but why they fail in specific ways\. That makes me a better consumer of AI tools in my day\-to\-day because I know where not to trust them\.

For me, the biggest shift AI has made is in the investigation and debugging phase\. When I’m debugging an unfamiliar system, I can give a model the logs, the architecture context, and a hypothesis — and use it as a rubber duck that talks back\. It compresses the time between ‘something is wrong’ and ‘here’s a theory worth testing\.’"

### __Key Points to Hit__

- You use AI for force multiplication, not replacement
- You have engineering\-side AI experience \(Scale AI\) — not just user\-side
- You understand model limitations, which makes you a more thoughtful user
- Specific use cases: test scaffolding, Terraform docs, debugging, code review

## __Q2: What is your SDLC and how do you partner with product teams?__

### __Full Answer__

"My SDLC has evolved across different contexts, but the common thread is a strong preference for trunk\-based development with feature flags for anything non\-trivial, and CI/CD as the heartbeat of the process — not an afterthought\.

At IBM on the entertainment engagement, I was embedded with a product team building real\-time staffing and scheduling systems\. The way I partnered with them was to make infrastructure decisions visible and legible\. If I was choosing between ECS and Lambda for a new service, I’d bring a one\-page decision doc to the sprint — not to ask permission, but to give product context on what each choice meant for cost, latency, and operational complexity\. That transparency built trust and prevented surprises\.

On the platform side at The Knot, my partnership with product teams was more structural — I was building the developer platform for them, so my ‘product’ was the internal developer experience\. I ran it like a product: I talked to engineers on different teams to understand pain points before building anything, I prioritized features by adoption friction rather than technical elegance, and I treated developer velocity as a metric I was accountable for\.

The principle that guides me: infrastructure decisions are product decisions\. If a deployment takes 40 minutes, that’s a product problem\. If a staging environment is flaky, that’s a product problem\. My job is to make engineers move faster and more safely — and that requires staying close to the teams, not just the tickets\."

## __Likely Follow\-Up Q&A__

__"Tell me about a specific AI tool you’ve built or contributed to\."__

- Scale AI: Built Python evaluation frameworks to systematically test LLM outputs at scale, processing TB\-scale datasets via BigQuery — caught hallucinations and logic errors programmatically\.
- PocketMechanic: Building an AI\-powered app that integrates LLM capabilities to parse vehicle diagnostic data and give users real\-time insights\. Shows AI product design thinking, not just tooling\.

__"How do you evaluate whether an AI tool is actually working vs\. just feeling fast?"__

*"That’s exactly the right question\. Feeling fast and being accurate are different things\. I look at two things: error rate on the output I wouldn’t have caught without review, and how often I’m throwing away what it produces\. If I’m rewriting 80% of AI\-generated code, I need to improve my prompting or change the task I’m asking it to do\. I measure the real time savings, not the perceived ones\."*

__"What separates a good AI\-first engineer from someone who just uses ChatGPT?"__

*"Intent and skepticism\. A good AI\-first engineer knows what the model is good at and what it’s likely to get wrong\. They prompt with context — they don’t ask ‘write me a function’, they describe the system, the constraints, the edge cases\. And they review outputs like a senior reviewing a junior’s PR — not with suspicion, but with standards\."*

__"How does your bilingual background factor into your work?"__

*This is likely part of why they reached out\. Be genuine and specific:*

- Interface with Spanish\-speaking team members or clients in technical contexts
- Localization/i18n awareness in software design
- OpenLane operates in automotive wholesale across North America — Spanish\-speaking dealers are a real customer segment

## __Your Questions for OpenLane__

1. "You described this as an ‘AI\-First’ role — can you walk me through what that looks like day to day? Is it more about building AI features, integrating AI into developer tooling, or something else?"
2. "What does the SDLC look like end\-to\-end here — from idea to production? Where does engineering partner most closely with product?"
3. "What’s the current state of AI tooling adoption on the engineering team? Are there established tools and workflows, or is this more of a build\-it\-out phase?"
4. "What would make someone exceptionally successful in this role in the first 90 days?"
5. "What’s the team structure — is this embedded with a product team or more of a platform\-style function?"

# __BETTERMENT  —  Sr\. Site Reliability Engineer \(Developer Experience\)__

__Format: __Intro call with Hiring Manager Andrew Allred — 30–45 min, low stress

__Time: __Today at 12:30 PM EST

__Resume: __Platform Engineer Resume

__This call is: __Fit \+ vibe check\. Andrew is evaluating whether to advance you; you’re evaluating the role\.

## __Your Elevator Pitch__

*"I’m a senior platform and SRE\-type engineer with about 7 years of experience\. The through\-line in my career is the intersection of reliability and developer experience — I care deeply about both the systems being healthy and the developers being able to move fast\. Most recently at IBM Consulting, I led platform and DevOps work for a major entertainment client and federal agencies — Kubernetes, Terraform, AWS, CI/CD pipelines, observability\. Before that I was at The Knot Worldwide as a Platform Engineer where I was directly building internal developer tooling and managing clusters\. I write Go and Python, which I know Betterment uses heavily on the SRE side\. What draws me to Betterment’s DX squad specifically is that framing — reliability and developer enablement treated as the same mission, not competing priorities\."*

## __How Your Experience Maps to Their Stack__

__Betterment Requirement__

__Your Experience__

__AWS__

EKS, Lambda, ECS Fargate, IAM, VPC — across entertainment and federal clients

__CI/CD \(developer\-first\)__

GitHub Actions, ArgoCD, Harness — built unified shared CI/CD library at The Knot, eliminated 40% duplicate pipeline code across 10\+ teams

__Container\-based apps__

Kubernetes \(5\+ clusters\), Docker, Helm — managed at The Knot and IBM Consulting

__SLOs / SLIs__

Prometheus \+ Grafana monitoring standards, IteratorAge alerting, tiered alert thresholds \(warning → Slack → page\)

__Go__

Goroutine\-based concurrent pipelines, Kubernetes Controller, telemetry service

__Observability__

Prometheus, Grafana, Splunk, CloudWatch — built stacks from scratch; know leading vs\. lagging indicators

__Code review / design docs__

Active reviewer, authored ADRs, runbooks, and DR protocols

*Note on their stack: CircleCI → you’ve used GitHub Actions/Harness \(concepts transfer directly\)\. Datadog → you’ve used Splunk/CloudWatch/Grafana \(same space\)\. Ruby on Rails → no direct Rails exp, but you’re language\-agnostic with strong Go/Python\.*

## __Experience Overview \(If Asked to Walk Through Your Background\)__

### __1\. IBM Consulting \(Mar 2024 – Feb 2026\)__

Technical lead on two major engagements: entertainment client \(Kinesis pipelines, Go services, CI/CD, cost optimization from Lambda → ECS Fargate\) and federal agencies \(legacy modernization, secure landing zones, event\-driven architecture with Lambda \+ EventBridge\)\.

### __2\. The Knot Worldwide \(May 2022 – Dec 2023\)__

Platform Engineer building internal developer tooling and managing Kubernetes infrastructure\. Built unified CI/CD shared library that eliminated 40% duplicate pipeline code across 10\+ teams\. Prometheus/Grafana observability standards, ArgoCD GitOps\.

### __3\. Scale AI \(Dec 2023 – Feb 2024\)__

Short\-term ML engineering contract — built tooling to evaluate LLM outputs systematically at scale\.

### __4\. IBM \(Jan 2019 – Jun 2022\)__

Started as Junior DevOps, grew to DevOps Engineer\. 99\.9% uptime for 120K\+ daily users, Go/Node telemetry services, MongoDB REST APIs, SonarQube quality gates\.

## __Why Betterment / Why This Role__

*"A few reasons\. First, the DX framing resonates with me — I’ve spent a lot of my career on the platform/SRE side but with a strong developer empathy bias\. I think the best SRE work makes developers faster, not just systems more reliable\. The DX squad at Betterment seems to hold both of those\. Second, the technical environment is interesting — AWS\-native, Go, real observability work with Datadog, meaningful CI/CD challenges\. And third, Betterment operates in a highly regulated environment where reliability genuinely matters — people’s retirement savings aren’t something you can have a bad deploy day with\. I find that responsibility motivating\."*

## __If Asked About SRE Depth — Be Honest, Frame It as a Strength__

__How to address it__

*"I want to be upfront — my background is more platform engineering and backend than deep SRE\. I've done SRE\-adjacent work: Kubernetes in production, observability stacks from scratch, incident response, runbooks\. But I haven't been in a dedicated SRE role with a formal on\-call rotation and SLO culture\. Part of why this role appeals to me is exactly that — the DX squad mandate is a natural extension of the platform engineering I've done, and it gives me the chance to deepen the reliability discipline in a domain where it genuinely matters\."*

*Why this works in your favor: pure SRE hires sometimes optimize for system reliability at the expense of developer experience\. You come from the developer empathy side — you've built things for developers to use\. For a Developer Experience squad specifically, that perspective is an asset, not a gap\.*

## __Your Questions for Andrew__

1. "How does the DX squad’s mission get defined — is it driven more by engineering teams coming to you with pain points, or do you proactively identify gaps in the developer experience?"
2. "What does the on\-call rotation look like for this role, and how mature is the incident response process?"
3. "You’re using CircleCI for CI/CD — is there active work happening on that stack, or is it in a maintain\-and\-improve mode?"
4. "What’s the biggest unsolved developer experience problem on the team right now?"
5. "What does success look like for someone in this role at the 6\-month mark?"
6. "How does the team think about the balance between building new tooling vs\. improving reliability of what exists?"

## __Stories to Have Ready__

*Andrew may not go deep technical — but be ready:*

- __Reliability / SRE work → __Story 5 \(Observability\)\. "We had a system that looked healthy but was silently delivering stale data — IteratorAge sitting at 45 minutes while all health checks showed green\."
- __CI/CD or platform work → __Story 7 \(Platform Adoption at The Knot\)\. "I had to get 10\+ teams to adopt a shared pipeline library without mandating it — the key was listening before building\."
- __Incident / post\-mortem → __Story 2 \(ECS vs\. Lambda\)\. "I once caused a database connection exhaustion incident by choosing Lambda without modeling the worst\-case scaling scenario\."

## __Key Talking Points to Weave In__

- You write Go — which they use on the SRE side
- You’ve built observability from scratch across multiple stacks and know the difference between monitoring failures and monitoring leading indicators
- You’ve managed Kubernetes at scale across multiple clusters with GitOps workflows
- You think about the developer as your customer — not just the system
- Your AWS certification is fresh \(December 2025\)

## __Salary Note__

Posted range: $175K–$205K \+ equity\.

If it comes up: "The posted range works for me — I’m more focused on making sure the role is the right fit technically and culturally\."

__*Good luck tomorrow, Stephan\. You’ve got strong, specific stories for both roles — trust the prep\.*__

