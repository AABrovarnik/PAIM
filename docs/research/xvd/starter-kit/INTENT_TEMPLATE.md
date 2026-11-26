# Intent contract — copy per task
Task ID / project / owner / date / source version:
Problem and beneficiary:
Expected observable outcome:
In scope / out of scope:
Inputs and authoritative sources:
Constraints and authorized operations:
Mode (VIBE/ENGINEERING/CRITICAL) + reason:
Acceptance criteria (Given/When/Then where useful):
Negative/regression checks:
Reuse / simplify / defer alternative (if material):
Budget (human minutes, elapsed limit, extra spend, max iterations):
Rollback / safe stop:
Decisions requiring owner:
For small reversible tasks, a few sentences containing these essentials suffice.
## Example (synthetic)
Goal: improve empty-state message in existing demo dashboard. Scope: one view and its test; no API/auth/schema changes. Mode ENGINEERING (existing app). Acceptance: empty result shows guidance, populated state unchanged. Reuse existing component. Local edits and existing tests allowed; no new paid services or deployment. Proposed budget 15 active minutes, 1 iteration, $0 new spend; owner sets actual limit. Rollback via scoped diff on task branch.
