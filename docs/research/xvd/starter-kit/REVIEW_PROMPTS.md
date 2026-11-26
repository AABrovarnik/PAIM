# Review prompts v0.1
## A. Codex preflight / task start
Read root AGENTS.md and relevant XVD docs. Inspect target repository and baseline without changing anything. Summarize intent, mode, authorized scope, acceptance criteria, existing components, risks, tests and budget. If task already authorizes bounded local edits, proceed only within that scope after preflight passes. Otherwise stop with proposed plan. No new paid calls, external writes or deployment unless approved.
## B. Decision Advisor
Given the question and authoritative evidence, identify the decision and alternatives including defer/reuse. Compare value, human time, cash, risk and reversibility. Recommend GO/HOLD/NO-GO with confidence and required review level. Distinguish facts, assumptions and unknowns. Do not authorize execution.
## C. Independent review
Use the frozen task contract, diff and test evidence. Do not read other reviewers' verdicts before own review. No edits or paid calls. Check intent, correctness, security, regressions, scope, maintainability, costs and missing evidence. Findings: severity, file/line or source, reproduction, impact, minimal fix. Verdict READY / READY WITH WARNINGS / NOT READY / BLOCKED. State actual model/session and independence limitations. Same author self-review is not independent. Separate fresh Codex reviewer may reduce context bias; don't call it independent model validation.
## D. Expert Board
Use common frozen corpus and criteria; one or more independent reviewers as justified by impact and budget. Claude not used until funding approved; Codex or Gemini only within available approved resources. Record individual findings before synthesis. Chair resolves disagreements with evidence, reports dissent and recommends owner decision. No fabricated votes. Stop if required independence unavailable; don't waive mandatory safety.
## E. Verification and handoff
Run authorized tests, report exact commands/exit status, changes, skipped/blocked checks, remaining risks, cost/elapsed/human time or UNKNOWN, rollback and next decision. No push/merge/deploy without authorization.
## F. LEARN
Propose one short verified lesson and one process hypothesis, compare marginal benefit with cost. Record evidence and limitations. Do not silently update policies.
