---
name: verify-change
description: Execute an approved verification matrix for a named AI-DLC work-packet implementation and produce reproducible evidence against requirements, architecture decisions, plan acceptance criteria, and accepted review disposition. Use only after explicit human authorization for that exact implementation version; do not repair code, waive failures, approve release, deploy, or change workflow state.
---

# Verify Change

Execute approved checks and report evidence. Do not edit product code or tests to make verification pass.

Read [references/verification-policy.md](references/verification-policy.md) and use [assets/verification-report-template.md](assets/verification-report-template.md).

Require exact baselines, packet and execution versions, accepted code-review disposition, verification approval, approved commands and environments, expected evidence, and known limitations. Return `BLOCKED` without running checks when identity or authorization is missing.

Run only authorized non-destructive checks. Record environment, command, exit status, relevant output, artifact path, and baseline traces. Distinguish product failure, test defect, environment failure, and not-run. Never reinterpret a failure as pass or silently skip a required check. Write `.ai-dlc/implementation/verifications/WP-NNN-verification-vNNN.md`.

Return `PASS_FOR_HUMAN_WORK_PACKET_ACCEPTANCE`, `REWORK_REQUIRED`, `ESCALATE_TO_HUMAN`, or `BLOCKED`. A pass is evidence, not acceptance or release approval.
