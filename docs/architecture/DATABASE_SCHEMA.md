# Логическая модель PostgreSQL
Типы: uuid PK, timestamptz UTC, date для date-only, text, integer, numeric, jsonb только для payload/metadata. Все доменные таблицы owner_id FK users; FK составные (owner_id,id) для изоляции. created_at/updated_at, version int>=1 на редактируемых объектах; soft delete где указано. Enum/check constraints. Ниже поля помимо общих.
| Таблица | Поля (основные) |
|---|---|
| users | login unique, password_hash, timezone IANA, active |
| sessions | token_hash unique, expires_at, revoked_at |
| preferences | owner unique, work_windows jsonb, reserve_pct 0..50, quiet_hours, weights jsonb, autonomy, budget_minor, currency |
| memory_items | key, value jsonb, provenance, confirmed_at, deleted_at |
| goals | title, outcome_metric, target_date, horizon, status, deleted_at |
| projects | title, goal_id nullable, status, start_date, target_date, deleted_at |
| tasks | title, description, parent_id nullable, project_id nullable, goal_id nullable, kind task/milestone, status, due_date OR due_at, due_tz, earliest_at, estimate_min nullable, actual_min nullable, completed_at, deleted_at, hard_deadline bool, result_text, recurrence_id nullable, occurrence_key nullable |
| dependencies | predecessor_id, successor_id, kind FS, unique pair |
| ideas | title, notes, goal_id nullable, promoted_task_id nullable, deleted_at |
| commitments | task_id XOR event_id, counterparty_label, promised_at, outcome, status |
| delegates | kind self/person/agent, display_name, contact_ref nullable |
| delegations | task_id, delegate_id, status, outcome_expected, outcome_actual, due_at, check_at, accepted_at, verified_at |
| recurrence_rules | template_task_id unique, dtstart_local timestamp, tzid, rrule text, duration_min, active, supersedes_id nullable |
| recurrence_exceptions | rule_id, occurrence_key (original local start + tz), action skip/override, patch jsonb, unique rule/key |
| events | title, starts_at/ends_at OR start_date/end_date exclusive, tzid, all_day, origin local/google, transparency, status, deleted_at |
| time_blocks | task_id, starts_at, ends_at, locked, deleted_at |
| reminders | task_id XOR event_id XOR delegation_id, absolute_at OR anchor+offset_min, channel, enabled, critical, snoozed_until |
| notifications | reminder_id nullable, delivery_key unique, scheduled_at, state, provider_message_id nullable, attempts, error_code |
| sources | type gmail/telegram/meeting/social/manual/calendar, account_ref, external_id, url nullable, content_excerpt, content_hash, received_at, retention_until, deleted_at |
| task_sources | task_id, source_id, evidence_excerpt, unique pair |
| inbox_items | source_id, kind task/idea/reference/unknown, status new/reviewed/dismissed, proposal_id nullable |
| priority_assessments | task_id, I/U/R/B nullable 0..10, weights_snapshot, score nullable, explanation, evidence jsonb, confidence, origin, manual_override, assessed_at |
| proposals | kind, payload jsonb, source_id nullable, status pending/approved/rejected/expired/executing/applied/conflict/failed/uncertain, target_version nullable, expires_at, payload_hash |
| approvals | proposal_id unique, actor_id, payload_hash, approved_at, revoked_at |
| integration_accounts | provider, provider_account_id, encrypted_credentials, scopes, status |
| sync_state | account_id, collection_id, cursor, last_success_at, last_error, unique account/collection |
| external_links | account_id, collection_id, external_id, event_id, etag, remote_snapshot jsonb, unique account/collection/external_id |
| jobs | kind, payload, dedupe_key unique, state, run_at, lease_until, attempts, last_error |
| idempotency_keys | actor_id, route, key, request_hash, response jsonb, expires_at, unique actor/route/key |
| audit_events | actor_type/id, action, entity_type/id, correlation_id, redacted_diff, occurred_at |
| ai_usage | model, feature, input/output_tokens, cost_minor nullable, currency, request_id unique, timestamp |
| reviews | kind daily_am/daily_pm/weekly, period_start, facts jsonb, advice jsonb, generated_at |
## Инварианты
task title nonblank, estimate/actual >=0; due_date XOR due_at если задан; all-day event uses date interval; ends>starts; no self/ancestor cycles or dependency cycles (service transaction + owner advisory lock); same-owner FK; parent/project consistency; unique (recurrence_id, occurrence_key) partial; source unique (owner,type,account_ref,external_id) where ext id exists; one active delegation per task (partial unique). Completed occurrence immutable for series edits; template excluded from actionable lists. Milestones estimate 0; no auto-close parent. Audit and domain mutation in one transaction.
## Связи и индексы
Goal 1:N Project; Project 1:N Task; Task self 1:N; Task N:M Source; Task N:M Task via dependencies; Task 1:N Block/Reminder/Assessment; Task 1:N Delegation history; recurrence template 1:N occurrence tasks. Index owner/status/due, owner/project/parent, owner/starts_at, jobs(state,run_at), source identity, audit(owner,occurred_at), reminders active. Migrations create tables in FK order; circular references add after creation. Seed synthetic data only.
## Retention
Proposed defaults: trash 30d, source excerpt 90d (user configurable; keep source identifiers/evidence required for retained tasks with explicit deletion option), audit 180d, encrypted backups 14d. Owner export and purge; redact deleted source from dependent AI cache; document backup expiration. Hard deletion preserves audit metadata without sensitive text. No raw email bodies persisted by default.
