# PAIM — instructions for coding agents
Read docs/requirements/MASTER_TZ.md and linked specs before coding. Concept V2 accepted; implementation requires owner approval of v1.1 after re-review and mandatory WP00 gate before WP03. Read docs/planning/SPIKE_GATE.md. Do not treat this file as permission to deploy or merge.
- Scope P0 first; P1 only approved reserve, P2 out of scope.
- Reference FR/US/T IDs in plan, commits and PR; update docs for changes.
- Modular monolith, PostgreSQL migrations, typed APIs, owner isolation, deterministic rules. AI only validated proposals; never direct DB/shell/external writes.
- Use synthetic data, no secrets, mail contents, personal calendar or production dumps in repo/logs. env.example placeholders only.
- Run lint/typecheck/unit/integration/e2e relevant to change; record actual results, blockers and untested parts. No invented results.
- Use dedicated branch and PR; never merge, deploy, alter production services, send external messages or change external calendar without explicit owner confirmation. No destructive migration without backup/rollback review.
- Preserve current VPS services. Verify versions/dependencies, pin compatible releases.
- At end of task summarize changed files, tests, residual risks, next step. Stop if critical security conflict or ambiguous external action.

- Follow A07 fencing/reconcile, never blindly retry an uncertain external effect. P0 manual day plan and inbox work with LLM disabled. Follow T00/T01–T17 and trace matrix; no invented PASS. Do not open implementation before documented gates. Reports are immutable source evidence; do not rewrite expert originals.
