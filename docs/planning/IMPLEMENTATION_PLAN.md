# Implementation plan — 6 weeks
Baseline: approved concept V2, specification pending Board #2 and owner approval. 90–150 hours owner effort estimate, not guaranteed. 
| Week | Work packages / FR | Exit gate |
|---|---|---|
|1| WP01 architecture spike: VPS resources, OAuth, OpenClaw adapter, TLS; WP02 repo scaffold, CI, auth, migrations (18)| spike results, no production changes, auth and migration tests |
|2| WP03 CRUD/tree/project/goal/idea/dependencies (01,02,09,10); WP04 recurrence/delegation (03,06)| T01,T02,T05,T08 |
|3| WP05 calendar/capacity (04,11); WP06 reminders/Telegram (05,16); WP07 Calendar sync (15)| T03,T04,T10,T11 |
|4| WP08 inbox/Gmail (07,14); WP09 AI proposals/priority (08,17)| T06,T07,T12, injection tests |
|5| WP10 reviews/memory/horizons (11–13); WP11 hardening, end-to-end, pilot start| T09,T13,T15,T17, safe P0 |
|6| WP12 fixes, restore/load tests, documentation, audit, release candidate| all mandatory tests, T14,T16, owner sign-off after pilot |
P1 only after P0 gates and time reserve; no silent scope cuts. If OAuth/VPS blocks, document alternative/manual mode, seek approval; mock not counted as real integration. If 7–14d pilot extends beyond week6, release candidate not production acceptance. 
## Codex work process
One issue per WP/subtask, branch, tests first for domain logic, PR with FR/US/T references, no merge without owner. Keep roadmap and decisions current. No prod operations or real credentials without approval. Review security and migrations independently.
## Open decisions before coding
D01 VPS CPU/RAM/disk, available ports and isolated DB; D02 OAuth application and selected calendars/labels; D03 domain/TLS; D04 OpenClaw transport and provider model; D05 monthly LLM budget ($10–30 proposal), pricing verification; D06 retention/backup destination; D07 timezone/work windows/quiet hours set in onboarding; D08 Gantt P1 priority. Owner can revise defaults.
