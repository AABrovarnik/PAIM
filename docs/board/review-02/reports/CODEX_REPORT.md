# CODEX Audit Report (CTO/QA) — Review 02

## 1) Метаданные
- Дата: 24.11.2026 (Europe/Moscow).
- Режим: audit только по спецификации (без runtime).
- Модель: Codex CLI (точный model-id не показан интерфейсом).
- SHA снимка: `67dd4c397ba4fdefe570798f411767828a01894f`.
- Область: `docs/spec-v1`, роль/префикс: `CX`.
- Ограничения: не проверялись live OAuth/Gmail/Calendar/Telegram, не запускались миграции/CI/нагрузка.
- Ключевые источники: `prompts/board-review-02/COMMON_PROTOCOL.md`, `docs/requirements/MASTER_TZ.md`, `docs/architecture/ARCHITECTURE.md`, `docs/architecture/API.md`, `docs/architecture/DATABASE_SCHEMA.md`, `docs/architecture/ALGORITHMS.md`, `docs/architecture/INTEGRATIONS_SECURITY.md`, `docs/testing/TEST_PLAN.md`, `docs/planning/IMPLEMENTATION_PLAN.md`, `docs/planning/ROADMAP_V2.md`.

## 2) Вердикт
**NOT READY** к старту основной реализации.

Причина: есть блокирующие неопределенности pre-implementation (spike gates), а также недоформализованные failure-контракты для lease/outbox и split COUNT (DST/skip/exceptions).

## 3) Замечания
| ID | Sev | Локация | FR/US/T | Доказательство | Последствие | Исправление | Приемка | Оценка |
|---|---|---|---|---|---|---|---|---|
| CX-01 | BLOCKER | `docs/requirements/MASTER_TZ.md:23`, `docs/planning/IMPLEMENTATION_PLAN.md:14`, `docs/architecture/INTEGRATIONS_SECURITY.md:15` | FR14/15/17; US31–36; T06/10/11/12 | Открыты D01–D08 и отдельно сказано, что production access/OAuth availability не подтверждены. | Риск срыва 6 недель и ложного старта интеграций. | Ввести `WP00 Spike Gate` (pass/fail+fallback) до WP03+. | Новый T00 по каждому провайдеру с фактом доступа/ограничений. | 2–3 дн |
| CX-02 | HIGH | `docs/architecture/ARCHITECTURE.md:7`, `docs/architecture/DATABASE_SCHEMA.md:32`, `docs/architecture/ALGORITHMS.md:15` | FR05/15/18; US12/41; T04/10/13 | Есть lease/SKIP LOCKED/reconcile, но не задан fencing token/state transition при lease expiry. | Двойной внешний эффект при race/crash. | Зафиксировать state machine job + `lease_token` compare-on-update. | Тест: lease expiry mid-flight, второй worker не дублирует side-effect. | 2 дн |
| CX-03 | HIGH | `docs/architecture/ALGORITHMS.md:7`, `docs/requirements/USER_SCENARIOS.md:10`, `docs/testing/TEST_PLAN.md:6` | FR03; US07–11; T02 | Для split `future` с COUNT нет точного правила остатка при DST skip/Feb31/исключениях. | Нестабильное число future occurrences. | Формула остатка COUNT + правила skipped/completed. | T02: детерминированный набор occurrence_key после split. | 1.5 дн |
| CX-04 | HIGH | `docs/architecture/API.md:4`, `docs/architecture/API.md:37`, `docs/architecture/ALGORITHMS.md:15` | FR15/17/18; US32/33/41; T10/12 | Нет единого API-статуса для "approved, external outcome uncertain" после timeout/crash. | Клиент не различает applied/uncertain, риск неверных retries. | Добавить `uncertain_reconcile_pending` + обязательные reconcile поля. | T10/T12: timeout -> uncertain state, повтор approve блокируется. | 1 дн |
| CX-05 | MEDIUM | `docs/architecture/ARCHITECTURE.md:5`, `docs/architecture/API.md:33`, `docs/architecture/DATABASE_SCHEMA.md:34` | FR18; US37–42; T13/14 | Нет contract-matrix обязательных audit events по критичным переходам. | Слабая расследуемость инцидентов. | Добавить audit coverage matrix (approve/replay/conflict/lease/migration). | T13/T14: проверка обязательных audit следов. | 1 дн |
| CX-06 | MEDIUM | `docs/planning/ROADMAP_V2.md:7`, `docs/planning/ROADMAP_V2.md:8`, `docs/planning/IMPLEMENTATION_PLAN.md:7` | FR05/14/15/16/17; US17–36; T04/06/10/11/12 | Недели 3–4 перегружены интеграциями+AI без hard gate результата spike. | Каскадный риск срыва и урезания качества. | Перенести часть write-интеграций в hardening после закрытых gates. | Обновленный критический путь с stop-conditions. | 0.5–1 дн |

## 4) Покрытие FR→US→T (CTO/QA)
| FR | US | T | Комментарий |
|---|---|---|---|
| FR01–02 | US01–06,42 | T01,T15,T17 | Покрыто |
| FR03 | US07–11 | T02 | Нужна формализация split COUNT (CX-03) |
| FR04–05 | US02,12,13,27,28 | T03,T04,T17 | Нужен fencing для lease/crash (CX-02) |
| FR07,14,17 | US17–21,35,36 | T06,T12 | Нужен статус uncertain/reconcile (CX-04) |
| FR15–16 | US31–34,41 | T10,T11 | Сценарии есть, нужен строже failure contract |
| FR18 | US37–42 | T13,T14,T16 | Нужен audit matrix и gate перед интеграциями |

Пробелы: (1) lease/reconcile state machine, (2) split COUNT formula, (3) formal spike gate.

## 5) Оценка
- Полнота: 7.5/10 (25%)
- Непротиворечивость: 7.0/10 (25%)
- Реализуемость: 6.0/10 (20%)
- Тестируемость: 7.5/10 (20%)
- Безопасность/контроль: 7.0/10 (10%)

**Итог: 70.25/100.** BLOCKER не компенсируется баллом.

## 6) Минимум до старта / P1-P2 / вопросы
Обязательные до старта:
1. `WP00 Spike Gate` по OAuth/Gmail/Calendar/OpenClaw/VPS/backup.
2. State machine + fencing для jobs/outbox.
3. Формула split COUNT (DST/skip/exceptions) + тесты.
4. API-статус `uncertain_reconcile_pending`.

Отложить: FR19–21 (P1) до стабилизации P0; P2 без изменений.

Вопросы владельцу:
1. Допустим ли fallback: read-only integrations + manual-confirmed writes later?
2. Приоритет: фикс 6 недель любой ценой или сдвиг срока ради качества/security?
3. Подтверждаете retention source excerpts 90d (или короче)?

## 7) План проверки изменений
1. Обновить `API.md`, `ALGORITHMS.md`, `IMPLEMENTATION_PLAN.md`; добавить `SPIKE_GATE.md`.
2. Расширить T02/T04/T10/T12 кейсами crash-before/after-effect, lease-expiry, replay, stale ETag.
3. Провести tabletop по: crash до/после внешнего эффекта, истечение lease, stale ETag, replay approval, concurrent cycle, DST+split COUNT.
4. Зафиксировать go/no-go перед WP03 и перед пилотом.

