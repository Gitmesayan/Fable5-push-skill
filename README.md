# FABLE-5 ULTRA

**Any agent. Any model. Ships like a 20-year engineering org.**

A portable elite-output protocol for **any agentic AI or coding agent** —
Claude Code, OpenAI Codex CLI, Cursor, Windsurf, Cline, Roo Code, Amp,
GitHub Copilot, JetBrains Junie, Aider, OpenHands, Devin, or any chat API —
tuned to the verified behavior of **Claude Fable 5** (Anthropic's
Mythos-class model, June 2026) and degrading gracefully on every other model.

## The pipeline

For any `fable`-tagged task the agent runs:

```
0 INTAKE → 1 ARCHITECT → 2 PLAN → 3 BUILD → 4 COUNCIL → 5 RED TEAM → 6 VERIFY → 7 SHIP
```

- **Spec-locked** — testable success criteria, binding non-goals, exact acceptance commands
- **8-role council** — architect, security, performance, QA, reliability, data, reviewer, docs; findings as `[ROLE] P0/P1/P2: … (file:line)`
- **Red team before ship** — 5 production-death questions, defensive framing
- **Rule of evidence** — nothing claimed as passing without execution; `[UNVERIFIED]` tags; the Verdict Block names the **model** and **harness** that did the work
- **Caveman mode** — 70–90% token compression (`caveman` / `caveman off`)
- **ULTRATHINK** — highest effort + explicit self-validation (`ultrathink`)
- **Harness-aware** — detects full-agentic (H1) / IDE-approval (H2) / chat-only (H3) and degrades the pipeline honestly instead of faking evidence

## Repo layout

```
fable5-ultra/
├── .claude-plugin/
│   └── plugin.json                 ← Claude Code plugin manifest
├── .cursor/rules/
│   └── fable5-ultra.mdc            ← Cursor project rule (alwaysApply)
├── CLAUDE.md                       ← always-on core for Claude Code
├── CURSOR.md                       ← always-on core for Cursor
├── EXAMPLES.md                     ← worked examples + common mistakes
├── README.md                       ← this file
├── README.zh.md                    ← 中文说明
└── skills/
    └── fable5-ultra/               ← the full skill (canonical)
        ├── SKILL.md                ← full protocol: stages, harness classes, model adapters, failure modes
        ├── references/
        │   ├── model-adapters.md   ← verified Fable 5 facts + adapter selection + degradation table
        │   ├── engineering-council.md ← 8 role cards, severity policy, defensive-security protocol
        │   └── caveman-mode.md     ← full Caveman spec + examples + edge cases
        └── templates/
            ├── SPEC.md             ← the contract (Stage 0)
            ├── fable-notes.md      ← the long-task ledger (Stage 2+)
            ├── adr.md              ← decision record (Stage 1)
            ├── risk-register.md    ← failure modes (Stage 5)
            └── verdict-block.md    ← the closing block (Stage 7)
```

`CLAUDE.md` / `CURSOR.md` / the `.mdc` file carry the **binding core** (~2k
tokens) so the protocol works even when only the rules file is loaded;
`skills/fable5-ultra/` carries the full protocol for agents that can load a
skill folder.

## Trigger words

| Say | Get |
|---|---|
| `fable` / `fable5` / `market-ready` | Full 8-stage pipeline |
| `ultrathink` / `think hardest` / `effort: high` | Highest effort + explicit self-validation |
| `caveman` / `caveman off` | 70–90% token-compressed voice |
| `ship` | Jump to Stage 7, gaps named honestly |
| `why` | 3-line model-process explanation of last decision |
| `harness` | Re-report detected capability class (H1/H2/H3) |

Quick start: `fable. Build me <task>. Ultrathink. Caveman.`

## Install

### Claude Code (best fidelity — plugin)
1. **As a plugin:** add this repo to your Claude Code plugin marketplace,
   then install `fable5-ultra` (the manifest at `.claude-plugin/plugin.json`
   and the `skills/` directory are auto-detected).
2. **Without a marketplace:**
   ```bash
   cp -r skills/fable5-ultra ~/.claude/skills/     # global
   # or project-scoped:
   cp -r skills/fable5-ultra <project>/.claude/skills/
   ```
3. Cloning this repo gives you the always-on core via `CLAUDE.md` either way.

### Codex CLI / Amp (AGENTS.md agents)
Keep this repo in your workspace and append to `AGENTS.md`:
```markdown
## Protocol: FABLE-5 ULTRA
For tasks tagged `fable` or `market-ready`, follow
`fable5-ultra/CLAUDE.md` (binding core) and, when the task is hard,
`fable5-ultra/skills/fable5-ultra/SKILL.md` (full protocol).
```

### Cursor / Windsurf / Cline / Roo Code / Copilot / Junie

| Agent | File to create | Content |
|---|---|---|
| Cursor | `.cursor/rules/fable5-ultra.mdc` | copy from this repo (frontmatter included); `CURSOR.md` at repo root works too |
| Windsurf | `.windsurf/rules/fable5-ultra.md` | binding core (from `CLAUDE.md`) |
| Cline | `.clinerules/fable5-ultra.md` | binding core |
| Roo Code | `.roo/rules/fable5-ultra.md` | binding core |
| GitHub Copilot | append to `.github/copilot-instructions.md` | binding core under `## FABLE-5 ULTRA` |
| JetBrains Junie | append to `.junie/guidelines.md` | binding core under `## FABLE-5 ULTRA` |

Paths accurate as of August 2026; they drift — if one 404s against your
agent's current docs, use that agent's current rules mechanism with the same
content.

### Aider
```bash
cp CLAUDE.md CONVENTIONS.md
aider --read CONVENTIONS.md        # or persist: read: [CONVENTIONS.md] in .aider.conf.yml
```

### Chat-only / any API (H3)
System prompt (or first message) = the content of `CLAUDE.md`. The agent
will self-report `harness: H3` and hand you exact commands to run instead of
claiming results.

## Verify the install (2 minutes, any agent)

Fresh session → send:
```
fable. Build a tiny CLI that converts Celsius to Fahrenheit. One test. Caveman.
```
Pass = all of: `harness: H1|H2|H3` on the first line · a SPEC block with a
testable criterion · incremental build with a "proves done" check per task ·
`[ROLE] P0/P1/P2` findings · 5 red-team answers · Verdict Block naming MODEL
and HARNESS · caveman prose with normal code.

| Symptom | Likely cause | Fix |
|---|---|---|
| Triggers ignored | rules file not loaded (path/name/frontmatter) | check the table above; restart the agent |
| Partially followed | core truncated in the rules file | re-paste in full |
| Invents test output | over-claims its harness class | send `harness. Re-probe your actual file/shell access.` |

## Model facts (verified 2026-08-31)

Claude Fable 5: Mythos-class tier (above Opus; same underlying model as
Claude Mythos 5 + safety safeguards) · 1M-token context · 128K max output ·
January 2026 knowledge cutoff · $10/$50 per Mtok · `claude-fable-5` ·
always-on adaptive thinking with effort levels (highest effort
self-validates) · improves outputs using its own notes (3× effect in
Anthropic's test) · classifier fallback to Opus 4.8 on flagged
cyber/bio-chem/distillation requests (user notified; >95% of sessions have
no fallback) · 30-day data retention.
Sources: `references/model-adapters.md` (anthropic.com/news/claude-fable-5-mythos-5,
/news/redeploying-fable-5, system card, Aug 6 2026 update).

The protocol is **version-robust**: community chatter about "Fable 5.1"
(Aug/Sep 2026) is unconfirmed; it targets documented traits, so it works on
5.1 unchanged if/when it ships.

## Token economics

| File | Size | Where it costs |
|---|---|---|
| Binding core (CLAUDE.md / CURSOR.md / .mdc) | ~1,800–2,000 tokens | every request (always-applied) |
| SKILL.md | ~6,500 tokens | when the agent loads the skill folder or system prompt allows it |
| references/ | ~2,500 tokens | on demand |
| templates/ | ~700 tokens | copied verbatim into the working repo at Stages 0/2/5/7 |

Never put SKILL.md in an always-apply rules file — ~4× the cost of the core
for the same binding rules.

## License

Add your choice — MIT recommended. Model facts cited from Anthropic's
public announcements; links in `references/model-adapters.md`.
