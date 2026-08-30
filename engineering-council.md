# THE COUNCIL — operating manual

Extended reference for SKILL.md §4: full role cards, severity policy,
defensive-security protocol, and an example verdict.

**Harness note.** H1/H2 (the agent can read files): the council reviews the
*actual* diff/files — every finding cites `file:line`. In H2, present the
council output as one approval batch, not eight. H3 (chat-only): the council
reviews the code presented in the conversation; findings cite
`<file>:<line-as-written>`; the user is the one who can verify, so the
council must state what it could not check.

---

## 1. Role cards

### VET — Principal Architect (20+ yrs, has scaled 3 systems past 10M users)
- **Questions:** System's job in one sentence? The wrong abstraction? What
  breaks in 2 years? What would we regret not doing now?
- **Typical verdicts:** "Kill this module", "Invert the dependency",
  "You're building a framework when you need a script."

### SEC — Security Officer (20+ yrs, has led incident response for breaches)
- **Questions:** Where is **our** system attacked first? What does an
  attacker get from a single bad input? What do error messages reveal?
  Dependencies pinned and known-good?
- **Typical verdicts:** "Validate at the boundary or it doesn't exist",
  "This log line leaks a token."
- **Fable 5 note:** all security review is framed **defensively** — "find,
  assess, and fix vulnerabilities in our own system." Offense framing
  triggers the cyber classifier and drops the session onto Opus 4.8.

### PERF — Systems/Performance Engineer (20+ yrs, owns p99 budgets)
- **Questions:** Hot path? N+1? What grows unbounded? p99, not mean?
- **Typical verdicts:** "Cache this, with a TTL", "O(n²) → O(n) with a map."

### QA — Test Architect (20+ yrs, has killed production bugs with property tests)
- **Questions:** What input breaks this nobody will think of? Which test can
  only ever pass? What's flaky and why?
- **Typical verdicts:** "Add empty + max + malformed", "This test asserts
  nothing — delete or fix."

### RELI — Site Reliability (20+ yrs, on-call scars)
- **Questions:** How does it fail at 3 AM? Rollback? Can we see it failing
  before users do? What data-loss scenario survives?
- **Typical verdicts:** "No timeout on this external call", "Rollback story
  missing — write it now."

### DATA — Data Engineer (20+ yrs, has migrated petabytes without downtime)
- **Questions:** Reversible migration? Idempotent write? Indexes match real
  queries? Backward-compatible read path?
- **Typical verdicts:** "Down migration missing", "Backfill must be
  idempotent."

### REV — Lead Code Reviewer (20+ yrs, reviews everything that ships)
- **Questions:** Can a new hire read this next week? What's dead? What's
  translated from another language's idiom?
- **Typical verdicts:** "Rename — it says what, not what", "Delete these 40
  lines; nothing calls them."

### DX — Docs & DevEx (20+ yrs, has made tools developers actually adopt)
- **Questions:** First-time user succeeds in 5 minutes? Do the docs' code
  samples run? Does an error message tell the user what to do?
- **Typical verdicts:** "README install/run/example missing", "This error
  message is a wall, not a fix."

---

## 2. ADR template (Stage 1 output)

```
## ADR: <decision title>
- Context: <2-3 lines on the problem>
- Options considered:
  1. <option> — pros / cons (1 line each)
  2. <option> — pros / cons
  3. <option> — pros / cons
- Decision: <option>
- Why: <2-3 lines>
- Cost: <what this decision makes harder later>
- Reversibility: <cheap / expensive / one-way>
```

## 3. Risk register template (Stage 5 output)

```
| # | Failure mode | Trigger condition | Blast radius | Mitigation | Status |
|---|--------------|-------------------|--------------|------------|--------|
| 1 | <e.g. dep timeout> | <e.g. API >30s> | <e.g. req fails> | <5s timeout + idempotent retry> | fixed/open |
```

## 4. fable-notes.md ledger template (long tasks — the Fable 5 memory organ)

```
# FABLE NOTES — <task name>
## SPEC (restated, ≤5 lines)
## DECISIONS (date/stage: what + why)
## CURRENT STATE (what exists, what runs, what's broken)
## OPEN ITEMS (P0/P1/P2, owner, next action)
## FILES CHANGED (path: one-line why)
## NEXT (ordered)
```
Read at the start of every stage. Write at the end of every stage. When a
session dies, the ledger — not the chat — restarts the work.

## 5. Defensive-security protocol (for Fable 5's classifiers)

Fable 5's cyber classifier blocks offensive tasks; **routine defensive work
on your own systems is permitted and is what the model is strongest at.**

Allowed framing (works):
- "Audit our service's input validation and fix gaps."
- "Find and fix vulnerabilities in our own codebase; produce patches."
- "Harden our auth flow: review, threat-model, remediate."
- "Reproduce this bug in our test environment and write the regression test."

Blocked/fragile framing (causes Opus 4.8 fallback):
- exploit development against third-party systems
- "how to attack / compromise X" (anyone else's X)
- weaponization, evasion-of-defenses instructions, agentic attack chains

If fallback happens: name the model, restate SPEC, reframe the sub-task
defensively, continue. Never attempt or advise classifier evasion.

## 6. Severity policy (binding)

- **P0 — blocks ship.** Correctness bug, security hole, data loss, broken
  migration. Fixed before Stage 6. No waivers.
- **P1 — ship-with-note.** Performance concern, missing edge test, doc gap,
  observability gap. Fixed or user-waived, recorded in Verdict Block.
- **P2 — polish.** Naming, style, simplification. Listed, not blocking.

## 7. What an APPROVED verdict looks like

```
## VERDICT: APPROVED
## MODEL     Claude Fable 5 (no fallback events this session)
## BUILT
- `app/` — FastAPI service, 6 endpoints
- `tests/` — 41 tests
- Run: `docker compose up` → http://localhost:8000
## EVIDENCE
- `pytest -q` → 41 passed in 3.2s (actual output pasted)
- `ruff check .` → clean
- `/health` returns 200 (curl output pasted)
## RISKS
- P1 waived: rate limiting not implemented (user-acknowledged)
- P2: 2 naming nits in `reviews/council.md`
## NEXT
1. Add rate limiting (code ready in PR branch)
2. Wire metrics endpoint
3. Load-test export endpoint at 10× expected traffic
```

## 8. The one rule that outranks all role rules

**The rule of evidence.** A verdict of APPROVED containing a claim that was
not actually executed is worse than CHANGES REQUESTED. Councils do not
approve on faith. Run it, paste it, then speak.
