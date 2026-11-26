# XVD-EXP-003 — final comparative report (bounded audit)
2026-11-26 | status: COMPLETED WITH LIMITATIONS / functional acceptance incomplete / economic hypothesis INCONCLUSIVE. Owner-supplied implementation and separate Codex audit reports; chair synthesis. One paired task, A then B; not blinded, not independent model validation. Private source, commit identifiers of target app, paths and logs intentionally omitted.
## Design and outcome
Same reported application baseline, two isolated copies, identical functional UI task. Existing dependency installation and baseline build/test succeeded in both; 9/9 original tests each. A ordinary Codex and B XVD kit snapshot b083644dcd6c93e1d97f87d8058c1e8a0ff65262. Exact model/effort equality, owner active time, kit setup time and audit duration not recorded. Both reported ~18 min implementation, one functional iteration plus technical retries. B reported $0 extra spend, A actual spend unknown; subscription/usage and TCO unknown. Shared setup reported ~22 min, later diagnostic ~2 min, A install ~2 min, B install ~69.64 s, build/test and retries additional; not active owner time and not a complete elapsed log. Do not infer net economic savings.
## Separate auditor findings
| Criterion | A | B |
|---|---|---|
| AC01–04 empty messages and add controls | PASS static | PASS static |
| AC05 nonempty rendering retained | PASS static | PASS static |
| AC06 first add and save draft | PARTIAL | PARTIAL |
| AC07 build | PASS | PASS |
| AC08 original tests | 9/9 PASS | 9/9 PASS |
| AC09 behavioral UI validation | PARTIAL | PARTIAL |
| AC10 scope | PASS | PASS (related test file) |
Both 8 PASS, 2 PARTIAL. Audit reran build/tests; no browser smoke. Auditor found A simpler with less duplicated controls, B adds two reproducible source-string checks (readFileSync/toContain) that do not establish DOM behavior or click/save correctness. Both dense UI component, maintainability medium. No build/test failures outside sandbox; EPERM inside sandbox observed. Neither ready for unconditional acceptance/production.
## Interpretation
Earlier subjective 7.5/8.2 rating superseded by direct audit findings, not a measured score. A preferred provisionally for this small code change on maintainability grounds, not proven cheaper or functionally superior. B's extra checks/retrospective are artifacts, not proven quality improvement; test count alone is inadequate. XVD benefit and cost superiority INCONCLUSIVE. No generalization from n=1. Full acceptance requires same behavioral smoke on both or at least selected candidate before release, separately approved; no extra iterations now. No deployment or merge authorized.
## LEARN / proposed v0.2
1. Lite profile within ENGINEERING (not fourth risk mode): one compact intent, preflight, acceptance, scoped diff/test, lesson and cost; no mandatory separate docs for low-risk changes. Preserve hard gates.
2. Test evidence classification: source inspection != component behavior != end-to-end; map each material criterion to evidence and report PARTIAL/NOT RUN. Check whether tests fail on realistic defect; avoid source-string tests as substitute for behavior. Reuse existing harness; do not add paid tools or deps without approval.
3. Windows preflight: verify correct branch/clean tree, npm.cmd when PS script blocked without changing policy; EPERM in this environment correlated with sandbox, one narrowly approved verified build/test outside sandbox only if necessary; no broad permission changes. Not universal diagnosis.
4. Record owner active time, kit setup, run/audit elapsed, iterations and spend/unknown; stop when expected marginal value falls below cost.
5. Explicit CHALLENGE decision, even one sentence for low-risk work; separate reviewer for material risks. No fabricated independence.
6. Prefer simpler implementation and test strength over generated artifacts; check duplication before accepting.
## Decision proposal
Close bounded EXP-003 research with limitations, keep V0.1 immutable, prepare v0.2 draft only after owner confirms, validate on another task and compare same behavioral criteria. PAIM Zero EXP-002 remains unchanged. No Claude, new services, app changes or merge.