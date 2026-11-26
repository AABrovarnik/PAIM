# XVD Workflow v0.1
Derived from XVD_CONCEPT_V0.1.md §§4–9,12–13. Experimental operationalization.
## Mode selection
VIBE: disposable sandbox, synthetic data, reversible experiments; one short intent and self-check. ENGINEERING: scoped repository change, acceptance criteria, baseline tests, diff review and regression. CRITICAL: auth, privacy, production, payments, destructive data, permission expansion or comparable impact; explicit risk contract, isolated tests, independent review, restore evidence and owner approval before external effect. Raise mode when risk grows; lower only with justification. No mode waives safety.
## Loop
| Stage | Minimum output | Stop / gate |
|---|---|---|
| INTENT | goal, scope, acceptance, mode, budget | ambiguity affects material outcome |
| EXPLORE | preflight, reuse/defer alternatives if meaningful | unsafe access, missing inputs |
| CHALLENGE | L1 self-check or L2/L3 review decision | unresolved critical risk |
| BUILD | smallest authorized reversible change | scope creep, budget, unknown external effect |
| VERIFY | actual test results, diff, evidence | failed mandatory criterion |
| LEARN | short plan/fact, one reusable lesson if any | no invented generalization |
## Preflight
Identify target repo/branch, git status, instructions, environment/runtime versions, available tests, allowed tools, data scope, cost and rollback. Prefer narrow read-only commands; don't print full configs, secrets or production records. Do not assume an installed tool works. If source unavailable label BLOCKED or use authorized manual snapshot, track separately. No installation or privilege escalation without approval.
## Decision Advisor
GO / HOLD / NO-GO for proposed action; compare alternatives incl. do nothing, benefit, time/cost, risks, reversibility, evidence and uncertainty. L1 low-impact self-review; L2 material change with distinct reviewer; L3 strategic/high-impact or unresolved dissent with independent reviewers and owner decision. Same model in new session is a separated review, not independent model evidence. No pretend consensus. Use cheapest sufficient review; critical unresolved risk blocks deployment.
## Parallel development
Only independent work with clear boundaries and owner-approved resources. Don't run parallel writers on same files. Integrate via reviewed diff. Coordinator does not claim actual execution by Codex without report.
## Retrospective
Compare accepted outcome, active human minutes, elapsed, rework, actual spend/unknown. Propose one useful process adjustment and test it. No automatic policy changes.
