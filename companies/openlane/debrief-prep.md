__INTERVIEW PREP GUIDE__

OpenLane — AI\-First Software Engineer

Coding Challenge Debrief — 45\-60 Minutes via Zoom

Friday, March 14, 2026 at 10:00 AM EST

# __What This Interview Is__

This is NOT a Rails quiz\. They know you had no prior Rails experience\. The entire point of the coding challenge was to test how you use AI tools to be productive in an unfamiliar codebase\. The debrief is about:

- __AI tools used: __Which tools, how you prompted them, when they helped vs\. didn’t
- __Challenges encountered: __What was hard, how you worked through it
- __Way of work: __Your process, decision\-making, how you approached an unfamiliar framework
- __Code quality: __They’ll walk through your code and ask about design decisions
- __What you’d improve: __If you had more time, what would you do differently

The meta\-message they want to hear: “I can ramp quickly on unfamiliar technology by leveraging AI tools intelligently, asking the right questions, and validating output rather than blindly trusting it\.”

# __What You Built — Quick Reference__

Review the PROGRESS\.md in detail\. Here’s a summary of each phase and the key talking points for each\.

__Phase__

__What You Did__

__Key Talking Point__

__Phase 1: Setup__

Fixed Ruby version, installed gems, removed deprecated chromedriver, ran migrations, seeded DB, fixed fixture typo

Identified and fixed starter code issues \(typo: tenent → tenant\) before writing any feature code\. Shows attention to detail\.

__Phase 1b: Gems__

bundle update — updated 30\+ gems to latest compatible versions

Stayed within version constraints rather than jumping major versions\. Pragmatic: security patches without breaking changes\.

__Phase 2: Routes & API__

Defined routes, created Api::QuestionsController with index/show, eager loading, JSON serialization

Used includes\(\) for eager loading to prevent N\+1 queries\. Controlled JSON output with as\_json \+ only/include\.

__Phase 3: Auth__

API key authentication via X\-Api\-Key header, Tenant\.authenticate class method, before\_action guard

401 vs 403 distinction\. Validation gotcha: allow\_nil because before\_create runs after validation\.

__Phase 4: Tracking__

Added request\_count column, atomic increment\! for counting

increment\! is atomic \(single SQL UPDATE\) vs read\-modify\-write which has race conditions\.

__Phase 5: Dashboard__

HTML dashboard with stats and tenant table

MVC pattern: controller loads data into @variables, view renders\. Used \.count for efficient SQL COUNT\(\*\)\.

__Phase 6: Tests__

41 runs, 60 assertions, 0 failures\. Model tests, controller integration tests, middleware tests

Used integration tests \(not ActionController::TestCase\) per Rails 5\+ best practice\. Full request cycle testing\.

__Phase 7: Extra Credit__

Search query param with LIKE, Rack middleware rate limiting \(100/day \+ 10s throttle\)

Middleware runs before controllers = faster rejection\. SQL injection safety via ? placeholder\. HTTP 429 for throttle\.

# __Your AI Workflow — This Is the Core of the Debrief__

They want to understand HOW you used AI, not just that you used it\. Be specific and honest\.

## __Tools You Used__

- __Claude: __Primary AI tool\. Used for understanding Rails conventions, generating scaffolding, explaining error messages, and drafting code that you then reviewed and modified\.
- __Documentation: __Rails guides, Bundler docs, boto3\-style API references for gems\.

## __How You Used AI — The Workflow__

__1\. Understand before generating: __Started each phase by reading the challenge requirements, then asked AI to explain the relevant Rails concepts \(routes, namespaces, migrations, middleware\) before writing any code\.

__2\. Spec\-driven prompting: __Gave AI the context of what existed \(models, routes, schema\) and what needed to be built\. Described the “what” and let it help with the “how\.”

__3\. Incremental validation: __Didn’t generate the entire app at once\. Built phase by phase, running tests and manual checks after each phase before moving on\.

__4\. Review, don’t copy\-paste: __Treated AI output like a junior’s PR\. Read every line, understood why it worked, fixed issues \(like the allow\_nil validation gotcha\) when the generated code didn’t handle edge cases\.

__5\. Documentation as learning: __Wrote the PROGRESS\.md as you went — documenting what you learned and why decisions were made\. This isn’t just a deliverable; it’s how you internalized the framework\.

## __Where AI Helped Most__

- Rails conventions and “magic” \(naming conventions, file locations, association syntax\)
- Generating boilerplate \(migrations, test fixtures, controller scaffolding\)
- Explaining error messages in Rails\-specific context
- Suggesting design patterns \(eager loading, middleware vs before\_action\)

## __Where AI Was Less Helpful / Where You Had to Think__

- The allow\_nil validation gotcha — AI initially generated code that failed because it didn’t account for before\_create callback timing
- Understanding the request pipeline order \(middleware → routes → before\_action → controller\)
- Deciding WHAT to build vs HOW — AI can’t tell you whether to put rate limiting in middleware or controller
- Test design: choosing which scenarios to test required understanding the business logic, not just code generation
- The deprecated chromedriver\-helper — AI suggested it might be an issue but you had to debug the actual error to confirm

# __Likely Questions & Answers__

## __"Walk us through how you approached the challenge\."__

*“I started by getting the project running — fixed the Ruby version mismatch, removed a deprecated gem, ran migrations and seeds\. I used AI to help me understand Rails conventions since I’d never used the framework\. Then I worked through each phase incrementally: routes and API controller, authentication, request tracking, dashboard, tests, and extra credit\. For each phase, I’d read the requirements, ask AI to explain the relevant Rails patterns, generate initial code, review it carefully, test it, and document what I learned\. The PROGRESS\.md was both a deliverable and my learning tool\.”*

## __"How did you decide what AI tools to use and when?"__

*“I primarily used Claude because it’s strong at explaining concepts and generating code with context\. My pattern was: when I needed to understand a Rails concept, I’d ask AI to explain it\. When I needed to write code, I’d give it the existing code context and describe what I needed\. I treated the output like a PR review — I read every line and made sure I understood why it worked\. The key was using AI as a force multiplier for learning, not as a replacement for understanding\.”*

## __"What was the hardest part?"__

*“The validation timing gotcha was the trickiest\. When creating a new tenant, Rails runs validations before the before\_create callback that generates the API key\. So the uniqueness validation on api\_key would fail because it was still nil\. The fix was adding allow\_nil: true to the validation\. AI initially generated code without this, so I had to debug and understand the Rails callback lifecycle to fix it\. That’s a good example of where AI gets you 80% there but the last 20% requires actually understanding the framework\.”*

## __"What would you do differently with more time?"__

- Add pagination to the API \(offset/limit or cursor\-based\)
- Use Jbuilder templates instead of as\_json for more complex JSON serialization
- Add API versioning \(/api/v1/\) for future\-proofing
- Implement proper API key rotation for tenants
- Add a Redis\-backed cache for the rate limiter instead of Rails\.cache \(ephemeral memory store\)
- Add request logging and structured error responses
- Switch from SQLite to PostgreSQL for production readiness

## __"How do you evaluate whether AI output is correct?"__

*“Same way I’d review any code: does it handle edge cases, does it follow the project’s conventions, does it pass tests, and do I understand why it works? I run the tests, I test manually with curl, and I read the code line by line\. The PROGRESS\.md reflects that — for every phase, I documented not just what the code does but the design decisions behind it\. If I can’t explain why a line is there, I don’t ship it\.”*

## __"Tell us about your way of work\."__

*“Incremental and documentation\-driven\. I built each phase fully before moving to the next, including tests\. I documented as I went rather than at the end, because writing about what I learned helped me internalize it\. I also kept a clean separation between understanding and implementation — I didn’t start coding each phase until I understood the underlying Rails concepts\. This approach meant I was slower in the first few phases but significantly faster by the end because the patterns were stacking\.”*

# __Key Concepts to Know Cold__

If they probe on Rails knowledge, these are the concepts that came up in your challenge:

- __N\+1 queries: __Loading associations one at a time instead of in bulk\. Fix: includes\(\) for eager loading\.
- __before\_action: __Controller\-level middleware\. Runs before every action\. If it renders/returns, the action is skipped\.
- __Rack middleware: __Sits in the request pipeline before Rails\. Has initialize\(app\) and call\(env\)\. Can short\-circuit with \[status, headers, body\]\.
- __Request pipeline order: __Web server → Rack middleware → Routes → before\_action → Controller action → View
- __Migrations: __Ruby files that define schema changes\. Run with rails db:migrate\. Never edit after they’ve been run in production\.
- __Fixtures: __YAML test data loaded into test DB\. Reference by name: tenants\(:acme\)\.
- __increment\! vs read\-modify\-write: __increment\! is atomic \(single SQL UPDATE\)\. Safe for concurrent access\. Read\-modify\-write has race conditions\.
- __401 vs 403: __401 = who are you? \(bad/missing credentials\)\. 403 = I know who you are but you can’t do this\.
- __CSRF protection: __Rails protects against cross\-site request forgery by default\. API endpoints use skip\_forgery\_protection because they use token auth instead\.
- __SQL injection: __Always use where\("col LIKE ?", value\) with placeholders\. Never interpolate user input into SQL strings\.

# __Questions to Ask Them__

- “How is the team thinking about the microservices migration for 2026? What’s the strategy for decomposing the Rails monolith?”
- “What does AI tool adoption look like across the engineering org today? Are there established standards or is it still exploratory?”
- “What would success look like for this role in the first 90 days?”
- “How does the distributed team coordinate across time zones? What’s the async vs sync balance?”
- “You mentioned trunk\-based development — how do you handle feature flags and rollback in that model?”

# __Final Reminders__

- Have your PROGRESS\.md open during the call — it’s your reference guide for design decisions and key concepts\.
- Have the actual code \(GitHub repo\) open in another tab so you can screen\-share and walk through it if asked\.
- This is a 45\-60 minute debrief, not a quiz\. It’s conversational\. Be confident about what you built\.
- Your bilingual background matters to them — the team is distributed across Latin America\. Don’t forget to mention it if relevant\.
- Connect your AI workflow to the role: “This challenge was essentially a microcosm of the job — using AI to ramp quickly and ship in an unfamiliar codebase\.”

