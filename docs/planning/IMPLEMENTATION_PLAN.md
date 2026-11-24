# Implementation plan — ТЗ v1.1
Статус: proposed/re-review. Базовая цель: WP00 (2–3 рабочих дня при доступности среды) + 6 недель, 90–150 часов владельца на основной цикл (плановая оценка, не гарантия). Концепция V2 сохранена. Качество и безопасность выше фиксированной даты; перенос P0 допускается только по согласованию. До WP03 нужны утверждение v1.1, повторная экспертиза и разрешенный gate T00.
| Период | Рабочий пакет / FR | Условие выхода |
|---|---|---|
| WP00, до основной разработки | Проверки VPS, Postgres, OAuth, Gmail, Calendar read/write, TG, OpenClaw/LLM, TLS, backup, бюджет | [SPIKE_GATE.md](SPIKE_GATE.md), T00 фактически выполнен, PASS или отдельно утвержденный fallback; BLOCKED/FAIL без решения — stop |
| Н1 | WP01 архитектура и контракты v1.1, tabletop; WP02 каркас, CI, auth, migrations (18) | повторный review, миграции, schema/API alignment, авторизация, audit/fencing contract |
| Н2 | WP03 CRUD/tree/project/goal/idea/dependencies/commitments (01,02,06,09,10); WP04 recurrence/delegation (03,06) | T01,T02,T05,T08, split COUNT, concurrency |
| Н3 | WP05 calendar/day plan/capacity/manual UX (04,11,12); WP06 reminder/Telegram (05,16); WP07 Google Calendar read (15) | T03,T04,T09,T11, read portion T10; no external write yet |
| Н4 | WP08 inbox/Gmail (07,14); WP09 AI proposals/priority (08,17), deterministic quick input | T06,T07,T12, no injection/direct side effects; controlled budget |
| Н5 | WP10 reviews/memory/horizons (11–13); WP11 hardening external Calendar write (15), E2E, UX, pilot if gate passes | T09,T10,T13,T15,T17, external ambiguity tests and actual live smoke after permission |
| Н6 | WP12 fixes, restore/load tests, audit, docs, release candidate | all required tests, T14,T16, critical risks closed, pilot evidence; final owner approval |
## Critical path and stop conditions
WP00 → approval of spec/contracts → WP02 → WP03/04 → WP05/06/07 read → WP08/09 → WP10/11 → WP12. WP08 can parallelize with WP05 only if core interfaces stable and capacity available. Calendar writes enabled only after A07/T10/T13 and provider permission. Do not use a mocked provider as proof of real readiness. If WP00 fails, stop affected mandatory work, resolve or obtain explicit fallback approval; read-only Calendar is proposed only, not preapproved. If OAuth issue remains, local manual mode may be demonstrated as partial product, not accepted full P0. No P1 until all P0 gates met. If week5 not safe, defer pilot; if 7–14 days not complete in week6, deliver RC only, extend acceptance. Reserve 7–8 weeks if complexity requires; no forced schedule by reducing security/testing.
## Codex process
Issue per WP/subtask, branch and PR, FR/US/T references, tests first, update decisions. No prod changes, secrets, external messages, real calendar changes, deploy or merge without separate owner permission. Separate static tabletop from execution evidence. 
## Open decisions
D01–D08 verified/recorded in SPIKE_GATE: VPS, OAuth scopes/calendar/labels, domain/TLS, OpenClaw adapter/model, budget, retention/backup destination, timezone/work windows/quiet hours, Gantt P1. Defaults in v1.1 provisional; owner approval pending. 
