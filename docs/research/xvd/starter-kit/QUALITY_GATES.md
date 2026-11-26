# Quality gates v0.1
Statuses PASS / FAIL / BLOCKED / NOT RUN / N/A (justify). PASS requires observable evidence. A blocked mandatory gate means no acceptance or deployment. No invented coverage.
| Gate | Requirement | Evidence |
|---|---|---|
| G0 Intent | authorized scope, criteria, mode, budget | task contract |
| G1 Preflight | correct repo, permissions, baseline, rollback | sanitized command outputs |
| G2 Design | minimal adequate option, review proportionate | short rationale |
| G3 Build | scoped diff, no unrelated edits or secret exposure | git diff / changed files |
| G4 Verify | acceptance + regression + relevant negative tests | commands, exit codes, observed behavior |
| G5 Review | actual reviewer when required, findings resolved or accepted | reviewer report, decision |
| G6 Release | explicit external-effect approval, security/restore checks | approval, release/rollback plan |
| G7 Learn | plan/fact and reusable lessons | concise record |
VIBE: G0–G4 scaled to experiment, G5 self-review, G6 N/A if no release, G7 brief. ENGINEERING: G0–G5 and G7, G6 if release. CRITICAL: all applicable gates, independent reviewer and explicit owner acceptance; no production action on failure.
## Testing
Before build specify positive, boundary, negative and regression scenarios. For web UI test actual navigation, empty/error/loading states and accessibility as relevant. For authorization test denied access and cross-user isolation with synthetic accounts; for persistence test restore/migration rollback when relevant. No real personal data unless separately authorized. Never confuse static review with runtime test. Record environment, command, expected/actual, exit status. Do not mark skipped tests PASS.
## Review findings
Critical/blocker: unsafe or incorrect core outcome, data loss/leak, unauthorized action. High: material functional failure or significant unresolved risk. Medium/low: record impact and remediation. No averaging away critical findings.
