# Логическая модель PostgreSQL
Типы: uuid PK, timestamptz UTC, date для date-only, text, integer, numeric, jsonb только для payload/metadata. Все доменные таблицы owner_id FK users; FK составные (owner_id,id) для изоляции. created_at/updated_at, version int>=1 на редактируемых объектах; soft delete где указано. Enum/check constraints. Ниже поля помимо общих.
| Таблица | Поля (основные) |
|---|---|
| users | login unique, password_hash, timezone IANA, active |
| sessions | token_hash unique, expires_at, revoked_at |
| preferences | owner unique, ui_mode simple/advanced default simple, work_windows jsonb, reserve_pct 0..50, quiet_hours, weights jsonb, autonomy, budget_minor, currency |
| memory_items | key, value jsonb, provenance, confirmed_at, deleted_at |
| goals | title, outcome_metric, target_date, horizon, status, deleted_at |
| projects | title, goal_id nullable, status, start_date, target_date, deleted_at |
| tasks | title, description, parent_id nullable, project_id nullable, goal_id nullable, kind task/milestone, status, due_date OR due_at, due_tz, earliest_at, estimate_min nullable, actual_min nullable, completed_at, deleted_at, hard_deadline bool, quick_priority high/medium/low/unset, if_done_text nullable, if_not_done_text nullable, result_text, recurrence_id nullable, occurrence_key nullable |
| dependencies | predecessor_id, successor_id, kind FS, unique pair |
| ideas | title, notes, goal_id nullable, promoted_task_id nullable, deleted_at |
| commitments | task_id XOR event_id, counterparty_label, promised_at, due_at nullable, check_at nullable, outcome, status open/fulfilled/cancelled, resolved_at nullable |
| delegates | kind self/person/agent, display_name, contact_ref nullable |
| delegations | task_id, delegate_id, status, outcome_expected, outcome_actual, due_at, check_at, accepted_at, verified_at |
| recurrence_rules | template_task_id unique, dtstart_local timestamp, tzid, rrule text, duration_min, active, supersedes_id nullable |
| recurrence_exceptions | rule_id, occurrence_key (original local start + tz), action skip/override, patch jsonb, unique rule/key |
| events | title, starts_at/ends_at OR start_date/end_date exclusive, tzid, all_day, origin local/google, transparency, status, deleted_at |
| time_blocks | task_id, starts_at, ends_at, locked, deleted_at |
| reminders | task_id XOR event_id XOR delegation_id, absolute_at OR anchor+offset_min, channel, enabled, critical, snoozed_until |
| notifications | reminder_id nullable, delivery_key unique, scheduled_at, state, read_at nullable, provider_message_id nullable, attempts, error_code |
| sources | type gmail/telegram/meeting/social/manual/calendar, account_ref, external_id, url nullable, content_excerpt, content_hash, received_at, retention_until, deleted_at |
| task_sources | task_id, source_id, evidence_excerpt, unique pair |
| inbox_items | source_id, candidate_key, kind task/idea/reference/unknown, status new/reviewed/dismissed, proposal_id nullable, accepted_task_id nullable, accepted_idea_id nullable, resolved_at nullable, unique owner/source/candidate_key |
| priority_assessments | task_id, I/U/R/B nullable 0..10, weights_snapshot, score nullable, explanation, evidence jsonb, confidence, origin, manual_override, assessed_at |
| proposals | kind, payload jsonb, source_id nullable, status pending/approved/rejected/expired/executing/applied/conflict/failed/uncertain_reconcile_pending/uncertain_manual/revoked, operation_id unique nullable, dispatch_started_at nullable, provider_resource_id nullable, reconcile_attempts, reconcile_at nullable, last_error_code nullable, target_version nullable, expires_at, payload_hash |
| approvals | proposal_id unique, actor_id, payload_hash, approved_at, revoked_at |
| integration_accounts | provider, provider_account_id, encrypted_credentials, scopes, status |
| sync_state | account_id, collection_id, cursor, last_success_at, last_error, unique account/collection |
| external_links | account_id, collection_id, external_id, event_id, etag, remote_snapshot jsonb, unique account/collection/external_id |
| jobs | kind, payload, dedupe_key unique, state queued/leased/dispatching/reconcile_pending/succeeded/failed/dead_letter/cancelled/uncertain_manual, run_at, lease_until, lease_token bigint default 0, attempts, last_error, operation_id nullable, dispatch_started_at nullable, reconcile_attempts, last_reconcile_at nullable |
| idempotency_keys | actor_id, route, key, request_hash, response jsonb, expires_at, unique actor/route/key |
| audit_events | actor_type/id, action, entity_type/id, correlation_id, outcome, reason_code nullable, redacted_diff, occurred_at |
| ai_usage | model, feature, input/output_tokens, cost_minor nullable, currency, request_id unique, timestamp |
| reviews | kind daily_am/daily_pm/weekly, period_start date, tzid, facts jsonb, advice jsonb, user_notes nullable, generated_at, unique owner/kind/period_start/tzid |
| daily_plans | local_date, tzid, status draft/confirmed, confirmed_at nullable, capacity_snapshot jsonb nullable, current_revision_no, unique owner/local_date/tzid |
| daily_plan_items | plan_id, task_id nullable (ON DELETE SET NULL only task reference; ownership validated), task_snapshot_title nullable, position, outcome_rank nullable 1..3, state active/completed/removed, unique plan/task for live ref, unique active plan/outcome_rank, unique plan/position |
| daily_plan_revisions | plan_id, revision_no, reason_code, snapshot jsonb, confirmed_at, unique plan/revision_no |
| purge_records | target_type, target_id, requested_at, completed_at, scope, no original sensitive content |
## Инварианты
task title nonblank, estimate/actual >=0; due_date XOR due_at если задан; all-day event uses date interval; ends>starts; no self/ancestor cycles or dependency cycles (service transaction + owner advisory lock); same-owner FK; parent/project consistency; unique (recurrence_id, occurrence_key) partial; source unique (owner,type,account_ref,external_id) where ext id exists; one active delegation per task (partial unique). Completed occurrence immutable for series edits; template excluded from actionable lists. Milestones estimate 0; no auto-close parent. Audit and domain mutation in one transaction.
## Связи и индексы
Goal 1:N Project; Project 1:N Task; Task self 1:N; Task N:M Source; Task N:M Task via dependencies; Task 1:N Block/Reminder/Assessment; Task 1:N Delegation history; recurrence template 1:N occurrence tasks. Index owner/status/due, owner/project/parent, owner/starts_at, jobs(state,run_at), source identity, audit(owner,occurred_at), reminders active. Migrations create tables in FK order; circular references add after creation. Seed synthetic data only.
## Retention
Proposed defaults: trash 30d, source excerpt 90d (user configurable; keep source identifiers/evidence required for retained tasks with explicit deletion option), audit 180d, encrypted backups 14d. Owner export and purge; redact deleted source from dependent AI cache; document backup expiration. Hard deletion preserves audit metadata without sensitive text. No raw email bodies persisted by default.

## Additional v1.1 invariants
Plans keyed by local date and recorded tzid; no implicit deadline/block from plan membership. Immutable confirmed snapshots exclude or redact sensitive titles on purge; live completion derived from task status, archived snapshot preserved. A plan change after confirmation creates draft and increments version; confirmation creates immutable revision, snapshot of capacity and items, reason required after first confirmation. Deletion/purge removes active items and nulls references, preserves minimal redacted history; re-ranking atomic. Outcome ranks unique 1..3 and only selected active/completed tasks. Task ownership and plan timezone validated; same owner FK, nullable task reference implemented without nulling owner_id. 
Inbox reviewed items retain outcome link or terminal marker after purge; candidate identity stable per source; re-analysis never auto-reopens reviewed/dismissed; new distinct candidate only explicit re-review; do not use prompt version as identity. Source key uniqueness uses non-null account_ref for external sources (canonical sentinel for manual provider if relevant), null external ID handled by import idempotency. Accepted task/idea refs mutually exclusive; reviewed reference has neither. 
Job lease_token strictly increases on claim/recovery, never reused. All worker state updates conditional on token and expected state. External operation identity immutable, dispatch marker durable before call. Job/proposal state changed together in same transaction. Retain operation identity and terminal result for lifetime of retained action; expire generic HTTP idempotency keys after configurable 24h but never rely on TTL for external replay protection. 
Retention proposal: excerpt 90d configurable, audit 180d, backup 14d. Explicit purge covers source/evidence copies, memory/prompt cache, review snapshots and logs where applicable, preserving noncontent identifiers if necessary for dedupe. Purge manifest stored separately from restore snapshot, encrypted and access restricted, retained at least longest backup retention plus restore window; replay on restored DB before serving or reconnecting. Provider originals not deleted. Purged sensitive data must not reappear from automatic reimport; store minimal tombstone where needed. 
