# Архитектура PAIM 1.1
## ADR-001: модульный монолит
React + TypeScript + Vite; FastAPI/Pydantic, SQLAlchemy, Alembic; PostgreSQL; отдельный worker того же Python-пакета; Nginx/TLS; Docker Compose. Версии pin после проверки совместимости, не latest. PostgreSQL отдельная БД и роль PAIM, не менять существующие сервисы VPS. Backend не публиковать наружу. UI same-origin /api. Один владелец, owner_id во всех доменных запросах.
## Границы модулей
identity/settings, tasks/recurrence, projects/goals, schedule, delegation, inbox/sources, priority/daily_plans/reviews, commitments, proposals/approval, integrations, notifications/jobs, audit/usage. HTTP handlers → services → repositories. Транзакции сервисного слоя; запрещены прямые записи от LLM и внешних адаптеров. Общие DTO и ошибки.
## ADR-002: worker
Postgres jobs/outbox; SELECT FOR UPDATE SKIP LOCKED, monotonic lease_token, run_at, attempts, unique dedupe key. At-least-once internal processing; external dispatch marker and operation_id durable before network. State transitions and fencing exactly as A07; expired dispatching lease triggers read-only reconcile, never second dispatch. Backoff with jitter, max 5 for known-safe retry/reconcile; uncertain_manual on inconclusive outcome. Worker heartbeat; health independent of LLM. Cron materializes jobs, no network in DB transaction. Provider idempotency/conditional write required where available; cannot promise exactly-once external delivery.
## ADR-003: источник истины
PAIM владеет задачами/блоками; Google внешними событиями. Link external ID + ETag; локальная проекция read-only, изменения через proposal/approval/outbox. Импорт не создает задачи автоматически. Не создавать дубли при retry. Конфликт сохраняет обе версии для владельца.
## ADR-004: AI adapter
Interface analyze(source,context,schema), suggest(action,context), health. OpenClaw/прямой LLM adapters; mock для тестов. Timeout, ограниченный retry и circuit breaker. Не использовать Codex CLI как пользовательский runtime по умолчанию. Codex — разработчик. OpenClaw транспорт без shell и DB доступа, только ограниченный API. Конкретный протокол проверить spike; если не подходит, согласовать прямой LLM fallback. 
## ADR-005: авторизация
Локальная учетная запись владельца, Argon2id, серверные сессии с random token hash, HttpOnly/Secure/SameSite cookie, CSRF для mutations, rate limit. Нет публичной регистрации. Telegram binding одноразовым кодом через Web (TTL 10 мин), allowlist chat/user ID, private chat. OAuth state/PKCE where supported; токены шифровать.
## NFR (целевые, проверить)
10000 задач и 1000 событий в 90-дневном окне: p95 локальный CRUD <=500мс, списки <=1с на согласованном VPS, исключая внешние API; пагинация 50, max 200. AI jobs асинхронные с прогрессом. RPO 24ч/RTO 2ч цель; daily encrypted backup + restore drill. Логи без текста писем/секретов, correlation ID, uptime/queue lag/last sync. Worker polling 5–15 сек. Напоминания при работающем сервисе целевой lag <=60с; внешняя доставка best-effort.
## Развертывание
apps/web, apps/api, tests, infra, docs; dev/test/prod раздельны. Compose web/api/worker и выделенная БД или согласованный существующий Postgres. env.example без секретов. CI lint/typecheck/unit/integration/e2e, миграции на пустой БД, secret scan. Backup до миграции, план rollback с учетом необратимых миграций. Не выполнять prod deploy без разрешения.

## ADR-006: audit contract (CX-05)
| Event | Required evidence (redacted) |
|---|---|
| auth/bind success/failure, OAuth grant/revoke | actor/account ID, outcome, reason |
| proposal create/approve/reject/revoke/expire/replay | proposal/operation ID, hash/version, outcome, actor |
| dispatch/lease claim/expiry/stale token reject | job ID, lease token, previous/new state, correlation ID |
| external result/conflict/reconcile/manual resolve | operation ID, provider type, safe resource reference, error code/outcome, actor for resolution |
| task/plan/recurrence/commitment edit, override, merge | entity ID, old/new version, reason, redacted field names |
| retention/purge/export/restore/migration | authorized actor, target/count, result, migration version or backup ID |
| AI budget reserve/refusal/reconcile and schema rejection | request ID, model, estimated/actual cost, error code, no prompt text |
Successful domain mutation and audit atomic; rejected attempts logged in separate transaction so rollback cannot erase evidence. Logs no secrets, raw source, private notes or full payload. Alert on repeated stale tokens, unknown outcomes and failed backups. Do not expose unrestricted audit export.
## ADR-007: WP00 gate
See ../planning/SPIKE_GATE.md. Before WP03: owner-approved spec, repeated document review, actual PASS or explicit acceptable fallback per mandatory provider/capability. Infrastructure spikes use isolated resources only, no live modifications without permission. Gate unresolved means NOT READY; mock != PASS. Read-only degraded Calendar needs owner approval of modified release scope. 
