# Betterment — Hiring Manager Round Notes

**Date:** March 6, 2026 (Thursday)
**Interviewer:** Andrew Allred, Director of Developer Platform & Security (~18 months at Betterment)
**Format:** 30-minute behavioral/conversational

---

## Team Genesis (Andrew's Story)

The DevX team emerged from necessity. When Andrew joined:
- CI builds took ~1 hour with 25% flake rate
- Incidents could take 3–4 hours to resolve (build → PR review → merge queue → flake → retry)

Since then:
- Build times under 10 minutes
- Success rate over 90%
- Hot fixes to production in ~15 minutes

The role is about picking up that torch and continuing to make product engineers' lives smooth and fast.

## Team Structure

SRE has two squads:
1. **Infrastructure Enablement** — AWS, IaC, multi-region, hot/warm/cold decisions
2. **Developer Experience** — CI/CD, shipping speed, AI enablement (this role)

Shared on-call rotation — primary and secondary, every 8–9 weeks. On-call can be a mix of K8s issues one day, AI questions the next.

Day-to-day is more like "old school DevOps" type work — building tools, improving pipelines, enabling developers.

## Questions Asked

### 1. How did you get into software engineering?
You told the story of bypassing parental controls as a kid, dad being a math teacher (liked right/wrong answers over subjective ones), IT/networking in college, pragmatic career choice with flexibility.

### 2. How did you end up in SRE/DevX?
Love of breadth — touching many areas, understanding SDLC end-to-end. Went from full-stack (Microsoft intern) → Salesforce (IBM) → Salesforce DevOps (niche) → Platform Engineering (The Knot) → consulting (IBM). Mentioned T-shaped engineers becoming more relevant with AI.

### 3. Change initiative you led?
Told the CI/CD platform adoption story from The Knot:
- First 2 weeks: interviewed engineers on every team (deployment process, pain points, sacred cows)
- Found common themes: everyone hated maintaining Docker caching logic, environment variable inconsistencies, deployment notifications
- Built shared libraries from best implementations across teams
- Didn't mandate adoption — let teams adopt one step at a time
- Started with vocal/influential teams as early adopters
- Paired with engineers during migration

**Andrew asked about success metrics.** You were honest that metrics weren't tracked from the start (no clean pre/post comparison). Mentioned: pipeline code deduplication, deployment time, failure rates, voluntary adoption rates.

### 4. Build vs Buy?
No direct vendor selection example. Emphasized staying cloud-agnostic and being pragmatic about spend (warm standby vs hot-hot vs just not doing multi-cloud).

### 5. AI workflows and tools?
Mentioned code reviews, human-in-the-loop for high stakes, compliance constraints in federal work. Used enterprise-level AI tools at IBM but more personal project usage. Andrew noted they're very encouraged by leadership to adopt AI — big opportunity for this team.

## Roadmap / Challenges

1. Continuous CI/CD optimization — always fighting entropy as org scales
2. AI adoption standardization — ground-up adoption already happening, team can help accelerate and standardize
3. Supporting teams with large codebases using AI effectively
4. Helping product designers use AI for UI testing
5. Internal developer platform exists but trying not to overbuild it

## Key Notes

- Backfill role
- Andrew said your answers were "very thoughtful"
- He's director-level, manages both IE and DevX squads
- Culture: operational excellence + continuous improvement, not firefighting
