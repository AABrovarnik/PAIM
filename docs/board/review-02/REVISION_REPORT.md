# PAIM — отчет о доработках ТЗ v1.1
Дата: 2026-11-24. Исходная версия экспертов: 67dd4c397ba4fdefe570798f411767828a01894f. Ветка docs/spec-v1. Основание: запрос владельца провести доработки; конкретные defaults предложены председателем и внесены на рассмотрение, не считать отдельным утверждением или разрешением на merge/deploy.
## Источники и статус
- [Gemini](reports/GEMINI_REPORT.md): 62.25/100, NOT READY; 2 BLOCKER, 6 HIGH, 4 MEDIUM.
- [Codex](reports/CODEX_REPORT.md): 70.25/100 (веса пересчитаны: 18.75+17.5+12+15+7=70.25), NOT READY; 1 BLOCKER, 3 HIGH, 2 MEDIUM.
- Среднее 66.25/100, не новый результат v1.1. 18 замечаний независимых экспертов. 8 дополнительных CH-01–08 предложены ChatGPT в предыдущей консолидации, не независимый отчет Claude. Claude недоступен; не имитировать его заключение.
- Статусы ниже: SPEC = исправление внесено в документы, требует re-review и будущей runtime-приемки; GATE = требование формализовано, исходный блокер открыт до факта; ADAPTED = цель включена альтернативным способом, детали в EXCLUSIONS_REPORT. Не означают PASS тестов.
## Реестр 18 независимых замечаний
| ID | Sev | Статус | Внесенное решение / документы | Проверка |
|---|---|---|---|---|
| GM-01 | BLOCKER | SPEC | daily_plans/items/revisions; GET/PUT daily-plan, manual review; FR11/12, schema/API/A02/UI | US51–54,T09,T17 |
| GM-02 | BLOCKER | SPEC | day/project/date ready; inbox unscoped; day membership, две секции, accept без proposal, per-item batch | US43/44/86,T01/T06/T15 |
| GM-03 | HIGH | ADAPTED | ui_mode, quick priority + known I/U matrix + full optional P; unknown not Q2; strategic list | US57/58/82,T07/T17 |
| GM-04 | HIGH | SPEC | My day=Today, list rules and horizons 7/30/90/year, UI and A02/API | US45/55/83,T03/T09/T17 |
| GM-05 | HIGH | SPEC | intersect W, union busy, demand separate, outside minutes, unknown count, 30min preview only, multi-block choice, transparent style | US47–49/80,T03 |
| GM-06 | HIGH | SPEC | bottom nav, persistent add, manual slot picker, state preservation | US50,T17 |
| GM-07 | HIGH | ADAPTED | commitments retained in cards/waiting, explicit lifecycle; task consequence fields, review/return/copy text | US58–61,T05/T07 |
| GM-08 | HIGH | SPEC | 46 additional US43–88; trace matrix FR01–18 positive/boundary, added test detail | T01–17, matrix |
| GM-09 | MEDIUM | SPEC | toggle Calendar/Assistant, default calendar; fallback if AI disabled | UI_UX,T17 |
| GM-10 | MEDIUM | SPEC | fresh preview on expired/conflict, field diff, preserve in-memory draft, recurrence skip warnings | US69/76/87,T02/T10/T13/T17 |
| GM-11 | MEDIUM | ADAPTED | deterministic chips today/tomorrow/time/project, no LLM, standardized 5 repeats each desktop/mobile | US43,T17 |
| GM-12 | MEDIUM | ADAPTED | one-dialog waiting preset, drafted default, sent only confirmed fact, no send | US59/60,T05 |
| CX-01 | BLOCKER | GATE | WP00 and T00 for actual infrastructure/provider access, explicit fallback and no-go | SPIKE_GATE T00-01..11 NOT RUN |
| CX-02 | HIGH | SPEC | job state/monotonic fencing token/dispatch marker, no blind resend, provider contract | US72/73/74,T04/T10 |
| CX-03 | HIGH | SPEC | count valid original occurrences, invalid skip != cancelled exception, original key split, explicit mapping/immutable history | US70/71/87,T02 |
| CX-04 | HIGH | SPEC | uncertain_reconcile_pending/manual, operation metadata, replay status, read-only reconcile | US73/75/81,T10/T12 |
| CX-05 | MEDIUM | SPEC | audit matrix ADR-006, success/rejected events, redact, migration/restore | T13/T14 |
| CX-06 | MEDIUM | SPEC | WP00 then 6 weeks, read before write, write hardening week5, gates, RC if pilot incomplete | implementation plan and roadmap |
## Дополнительные замечания председателя
| ID | Решение | Проверка |
|---|---|---|
| CH-01 | Записанная timezone для due/series/plans, новые даты используют новую зону | US53/T03/T09 |
| CH-02 | Удаление задачи из live plan, сохранение redacted revision | US52/T09 |
| CH-03 | Draft при изменении подтвержденного плана, версии и причины | US52/T09 |
| CH-04 | Max 3 AI advice по умолчанию, раскрытие | US80/T09 |
| CH-05 | Общая авторизация/версии Web/TG | US76/T13 |
| CH-06 | Stale timestamp, revalidation, no guarantee free time | US65/T03/T10 |
| CH-07 | Стабильные source/candidate, terminal decision сохраняется | US64/T06 |
| CH-08 | Purge derived copies/cache; backup replay manifest before service, retention | US63/77/T14 |
## Состав изменений
Обновлены MASTER_TZ, FUNCTIONAL_REQUIREMENTS, USER_SCENARIOS, UI_UX, DATABASE_SCHEMA, API, ALGORITHMS, ARCHITECTURE, INTEGRATIONS_SECURITY, AI_AGENTS, TEST_PLAN, IMPLEMENTATION_PLAN, ROADMAP_V2, RISK_REGISTER, AGENTS. Новые SPIKE_GATE, REVISION_REPORT, EXCLUSIONS_REPORT. P0 FR01–18 прежний по направлениям, увеличена формализация; P1/P2 не переведены в обязательный объем. Исходные экспертные отчеты и согласованная концепция V2 не переписаны.
## Предлагаемые defaults и ограничения
My day=Today, простой режим, обязательства в карточках и Ожидаю, 30мин только предложение при ручном размещении, 90d excerpt configurable; read-only fallback только с отдельным одобрением; шесть недель после WP00, продление ради качества. Эти конкретные решения еще подлежат утверждению владельцем. 
## Проверка и остаточные условия
Документная трассировка выполнена при редактировании; независимый повторный аудит, реальные миграции/CI/UX/интеграции/нагрузка и T00 не выполнены. Отдельный tabletop статического анализа — не доказательство исполнения. GM-01/02 адресованы на уровне ТЗ, CX-01 открыт. Не присваивать v1.1 новый балл или READY без повторной проверки. До WP03: owner approval, повторное review, T00 PASS/approved fallback, устранение блокеров. До пилота/релиза: T01–T17, live smoke, безопасность, restore, pilot. Риск сроков сохраняется. 
См. [невключенные замечания и варианты](EXCLUSIONS_REPORT.md).
