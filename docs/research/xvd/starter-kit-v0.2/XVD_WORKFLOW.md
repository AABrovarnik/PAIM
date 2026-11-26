# XVD Workflow v0.2 — adaptive profiles
Source: XVD concept v0.1 §§4–9,12–13 and EXP-003 final comparative report. Three risk modes remain; Lite is a profile, not a fourth mode.
| Mode/profile | Selection | Minimum procedure |
|---|---|---|
| VIBE | isolated disposable exploration, synthetic data | intent, safe experiment, self-check, result/lesson; no release |
| ENGINEERING/Lite | small localized reversible change, clear criteria, existing architecture, no material security/data/contract risk | one compact record, baseline, L1 challenge, scoped change, evidence, time/cost, lesson |
| ENGINEERING/Full | broader integration, nontrivial regression or design uncertainty | explicit contract, alternatives, risk-based separate review, regression, evidence and rollback |
| CRITICAL | auth/privacy/payment/production/destructive data/permission change or comparable harm | explicit authorization, isolation, independent review, relevant negative/restore checks, owner approval before external effect |
Escalate at new risk or scope. Do not lower evidence or safety to fit a timebox. Choose least sufficient process; no automatic multi-agent escalation.
## Six-stage loop
| Stage | Lite output | Full/Critical additions |
|---|---|---|
| INTENT | goal, scope, criteria, budget | documented acceptance/risk contract |
| EXPLORE | actual repo/HEAD/status, baseline, reuse | alternatives, dependencies, environment/authority proof |
| CHALLENGE | L1 and reason; simplest alternative, one failure risk | L2/L3 as justified; dissent and unresolved risks |
| BUILD | minimal reversible diff | small reviewed increments, isolation |
| VERIFY | evidence matrix; regression; duplication check | deeper negative, integration, restore/security as relevant |
| LEARN | actual effort/cost and one lesson if useful | plan/fact, review outcome, hypothesis and next experiment |
## Decision Advisor
GO / HOLD / NO-GO, options incl. defer/reuse, expected value, cost/effort, risk, reversibility, evidence/unknown and confidence. L1 self-check; L2 distinct reviewer for material changes; L3 independent expert board for strategic/high-impact, unresolved dissent or consequential uncertainty. Separate same-model session reduces carryover but not independent model validation. If mandatory independent review unavailable, block relevant acceptance/release. Owner makes consequential decisions.
## Simplicity and stopping
Prefer existing components and single source of behavior. Compare changed code for duplicate controls, handlers, branches, unnecessary abstractions and scope creep; justify any trade-off. No new artifact merely to satisfy a procedure. At budget exhaustion or hard gate failure stop affected step, present evidence, next cheapest option and residual risk. No blind retries.
## Parallel work and learning
Separate workspaces for parallel writers. Record comparable versions, methods, review conditions and outcomes. Do not attribute causal gain from one pair. Propose one process adjustment with falsifier and resource ceiling; owner approval for core principles/permissions. No forced daily document generation.
