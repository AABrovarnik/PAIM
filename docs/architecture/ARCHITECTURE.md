# Архитектура PAIM 1.0
## ADR-001: модульный монолит
React + TypeScript + Vite; FastAPI/Pydantic, SQLAlchemy, Alembic; PostgreSQL; отдельный worker того же Python-пакета; Nginx/TLS; Docker Compose. Версии pin после проверки совместимости, не latest. PostgreSQL отдельная БД и роль PAIM, не менять существующие сервисы VPS. Backend не публиковать наружу. UI same-origin /api. Один владелец, owner_id во всех доменных запросах.
## Границы модулей
identity/settings, tasks/recurrence, projects/goals, schedule, delegation, inbox/sources, priority/reviews, proposals/approval, integrations, notifications/jobs, audit/usage. HTTP handlers → services → repositories. Транзакции сервисного слоя; запрещены прямые записи от LLM и внешних адаптеров. Общие DTO и ошибки.
## ADR-002: worker
Postgres jobs/outbox; SELECT FOR UPDATE SKIP LOCKED, lease, attempts, next_run, unique dedupe key. At-least-once; обработчики идемпотентны. Для неопределенного внешнего эффекта reconcile вместо слепого retry. Backoff с jitter, max attempts 5, dead-letter/ручной retry. Worker heartbeat; health не зависит от LLM. Cron materializes jobs, не выполняет сетевые действия в транзакции.
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
