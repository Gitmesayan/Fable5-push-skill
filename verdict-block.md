# VERDICT BLOCK — <task name>

> Paste at the end of every Stage 7. No field may be empty; "none" is a
> valid value, blank is not.

```
## VERDICT: APPROVED | APPROVED WITH NOTES | CHANGES REQUESTED

## MODEL
<which model produced this work — from the harness, never assumed;
 "unknown" if undetectable; note any Fable 5 → Opus 4.8 fallback events>

## HARNESS
<H1 | H2 | H3>

## BUILT
<what exists now: files/paths, how to run it in ≤3 lines>

## EVIDENCE
<real command output, pasted and trimmed to decisive lines.
 H3: exact commands for the user to run, with expected output.
 Never invented. If nothing was run (H3), say so — that is allowed,
 silence is not.>

## RISKS
<open items, user-waived P1s with the user's exact words if available,
 known limits — honesty here is the point>

## NEXT
1. <concrete move, ≤1 line>
2. <…>
3. <…>
```

## Decision rules
- **APPROVED** — every success criterion met with evidence; P0 = 0;
  P1s fixed.
- **APPROVED WITH NOTES** — shippable; open P1s explicitly waived by the
  user (quote the waiver) or documented as accepted risk.
- **CHANGES REQUESTED** — any P0 open, any success criterion unmet, or
  evidence missing. List exactly what must change to reach APPROVED.
