# OpenLane Debrief — Q&A Reference Guide

**Interview:** Coding Challenge Debrief (45–60 min Zoom)
**Date:** Friday, March 14, 2026 at 10:00 AM EST
**Interviewer:** Brian O'Hara

---

## What You Built — Quick Summary

Took a starter Rails app (Q&A platform with Users, Questions, Answers, Tenants) and built a full feature set across 7 phases with **zero prior Rails experience**, using AI tools throughout.

| Phase | What You Did | Key Detail |
|-------|-------------|------------|
| 1. Setup | Fixed Ruby version, installed gems, removed deprecated chromedriver, ran migrations, seeded DB, fixed "tenent" → "tenant" typo | Caught starter code bugs before writing any feature code |
| 1b. Gems | `bundle update` — updated 30+ gems | Stayed within version constraints (security patches, no breaking changes) |
| 2. Routes & API | `Api::QuestionsController` with index/show, eager loading, JSON serialization | `includes()` prevents N+1 queries |
| 3. Auth | API key auth via `X-Api-Key` header, `Tenant.authenticate`, `before_action` guard | 401 for missing or invalid key |
| 4. Tracking | Added `request_count` column, atomic `increment!` | Single SQL UPDATE avoids race conditions |
| 5. Dashboard | HTML dashboard with stats and tenant table | MVC pattern, `.count` for efficient SQL |
| 6. Tests | 41 runs, 60 assertions, 0 failures | Integration tests per Rails 5+ best practice |
| 7. Extra Credit | Search with LIKE + Rack middleware rate limiting (100/day + 10s throttle) | Middleware rejects before routing/controllers; `?` placeholder prevents SQL injection |

---

## Q1: Which AI tools did you use? When did AI help vs. fall short?

**Answer:**

"I used Claude Code as my primary development tool and OpenAI as a reviewer — almost like a pull request review. Different models have different blind spots, so cross-checking gave me more confidence before moving to the next phase.

My workflow was deliberate: I broke the challenge into small phases, had Claude Code execute each one, then reviewed the output before moving on. I wasn't just accepting what AI generated — I was verifying it against actual behavior.

For example, when I added API key authentication, there was a validation gotcha. The model validated presence of `api_key` on create, but the key gets generated in a `before_create` callback which runs *after* validation. I had to add `allow_nil` to the validation to fix it. AI may have surfaced that, but I verified it by tracing the actual execution.

Tests were my ground truth. If the tests passed and I understood *why* they passed, I moved on. If something felt off, I dug in. I ended with 41 tests, 60 assertions, 0 failures — that wasn't an accident."

---

## Q2: Why Rack middleware for rate limiting instead of a `before_action`?

**Key Concepts:**

- **`before_action`** = runs inside the controller, *after* Rails routing and framework have loaded. Bouncer inside the club.
- **Rack middleware** = runs *before* Rails even starts processing. Bouncer at the building entrance.

**Answer:**

"Middleware runs earlier in the request pipeline, before the request reaches Rails routing and controllers. That means throttled requests get rejected faster with fewer server resources. I used `Rails.cache` for lightweight counters in the middleware; for this challenge the trade-off of ephemeral data is acceptable, though for a real multi-process deployment I'd move to Redis or another shared cache. This is the standard pattern for rate limiting."

---

## Q3: Why `increment!` instead of read-modify-write?

**Key Concept — Race Conditions:**

Read-modify-write with two concurrent requests:
- Request A reads count: 50
- Request B reads count: 50
- Request A writes: 51
- Request B writes: 51
- **Lost a request. Should be 52.**

`increment!` sends a single atomic SQL: `UPDATE tenants SET request_count = request_count + 1 WHERE id = ?` — the database handles concurrency.

**Answer:**

"increment! generates a single atomic SQL UPDATE rather than the read-modify-write pattern. With read-modify-write, two concurrent requests can read the same value and overwrite each other — a classic race condition. The atomic update lets the database handle concurrency safely. This matters especially for rate limiting because high-traffic endpoints are exactly where race conditions are most likely."

**Follow-up — Why not a transaction?**

"A transaction would also work, but it's heavier machinery than needed for a single counter update. increment! compiles down to one atomic SQL UPDATE, which is the standard Rails pattern for concurrent-safe counters. I'd reach for a transaction if I needed to coordinate multiple related writes together — like incrementing a count AND logging request details AND updating billing."

---

## Q4: Why 401 for both missing and invalid API keys?

**Answer:**

"In this implementation I kept auth failure handling simple: if `Tenant.authenticate` returns nil, the controller responds with 401 unauthorized. That covers both a missing header and a bad API key, and it matches the current tests. I wouldn't use 403 here because 403 usually means the client is authenticated but not allowed to access the resource; in these two cases authentication never succeeded in the first place. If a team wanted finer error semantics, it could distinguish the two cases, but this repo intentionally keeps them under the same failure path."

---

## Q5: No Rails experience — what was hardest?

**Answer:**

"The biggest adjustment was Rails' convention-over-configuration philosophy. Coming from Go and Python where everything is explicit, Rails hides a lot — the schema is auto-generated from migrations, routes map to controllers by naming convention, instance variables magically bridge from controller to view.

Instead of fighting that, I leaned into AI to explain *why* Rails works that way, then verified each piece by tracing actual behavior. For example, when I built the dashboard, I confirmed that setting `@user_count` in the controller actually showed up in the ERB template by checking the rendered HTML.

That pattern — learn the convention, then verify it works — is how I moved through all seven phases."

---

## Q6: If you had another day, what would you add or change?

**Answer (hit three buckets: refactor, tests, features):**

"A few things:

1. **Infrastructure:** I'd swap the current `Rails.cache`-backed throttling for Redis or another shared cache. In this repo the cache store is environment-dependent, so it's fine for a coding challenge but not strong enough for production-grade, multi-process rate limiting.

2. **Scalability:** Add pagination to the questions index endpoint — returning all results doesn't scale.

3. **Testing:** More edge case coverage around rate limiting boundaries — what happens at exactly 100 requests, concurrent request behavior.

4. **Architecture:** Extract auth into an `Api::BaseController` so all future API controllers inherit authentication automatically, while keeping the dashboard public since it inherits directly from `ApplicationController`.

5. **Features:** If there was time, CRUD endpoints for creating questions and answers, which would also mean rethinking the auth model to distinguish between tenant API access and individual user sessions."

---

## Q7: Walk through the full request flow — `GET /api/questions` with an API key

**Answer (correct order matters):**

"1. **Rack middleware (TenantThrottle) runs first.** Before the request reaches Rails routing and controllers, the middleware extracts the API key and checks rate limits against `Rails.cache`. If that API key is over the limit, it short-circuits with a 429 and the request never reaches the controller.

2. **Rails router** maps `GET /api/questions` to `Api::QuestionsController#index`.

3. **`before_action` runs authentication.** It reads the `X-Api-Key` header, calls `Tenant.authenticate`, and if authentication succeeds it immediately calls `track_request!`. Missing key or invalid key → 401.

4. **Controller action executes.** It queries shareable questions with `includes()` for eager loading. If a `search` param exists, it adds a LIKE filter.

5. **JSON response** is built with `as_json` using `only` and `include` to control the output. Response goes back up through the middleware stack to the client."

**Key insight if asked why middleware runs before auth:**

"Why waste time authenticating a request you're going to throttle anyway? A completely fake API key can still get its own throttle bucket in middleware on its first request, and then it gets rejected at the controller layer. Each layer has a single responsibility: middleware handles request volume, controllers handle identity."

---

## Bonus: Auth Architecture — Why not `ApplicationController`?

If they ask about where auth lives and why:

"I put auth on `Api::QuestionsController` directly, which works for a single controller. I didn't put it on `ApplicationController` because the dashboard is a public route — putting auth there would block browser access to the dashboard.

If I had multiple API controllers, I'd create an `Api::BaseController` with the auth `before_action`, and have all API controllers inherit from it:

- `ApplicationController` → no auth (everything inherits from this)
  - `DashboardController` → public
  - `Api::BaseController` → auth lives here
    - `Api::QuestionsController` → inherits auth
    - `Api::UsersController` → inherits auth

Auth is defined once, applied everywhere, and public routes stay unaffected."

---

## Q8: What does `includes()` solve, and what happens without it?

**Key Concept — N+1 Problem:**

Without `includes()`, Rails lazy-loads associations as serialization touches them, so a list of questions with users, answers, and answer users can balloon into many extra queries.
With `includes(:user, answers: :user)`, Rails eager-loads that graph up front in a small fixed number of queries. In this code path, that measured at 4 queries total.

**Answer:**

"includes() solves the N+1 query problem. In this endpoint I'm serializing each question with its user, its answers, and each answer's user. Without eager loading, Rails would keep issuing extra queries as that serialization walks the associations. `includes(:user, answers: :user)` pulls that data up front, keeping the query count bounded and predictable. For an API endpoint that could get hit frequently, that difference in database load is significant."

---

## Q9: Why is the `?` placeholder important in your search query?

**Key Concept — SQL Injection:**

`where("title LIKE ?", "%#{search}%")` → Rails parameterizes the input. The database treats it as data, never as code.

Without it — `where("title LIKE '%#{search}%'")` — a malicious user could send `'; DROP TABLE questions; --` and it gets executed as SQL.

**Answer:**

"The `?` placeholder parameterizes the input so Rails sends the query structure and the user value separately to the database. Without it, if I used string interpolation, a malicious user could inject arbitrary SQL into the search parameter — classic SQL injection. The `?` ensures user input is always treated as data, never as executable code. It's a fundamental security practice for any database query that includes user input."

---

## Q10: How does Rails know to return JSON from the API and HTML from the dashboard?

**Answer:**

"The API controller explicitly uses `render json:` to return JSON responses. The dashboard controller doesn't specify a render call at all — Rails automatically finds the matching view template based on the controller name and action, which is `app/views/dashboard/index.html.erb`, and renders it as HTML. The `Api::` namespace affects the route structure and file organization, but the response format comes down to whether I explicitly render JSON or let Rails default to its view template convention."

---

## Q11: A moment where you were stuck — how long and what did you do?

**Answer:**

"The very first phase — just getting the app to run. I'd never used Rails, so I didn't even know the basic commands. The starter code had multiple issues stacked on top of each other: the Ruby version file didn't match the Gemfile, a deprecated gem was throwing errors on startup, and a system dependency was missing for one of the gems. I used AI to interpret each error message, but I wasn't just running suggested commands blindly — I had it explain *what* each command does and *why* it was needed, so I was building a mental model of how Rails projects are structured. Once the app booted, I ran the tests and found a fixture typo — 'tenent' instead of 'tenant' — which I caught and fixed before writing any feature code. That whole cycle probably took the first hour, but it meant everything after that was built on a solid foundation."

---

## Q12: Why 404 for empty search results instead of 200 with empty array?

**Note:** This is debatable — own the trade-off, don't pretend there's one right answer.

**Answer:**

"The challenge spec asked for an appropriate status code when no results are found, so I went with 404 to signal that nothing matched. That said, I know it's debatable — many production APIs return 200 with an empty array because the request itself succeeded, and 404 can be ambiguous between 'no results' and 'bad endpoint.' If I were building this for a real team, I'd align with whatever convention the existing API uses. The important thing is consistency across endpoints."

---

## Q13: Did you write tests first or after features? Why?

**Context:** Starter code had test file stubs but no real assertions. Fixtures had placeholder `MyString` data. You wrote features first (Phases 2-5), then tests (Phase 6).

**Answer:**

"The starter code had test file stubs but no real assertions — the fixtures were placeholder data and the model tests were empty. I wrote the features first, then built out the test suite in Phase 6. I went features-first because I was learning Rails as I went — writing tests for a framework I didn't understand yet would have slowed me down without adding confidence. Once the features worked and I had a mental model of how Rails testing works, I rewrote the fixtures with meaningful data and wrote integration tests that exercise the full request cycle — auth, search, rate limiting, the dashboard. If I were in a codebase I already knew, I'd lean more toward test-first, but in this context, getting working code and then validating it was the more productive sequence."

---

## Q14: Unit tests vs integration tests — which did you write more of and why?

**Key Concepts:**

- **Unit tests** = test one piece in isolation (model method, single class)
- **Integration tests** = test the full request flowing through the system (routing → middleware → auth → controller → database → response)

**Answer:**

"Unit tests isolate a single piece — I wrote those for model validations and the middleware logic. Integration tests exercise the full request cycle — routing, auth, database queries, response formatting all together. I wrote more integration tests because for this challenge, proving the features work end-to-end was more valuable than testing every individual method. One integration test that sends an authenticated request and verifies the JSON output covers more surface area than several unit tests would separately. In a larger production codebase, you'd want a heavier base of unit tests for fast feedback, but for a time-boxed challenge, integration tests gave me the most confidence per test written."

---

## Q15: How would you teach/document this AI workflow for other engineers?

**Context:** This role's mandate includes establishing org-wide AI development processes.

**Answer:**

"I'd document it as a phased workflow that any engineer can follow when using AI on unfamiliar code. First — plan before you prompt. I broke the challenge into seven phases before writing any code. AI is most useful when you give it a scoped, well-defined task rather than 'build me the whole thing.' Second — use AI to learn, not just to generate. Before implementing auth, I had AI explain how Rails authentication patterns work so I understood what it was producing. Third — verify, don't trust. I ran every piece of generated code, checked the actual behavior, and used a second AI model as a reviewer — like a pull request with a different perspective. Fourth — tests as ground truth. Every phase ended with tests passing. If tests break, you stop and fix before moving on.

For rolling this out to a team, I'd create a lightweight guide with those four principles, run a workshop where engineers pair with AI on a small task using the workflow, and then collect feedback on what worked and what didn't. The goal isn't to prescribe specific tools — it's to establish a discipline around how you interact with AI so the output is reliable and the engineer stays in control."

---

## Q16: Why organize the API under an `Api::` namespace?

**Answer:**

"The `Api::` namespace gives me three things. First, clean URL structure — all API endpoints live under `/api/`, which is the standard convention consumers expect. Second, code organization — API controllers are in their own directory, separate from public-facing controllers like the dashboard. Third, and most importantly, it creates a natural auth boundary. If I add more API endpoints, I can introduce an `Api::BaseController` with authentication, and every controller in that namespace inherits it automatically. Public routes outside the namespace stay unaffected. Without the namespace, I'd be mixing authenticated and public actions in the same controller hierarchy, which gets messy fast."

---

## Q17: How would you approach OpenLane's microservices migration?

**Context:** OpenLane is migrating their Rails monolith to microservices in 2026, starting with the customer domain. Connect to your real experience.

**Answer:**

"The challenge showed me how tightly coupled Rails conventions make everything — models, views, and controllers are all intertwined, which is exactly why monolith extraction is hard. I'd start by mapping domain boundaries — which models and tables belong together logically. Then I'd use a strangler fig pattern: put a routing layer in front of the monolith, extract one domain at a time into its own service, and gradually shift traffic over. The database is always the hardest part — you need to untangle shared tables into per-service data stores, which means dealing with data migration and eventual consistency.

From my experience at IBM doing COBOL modernization, I learned that migration is never just a code problem — it's a people and process problem. You need domain experts involved, you need strong test coverage before you start extracting, and you need observability from day one so you can verify the new services behave identically to the monolith. CI/CD per service is also critical — at The Knot I built a unified pipeline platform that supported multiple services independently, which is exactly the kind of infrastructure you need when you're breaking apart a monolith."

**Key term — Strangler Fig Pattern:**

Named after a vine that grows around a tree and eventually replaces it. You build new services alongside the monolith, gradually routing traffic to them, until the monolith is empty and can be retired. This avoids the risk of a big-bang rewrite.

---

## Meta-Message to Convey Throughout

> "I can ramp quickly on unfamiliar technology by leveraging AI tools intelligently, breaking work into validated phases, asking the right questions, and verifying output rather than blindly trusting it."
