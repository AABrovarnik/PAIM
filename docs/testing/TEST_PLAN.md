# Test plan and acceptance
Every FR01–18 is mandatory; US01–88 are executable acceptance scenarios in USER_SCENARIOS. P1 optional, if implemented must pass same quality gates. Test data synthetic; isolated test DB, test bot/calendar with explicit permission. No real mail/calendar writes for tests by default.
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
| T09 |11,12,13| deterministic daily/weekly facts, no LLM fallback, memory edit/delete/cache invalidation |
| T10 |15| sync pagination/410, recurring read, all-day, ETag conflict, rejection/expiry/replay, uncertain create reconciliation, live isolated calendar smoke |
| T11 |16| owner binding, unauthorized group/user, expired code, duplicate update, callback actor/hash, reminder delivery |
| T12 |17| structured output, source evidence, invalid schema, injection, unknown date/assignee, timeout/limit; no external effect |
| T13 |18| unauthenticated/CSRF/foreign owner ID, brute-force throttle, stale version, idempotency key collision, secret scan, audit redaction |
| T14 |18| clean migration, backup+restore to fresh DB, verify row counts/links and RPO/RTO, rollback instructions |
| T15 |01–18| e2e: inbox→approved task→subtask→plan→reminder→done→review, without AI for manual path |
| T16 |18| load 10k tasks/1k events; p95 targets on actual VPS; record environment |
| T17 |01,04,08,11,12| mobile 360px, keyboard, visible focus, contrast, 5 quick-create repetitions median <10s |
| T18 |19–21| if delivered: Gantt consistency, slot preview respects constraints, analytics plan/fact and month |
## AI evaluation
50 synthetic labeled source examples minimum: positive and negative, email/meeting/social, ambiguous date, duplicate and malicious text. Freeze ground truth before prompt tuning; report TP/FP/FN per extracted action, precision>=0.90 target, recall separately (proposed minimum 0.80 for pilot), date/assignee field accuracy and unsupported claims separately. No invented critical deadlines or unapproved external actions tolerated. Record model/prompt version and dataset hash. No using evaluation set as examples in prompt.
## Pilot
7–14 days, start end of week 5 if P0 safe. Owner measures median quick entry <10s, daily planning <=10min, forgotten commitments, perceived usefulness and overhead; baseline before pilot. Zero unauthorized external writes and duplicate internal effects on replay. Record notification failures, not promise guaranteed Telegram delivery. All critical defects block release. If pilot incomplete at week 6, release candidate only.
## Reporting
docs/testing/results/YYYY-MM-DD.md: commit SHA, environment, test ID, command, expected/actual, pass/fail/blocked, evidence redacted, defects and retest. CI: lint, typecheck, unit, integration with Postgres, e2e, secret scan. No fabricated test results or coverage. Owner signs acceptance; Board #2 reviews spec before coding, Board #3 reviews release candidate.

## Дополнение T17 — согласованные UX-референсы
Проверить умные списки на одной БД (Сегодня/Предстоящие/Без срока/Ожидаю); desktop показывает список и календарь совместно, mobile позволяет переключение без потери состояния; перенос блока сохраняет дедлайн. Дневной ритуал позволяет выбрать три результата, увидеть перегрузку и подтвердить план без внешней записи. Проверить отсутствие новых обязательных полей быстрого ввода.

## v1.1 обязательные дополнения (план, не результаты)
T00 — до WP03: выполнить SPIKE_GATE.md, все факты доступа, версия/ресурсы, изолированные тесты; PASS или явно согласованный fallback для затронутого P0. Непроверенное = BLOCKED, мок != live. 
T01: US43–46,79,82; быстрое создание ready+day membership атомарно, raw inbox отдельно, accept без proposal и replay, search/edit/trash, режим UI.
T02: US70/71/87; счет валидных оригинальных ключей от DTSTART, invalid skip != EXDATE skip, moved/completed consume COUNT, future split beyond 90d, exceptions preview and conflict, historical immutable; DST gap/fold. 
T03: US47–50,53; пересечение W, demand/overload, locked union, outside minutes, unestimated count и remainder, transparent styles, no auto schedule, keyboard/mobile, stale data. 
T04: US62/72/74/85; controlled clock, lease expiry before/after marker, old token fenced, ambiguous Telegram no resend, durable in-app notice. 
T05: US59–61; commitment creation and explicit resolution, drafted not sent, copy-only, return for rework, no external send.
T06: US44/64/65/86; manual acceptance, source identity across prompt versions, terminal item not reopened, batch partial outcome, revoked access. 
T07: US57–58; quick priority independent, unknown U not Q2, strategic list, manually edited consequence fields, P=null if weighted dimension missing. 
T08: US55/56/83; goal metric/progress distinction, horizon boundaries, archive and active project delete constraint, concurrent dependency cycle. 
T09: US51–54/63/80; no LLM sync daily plan/manual review; max 3 ranks, membership and revisions with reason, no implicit deadlines/blocks, reload, completion vs structural revision, purge cache. 
T10: US65/67/69/72–75/81; timeout/crash before and after effect, lease expiry, late old response, exact applied evidence, no retry while in-flight ambiguous, stale ETag and expired approval, replay returns status, approval revoke race. Known-no-effect retry only before expiry and after fresh validation; external create/modify/delete with isolated calendar and permission.
T11: US66/67/88; positive bind/add/today/callback, owner/private chat, duplicate update, expired code, unauthorized actor. 
T12: US35/68/78/84; injection, malformed schema, ambiguity, atomic concurrent budget and unknown price, no direct tools; uncertain external result not AI-judged.
T13: US76/81; auth/CSRF, same-owner FK, optimistic concurrency Web/TG, audit matrix ADR-006 including rejected attempts, operation transitions, redact error logs.
T14: US77; backup restore, purge manifest replay before service, old secrets disabled, audit restore/migration evidence.
T15: end-to-end manual and approved external path with mocks plus separate authorized live smoke; no external write if declined; day plan and evening review persisted.
T17: see UI_UX protocol; 5 reps desktop and 360px separately LLM off (3 titles + 2 dates/projects), <10sec median; four-step ritual <=10 min in pilot, each step skippable; 44px touch targets, no conflicting scroll, keyboard focus, status not color alone, retention of draft on network/409/expiry. Confirmed plan changes never silently write to external calendar.
T18: if approved P1 only, add cases before implementation.
## Traceability v1.1 (positive / boundary scenarios; tests)
| FR | Positive US | Boundary US | T |
|---|---|---|---|
|01|43,46|45,79|01,15,17|
|02|05|06,79|01,08|
|03|07,08|70,71,87|02|
|04|47,50|48,49|03,17|
|05|12,62|13,74,85|04|
|06|59,61|60,16|05|
|07|44|64,86|06|
|08|22,58|57,23|07|
|09|25,05|06,56|08|
|10|26,55|56,83|08|
|11|51,55|27,28,48,53|03,09|
|12|51,54,29|52,80|09,17|
|13|63|30,77|09,14|
|14|17|18,64,65|06,00|
|15|31,75|32,69,73,81|10,00|
|16|66,67|34,88|11,00|
|17|19,58|35,68,78,84|12,00|
|18|37,40|38,42,76,77|13,14,16,00|
Before WP03: documentation re-review, tabletop cases recorded separately (no runtime claims). Before pilot: all required P0 tests PASS, no critical outstanding, actual T00 verified, approved live integrations smoke evidence, no secret leakage. Before release: owner accepts pilot 7–14 days, approved deviations recorded. If gate blocked, stop dependent packages; do not silently shrink P0.
