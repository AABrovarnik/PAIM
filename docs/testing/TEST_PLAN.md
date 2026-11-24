# Test plan and acceptance
Every FR01–18 is mandatory; US01–42 are executable acceptance scenarios in USER_SCENARIOS. P1 optional, if implemented must pass same quality gates. Test data synthetic; isolated test DB, test bot/calendar with explicit permission. No real mail/calendar writes for tests by default.
| Test ID | FR | Acceptance / evidence |
|---|---|---|
| T01 |01,02| CRUD, trash/restore, reopen, hierarchy depth/cycle, progress; API+UI tests |
| T02 |03| daily/weekly/monthly/yearly, count/until, single/future/all, invalid rule, Feb31 skip, DST gap/fold, restart; unique instance keys |
| T03 |04,11| drag/drop + keyboard, due unchanged, overlapping events union, 480/120/25% capacity=240, 300 planned overload=60, stale calendar |
| T04 |05| clock-controlled scheduler, quiet/snooze, crash before/after lease, retry/uncertain delivery, no duplicate in-app notice |
| T05 |06| sent != accepted, submitted != verified, overdue check, rejected result, one active assignment |
| T06 |07,14| Gmail mock pagination, replay, filters, revoked token, source evidence, no send/write; isolated live read smoke |
| T07 |08| score 73/Q2, null handling, custom weight sum, manual override, hard deadline separate |
| T08 |09,10| dependency DAG concurrent cycle rejection, blocked task, milestone, goal/idea promotion once |
| T09 |12,13| deterministic daily/weekly facts, no LLM fallback, memory edit/delete/cache invalidation |
| T10 |15| sync pagination/410, recurring read, all-day, ETag conflict, rejection/expiry/replay, uncertain create reconciliation, live isolated calendar smoke |
| T11 |16| owner binding, unauthorized group/user, expired code, duplicate update, callback actor/hash, reminder delivery |
| T12 |17| structured output, source evidence, invalid schema, injection, unknown date/assignee, timeout/limit; no external effect |
| T13 |18| unauthenticated/CSRF/foreign owner ID, brute-force throttle, stale version, idempotency key collision, secret scan, audit redaction |
| T14 |18| clean migration, backup+restore to fresh DB, verify row counts/links and RPO/RTO, rollback instructions |
| T15 |01–18| e2e: inbox→approved task→subtask→plan→reminder→done→review, without AI for manual path |
| T16 |18| load 10k tasks/1k events; p95 targets on actual VPS; record environment |
| T17 |01,04| mobile 360px, keyboard, visible focus, contrast, 5 quick-create repetitions median <10s |
| T18 |19–21| if delivered: Gantt consistency, slot preview respects constraints, analytics plan/fact and month |
## AI evaluation
50 synthetic labeled source examples minimum: positive and negative, email/meeting/social, ambiguous date, duplicate and malicious text. Freeze ground truth before prompt tuning; report TP/FP/FN per extracted action, precision>=0.90 target, recall separately (proposed minimum 0.80 for pilot), date/assignee field accuracy and unsupported claims separately. No invented critical deadlines or unapproved external actions tolerated. Record model/prompt version and dataset hash. No using evaluation set as examples in prompt.
## Pilot
7–14 days, start end of week 5 if P0 safe. Owner measures median quick entry <10s, daily planning <=10min, forgotten commitments, perceived usefulness and overhead; baseline before pilot. Zero unauthorized external writes and duplicate internal effects on replay. Record notification failures, not promise guaranteed Telegram delivery. All critical defects block release. If pilot incomplete at week 6, release candidate only.
## Reporting
docs/testing/results/YYYY-MM-DD.md: commit SHA, environment, test ID, command, expected/actual, pass/fail/blocked, evidence redacted, defects and retest. CI: lint, typecheck, unit, integration with Postgres, e2e, secret scan. No fabricated test results or coverage. Owner signs acceptance; Board #2 reviews spec before coding, Board #3 reviews release candidate.
