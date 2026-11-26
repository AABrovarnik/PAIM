# OpenClaw preflight — 2026-11-26
Owner supplied diagnostic report. Earlier Codex reasoning run was standalone ChatGPT Codex Desktop, not OpenClaw worker. The two environments must not be conflated.
## Findings (self-report, not independently verified)
Read-only GitHub tool interfaces, memory search/get and document analysis capability reported available; actual retrieval, attachment parsing and RAG not tested. Effective execution/file mutation restrictions and model billing remain unverified. Deployment-specific security configuration and account details intentionally excluded from public Git.
## Gate
GO for owner-operated read-only diagnostics; NO-GO for tool-enabled corpus/injection tests until effective policy and billing eligibility confirmed. No infrastructure changes, paid calls, service installs or production configuration modifications authorized by this gate. A prompt is not a sandbox.
## Next check
Owner inspects effective policy for actual Telegram agent/session via local CLI, if supported by installed release (sandbox explain --help; sandbox explain --agent main if applicable). Confirm owner-only channel, read-only connector credentials and allowlist, no shell/elevated execution, filesystem mutation, external writes or delegation in experimental context. Check model entitlement/usage and paid fallback separately. Share sanitized conclusions only; do not publish raw config, account details, tokens or deployment-specific security findings.
Official documentation (current, may differ from installed release):
- https://docs.openclaw.ai/gateway/sandbox-vs-tool-policy-vs-elevated
- https://docs.openclaw.ai/gateway/config-tools/tool-policy
- https://docs.openclaw.ai/concepts/usage-tracking
- https://docs.openclaw.ai/providers/openai/coverage-and-cost
If gates cannot be confirmed within resource budget, STOP; use existing ChatGPT connector as provisional baseline, not claim OpenClaw failure of reasoning. Prefer dedicated restricted test agent over modifying main agent, subject to separate owner approval. No BUILD or platform acceptance. 
