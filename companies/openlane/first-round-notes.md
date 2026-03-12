# Openlane — First Round Notes

**Date:** March 6, 2026 (Thursday)
**Call Type:** First round with Nicholas (Senior Engineer, Uruguay, 6 years at company)
**Role:** Senior Engineer on new AI-first team (3–4 engineers)

---

## Company Overview

Openlane is a dealer-to-dealer vehicle marketplace. Acquired BackdotCars (originally a Uruguayan startup). Has US, Canadian, European marketplaces + private label division. Distributed team across Uruguay, Brazil, US (East/Central/West), Philippines, India.

## The Role — AI-First Experimental Team

Brand new team being built (not started yet). 3–4 engineers. Dual mandate:

1. **Build production features** using AI tools
2. **Establish org-wide AI development processes** — recommendations for tools, workflows, skills, guidelines, documentation

Looking for senior engineers who have worked both with and without AI, who can bridge traditional development and AI-augmented workflows.

## Tech Stack

- **Backend:** Ruby on Rails
- **Frontend:** React, React Native (iOS/Android)
- **Development:** Trunk-based (merge to master, deploy daily to production)
- **Testing:** Unit tests, manual QA, some automated QA, smoke tests in staging
- **Environments:** Personal dev environments (mini-production), staging (mirrors production)

## Major 2026 Initiative

Migrating to microservices architecture for integration with parent company's enterprise stack. Currently a monolith — this will be a year-long effort breaking it into separate services by domain (starting with customer domain). Massive data migration involved.

## AI Tools Landscape

- Started with ChatGPT, moved through Anthropic/Claude, currently using OpenAI, Anthropic, and Google
- Exploring: central skills repository, per-team skills, automated process books, gateway checks before merging

## Your Conversation Highlights

- Discussed spec-driven development, TDD with AI, multi-agent orchestration
- Told the COBOL modernization story (cross-functional collaboration with SMEs, building test harnesses)
- Discussed AI hallucination detection from Scale AI experience
- Connected IBM COBOL → AWS news to the reality that migration is more than just code

## Interview Process

1. ✅ First round (completed)
2. **Coding challenge** — Ruby on Rails app, add a couple endpoints/features, 3–5 hours, AI tools encouraged
3. **Technical interview** — discuss the code you submitted, focused on AI workflow and approach rather than Rails-specific questions

## Success at 90 Days

- Deploy to production within 10–15 days
- Produce documentation, guidelines, and process recommendations for AI-integrated development
- Establish discussions around skills management, automated processes, merge gateways

## Key Notes

- Team structure TBD — starting as product team (embedded), may evolve to platform team
- Nicholas mentioned the coding challenge is Rails but they won't ask Rails-specific questions since you don't have Rails experience
- 24-hour follow-up expected on next steps
