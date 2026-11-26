# Quality gates v0.2 — evidence over test count
Statuses PASS / PARTIAL / FAIL / BLOCKED / NOT RUN / N/A (justify). PASS only when required evidence meets criterion. PARTIAL means insufficient evidence, not acceptance. Mandatory non-PASS blocks unconditional acceptance/release; report limited readiness separately. No averaging away critical findings.
| Gate | Requirement | Evidence |
|---|---|---|
| G0 Intent | scope, mode/profile, acceptance, approved budget | compact task record |
| G1 Preflight | actual repo/HEAD/status, environment, authority, baseline, rollback | sanitized observations |
| G2 Design/Challenge | least sufficient solution, review decision, risk | rationale, reviewer if required |
| G3 Build | minimal diff, no unintended changes/duplication or secrets | diff and justification |
| G4 Verify | criterion-to-evidence map, regression, relevant negative checks | actual commands/exit codes/observed behavior |
| G5 Review | proportionate actual review; material findings addressed | report, independence limits |
| G6 Release | explicit external-effect approval, applicable security and recovery evidence | owner decision, release/rollback plan |
| G7 Learn | plan/fact costs, reusable lesson if useful | compact record |
Lite keeps all applicable gates in one record; G5 L1 when low risk; G6 N/A only without release. Full separate review as risk requires. Critical independent review and owner acceptance; no production action on failed or incomplete mandatory gate.
## Evidence taxonomy (not a universal ranking)
T1 static: diff, typecheck, source-string assertions; proves only inspected properties. T2 unit: function behavior. T3 component: rendered UI/DOM, events/state. T4 integration: component/service interaction, persistence/API. T5 end-to-end: actual user workflow across boundaries. Manual observed smoke can provide T3/T4/T5 evidence depending on scope; distinguish manual from automated. Build success is build evidence, not UI behavior. Existing API tests do not validate newly added UI by default.
## Test design
Before BUILD specify evidence per criterion: expected behavior, failure case, minimum sufficient method. For UI empty-state: rendered empty and populated states; click adds item; save/reload if persistence is in scope; existing regressions. Prefer existing harness. Do not add dependencies or install browsers without approval. If unavailable, request bounded manual smoke or report PARTIAL/BLOCKED. Text assertions may complement behavior tests, not substitute. Ask: would this test fail if click handler were disconnected, condition reversed, or save broken? If not, narrow the claim. Optional safe negative-control/mutation in disposable fixture only if authorized; never corrupt target or falsify tests.
## Evidence record
AC / required method / actual method / expected vs observed / command+exit or manual observation / status / gap. Record version/environment and actual evidence, not only count. Review diff for duplicated controls, handlers and unnecessary abstractions. Severity: critical/blocker unsafe/core failure/data loss/leak/unauthorized action; high material failure; medium/low impact and remediation. No unconditional release with unresolved mandatory gaps.
