# AI-Guider

> A practical guide for using AI to build better software: UI/UX, Performance, Security, and Software Quality.

---

## Use It With Any AI Agent

This README is the human guide. [`AGENTS.md`](AGENTS.md) is the condensed version written for AI coding agents — it works with any agent, not only one vendor's:

- **Part A — General:** rules for every task (think first, simplicity, surgical changes, goal-driven, taste, impeccable).
- **Part B — By category:** UI/UX, Frontend, Backend & API, System & Architecture, Performance, Security, Testing & Quality. An agent reads only the categories its task touches.

**Setup:** copy `AGENTS.md` into your project root. Many agents (e.g. Codex, Cursor, GitHub Copilot's coding agent) read `AGENTS.md` directly. For agents that look for their own filename, symlink it so there is one source of truth:

```bash
ln -s AGENTS.md CLAUDE.md   # Claude Code
ln -s AGENTS.md GEMINI.md   # Gemini CLI
```

This repo ships both symlinks already. Edit `AGENTS.md` only.

---

## Table of Contents

1. [AI Coding Guidelines (Karpathy Principles)](#1-ai-coding-guidelines-karpathy-principles)
2. [UI/UX Critique with AI](#2-uiux-critique-with-ai)
3. [Performance Guidance](#3-performance-guidance)
4. [Security Guidance](#4-security-guidance)
5. [Software Quality](#5-software-quality)
6. [Taste — The Aesthetic Standard](#6-taste--the-aesthetic-standard)
7. [The Impeccable Rule](#7-the-impeccable-rule)
8. [Effective AI Prompting Patterns](#8-effective-ai-prompting-patterns)
9. [API & System Design Patterns](#9-api--system-design-patterns)

---

## 1. AI Coding Guidelines (Karpathy Principles)

Derived from [Andrej Karpathy's observations](https://x.com/karpathy/status/2015883857489522876) on LLM coding pitfalls.

> "The models make wrong assumptions on your behalf and just run along with them without checking. They don't manage their confusion, don't seek clarifications, don't surface inconsistencies, don't present tradeoffs, don't push back when they should."

### 1.1 Think Before Coding

**Don't assume. Don't hide confusion. Surface tradeoffs.**

Before asking AI to implement anything:
- State your assumptions explicitly in the prompt. If uncertain, ask first.
- If multiple interpretations exist, ask the AI to present them — don't let it pick silently.
- If a simpler approach exists, ask the AI to say so and push back when warranted.
- If something is unclear, stop. Name what's confusing. Ask for clarification.

**Prompt template:**
```
Before implementing, state:
1. Your assumptions about [X]
2. Any ambiguities you see
3. The simplest possible approach
4. Any tradeoffs vs. alternative approaches
```

### 1.2 Simplicity First

**Minimum code that solves the problem. Nothing speculative.**

- No features beyond what was asked.
- No abstractions for single-use code.
- No "flexibility" or "configurability" that wasn't requested.
- No error handling for impossible scenarios.
- If the AI writes 200 lines and it could be 50, ask it to rewrite.

**Check:** "Would a senior engineer say this is overcomplicated?" If yes, simplify.

### 1.3 Surgical Changes

**Touch only what you must. Clean up only your own mess.**

When asking AI to edit existing code:
- Instruct it not to "improve" adjacent code, comments, or formatting.
- Tell it not to refactor things that aren't broken.
- Ask it to match existing style, even if it would do it differently.
- If it notices unrelated dead code, it should mention it — not delete it.

**The test:** Every changed line should trace directly to your request.

### 1.4 Goal-Driven Execution

**Define success criteria. Loop until verified.**

Transform tasks into verifiable goals:

| Instead of... | Transform to... |
|---|---|
| "Add validation" | "Write tests for invalid inputs, then make them pass" |
| "Fix the bug" | "Write a test that reproduces it, then make it pass" |
| "Refactor X" | "Ensure tests pass before and after" |

For multi-step tasks, ask for a brief plan:
```
1. [Step] → verify: [check]
2. [Step] → verify: [check]
3. [Step] → verify: [check]
```

> LLMs are exceptionally good at looping until they meet specific goals. Give success criteria, not just instructions.

---

## 2. UI/UX Critique with AI

Use AI as a systematic UI reviewer — not just for aesthetics, but for usability, accessibility, and consistency.

### 2.1 The Critique Framework

When asking AI to critique a UI, always provide:
1. **Context** — Who is the user? What is the goal of this screen?
2. **Screenshot or description** — What does the UI currently look like?
3. **Evaluation dimensions** — Which of these to focus on:

| Dimension | What to check |
|---|---|
| **Clarity** | Can the user immediately understand what to do? |
| **Hierarchy** | Is the most important information visually prominent? |
| **Consistency** | Do UI elements follow the same patterns throughout? |
| **Affordance** | Do interactive elements look clickable/tappable? |
| **Feedback** | Does the UI respond visibly to user actions? |
| **Error prevention** | Does the UI prevent mistakes before they happen? |
| **Accessibility** | Color contrast, font size, keyboard navigation, screen reader support |
| **Mobile/responsive** | Does it work on all viewport sizes? |
| **Loading states** | Are empty states, loading, and error states designed? |
| **Cognitive load** | How many decisions must the user make at once? |

### 2.2 Critique Prompt Templates

**General critique:**
```
Critique this UI from a UX perspective. The user is [description] and their goal is [goal].
Evaluate: clarity, visual hierarchy, consistency, and accessibility.
For each issue: describe the problem, explain the impact on the user, and suggest a fix.
```

**Accessibility audit:**
```
Audit this UI for WCAG 2.1 AA compliance. Check:
- Color contrast ratios (minimum 4.5:1 for normal text, 3:1 for large text)
- Focus indicators for keyboard navigation
- Alt text / ARIA labels for non-text content
- Touch target sizes (minimum 44x44px)
- Form label associations
List each violation with its WCAG criterion and a concrete fix.
```

**Component consistency review:**
```
Review these UI components for design consistency. Check:
- Spacing and padding alignment
- Typography scale usage
- Color palette adherence
- Border radius uniformity
- Icon style consistency
List inconsistencies and suggest which pattern to standardize on.
```

**Information architecture review:**
```
Review the navigation and information architecture of this app.
User goal: [goal]. Current structure: [describe].
Identify: navigation dead-ends, unclear labels, missing feedback, and cognitive overload points.
```

### 2.3 Common UI Anti-Patterns to Catch

Ask AI to check for these specifically:

- **Jargon labels** — buttons/labels that only make sense to developers
- **Modal overuse** — confirmation dialogs for low-risk actions
- **Hidden affordances** — interactive elements that don't look interactive
- **Inconsistent primary actions** — different button styles for the same action type
- **Missing empty states** — tables/lists with no "no results" design
- **Unhandled loading** — operations with no spinner/skeleton/progress indicator
- **Form error UX** — errors shown only on submit, not inline
- **Cluttered density** — too much information without visual grouping

### 2.4 Before/After Critique Workflow

```
1. Describe the current UI: "This screen shows [X] with [components]"
2. State the user goal: "The user is trying to [accomplish Y]"
3. Ask for: specific problems → impact → fix
4. Ask AI to rewrite the screen description with improvements applied
5. Ask AI to list what changed and why
```

---

## 3. Performance Guidance

### 3.1 Ask AI to Profile Before Optimizing

Never ask AI to optimize blindly. Always frame it as:

```
Here is the [function/query/component]. It's slow in [specific scenario].
Before suggesting optimizations, identify the likely bottleneck and explain why.
Then suggest one targeted fix, not a full rewrite.
```

### 3.2 Frontend Performance Checklist

Ask AI to review for:

| Area | What to check |
|---|---|
| **Bundle size** | Unused imports, large dependencies with lighter alternatives |
| **Render performance** | Unnecessary re-renders, missing memoization |
| **Images** | Unoptimized sizes, missing lazy loading, wrong formats (use WebP/AVIF) |
| **Network** | Waterfall requests, missing caching headers, over-fetching |
| **Core Web Vitals** | LCP, CLS, INP — ask AI to identify likely contributors |
| **JavaScript** | Blocking scripts, long tasks, synchronous operations on main thread |

**Prompt:**
```
Review this [React component / page / API call] for performance issues.
Identify the top 3 issues by impact, explain why each is a problem,
and suggest the minimal change needed to fix each one.
```

### 3.3 Backend Performance Checklist

Ask AI to review for:

| Area | What to check |
|---|---|
| **N+1 queries** | Loops that execute a query per iteration |
| **Missing indexes** | Columns used in WHERE/ORDER BY without indexes |
| **Over-fetching** | SELECTs that fetch unused columns |
| **Transaction scope** | Unnecessarily long transactions holding locks |
| **Caching** | Repeated expensive queries without caching |
| **Pagination** | Missing LIMIT/OFFSET or cursor-based pagination on large tables |

---

## 4. Security Guidance

### 4.1 Security Review Prompt

```
Review this code for security vulnerabilities. Check specifically for:
- Injection (SQL, command, LDAP, XPath)
- Authentication/authorization bypass
- Insecure direct object references (IDOR)
- Sensitive data exposure (logging secrets, leaking PII)
- Missing input validation or output encoding
- Insecure dependencies
For each finding: describe the vulnerability, its CVSS severity, and a concrete fix.
```

### 4.2 OWASP Top 10 Checklist

Ask AI to verify each endpoint/module for:

1. **A01 Broken Access Control** — Can a user access another user's data?
2. **A02 Cryptographic Failures** — Is sensitive data encrypted in transit and at rest?
3. **A03 Injection** — Are all inputs parameterized / sanitized?
4. **A04 Insecure Design** — Are business logic rules enforced server-side?
5. **A05 Security Misconfiguration** — Default credentials, verbose errors, open CORS?
6. **A06 Vulnerable Components** — Are dependencies up to date?
7. **A07 Auth Failures** — Brute force protection, session expiry, secure tokens?
8. **A08 Data Integrity Failures** — Is deserialized data validated?
9. **A09 Logging/Monitoring Failures** — Are security events logged without leaking secrets?
10. **A10 SSRF** — Can user input cause server-side requests to internal services?

### 4.3 Secrets and Configuration

Ask AI to scan for:
- Hardcoded credentials, API keys, or tokens in source code
- `.env` files committed to version control
- Secrets logged to console or error responses
- Database URLs with credentials embedded in code

**Prompt:**
```
Scan this codebase/file for hardcoded secrets, exposed credentials,
or insecure configuration. List every occurrence with file:line and severity.
```

---

## 5. Software Quality

### 5.1 Code Review Checklist

Ask AI to review code against:

| Category | Checks |
|---|---|
| **Correctness** | Does it do what it claims? Edge cases handled? |
| **Readability** | Can a new developer understand it in 5 minutes? |
| **Testability** | Can this be unit tested without mocks of mocks? |
| **Error handling** | Are errors caught at the right level and surfaced clearly? |
| **Side effects** | Does it mutate shared state unexpectedly? |
| **Naming** | Do names reflect intent, not implementation? |
| **Dead code** | Are there unreachable branches or unused variables? |
| **Complexity** | Is cyclomatic complexity manageable (aim for < 10 per function)? |

### 5.2 Test Quality

Ask AI to review tests for:

```
Review these tests for quality. Check:
- Do tests assert behavior, not implementation details?
- Are test names descriptive (Given/When/Then format)?
- Are there negative cases and edge cases?
- Are mocks overused (testing the mock, not the code)?
- Is test data realistic?
- Do tests fail for the right reason when the code breaks?
```

### 5.3 Architecture Review

```
Review this module/service architecture. Check:
- Single Responsibility: does each class/module do one thing?
- Coupling: are dependencies explicit and minimal?
- Cohesion: are related things grouped together?
- Testability: can components be tested in isolation?
- Dependency direction: do higher layers depend on lower layers, not vice versa?
Suggest the smallest refactor that improves the biggest weakness.
```

---

## 6. Taste — The Aesthetic Standard

**Good code has taste. Ask AI to have it too.**

Taste is the ability to recognize the elegant solution over the merely correct one. It is learned through exposure to well-crafted code and ruthless editing.

### 6.1 What Taste Means in Code

| Tasteless | Tasteful |
|---|---|
| `getUserByIdFromDatabase(id)` | `findUser(id)` |
| 12 parameters in a function | A single config object |
| A 400-line service class | Four 80-line focused modules |
| `if (condition === true)` | `if (condition)` |
| Nested ternary chains | Named variables with clear intent |
| Comments that restate the code | Code that explains itself |
| `data`, `info`, `result`, `thing` | `invoice`, `userProfile`, `paymentResult` |

### 6.2 Asking AI for Tasteful Code

```
This code is correct but feels clunky. Rewrite it with better taste:
- Simpler names that reveal intent
- Fewer lines without losing clarity
- No clever tricks that require explanation
- Matches the style of a senior engineer who values readability
Show the before/after and explain each change.
```

### 6.3 Taste in UI Design

Tasteful UI is quiet. It doesn't show off. Ask AI:

```
Review this design for visual noise and unnecessary complexity:
- Remove decorative elements that don't add meaning
- Reduce the number of font sizes to 3-4 max
- Ensure whitespace is used to group related items
- Make the primary action obvious without making it loud
- Check that color is used purposefully, not decoratively
```

### 6.4 The "Delete Test"

A tasteful solution survives the delete test:
```
Can any line in this code be deleted without breaking behavior?
Can any UI element be removed without reducing usability?
If yes, delete it.
```

---

## 7. The Impeccable Rule

**Be impeccable with your word. In code, in communication, in outputs.**

Adapted from "The Four Agreements" by Don Miguel Ruiz: the first agreement is *be impeccable with your word* — only say what you mean and mean what you say. Applied to AI-assisted development:

### 7.1 What Impeccable Means for AI Agents

An AI agent must be impeccable in four ways:

1. **Accuracy** — Never state something as fact without evidence. Say "I believe" or "based on the code" when uncertain.
2. **Completeness** — Don't deliver half-finished work. If a solution is partial, say so explicitly.
3. **Traceability** — Every change should be explainable. "I changed X because Y" — not just "I improved it."
4. **Honesty about limits** — If the AI doesn't know, it says so. It doesn't hallucinate an answer.

### 7.2 Impeccable Code

Impeccable code:
- Does exactly what the name says, no more, no less.
- Has no hidden side effects.
- Fails loudly when its preconditions are violated.
- Leaves the codebase cleaner than it found it.
- Has tests that prove the behavior, not just cover the lines.

**Prompt to enforce:**
```
Review this implementation for impeccability:
- Does every function name accurately describe what it does?
- Are there any hidden side effects not implied by the name?
- Does it fail with a clear error when given invalid input?
- Is there any behavior that's not covered by a test?
List each violation and a fix.
```

### 7.3 Impeccable Communication

When AI presents findings, plans, or code:
- **Be specific** — "Line 42 has a race condition" not "there might be threading issues"
- **Be complete** — List all findings, not just the easy ones
- **Be honest** — "I'm not sure about X, you should verify" beats confident misinformation
- **Be direct** — State the conclusion first, then the reasoning

**Enforce this with:**
```
When you give me a finding or recommendation:
1. State the conclusion first (one sentence)
2. Provide evidence (file:line, log excerpt, test result)
3. State your confidence level (high/medium/low)
4. If low confidence, list what you'd need to verify it
```

### 7.4 Impeccable Commit Messages

A commit is a promise about what changed and why:

```
Ask AI to write a commit message that:
- Uses the imperative mood ("Add", "Fix", "Remove" — not "Added", "Fixed")
- Describes WHY the change was made, not just WHAT changed
- References the ticket/issue if applicable
- Is complete: someone reading it in 2 years can understand the intent
- Does not say "misc fixes", "updates", or "WIP"
```

---

## 8. Effective AI Prompting Patterns

### 8.1 The Decomposition Pattern

Break complex tasks before delegating:
```
I want to [goal]. Break this into 3-5 independent sub-tasks.
For each sub-task: describe it, its inputs/outputs, and success criteria.
Do not implement yet — just the breakdown.
```

### 8.2 The Constraint Pattern

Prevent over-engineering:
```
Implement [X]. Constraints:
- Maximum [N] lines of new code
- No new dependencies
- No new abstractions unless reused 3+ times
- Match the existing code style in [file]
```

### 8.3 The Explain-First Pattern

Understand before trusting:
```
Before writing code: explain your approach in plain English.
What problem does this solve? What are the tradeoffs vs. alternatives?
I'll confirm the approach before you implement.
```

### 8.4 The Verification Pattern

Close the loop:
```
After implementing, verify:
1. Does the implementation match all requirements in [spec]?
2. What edge cases are NOT handled?
3. What would break this in production?
4. Write a one-line test for the most important behavior.
```

### 8.5 The Diff Pattern

Keep changes small and reviewable:
```
Show me only the diff (changed lines) with a brief explanation of each change.
Do not show unchanged context beyond 3 lines.
```

### 8.6 The Taste Check Pattern

Ask for the tasteful version:
```
This works. Now make it tasteful:
- Simpler names, fewer lines, no clever tricks
- Would a senior engineer be proud to ship this?
- Show before/after with your reasoning.
```

### 8.7 The Impeccable Check Pattern

Before finalizing any output:
```
Before you give me the final answer:
1. Is every statement in it accurate and grounded in evidence?
2. Is anything missing that I need to know?
3. Are there any claims you're not confident about?
If yes to #3, flag them explicitly.
```

---

## 9. API & System Design Patterns

Every design or implementation should consider these patterns — and apply one only when it is applicable and really necessary. A pattern added "just in case" is complexity without a payoff.

### 9.1 Patterns to Evaluate

**Backend & API patterns:**

| Pattern | Use when | Limitation / cost |
|---|---|---|
| **Resource-Oriented Design** | Exposing domain entities over an API (nouns + standard methods, consistent URLs) | Awkward for action-heavy workflows; don't force RPC-style operations into fake resources |
| **Pagination** | Any list that can grow unbounded | Offset pagination degrades on large tables and drifts under concurrent writes; cursor pagination can't jump to page N |
| **Filtering & Field Selection** | Clients need subsets of rows or fields; payload size matters | Each filter is a query path to index, validate and secure; arbitrary filters invite slow queries and injection |
| **Idempotency** | Retried writes: payments, orders, webhooks, anything behind a flaky network | Needs an idempotency-key store with TTL and a defined response for replays |
| **Asynchronous API** | Work is slow (> a few seconds), bursty, or must survive restarts (202 + job id, queue, webhook/polling) | Harder to debug and test; needs job status, retries, dead-letter handling and eventual-consistency UX |
| **Versioning** | Public or third-party clients you can't update in lockstep | Every live version is code to maintain; prefer additive, backward-compatible changes first |

**System & architecture patterns:**

| Pattern | Use when | Limitation / cost |
|---|---|---|
| **Backend for Frontend (BFF)** | Clients (web, mobile, partner) need very different shapes or auth flows | One more service per client to build, deploy and keep in sync; logic can get duplicated across BFFs |
| **API Gateway** | Many services need one entry point for auth, rate limiting, routing, logging | Extra hop and single point of failure; overkill for a single backend |

### 9.2 Evaluation Checklist

Before choosing (or rejecting) a pattern, state:
- [ ] **Good points** — the concrete problem it solves here, not in general
- [ ] **Limitations** — complexity, cost, and what it makes harder
- [ ] **Scale metrics** — total users, concurrent active users, request rate, data volume, growth expectation (name the numbers or the assumption)
- [ ] **Security** — authN/authZ placement, data isolation, input validation, rate limiting, replay/abuse risk
- [ ] **Performance** — latency, throughput, query/index cost, payload size, caching
- [ ] **Operability** — monitoring, failure modes, rollback, team ability to maintain it

If the current scale doesn't justify a pattern, skip it and note the threshold at which it would become necessary.

### 9.3 Design Pattern Review Prompt

```
Review this design against these patterns: Resource-Oriented Design, Pagination,
Filtering & Field Selection, Idempotency, BFF, API Gateway, Asynchronous API, Versioning.
For each one that applies:
1. Why it is needed here (the concrete problem)
2. Its limitations and cost
3. The scale it assumes: total users, concurrent active users, request rate
4. Security and performance impact
For each one you reject, say why in one line.
Do not recommend a pattern the current scale doesn't justify.
```

---

## How to Know Your AI Workflow Is Working

These guidelines are working if you see:

- **Fewer unnecessary changes in diffs** — only requested changes appear
- **Fewer rewrites due to overcomplication** — code is simple the first time
- **Clarifying questions come before implementation** — not after mistakes
- **Clean, minimal PRs** — no drive-by refactoring or "improvements"
- **UI critiques are actionable** — specific problems, not vague feedback
- **Security reviews catch real issues** — not just style complaints
- **Tests actually fail when code is broken** — not just green for green's sake
- **AI outputs feel trustworthy** — findings are grounded, confidence is stated, limits are named

---

## Contributing

Add new guidance sections, prompting patterns, or real-world examples via pull request. Keep each section:
- **Concrete** — specific prompts, not abstract advice
- **Actionable** — what to do, not just what to avoid
- **Evidence-based** — link to source material where available

## License

MIT
