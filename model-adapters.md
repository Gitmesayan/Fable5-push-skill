# MODEL ADAPTERS — selection, verified facts, degradation

How the skill knows which model it's on, what is verified about Claude
Fable 5, and exactly which protocol levers to apply per model family.

---

## 1. Adapter selection (do this once, at Stage 0)

1. Read the model identity your **harness** provides: UI model picker,
   API response `model` field, or what the user told you.
2. **Do not self-identify from vibes.** A model's claimed identity in chat
   is not evidence.
3. Map to adapter:
   - string contains `claude-fable-5` → **§2 Fable 5**
   - other `claude-*` → **§3 other Claude**
   - anything else / unknown → **§4 generic**
4. Unknown and it matters (long task, high stakes)? Ask **one** question:
   "Which model is this running on?" One no-answer → generic, proceed.

## 2. Claude Fable 5 — verified facts (retrieved 2026-08-31)

| Fact | Source |
|---|---|
| Mythos-class tier, **above Opus**. Same underlying model as Claude Mythos 5; Fable differs only in safeguards (Latin *fabula*, "that which is told," akin to Greek *mythos*). | Launch post, Jun 9 2026 |
| SOTA on nearly all tested benchmarks; **the longer and more complex the task, the larger the lead**. Stripe: codebase-wide migration of a 50M-line Ruby repo in 1 day (≈2 months for a team). Highest frontier score on Cognition FrontierCode — **most token-efficient frontier model there**. | Launch post |
| Knowledge work: top of Hebbia's finance benchmark; IMC trading-analysis evals. Vision: SOTA — rebuilds a web app's source from screenshots; beat Pokémon FireRed with a minimal vision-only harness. | Launch post |
| **Long-horizon autonomy**: works autonomously longer than any previous Claude; **stays focused across millions of tokens; improves outputs using its own notes** — file-based persistent memory gave a 3× larger performance gain than on Opus 4.8 (Slay the Spire; final act reached 3× more often). | Launch post |
| **Always-on adaptive thinking** with effort levels; at **highest effort the model reflects on and validates its own work** (customer: "the extra thinking pays for itself"). | Launch post + customer quotes |
| "Works at senior research scientist grade — picking directions, allocating resources, **killing its incorrect beliefs**" (early customer). | Launch post |
| Specs (consistent across launch post + third-party model trackers): **1M-token context, 128K max output, January 2026 knowledge cutoff, $10/$50 per Mtok**, API id `claude-fable-5`; Claude API, AWS, Google Cloud, Microsoft Foundry, Claude.ai, Claude Code, Claude Cowork. | Launch post + trackers |
| **Safeguards**: separate safety classifiers (defense-in-depth with trained refusals + retroactive misuse analysis) cover **cybersecurity, biology/chemistry, distillation** → flagged requests answered by **Claude Opus 4.8 fallback**, user notified. **>95% of sessions: no fallback.** Conservative "safety margin" tuning causes some false positives on benign requests; biology false-positive reduction shipped Aug 6 2026. | Launch post; redeploy post; Aug 6 2026 update |
| Safeguards **not expected to block all low-risk routine cyberdefense** — only potentially harmful tasks. | Redeploy post |
| Jun 12 2026: US export-control directive after an Amazon report of a classifier bypass (vulnerability identification + one exploit demonstration); Anthropic confirmed the behavior was **routine defensive work** reproducible on less capable models; improved classifier blocks that technique >99%; redeployed Jul 1 with an industry jailbreak-severity framework (Amazon, Microsoft, Google, Glasswing partners). | Redeploy post |
| **Data**: 30-day retention on all Mythos-class traffic; not used for training; deleted after 30 days in almost all cases. | Launch post |
| Alignment: measured misalignment (deception, misuse cooperation) low and similar to Opus 4.8; Fable similar. | Launch post + system card |

**Fable 5 levers (applied in SKILL.md §3.1):** explicit difficulty framing →
max adaptive effort; highest-effort self-validation invoked at Stage 6;
`fable-notes.md` ledger for long tasks; belief-question at Stage 4; SPEC
constrains outcomes not plans; Fallback Protocol on classifier routing;
Caveman as cost control at $10/$50.

## 3. Other Claude (Opus / Sonnet / Haiku, 2025–2026 generations)

- Extended thinking where the harness exposes it; otherwise explicit
  "plan, then execute, then self-critique" works (Constitutional
  self-critique is trained into Claude generally).
- **No documented request-routing fallback** — refusals are in-model, not
  routed to another model. If refused: reframe once, defensively; if it
  repeats, tell the user and continue on the rest of the task.
- Context commonly 200K (verify against your harness's current docs) →
  index + targeted re-injection for long tasks; `fable-notes.md` still
  recommended (it's harness memory, not just a model lever).
- Council / red team / evidence rules are **identical** — they are process,
  not model behavior.

## 4. Generic / unknown model

- Make **planning explicit**: "first list the plan, then execute it."
- Make **verification explicit**: "before answering, re-read each
  requirement and check it against your output; list every gap."
- Assume **finite context**: SPEC restated at every gate; re-quote the
  ~20 relevant lines, not 20,000.
- Assume **no named self-validation mode**: perform the Stage-6 check as an
  explicit step in the reply.
- Everything else in the protocol is model-independent — **run it all**.

## 5. Degradation table (lever → per-adapter behavior)

| Lever | Fable 5 | Other Claude | Generic |
|---|---|---|---|
| Max reasoning | explicit difficulty framing; highest effort self-validates | explicit "think step by step" + self-critique pass | explicit plan-first instruction |
| Long-task memory | `fable-notes.md` (documented 3× mechanism) | `fable-notes.md` (harness memory) | `fable-notes.md` (harness memory) |
| Belief revision | native ("kills incorrect beliefs") — trigger it | trigger via "what might be wrong?" | trigger via "what might be wrong?" |
| Context scale | 1M — full repo in context | ~200K — index + re-inject | per harness docs |
| Fallback handling | Opus 4.8 routing; Fallback Protocol | in-model refusals; reframe once | refusals; reframe once, then continue |
| Token economics | $10/$50 — Caveman as cost control | plan-dependent | plan-dependent |

## 6. Honesty rules

- Facts marked **verified** above come from the cited Anthropic pages;
  specs from third-party trackers are labeled as such.
- If a fact here conflicts with your harness's current docs, **the harness
  docs win** — model specs change.
- Never present an inference about model internals as a verified fact.
  Architecture-level claims (attention, MoE, sampling) are standard
  transformer inference, not Fable-5 documentation.
