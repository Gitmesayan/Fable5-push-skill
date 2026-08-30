# SPEC — <task name>

> Rule: every success criterion must name a **command** or an **observable
> outcome**. "Works well", "clean code", "fast" are not criteria — reject
> and rewrite them at Stage 0.

## GOAL (one sentence)
<what exists after this task that does not exist now>

## INTENT
<who it is for, why they want it, what "done" means to them in their words>

## INPUTS
<files, data, existing APIs, external services, upstream constraints>

## OUTPUTS
<artifacts, formats, file locations, interfaces exposed>

## CONSTRAINTS
<language/framework pinned? style? budget (tokens, $, time)? perf targets?
compliance? what must NOT be touched?>

## SUCCESS CRITERIA
1. <testable statement> — proves: `<command>` → expect <output>
2. <testable statement> — proves: <observable, e.g. "GET /health returns 200">
3. …

## NON-GOALS (at least one — scope is a feature)
- <explicitly out of scope, and why deferring is safe>

## ACCEPTANCE COMMANDS (exact; run verbatim at Stage 6)
1. `<command>` → expect: <exact output or condition>
2. `<command>` → expect: <…>

> Source of acceptance commands: the repo's own CI config first
> (`.github/workflows`, `.gitlab-ci.yml`, `Makefile`), then the stack list
> in SKILL.md §6.

## STATUS
locked <date> · restated at gates: <list stage numbers as they happen>
