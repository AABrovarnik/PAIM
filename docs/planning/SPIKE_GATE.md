# WP00 / T00 — предварительный инфраструктурный допуск
Дата: 2026-11-24. Статус всех проверок: NOT RUN. Владелец разрешил доработать ТЗ, не разрешил автоматически доступ/изменения реальных систем. Оценка 2–3 рабочих дня при доступности ресурсов, не обещание. Выполняет Codex/владелец в согласованной изолированной среде после отдельного разрешения.
## Правила
Сначала read-only диагностика. Не менять существующие службы/порты, nginx, PostgreSQL, OpenClaw, OAuth grants, почту, реальный календарь. Не читать реальные письма ради smoke: использовать выделенную тестовую метку/аккаунт и синтетическое сообщение с разрешением. Не записывать токены, реальные адреса, данные или полные stdout с секретами в Git. Проверки write только отдельный тестовый календарь/бот/БД после явного согласия. Mock/документация не заменяют live proof.
| ID | Проверка | PASS / доказательство | Если не проходит |
|---|---|---|---|
| T00-01 D01 | VPS/Ubuntu: CPU, RAM, диск, порты, существующие службы, Docker/Compose | redacted nproc/free/df/ss/versions, оценка headroom для dev/test/prod и NFR, согласованные порты | локальная dev или отдельный VPS по решению владельца; прод не трогать |
| T00-02 D01 | PostgreSQL: версия и изоляция | в отдельной тестовой БД/роли миграция, транзакции, locks, test-only create/read; не требовать superuser приложения | отдельный контейнер/БД после согласования |
| T00-03 D02 | Gmail OAuth | consent, минимальный read-only scope, синтетическое письмо из выделенной метки, refresh/revoke test где разрешено; отсутствие write scope | manual import как предлагаемый degraded fallback, требуется изменение P0 |
| T00-04 D02 | Calendar read | selected test calendar, event/all-day/transparent/ETag, sync and stale handling | ручной локальный календарь только согласованный fallback |
| T00-05 D02 | Calendar write | isolated test calendar, explicit approval, create/update/delete test event, stable ID/conditional ETag supported or documented safe restriction, lookup/reconcile contract; no guests | read-only release only after explicit scope approval; write capability remains unaccepted |
| T00-06 | Telegram | separate test bot or approved existing dedicated bot (avoid competing pollers), private owner bind, /add and test notification, duplicate update handling, no token logged | Web notification as proposed fallback; external delivery best-effort |
| T00-07 D04 | OpenClaw | version and supported transport, schema-constrained synthetic request, timeout/failure, no arbitrary shell/DB access, no modification to current services | direct LLM adapter if approved; otherwise AI integration BLOCKED |
| T00-08 D05 | LLM | selected actual provider/model, test structured request, price from dated official source, max token and request cap, budget reservation policy and cost estimate | no paid requests on unknown pricing; mock/manual core only, approve fallback |
| T00-09 D03 | TLS/domain | agreed domain, certificate/HTTPS and loopback backend on isolated test setup, no production nginx changes without approval | isolated dev endpoint; prod release blocked without TLS |
| T00-10 D06 | Backup & purge | encrypted test DB backup to agreed storage, restore fresh DB, validate counts/links; separate purge manifest restore replay test, measure duration | resolve storage/key/restore issues before real data use |
| T00-11 D07/08 | Owner settings/scope | timezone, work/quiet windows, reserve, retention, backup, budget and Gantt P1 recorded; approval of actual v1.1 | mark BLOCKED for unresolved material scope/policy decision |
## Safe initial read-only commands (Linux, examples; run by owner/Codex)
```bash
uname -a
nproc
free -h
df -h /
docker --version
docker compose version
psql --version
ss -ltn
systemctl is-active nginx
```
Report only necessary aggregates/versions; do not expose public IP, secrets, full service env or personal data. Commands shown not executed. Check OpenClaw via documented local CLI help/version before adapter test; don't guess endpoints. For live tests specify exact scope and test account in private working context, not public report.
## Report template
docs/testing/results/T00-YYYY-MM-DD.md: ID, status PASS/FAIL/BLOCKED/NOT RUN, date, actual environment (redacted), command/test, expected vs actual, evidence ref, risk, approved fallback with owner decision/date, next action. For unavailable verification mark BLOCKED, never PASS from mock. All mandatory safety controls must PASS (authorization, isolation, no unauthorized writes, secure storage, TLS before production, backup/restore before real data); capability fallback cannot waive these. 
## Gate decision
Before WP03: spec review + owner approval, T00 PASS for mandatory capabilities or explicitly owner-approved fallback for affected release scope; any critical safety FAIL/BLOCKED => NO-GO. Before pilot: repeat relevant live smoke on isolated resources, all P0 tests PASS and no unresolved critical issues. Before release: pilot evidence and owner approval. The gate document is prepared; no checks are executed by this revision.
