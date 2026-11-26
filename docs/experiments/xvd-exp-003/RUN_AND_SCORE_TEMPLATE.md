# EXP-003 — run and score template
Do not run until preflight and owner task/budget confirmation. Freeze one task contract with exact paths, acceptance, base SHA and test commands. Use identical contract for both arms. 
## Shared prompt
Implement the frozen task in this isolated local copy only. Follow existing repository instructions. Preserve unrelated behavior and changes. No new dependency installs, paid calls, secret access, real data, commit/push/merge or deployment. Run authorized existing tests and focused acceptance checks. Report diff, commands/exit codes, remaining risks, time/usage if known; stop at budget or safety gate. 
## Arm A
Existing project instructions only. Fresh session. No kit files/context. 
## Arm B
Install kit snapshot locally in separate copy (merge existing AGENTS.md carefully; never replace it). Same shared task; follow kit. Track installation and documentation time separately. Fresh session. 
## Record privately
| Metric | A | B |
|---|---|---|
| Base SHA, model/effort/version, order | | |
| Human setup min | | |
| Human implementation/review/rework min | | |
| Elapsed min | | |
| Acceptance PASS/FAIL/BLOCKED | | |
| Regression and negative tests | | |
| Defects by severity | | |
| Attempts / rework | | |
| Extra cash / usage / unknown | | |
| Docs/process overhead | | |
| Owner accepted yes/no | | |
| Rollback verified | | |
Keep source and actual logs private. Public aggregate only after review. If both pass, compare total active effort and cash separately; if only one passes, describe quality difference and cost without claiming universal superiority. If neither passes or conditions differ materially, INCONCLUSIVE. Record one lesson and one next hypothesis. No automatic method changes.