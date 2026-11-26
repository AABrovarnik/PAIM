# PAIM Zero — первичный benchmark платформ
Дата проверки публичной документации: 2026-11-26. Статус: desk research, не runtime benchmark. Не утверждает доступность конкретных функций в установленной версии/тарифе. Все ссылки ниже — публичные первичные источники; личные данные не использованы.
## 1. Пять архитектур (в пределах согласованного лимита)
| ID | Вариант | Подтверждено документацией | Непроверенные ограничения / цена | Роль |
|---|---|---|---|---|
| A | OpenClaw First | memory search, Skills, автоматизация, Telegram, sandbox/allowlists [S1–S4] | фактическая версия, доступность чтения Gmail/Calendar/GitHub, права, ограничение расходов, русский retrieval, ссылки на источник, reindex/delete; LLM/embeddings/VPS не бесплатны автоматически | первый кандидат Telegram |
| B | ChatGPT First | Projects с контекстом/файлами/приложениями, scheduled tasks [S5–S6] | аккаунт/лимиты, переносимость памяти/индекса, нет подтвержденного Telegram runtime; scheduled task в проекте не читает файлы этого проекта по S6 | baseline без новой инфраструктуры |
| C | Knowledge First (Dify) | pipeline RAG, knowledge retrieval, agent workflows, Docker Compose self-host [S7–S9] | default deployment включает отдельные сервисы, минимум 2 CPU/4 GiB RAM по S7; реальный headroom, лицензия/тариф, privacy, Telegram, live adapters | только если штатный поиск недостаточен |
| D | Automation First (n8n) | free self-host Community, Schedule Trigger, PGVector node, workflow platform [S10–S13] | операционная поддержка, credentials, execution limits, лицензия/платные функции, память/синтез и права требуют конфигурации | резерв для конкретных интеграций |
| E | Hybrid PAIM | сочетает проверенные компоненты A–D, небольшой слой унификации | больше компонентов, тестов, сопровождения, риска; не самостоятельный готовый продукт | только если конкретные пробелы A/B оправдают TCO |
## 2. Матрица экспертного предварительного ранжирования
Шкала 0–10, субъективная пригодность в текущих условиях, не измеренные характеристики. C1 intent 25%, C2 TCO 20%, C3 минимум нового кода 15%, C4 memory/RAG 15%, C5 integrations 10%, C6 control/security 10%, C7 portability 5%. C2 оценивает ожидаемое направление затрат, не цену в долларах. C6 оценивает предположительную управляемость, не прохождение security gate.
| ID | Архитектура | C1 | C2 | C3 | C4 | C5 | C6 | C7 | Итог /10 |
|---|---|---:|---:|---:|---:|---:|---:|---:|---:|
|A|OpenClaw First|8|8|9|7|8|6|8|7.80|
|B|ChatGPT First|8|9|10|6|8|7|5|7.95|
|C|Knowledge First / Dify|6|6|7|9|6|7|7|6.75|
|D|Automation First / n8n|7|7|7|7|9|7|8|7.25|
|E|Hybrid|9|6|5|9|9|7|9|7.60|
Формула sum(score_i * weight_i)/100. Не использовать разницу B/A 0.15 как статистически значимую. A выигрывает для Telegram-first и переносимости при условии проверки, B — по скорости старта без новой инфраструктуры. C выигрывает в специализированном RAG, но дороже по установке/поддержке; D лучше для детерминированных workflows, не полноценная модель владельца; E максимальная гибкость ценой сложности. Веса ранее предложены, баллы и выводы председателя подлежат целевому review. 
## 3. Органайзер: два кандидата, не часть пяти архитектур
Todoist: официальный API v1 описывает tasks, recurrent subtasks и операции; free tier заявлен [S14–S16]. Предпочтительный первый API-кандидат, но реальные scopes, фильтры, глубина истории, export, лимиты тарифа и отображение повторов не проверены.
TickTick: официальный сайт заявляет MCP/CLI/OpenClaw, справка описывает task hierarchy [S17–S18]. UX-кандидат, особенно интересен для интеграции; точный контракт read-only, доступность API/MCP и тариф требуют отдельной проверки, маркетинговое заявление не заменяет документацию интерфейса. 
Не выбирать по UX-бенчмарку V2 без учета API; не переносить реальные задачи. Для UC03/04 органайзер пока вообще не нужен.
## 4. Экономика (12 месяцев, не фактические тарифы)
| Вариант | Дополнительные расходы исследования | Основные драйверы TCO прототипа |
|---|---|---|
| A | 0 новых покупок | существующий VPS и LLM entitlement/usage, время security/setup, retrieval |
| B | 0 новых покупок | существующая подписка, лимиты/доступность приложений, переносимость |
| C | не устанавливался | дополнительная RAM/CPU, embedding/LLM, upgrade/backup |
| D | не устанавливался | VPS headroom, execution/support, LLM/embeddings, платные функции при необходимости |
| E | не реализован | сумма нужных компонентов без двойного счета, интеграция/поддержка |
Нет доступа к фактическим счетам/лимитам, RAM VPS и цене модельных вызовов; невозможно честно дать подтвержденный TCO в долларах. Free edition != бесплатная эксплуатация. На этапе исследования не инициировать новых платных операций. До BUILD подтвердить цены, исходные ресурсы и план/факт затрат по VALUE_COST_CONTRACT.
## 5. Решение для проверки (не финальный выбор)
B — baseline на уже доступном ChatGPT/GitHub, A — кандидат на PAIM Zero в Telegram. Первый test: найти актуальную концепцию и ответить на вопрос о статусе проекта по фиксированным публичным MD. Проверить ссылки, draft vs approved, дату/версию, отказ при отсутствующем документе и устойчивость к подложной инструкции. Не нужен собственный UI, PostgreSQL/pgvector, Dify или n8n для этого теста. Затем только при недостаточности built-in memory — сравнить гибридный retrieval. 
### Минимальный spike (после разрешения)
- На VPS только read-only version/help/available Skills + CPU/RAM/disk aggregate; не выводить токены/полный config. Проверить текущую версию и сопоставить с docs; не обновлять автоматически.
- Выделенный sandbox/агент, owner-only, запрет exec/browser/gateway/cron и внешних write для агента, читающего недоверенные тексты; проверить фактическую enforceability, а не только system prompt.
- Тот же фиксированный public corpus для A/B. Не использовать реальные письма, календарь, секреты. Проверить запросы по UC03/04 и негативные случаи. Записать источник, версию, качество, elapsed/human time, расходы/unknown.
- Если A недоступен/опасен или не проходит минимальный результат, не расходовать больше: baseline B или согласованное ограничение; C/D только под конкретный gap. Не считать PASS без actual result.
## 6. Источники
S1 https://docs.openclaw.ai/concepts/memory-search — поиск по памяти.
S2 https://docs.openclaw.ai/help/faq/skills-and-automation — Skills и automation.
S3 https://docs.openclaw.ai/channels/telegram — Telegram channel.
S4 https://docs.openclaw.ai/help/faq/security-and-access-control — controls for untrusted input, allowlist, sandbox.
S5 https://help.openai.com/en/articles/10169521-projects-in-chatgpt — Projects, memory, apps.
S6 https://help.openai.com/en/articles/10291617-scheduled-tasks-in-chatgpt — scheduled tasks, limits, project-file restriction.
S7 https://docs.dify.ai/en/self-host/deploy/quick-start/docker-compose — default services, minimum hardware.
S8 https://github.com/alibaba/dify — RAG pipeline and deployment.
S9 https://github.com/langgenius/dify-docs/blob/main/en/self-host/use-dify/nodes/knowledge-retrieval.mdx — retrieval node.
S10 https://docs.n8n.io/hosting/community-edition-features/ — free Community and edition differences.
S11 https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.scheduletrigger — scheduling.
S12 https://docs.n8n.io/integrations/builtin/cluster-nodes/root-nodes/n8n-nodes-langchain.vectorstorepgvector/ — vector store node.
S13 https://docs.n8n.io/ — workflow platform.
S14 https://developer.todoist.com/api/v1/ — API contract.
S15 https://developer.todoist.com/ — developer entrypoint.
S16 https://www.todoist.com/pricing/ — free plan.
S17 https://ticktick.com/?language=en_US — claimed integration options.
S18 https://help.ticktick.com/articles/7055792921664028672 — hierarchy.
## 7. Limitations
Документация меняется; использованы доступные на дату страницы/результаты, не полный аудит лицензий или тарифов. Независимый экспертный раунд и практические тесты не выполнены. Никаких реальных аккаунтов, VPS или новых подписок не подключалось. Оценки — гипотезы для следующего шага, а не подтвержденная производительность.
