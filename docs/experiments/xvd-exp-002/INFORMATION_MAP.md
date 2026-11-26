# Information Map — PAIM Zero v0.1
2026-11-26 | public-only initial scope. Source of truth is domain-specific, not model memory.
| ID | Entity | Authoritative locator | Allowed | Freshness / fallback |
|---|---|---|---|---|
| S01 | PAIM V3 draft | https://github.com/AABrovarnik/PAIM/pull/3 ; docs/concept/CONCEPT_V3.md | public read | live PR status + pinned document; no claim of acceptance |
| S02 | EXP-001 evidence | https://github.com/AABrovarnik/PAIM/pull/4 ; docs/experiments/xvd-exp-001/ | public read | baseline 019b5c4a7d2f3a69d73497a4955b22cf440e1eb6; current PR metadata separately |
| S03 | XVD draft | https://github.com/AABrovarnik/PAIM/blob/3a796d3ad160538f7d6690a258a624a82a3a1ee1/docs/research/xvd/XVD_CONCEPT_V0.1.md | public read | pinned snapshot; newer versions not assumed |
| S04 | EXP-002 instructions | branch experiments/xvd-exp-002, docs/experiments/xvd-exp-002/ | public read | record exact commit per run |
| S05 | Owner decisions | explicit owner statement in current chat; public sanitized register for approved public project decisions | read current supplied text | separate proposal/confirmed, conflicts visible |
| S06 | Personal Gmail/Calendar/tasks/files | not in scope | DENIED for pilot | explicit consent and precise source subset needed |
| S07 | VPS/OpenClaw/private logs | not in scope | DENIED | prior sanitized summary only |
## Access verification
GitHub connector source-read smoke succeeded in earlier research; technical read-only credential scope not independently verified. Restrict runtime to fetch/read metadata on listed public project; never call write tools. No connector permission expansion. If stricter technical scope is required or uncertain, use manually supplied public files, separately label results. Do not inspect real account data. Source links use commit SHA where available. Read current PR state when asked about current status; do not treat historical snapshots as live. No personal data or account identifiers in public Git. Built-in memory is not a verified RAG index, and persistence/export not tested.