---
name: plan-implementation
description: Create or revise a traceable implementation plan inside a human-authorized AI author-review loop from approved requirements and architecture baselines. Use when an orchestrator supplies active loop authorization, target version, and independent AI plan-review findings; produce ordered work packets, scope, dependencies, migration, verification, rollout, rollback, and traceability without changing code or claiming approval.
---

# Plan Implementation

Create a proposal only. Do not modify product code, approve the plan, or advance workflow state.

## Required resources

Read [references/implementation-plan-contract.md](references/implementation-plan-contract.md). Use [assets/implementation-plan-template.md](assets/implementation-plan-template.md).

## Preconditions

Require approved requirements and architecture baselines, repository guidance, target `vNNN`, active implementation-plan loop authorization, and routed AI review findings for revisions. Return `BLOCKED` when any item is missing, versions disagree, or the loop limit is exceeded.

## Workflow

1. Inspect baselines and the repository without changing files.
2. Map approved requirements and architecture decisions directly into the smallest independently reviewable work packets.
3. Define packet order, dependencies, allowed paths, prohibited scope, acceptance evidence, tests, migrations, rollout, and rollback.
4. Separate prerequisite decisions from implementable work; expose gaps as `OPEN-IMPL-*`.
5. Require the orchestrator-supplied `project_id` and `project_root`, reject cross-project baselines or a target outside that root, and write one plan to `<project_root>/implementation/plans/implementation-plan-vNNN.md`.

Use stable `WP-NNN` IDs. Add other IDs only when independent cross-packet management is genuinely needed. Each packet must be safe to authorize independently, reference relevant baseline IDs, name expected files or components, and include completion and verification criteria. Do not invent technology choices or combine unrelated changes.

Return the path, version, packet and open-decision counts, baseline coverage summary, dependency order, and `READY_FOR_AI_REVIEW` or `BLOCKED`. Stop before review or implementation.
