# RISK REGISTER — <task name>

> Stage 5 red-team output. All five questions must be answered; anything
> unanswerable is a P0 and returns to Stage 4. Framing: defensive, about our
> own system only.

## The five answers

1. **5 production failure modes:** see table (rows 1–5)
2. **Likeliest malicious input to our endpoints:** <input class, where it
   lands, what the boundary check does with it>
3. **Dependency dies mid-request:** <what happens, what the user sees,
   what's in the logs, what the timeout/retry does>
4. **100× expected load:** <what saturates first, what degrades
   gracefully, what hard-fails>
5. **Rollback:** <exact steps> · <data that survives / is lost>

## Register

| # | Failure mode | Trigger condition | Blast radius | Mitigation | Status |
|---|---|---|---|---|---|
| 1 | <…> | <what causes it> | <who/what is affected> | <fix or why-acceptable> | fixed / open / accepted |
| 2 | | | | | |
| 3 | | | | | |
| 4 | | | | | |
| 5 | | | | | |

## Confidence
<high / medium / low> — justified by: <what was actually run/tested>.
(Never "high" on a system that was not executed.)
