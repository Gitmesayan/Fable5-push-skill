# EXAMPLES — FABLE-5 ULTRA in action

Worked examples of the protocol's artifacts and behaviors, plus the common
mistakes the protocol exists to prevent. Copy the **shape**, not the content.

---

## 1. Stage 0 — SPEC: rejected vs accepted

**Rejected at Stage 0** (vague — the agent must rewrite it, not lock it):
```
GOAL: make the API better.
SUCCESS: "it works", "clean code", "fast".
```
Why: no testable criteria, no non-goals, no acceptance commands.

**Accepted:**
```
## GOAL
POST /v1/export produces a zipped CSV for a user's dataset.

## SUCCESS CRITERIA
1. `curl -X POST /v1/export -d '{"user":42}'` → 202 with a job id
2. Job completes < 30s on a 100k-row dataset — proves:
   `pytest tests/test_export_scale.py -q`
3. Repeat POST with same user+params returns the existing job id
   (idempotent) — proves: `pytest tests/test_export_idempotent.py -q`

## NON-GOALS
- No streaming/SSE export (phase 2)

## ACCEPTANCE COMMANDS
1. `pytest -q` → all pass
2. `curl -s -X POST localhost:8000/v1/export -d '{"user":42}'` → 202
```

## 2. Stage 3 — a build increment (H1)

```
Task 3: rate limiting for /v1/export.
  read   fable-notes.md        → state: endpoint done, 14 tests green
  read   src/api/export.py:1-40 (existing handler, conventions)
  write  src/api/rate_limit.py, tests/test_rate_limit.py
  run    pytest tests/test_rate_limit.py -q
         → 4 passed in 0.8s          ← real output, pasted
  ledger STATE += token bucket 10/min/user
         FILES += src/api/rate_limit.py, tests/test_rate_limit.py
  next   task 4: wire limiter into /v1/export
```
Note the shape: read → change → test → **run & paste** → ledger → next.
Any missing arrow = the increment is incomplete.

## 3. Stage 4 — council findings (format)

```
[SEC]  P0: user id from request body, no authz check (src/api/export.py:22) — anyone can export anyone
[PERF] P1: row-by-row fetch in export loop (src/export/service.py:58) — N+1; 100k rows ≈ 100k queries
[QA]   P1: no test for empty dataset (tests/test_export.py)
[DATA] P1: job-table backfill has no down migration (migrations/0042.sql)
[DX]   P2: "500" error tells user nothing (src/api/export.py:31)

Belief check: believing "job store is atomic under concurrent POSTs".
Cheap test: pytest tests/test_export_concurrency.py -q
→ 1 FAILED (duplicate job rows) → promoted to P0, fixed before Stage 6.
```

## 4. Stage 5 — red team (filled, brief)

```
1. Failure modes: (a) zip write interrupted → partial file served
   (b) job table unbounded, no retention (c) CSV serializer OOM at 1M rows
   (d) clock skew breaks rate-limit window (e) storage backend down → 500, no retry guidance
2. Malicious input: 10MB JSON body → 16KB boundary limit → 413, structured log
3. Dep dies mid-request: job → FAILED at 30s timeout; re-POST is idempotent; log line carries job id
4. 100× load: workers saturate ≈40 concurrent (measured); queue + 503 + Retry-After beyond; API unaffected
5. Rollback: EXPORT_ENABLED=false (env) → new POSTs 404, running jobs finish; job table + completed zips survive

Confidence: high — all five exercised in tests/test_export_* (run; output in EVIDENCE)
```

## 5. Stage 7 — Verdict Blocks

**APPROVED WITH NOTES:**
```
## VERDICT: APPROVED WITH NOTES
## MODEL     Claude Fable 5 (no fallback events this session)
## HARNESS   H1
## BUILT     src/api/export.py, src/export/{service,store}.py, migrations/0042.sql, 18 tests
             run: docker compose up → POST /v1/export
## EVIDENCE  pytest -q → 18 passed in 6.1s
             curl POST /v1/export → 202 {"job":"e_9f3"}
             pytest -k rate -q → 4 passed in 0.8s
## RISKS     P1 waived by user: "no retention policy for now" (their words)
             known limit: single export worker
## NEXT      1. add retention job  2. load-test at 10×  3. wire metrics
```

**CHANGES REQUESTED:**
```
## VERDICT: CHANGES REQUESTED
## MODEL     Claude Fable 5 (steps 3–4 fell back to Opus 4.8 — flagged, re-anchored)
## HARNESS   H1
## BUILT     draft in src/ — do not deploy
## EVIDENCE  pytest -q → 12 passed, 2 FAILED (tests/test_export_concurrency.py)
## RISKS     P0 open: duplicate job rows under concurrent POST (fix in ledger OPEN ITEMS)
## NEXT      1. unique constraint on (user, params_hash)
            2. re-run concurrency test  3. re-verify full suite
```

## 6. Caveman mode

```
Normal:   "I've reviewed the pull request. I noticed that the retry logic
           doesn't include an idempotency key, which could result in
           duplicate charges if the payment provider times out…"
Caveman:  "P0: retry without idempotency key (pay.py:88).
           Timeout → double charge. Fix: pass `Idempotency-Key` on retry.
           Test added. Pass."
```
Code, line numbers, risk statement — intact. Only the fat is gone.

## 7. Fallback protocol (Fable 5 → Opus 4.8 mid-session)

```
user:  fable. Harden our auth service against session fixation.
agent: harness: H1 · SPEC locked (≤5 lines) …
       [Stage 3, task: enumerate fixation flaws in our token rotation]
       ⚠ classifier flagged the request → answered by Opus 4.8 (user notified)
agent: model: Opus 4.8 (fallback on this step; re-anchored).
       SPEC restated: review + fix for OUR auth service's rotation.
       Reframed: "review our rotation code for the fixation bug class; produce the patch."
       [continues; pasted tests for the patch]
```
Name the model · restate SPEC · reframe defensively · continue · no re-send
of the same phrasing · never advise evasion.

## 8. H3 (chat-only) — what evidence looks like

```
user:  fable. Write a retry decorator for Python requests.
agent: harness: H3 (no file/shell access) · SPEC: <5 lines>
       [code block: retry.py + tests/test_retry.py]

       ## EVIDENCE
       I cannot execute code (H3). Run these and paste back:
       1. python -m pytest tests/test_retry.py -q  → expect: 3 passed
       2. python -c "import retry"                 → expect: no error
```
An H3 agent that pastes "test output" instead of commands has violated the
rule of evidence.

## 9. Common mistakes → the protocol correction

| Mistake | What it looks like | Correction |
|---|---|---|
| Invented green | "All tests pass" with no output | Rule of evidence: paste it, or say you can't run it |
| Harness mismatch | Claims H1, then pastes "output" it never ran | Probe first, report class, re-probe on `harness` |
| Sycophancy | "Great idea!" before any review | Defects first, always |
| Scope bloat | Adds caching nobody asked for | SPEC non-goals are binding |
| Ledger rot | Re-decides the same design at turn 40 | Ledger read at stage start, written at stage end |
| Effort underspend | One-shot answer on a P0 production task | Make difficulty explicit; `ultrathink` |
| Offensive framing | "exploit the login" (on Fable 5) | Defensive framing; Fallback Protocol; never evasion |
| Context re-dump | Re-pastes a 40k-line file every turn | Targeted re-injection: the 20 relevant lines |
| Unnamed model | Verdict Block with no MODEL line | DoD #7: model + harness always named |
