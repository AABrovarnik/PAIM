# Intent contract v0.2
## Lite — one compact record
Task / actual repo + base:
Goal and beneficiary:
Scope / exclusions / authorized operations:
Mode/profile + reason:
Observable acceptance (criterion → required evidence):
Baseline / regression / relevant negative case:
Reuse and simplest alternative / CHALLENGE L1 reason:
Budget (owner active minutes, elapsed, attempts, new cash):
Rollback / stop:
Owner decisions needed:
## Full/Critical additions
Source versions, architecture/contract impacts, alternatives and rationale, risk/permissions, test matrix, separate reviewer and independence, security/privacy/restore criteria as relevant, release approval and rollback evidence. No approval inferred from template.
## Synthetic example
Existing admin list: show guidance if empty; reuse add action, populated list unchanged. ENGINEERING/Lite, no backend/auth/DB/dependency changes. AC1 rendered empty guidance, AC2 click adds first row, AC3 draft save confirmed in isolated synthetic environment if task includes persistence, AC4 nonempty regression; source inspection supports structure only. Existing behavior harness or approved manual smoke; report PARTIAL if unavailable. Baseline build/test, scoped diff, no deployment. Proposed 15 owner minutes / 20 elapsed / 1 attempt / $0 new spend; actual limits owner-approved. L1: reuse existing handler, risk duplicate controls. Rollback scoped changes without discarding other work.
