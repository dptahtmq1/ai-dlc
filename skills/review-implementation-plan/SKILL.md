---
name: review-implementation-plan
description: Independently review a named implementation plan inside a human-authorized AI author-review loop for baseline traceability, work-packet completeness, dependency order, scope boundaries, test coverage, migration safety, rollout, rollback, feasibility, and operational risk. Use when an AI-DLC orchestrator supplies the exact plan version, active loop authorization, and a reviewer invocation distinct from the planner; route correctable findings to AI rework or pass the plan to human approval.
---

# Review Implementation Plan

Review independently. Do not edit the plan, implement code, approve the plan, or change workflow state.

## Required resources

Read [references/implementation-plan-review-policy.md](references/implementation-plan-review-policy.md). Use [assets/implementation-plan-review-template.md](assets/implementation-plan-review-template.md).

## Preconditions

Require the exact plan and version produced in the active loop, approved requirements and architecture baselines, plan contract, loop authorization and iteration record, repository guidance, and a reviewer invocation distinct from the planner. Return `BLOCKED` without writing when identity, independence, or inputs cannot be established.

## Workflow

1. Confirm plan identity, status `PROPOSED`, baselines, loop authorization, and independence.
2. Map every required baseline item to work packets and verification evidence.
3. Inspect packet atomicity, ordering, dependencies, allowed and prohibited scope, acceptance criteria, tests, migrations, compatibility, rollout, rollback, and operational risk.
4. Identify omissions, contradictions, unsafe sequencing, unverifiable completion, unsupported choices, and packets too broad for independent authorization.
5. Require the orchestrator-supplied `project_id` and `project_root`, reject cross-project inputs or a target outside that root, and write one immutable report to `<project_root>/implementation/plan-reviews/implementation-plan-review-vNNN.md`.

Every `PLAN-FIND-NNN` includes severity, confidence, blocking status, affected packet and baseline IDs, evidence, violated rule, impact, and recommended correction or human decision.

Return `PASS_FOR_HUMAN_APPROVAL`, `REWORK_REQUIRED`, `ESCALATE_TO_HUMAN`, or `BLOCKED`. Correctable findings route back to `$plan-implementation` while loop capacity remains. A pass ends the AI loop and moves the plan to human approval. This review never approves the plan.
