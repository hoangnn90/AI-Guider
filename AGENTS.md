# AGENTS.md — AI Agent Instructions

Guidelines for any AI coding agent (Claude Code, Codex, Cursor, GitHub Copilot, Gemini CLI, Aider, ...). Derived from Karpathy coding principles, Taste, and the Impeccable Rule.

This file is the single source of truth. Tool-specific files (`CLAUDE.md`, `GEMINI.md`) are symlinks to it — edit this file only.

**How to use it:**
- **Part A (General)** applies to every task.
- **Part B (By category)** applies when the task touches that area. Read only the categories you need.
- Project-specific instructions override this file where they conflict.

**Tradeoff:** These guidelines bias toward caution over speed. For trivial tasks, use judgment.

---

# Part A — General (every task)

## 1. Think Before Coding

**Don't assume. Don't hide confusion. Surface tradeoffs.**

Before implementing:
- State your assumptions explicitly. If uncertain, ask.
- If multiple interpretations exist, present them — don't pick silently.
- If a simpler approach exists, say so. Push back when warranted.
- If something is unclear, stop. Name what's confusing. Ask.

## 2. Simplicity First

**Minimum code that solves the problem. Nothing speculative.**

- No features beyond what was asked.
- No abstractions for single-use code.
- No "flexibility" or "configurability" that wasn't requested.
- No error handling for impossible scenarios.
- If you write 200 lines and it could be 50, rewrite it.

Ask yourself: "Would a senior engineer say this is overcomplicated?" If yes, simplify.

## 3. Surgical Changes

**Touch only what you must. Clean up only your own mess.**

When editing existing code:
- Don't "improve" adjacent code, comments, or formatting.
- Don't refactor things that aren't broken.
- Match existing style, even if you'd do it differently.
- If you notice unrelated dead code, mention it — don't delete it.

When your changes create orphans:
- Remove imports/variables/functions that YOUR changes made unused.
- Don't remove pre-existing dead code unless asked.

The test: Every changed line should trace directly to the user's request.

## 4. Goal-Driven Execution

**Define success criteria. Loop until verified.**

Transform tasks into verifiable goals:
- "Add validation" → "Write tests for invalid inputs, then make them pass"
- "Fix the bug" → "Write a test that reproduces it, then make it pass"
- "Refactor X" → "Ensure tests pass before and after"

For multi-step tasks, state a brief plan:
```
1. [Step] → verify: [check]
2. [Step] → verify: [check]
3. [Step] → verify: [check]
```

Strong success criteria let you loop independently. Weak criteria ("make it work") require constant clarification.

## 5. Taste

**Prefer the elegant solution over the merely correct one.**

Good code has taste. Taste means choosing clarity over cleverness, simplicity over flexibility, and honesty over performance theater.

- Use names that reveal intent, not implementation.
- Prefer fewer concepts over more powerful ones.
- Delete everything that doesn't earn its place.
- Ask: "Would a senior engineer be proud to ship this?"

Signs of tasteless code:
- 12-parameter functions → use a config object
- Nested ternary chains → use named variables
- Comments that restate the code → rewrite the code to be clear
- `data`, `info`, `result`, `thing` as names → name the actual concept

**The delete test:** Can any line be removed without breaking behavior? If yes, remove it.

## 6. The Impeccable Rule

**Be impeccable with your word. Every output must be accurate, complete, and traceable.**

An impeccable agent:
1. **Never states something as fact without evidence.** Say "I believe" or "based on the code" when uncertain.
2. **Never delivers half-finished work silently.** If a solution is partial, say so explicitly.
3. **Traces every change.** "I changed X because Y" — not just "I improved it."
4. **Names its limits.** If you don't know, say so. Don't hallucinate an answer.

Impeccable code:
- Does exactly what the name says, no more, no less.
- Has no hidden side effects.
- Fails loudly when preconditions are violated.
- Has tests that prove behavior, not just cover lines.

Impeccable communication:
- State the conclusion first, then the reasoning.
- Be specific: "Line 42 has a race condition" not "there might be threading issues."
- State confidence: high / medium / low. For low confidence, name what's needed to verify.

---

# Part B — By category

## 7. UI/UX

- **Design every state**, not just the happy path: empty, loading, error (with a recovery action), zero results, permission denied, success (with a next step).
- **Clarity & hierarchy:** the primary action and the most important information are the most prominent. One primary action per screen.
- **Consistency:** reuse existing components, spacing, typography and color tokens. No one-off styling.
- **Feedback:** every user action gets a visible response; destructive actions need confirmation, low-risk ones don't.
- **Forms:** validation errors appear inline at the field, not only on submit.
- **Accessibility (WCAG 2.1 AA):** contrast ≥ 4.5:1 (3:1 for large text), visible focus, keyboard navigable, labels/alt text/ARIA, touch targets ≥ 44×44px.
- **Copy:** labels in the user's language, not developer jargon.

## 8. Frontend

- **Responsive by default:** verify mobile, tablet and desktop widths, not just desktop.
- **Render performance:** avoid unnecessary re-renders; memoize only where measured.
- **Bundle size:** no unused imports; prefer lighter dependencies; code-split large routes.
- **Images:** correct size, lazy-loaded, modern formats (WebP/AVIF).
- **Network:** no request waterfalls or over-fetching; use caching; paginate long lists.
- **Core Web Vitals:** keep LCP, CLS and INP in mind for every visible change.
- **Never trust the client:** UI hiding is not access control — the backend must enforce it.

## 9. Backend & API

**Rules:**
- Validate every input at the boundary; return clear, consistent error shapes.
- Enforce authN/authZ on every endpoint; scope every query to the caller's data (prevent IDOR).
- Avoid N+1 queries; index columns used in `WHERE` / `ORDER BY`; select only needed columns.
- Keep transactions short; never hold locks across network calls.
- Migrations are backward-compatible and reversible where possible.

**API design patterns** — consider each; apply only when applicable and really necessary (evaluate with §10.2):

| Pattern | Use when | Limitation / cost |
|---|---|---|
| **Resource-Oriented Design** | Exposing domain entities over an API (nouns + standard methods, consistent URLs) | Awkward for action-heavy workflows; don't force RPC-style operations into fake resources |
| **Pagination** | Any list that can grow unbounded | Offset pagination degrades on large tables and drifts under concurrent writes; cursor pagination can't jump to page N |
| **Filtering & Field Selection** | Clients need subsets of rows or fields; payload size matters | Each filter is a query path to index, validate and secure; arbitrary filters invite slow queries and injection |
| **Idempotency** | Retried writes: payments, orders, webhooks, anything behind a flaky network | Needs an idempotency-key store with TTL and a defined response for replays |
| **Asynchronous API** | Work is slow (> a few seconds), bursty, or must survive restarts (202 + job id, queue, webhook/polling) | Harder to debug and test; needs job status, retries, dead-letter handling and eventual-consistency UX |
| **Versioning** | Public or third-party clients you can't update in lockstep | Every live version is code to maintain; prefer additive, backward-compatible changes first |

## 10. System & Architecture

### 10.1 Principles
- Single responsibility per module; explicit, minimal dependencies; related things grouped together.
- Dependencies point from higher layers to lower layers, never the reverse.
- Shared logic lives in one place — no copy-paste across modules or services.
- No hard-coded values that belong in config, enums or constants.
- Suggest the smallest refactor that fixes the biggest weakness, not a rewrite.

**System patterns** — consider each; apply only when applicable and really necessary:

| Pattern | Use when | Limitation / cost |
|---|---|---|
| **Backend for Frontend (BFF)** | Clients (web, mobile, partner) need very different shapes or auth flows | One more service per client to build, deploy and keep in sync; logic can get duplicated across BFFs |
| **API Gateway** | Many services need one entry point for auth, rate limiting, routing, logging | Extra hop and single point of failure; overkill for a single backend |

### 10.2 Pattern Evaluation (applies to §9 and §10 patterns)

Before choosing (or rejecting) any design pattern, evaluate and state:
- **Good points** — the concrete problem it solves here, not in general.
- **Limitations** — complexity, cost, and what it makes harder.
- **Scale metrics** — total users, concurrent active users, request rate, data volume, growth expectation. Name the numbers (or the assumption) that justify the choice.
- **Security** — authN/authZ placement, data isolation, input validation, rate limiting, replay/abuse risk.
- **Performance** — latency, throughput, query/index cost, payload size, caching.
- **Operability** — monitoring, failure modes, rollback, team ability to maintain it.

If the current scale doesn't justify a pattern, say so and don't add it (§2 Simplicity First) — note the threshold at which it would become necessary.

## 11. Performance

- **Measure before optimizing.** Identify the bottleneck and explain why it is one before changing code.
- Propose one targeted fix per bottleneck, not a full rewrite.
- Rank issues by impact; fix the top ones first.
- Cache repeated expensive work, with a clear invalidation rule.
- State the expected gain and how to verify it (benchmark, profile, query plan).

## 12. Security

**Highest priority in every review.**
- **Access control:** a user must never read or change another user's/tenant's data.
- **Injection:** parameterize every query and command; encode output.
- **Secrets:** never hard-code credentials, keys or tokens; never commit `.env` files; never log secrets or PII.
- **Auth:** brute-force protection, session expiry, secure token handling.
- **Business rules** are enforced server-side, never only in the client.
- **Configuration:** no default credentials, verbose production errors or open CORS.
- **Dependencies:** no known-vulnerable packages; no untrusted downloads.
- **SSRF:** user input must not trigger requests to internal services.
- Check against the OWASP Top 10 for any new endpoint or module.

## 13. Testing & Quality

- Tests assert behavior, not implementation details.
- Cover happy path, invalid input, edge/boundary cases and unauthorized access.
- A test must fail when the code it covers is broken.
- Don't over-mock — test the code, not the mock.
- Use realistic test data and descriptive names (Given/When/Then).
- A test you didn't run is not a pass — report actual results.

**Code review checklist:** correctness, readability, testability, error handling at the right level, no hidden side effects, intent-revealing names, no dead code, manageable complexity.

---

**These guidelines are working if:** fewer unnecessary changes in diffs, fewer rewrites due to overcomplication, clarifying questions come before implementation rather than after mistakes, and every output feels trustworthy.
