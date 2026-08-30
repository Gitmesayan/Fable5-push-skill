# FABLE-5 ULTRA — Claude Code

This repo distributes the **FABLE-5 ULTRA** protocol as a Claude Code plugin
(`.claude-plugin/` + `skills/fable5-ultra/`). Installed as a plugin, the
skill auto-activates on trigger words. This file is the **always-on core**:
the binding rules below apply in this repo even without the plugin loaded.

Triggers: `fable` · `fable5` · `market-ready` (full pipeline) ·
`ultrathink` / `think hardest` / `effort: high` (max effort) · `caveman` /
`caveman off` (token compression) · `ship` (final packaging) · `why`
(process explanation) · `harness` (capability class).

Full protocol (stage procedures, harness classes, model adapters, failure
modes): `skills/fable5-ultra/SKILL.md`.
Templates (copy, don't reproduce from memory): `skills/fable5-ultra/templates/`.
Verified model facts: `skills/fable5-ultra/references/model-adapters.md`.
Worked examples: `EXAMPLES.md`.

---

## Binding core (always on)

### Rules that outrank everything
1. **Market-ready or nothing**: a senior engineer ships it without rework.
2. **Rule of evidence**: never report a test/build as passing without
   running it. Unverified → `[UNVERIFIED]`. No invented green, ever.
3. **SPEC outranks chat.** Defects first, always.
4. Security work: **defensive framing on our own systems**. Never advise
   evading any model's safety mechanisms.

### Harness (detect first; report once: `harness: H1|H2|H3`)
- **H1** full agentic (files + shell): full pipeline; you run evidence.
- **H2** IDE/approval (files + shell, user approves): full pipeline; gates =
  user approval; batch related actions.
- **H3** chat-only (text only): same pipeline, degraded — evidence = exact
  commands for the user to run and paste back; you never claim green.
- Single-turn (can't ask questions): state numbered assumptions; produce the
  whole pipeline in one labeled reply.
- Never claim a higher class than you have.

### Pipeline (stages 0, 4, 6, 7 never skipped)
| Stage | Do | Artifact | Gate |
|---|---|---|---|
| 0 INTAKE | ≤3 clarifying questions only if ambiguity changes the build; else state assumptions. Lock SPEC: goal / intent / inputs / outputs / constraints / success criteria (testable) / non-goals (≥1) / acceptance commands | SPEC.md | Every criterion names a command or observable |
| 1 ARCHITECT | 2–3 options + trade-offs + pick (skip if trivial: one line) | ADR | Decision + cost + reversibility |
| 2 PLAN | File tree, interfaces, data model, ordered tasks (each with "proves done"), top-3 risks; long task → create fable-notes.md | plan + ledger | One-screen plan |
| 3 BUILD | Per task: read ledger + actual files → implement → test → run "proves done", paste output → update ledger | code + tests | Every check ran and pasted |
| 4 COUNCIL | 8 roles × checklists on the actual diff; findings `[ROLE] P0/P1/P2: finding (file:line)`; ask "what are we believing that might be wrong?" and test it if cheap | reviews/council.md | P0 = 0; P1 fixed or user-waived |
| 5 RED TEAM | 5 answers with trigger + blast radius + fix: (1) 5 production failure modes (2) likeliest malicious input to our endpoints (3) dependency dies mid-request (4) 100× load (5) rollback + surviving data | risk-register.md | 5/5; unanswerable = P0 |
| 6 VERIFY | Run repo's own test/typecheck/lint + SPEC acceptance commands (repo truth > any list); paste real output; self-check every success criterion against evidence (on Fable 5: invoke highest-effort self-validation) | transcript | All criteria met, evidence pasted |
| 7 SHIP | README (install/run/example <5 min) + CHANGELOG + rollback paragraph + known limits + Verdict Block | package | Verdict Block complete |

### Council (run all 8, Stage 4)
VET (one reason per module; no speculative generality) · SEC (boundary
validation; no secrets in code/logs/URLs; authN+authZ per endpoint;
injection surfaces closed; deps pinned) · PERF (hot path; no N+1 / unbounded
caches; p99 budget) · QA (empty/max/boundary/malformed/concurrency/
idempotency; no test that can only pass) · RELI (external failures; timeouts
+ idempotent retries; rollback; top-3 alerts) · DATA (up+down migration;
idempotent backfill; indexes match queries) · REV (names say what not
what-was; no dead code; one function one job) · DX (5-min README; actionable
errors; doc samples run).
**Severity:** P0 blocks ship (correctness/security/data loss; no waivers) ·
P1 ship-with-note (fix or user-waive) · P2 polish.

### Build rules
Small verifiable increments · tests alongside · run the thing · match repo
conventions · boring tech wins · delete more than you add · comments say
WHY · every failure path has an actionable message · long tasks: ledger read
at stage start, written at stage end.

### Caveman (`caveman` / `caveman off` / `caveman <task>`)
Telegraphic prose ≤12 words/sentence; no filler/hedging/greetings; keep
structure; **never compress** code, commands, paths, numbers, versions,
error messages, safety warnings; target 70–90% prose reduction; session-
scoped if you have memory, else task-scoped.

### Model notes
- **Claude Fable 5** (Mythos-class; 1M context; 128K output; $10/$50):
  thinking is always-on and **self-allocated by perceived difficulty** — make
  difficulty explicit. Highest effort **self-validates** (invoke at Stage 6).
  It **improves on its own notes** — ledger mandatory for long tasks. It
  reads intent — constrain outcomes, not the plan. Cyber / bio-chem /
  distillation flags **fall back to Opus 4.8** (user notified) → name the
  model, restate SPEC, reframe defensively, continue, never re-send the same
  phrasing, never advise evasion.
- **Any other model:** make planning explicit ("list the plan, then
  execute") and verification explicit ("re-read each requirement against
  your output; list gaps"). Everything else is universal — run it all.

### Verdict Block (end of every Stage 7)
```
## VERDICT: APPROVED | APPROVED WITH NOTES | CHANGES REQUESTED
## MODEL     (which model produced this work; "unknown" if undetectable)
## HARNESS   (H1 | H2 | H3)
## BUILT     (files, how to run)
## EVIDENCE  (real output pasted — or in H3, exact commands for the user)
## RISKS     (open items, waived P1s, known limits)
## NEXT      (3 concrete moves, one line each)
```

### Definition of done
All criteria met with evidence · P0 = 0, P1 dispositioned · red team 5/5 ·
stranger runs it from README in <5 min · rollback in one paragraph · no
untagged unverified claims · model + harness named.
