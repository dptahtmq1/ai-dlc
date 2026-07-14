---
name: plan-implementation
description: Create or revise a traceable implementation plan from human-approved requirements and architecture baselines. Use when an AI-DLC orchestrator has explicit human authorization for a named plan version and needs ordered work packets, file and component scope, dependencies, migration and rollout steps, verification coverage, rollback strategy, and requirement traceability without changing production code.
---

# Plan Implementation

Create a proposal only. Do not modify product code, approve the plan, or advance workflow state.

## Required resources

Read [references/implementation-plan-contract.md](references/implementation-plan-contract.md). Use [assets/implementation-plan-template.md](assets/implementation-plan-template.md).

## Preconditions

Require the human-approved requirements and architecture baselines, repository guidance, target `vNNN`, authorization for that version, and accepted findings for revisions. Return `BLOCKED` without writing when any item or version is missing.

## Workflow

1. Inspect baselines and the repository without changing files.
2. Map requirements and architecture decisions to the smallest independently reviewable work packets.
3. Define packet order, dependencies, allowed paths, prohibited scope, acceptance evidence, tests, migrations, rollout, and rollback.
4. Separate prerequisite decisions from implementable work; expose gaps as `OPEN-IMPL-*`.
5. Write one plan to `.ai-dlc/implementation/plans/implementation-plan-vNNN.md`.

Use stable `WP-NNN`, `TEST-NNN`, `MIG-NNN`, `ROLL-NNN`, and `OPEN-IMPL-NNN` IDs. Each packet must be safe to authorize independently, trace to baseline IDs, name expected files or components, and include completion and verification criteria. Do not invent technology choices or combine unrelated changes.

Return the path, version, packet and open-decision counts, trace coverage, dependency order, and `READY_FOR_HUMAN_PLAN_ACCEPTANCE` or `BLOCKED`. Stop before implementation.
