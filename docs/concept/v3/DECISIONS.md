# V3 — переход от V2 и вопросы владельцу
Статус: DRAFT | 2026-11-25. Решения ниже предложены, не утверждены.
## 1. Что сохранить из v1.1
| Компонент | Решение V3 |
|---|---|
| Task CRUD, recurrence, hierarchy, delegation engine | Не писать заново; выбрать готовый organizer с API и UX. Учет ожиданий и candidate obligations — аналитика PAIM, не второй task manager |
| Собственный calendar editor, time blocks | Не писать; существующий Calendar SoT, read availability, рекомендации |
| Gantt/resource planner | Не писать; внешнее представление/ссылки |
| Day plan/reviews, three outcomes | Сохранить аналитические сценарии; внутренняя подтвержденная заметка/план только при необходимости, не копия задач |
| I/U/R/B, Eisenhower, consequences, null | Сохранить как опциональный способ оценки и советов, quick priority; неизвестное не считать низким, не менять SoT автоматически |
| Commitments/decision | Сохранить явные обязательства и журнал, связать с sources; письма не считать принятыми |
| Gmail/Calendar/Telegram permissions | Сохранить ограничения, read-only initial pilot |
| Worker/outbox, audit, ETag/confirmation | Переиспользовать принципы v1.1 там, где реальные write/notifications необходимы; не копировать сложность без use case |
| Memory/RAG | Расширить: source map, semantic+episodic+strategic, hybrid retrieval and provenance |
| UX tests | Переформулировать вокруг времени поиска, качества брифингов, актуальности и экономии усилий |
## 2. Десять вопросов для обсуждения
| Вопрос | Предложение по умолчанию |
|---|---|
| Q1. Главная ценность | экономия времени и помощь в решениях, не новый органайзер |
| Q2. Основной интерфейс | Telegram first + компактный Web dashboard, не 12 отдельных приложений |
| Q3. Авторитетный organizer | один выбранный готовый продукт после API/UX/cost исследования |
| Q4. Источники P0 | календарь, organizer, выбранная почта, GitHub/малый corpus; доступ к личным данным только после согласия |
| Q5. RAG corpus | проекты, учебные материалы, инструкции/промпты и исследования; выборочная библиотека, не весь диск/почта |
| Q6. Персональная память | пользователь видит/правит/удаляет; факты и гипотезы разделены, значимые предпочтения подтверждаются |
| Q7. Действия ассистента | read-only external pilot; уведомления по явным подпискам; внешние изменения после отдельного согласования |
| Q8. Развертывание | существующая VPS если проходит WP00, private data, изолированный сервис; локальные/облачные модели по privacy/cost |
| Q9. Проактивность | утро/вечер/неделя, одна тема дайджеста, quiet hours, опциональные подписки |
| Q10. Первый успех | полезный ответ на 10 повседневных запросов, measurable time saving, безопасное чтение, переносимость знаний |
## 3. Открытые риски
- R-V3-01: интеграционная сложность вместо разработки органайзера. Mitigation: ready-made connector first, ограничить источники, live smoke.
- R-V3-02: «единая панель» становится еще одним приложением. Mitigation: Telegram-first, reusable dashboards, no duplicate CRUD.
- R-V3-03: устаревшие и противоречивые знания. Mitigation: SoT/freshness/version citations, abstention.
- R-V3-04: утечка персональной памяти через RAG/model/Telegram/public repo. Mitigation: scope before retrieval, encryption, minimal excerpts, explicit policy, redaction and purge.
- R-V3-05: гипотезы о владельце становятся фактами. Mitigation: confirmation, confidence, review, expiry.
- R-V3-06: агенты увеличивают latency/cost и ошибки. Mitigation: one coordinator/Skills first, measured escalation.
- R-V3-07: неактуальные уведомления и перегрузка. Mitigation: user subscriptions, grouping, quiet hours, dedup.
- R-V3-08: зависимость от провайдера. Mitigation: portable memory/docs/journal, adapter boundaries, fallback only after approval.
- R-V3-09: ложная ретроспектива. Mitigation: observed facts vs inferred outcomes, coverage gaps and owner feedback.
## 4. Правила перехода
Сохранить старый PROJECT_CHARTER, V2, оба PR и v1.1 без переписывания истории. Ветка docs/concept-v3 — черновик, основан на docs/spec-v1 для сохранения контекста. Если V3 согласована, оформить новый charter/ADR supersession и решить судьбу старых PR (архив/закрытие/перенос нужных документов) отдельным решением. Не merge автоматически, не начинать реализацию по v1.1 или V3 до подтверждения. Независимая экспертиза V2 не переносится как оценка V3; полезные замечания переиспользуются. 
