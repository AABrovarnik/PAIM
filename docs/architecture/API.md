# REST API v1
Base /api/v1, JSON UTF-8, UUID, RFC3339 timestamps with offset, IANA timezone, date YYYY-MM-DD. Owner from session, never trust owner_id from client. OpenAPI generated from Pydantic and checked into repo during implementation. This is a contract draft, not a running API.
## Common semantics
GET list: limit default 50 max 200, cursor opaque, stable sort; filters status/project/goal/due range/search. Response {items,next_cursor}. POST returns 201, PATCH returns 200; DELETE soft-deletes and returns 204. Mutations include Idempotency-Key for create/actions, If-Match version for edit/delete; mismatch 409. Same key different payload 409. Error {code,message,field_errors,correlation_id}; 400 malformed, 401 unauthenticated, 403 forbidden/CSRF, 404 not found or other owner, 409 conflict, 422 invalid rule/cycle, 429 rate/budget, 503 unavailable. No secret in errors.
## Routes
| Method | Path | Contract |
|---|---|---|
| POST | /auth/login, /auth/logout | session cookie, logout revoke |
| GET/PATCH | /settings | work windows, tz, weights, budget, quiet hours |
| GET/POST | /tasks | list/create; title required |
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
| POST | /inbox/{id}/dismiss, /merge | dismiss or explicit target task |
| POST | /tasks/{id}/assess | manual scores or enqueue AI |
| GET | /tasks/{id}/assessments | history |
| GET | /horizons, /reviews, /usage, /audit | filtered summaries |
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
POST /tasks: {"title":"Подготовить отчет","due_date":"2026-12-01","estimate_min":60}. Response includes id,status=inbox,version=1, owner-local due timezone. PATCH /tasks/{id} with If-Match: 1 and {"title":"Отчет v2"} returns version=2.
POST /proposals/{id}/approve: {"payload_hash":"<hash>","expected_version":3}; 202 job id. Worker state visible through proposal endpoint; GET /proposals/{id} returns current state and redacted diff. 
POST /recurrences/{id}/change: {"scope":"future","occurrence_key":"2026-12-01T09:00:00[Europe/Moscow]","patch":{"local_time":"10:00"},"preview_hash":"<hash>"}.
API must validate field allowlists, payload size (text import max 100k chars initially), date intervals (schedule max 366d), statuses and foreign keys. Integration callback never uses client-supplied account ownership. Telegram polling worker for MVP; no public webhook required.
