# REST API v1
Base /api/v1, JSON UTF-8, UUID, RFC3339 timestamps with offset, IANA timezone, date YYYY-MM-DD. Owner from session, never trust owner_id from client. OpenAPI generated from Pydantic and checked into repo during implementation. This is a contract draft, not a running API.
## Common semantics
GET list: limit default 50 max 200, cursor opaque, stable sort; filters status/project/goal/due range/search. Response {items,next_cursor}. POST returns 201, PATCH returns 200; DELETE soft-deletes and returns 204. Mutations include Idempotency-Key for create/actions, If-Match version for edit/delete; mismatch 409. Same key different payload 409. Error {code,message,field_errors,correlation_id}; 400 malformed, 401 unauthenticated, 403 forbidden/CSRF, 404 not found or other owner, 409 conflict, 422 invalid rule/cycle, 429 rate/budget, 503 unavailable. No secret in errors.
## Routes
| Method | Path | Contract |
|---|---|---|
| POST | /auth/login, /auth/logout | session cookie, logout revoke |
| GET/PATCH | /settings | work windows, tz, weights, budget, quiet hours, ui_mode, retention |
| GET/POST | /tasks | list/create; title required; context inbox/day/project/date; quick_priority and consequence fields |
| GET/PATCH/DELETE | /tasks/{id} | details/versioned update/soft delete |
| POST | /tasks/{id}/restore, /complete, /reopen | explicit status transition |
| GET/POST | /tasks/{id}/children | children, create child |
| POST/DELETE | /dependencies, /dependencies/{id} | predecessor/successor, cycle validation |
| GET/POST | /recurrences | create rule with template, preview |
| POST | /recurrences/{id}/preview, /change | scope single/future/all, occurrence_key, patch, preview hash |
| GET/POST | /projects, /goals, /ideas | list/create |
| GET/PATCH/DELETE | /projects/{id}, /goals/{id}, /ideas/{id} | details/edit/archive |
| POST | /ideas/{id}/promote | create linked task idempotently |
| GET/POST | /commitments, /delegates, /delegations | create/list |
| PATCH | /commitments/{id}, /delegates/{id}, /delegations/{id} | version/state validation |
| GET | /schedule?from=&to= | events, blocks, due markers, capacity |
| GET/POST | /events, /blocks | local event/block create; external via proposals |
| PATCH/DELETE | /events/{id}, /blocks/{id} | local only, external direct write denied |
| GET/POST | /reminders | list/create |
| PATCH/DELETE | /reminders/{id} | edit/disable |
| POST | /reminders/{id}/snooze | timestamp or minutes |
| GET/PATCH | /notifications, /notifications/{id} | list, read status |
| GET/POST | /inbox, /sources | list/import text |
| POST | /inbox/{id}/dismiss, /merge, /accept | dismiss, explicit merge, or accept with edited payload (manual without proposal supported) |
| POST | /tasks/{id}/assess | manual scores or enqueue AI |
| GET | /tasks/{id}/assessments | history |
| GET | /horizons, /reviews, /usage, /audit | filtered summaries |
| GET/PUT | /daily-plan/{date} | sync manual plan read/save/confirm; versioned; no LLM |
| GET | /daily-plan/{date}/history | confirmed revisions |
| POST | /reviews/manual | sync facts + optional user notes, no LLM |
| GET | /schedule/free-slots?date= | deterministic free intervals for manual selection, no ranking/auto-plan |
| POST | /reviews/generate, /ai/commands | async 202 job id |
| GET | /jobs/{id} | safe status/progress |
| GET/POST | /proposals | list/create validated proposal |
| POST | /proposals/{id}/approve, /reject, /revoke | hash + expected version, expiry |
| GET/POST/PATCH/DELETE | /memory, /memory/{id} | list/create/edit/delete confirmed preferences |
| GET | /integrations | statuses, scopes, last sync |
| POST | /integrations/{provider}/connect, /disconnect, /sync | OAuth initiation/revoke/async sync |
| GET | /integrations/{provider}/callback | state validation |
| POST | /telegram/bind-code | one-time code |
| GET | /export | owner JSON, no credentials |
| GET | /health/live, /health/ready | no private data |
## Examples
POST /tasks: {"title":"Подготовить отчет","due_date":"2026-12-01","estimate_min":60}. Response includes id,status=ready,version=1, owner-local due timezone (explicit due date context). PATCH /tasks/{id} with If-Match: 1 and {"title":"Отчет v2"} returns version=2.
POST /proposals/{id}/approve: {"payload_hash":"<hash>","expected_version":3}; 202 job id. Worker state visible through proposal endpoint; GET /proposals/{id} returns current state and redacted diff. 
POST /recurrences/{id}/change: {"scope":"future","occurrence_key":"2026-12-01T09:00:00[Europe/Moscow]","patch":{"local_time":"10:00"},"preview_hash":"<hash>"}.
API must validate field allowlists, payload size (text import max 100k chars initially), date intervals (schedule max 366d), statuses and foreign keys. Integration callback never uses client-supplied account ownership. Telegram polling worker for MVP; no public webhook required.

## v1.1 contract details
POST /tasks accepts title, context optional, context_date optional, project_id, due_date/due_at, estimate_min, quick_priority, if_done_text/if_not_done_text. context=day requires selected local date (defaults today only if absent); atomically create ready task and unranked daily_plan_item, creates draft plan if absent, confirmation becomes draft if changed. context=project requires project_id; explicit date or project implies ready unless context=inbox explicit. No context -> inbox. Manual PATCH inbox→ready; no silent date. 
POST /inbox/{id}/accept with If-Match and Idempotency-Key, body {kind:task|idea|reference, title?, task_fields?, idea_fields?}; atomically terminalize item, create linked ready task/idea or reference disposition and audit; same accepted item returns existing outcome for identical replay, conflicting payload 409. Merge links evidence to existing target; dismissed/reviewed cannot auto-reopen. Batch UI uses per-item calls with explicit preview, not atomic batch.
GET /daily-plan/{date}?tzid= returns plan or empty draft version=0, items [{task_id,position,outcome_rank}], capacity live, unknown_count, confirmed snapshot, stale flag. PUT with If-Match (0 for creation) and Idempotency-Key, body {tzid,items:[{task_id,position,outcome_rank:null|1|2|3}],confirm:boolean,reason?:string}; complete replacement of active membership, validate owner/unique IDs, eligible tasks (no inbox/cancelled/deleted; done allowed only if already planned), max 3 ranks; no due/block/external writes. Confirm saves immutable revision and capacity snapshot; edits after confirm require reason and draft/confirm explicitly. 409 if stale, 422 if invalid ranks/IDs. GET history returns redacted immutable revisions. Existing plans use recorded zone; caller selects tzid for old plan. Manual review POST {kind,period_start,tzid,user_notes?} -> 200 with SQL facts, no LLM. 
GET /tasks?smart_list=today|upcoming|undated|waiting&date=&tzid= semantics A02; horizons GET includes windows, overdue and goals. GET /schedule/free-slots returns intervals + stale flag, not recommendations; POST /blocks requires confirmed duration and validates overlap, explicit override for local conflicts only. 
Proposal/job responses expose state, operation_id, dispatch_started_at, reconcile_attempts, last_reconcile_at, next_reconcile_at, error_code, safe result_ref nullable, requires_manual_resolution. uncertain_reconcile_pending and uncertain_manual are not applied; repeated approve returns existing action status with no new dispatch. POST /proposals/{id}/refresh-preview only for expired/conflict/failed after known no effect; new proposal/hash, reauthorization. For uncertain state, only read-only reconcile or manual recorded resolution after evidence, never resend old action. POST /proposals/{id}/reconcile triggers lookup only; POST /proposals/{id}/resolve {outcome:applied|not_applied,evidence_note,version} requires owner and documented provider verification; new write requires fresh preview. 
409 payload contains safe current version and changed fields; retain local draft, compare and explicitly resubmit with fresh If-Match. External ETag conflicts require new preview, no forced overwrite. Web and Telegram share authorization/version checks; callback stores server-side exact proposal and actor/hash. Audit endpoints read-only, no raw content; internal jobs/approvals/purge/sessions not exposed as generic CRUD. 
