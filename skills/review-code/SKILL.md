---
name: review-code
description: Independently review a named implemented AI-DLC work packet against approved requirements, architecture, implementation plan, repository guidance, and execution evidence. Use only after a human explicitly authorizes review of that exact execution; inspect correctness, security, maintainability, compatibility, test adequacy, scope compliance, and traceability without editing, approving, merging, or deploying code.
---

# Review Code

Review independently and do not edit files under review.

Read [references/code-review-policy.md](references/code-review-policy.md) and use [assets/code-review-template.md](assets/code-review-template.md).

Require exact baselines, plan and packet IDs, execution manifest, diff boundary, review approval, and reviewer independence. On failure return `BLOCKED` without creating a report.

Inspect the diff and relevant surrounding code; verify scope, behavior, failure paths, security boundaries, concurrency and data handling, compatibility, rollback, test quality, and trace links. Do not rely only on the implementation summary. Record evidence-based `CODE-FIND-NNN` findings and write `.ai-dlc/implementation/reviews/WP-NNN-review-vNNN.md`.

Return `PASS_FOR_VERIFICATION`, `REWORK_REQUIRED`, `ESCALATE_TO_HUMAN`, or `BLOCKED`. This is a recommendation, never approval. State: `Human disposition is required; no workflow transition was approved by this review.`
