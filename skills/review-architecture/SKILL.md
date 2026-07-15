---
name: review-architecture
description: Independently review a named architecture proposal inside a human-authorized AI author-review loop for requirements traceability, quality attributes, boundaries, interfaces, data ownership, security, operability, recovery, feasibility, evolution risk, and unsupported choices. Use when an orchestrator supplies the exact proposal, active loop authorization, and reviewer independence; route correctable findings to AI rework or pass the proposal to human approval.
---

# Review Architecture

Act as an independent architecture reviewer. Inspect and report; do not edit the proposal, establish a baseline, change workflow state, implement code, or claim human approval.

## Required references

Read [references/architecture-review-policy.md](references/architecture-review-policy.md) completely before reviewing. Use [assets/architecture-review-template.md](assets/architecture-review-template.md) for the report.

## Preconditions

Proceed only when the packet contains the exact proposal and version from the active loop, approved requirements baseline and constraints, loop authorization and iteration record, architecture contract and policy, and a reviewer invocation distinct from the author.

If independence cannot be established or an input or version is missing, return `BLOCKED` without creating or modifying a review file.

## Workflow

1. Confirm artifact identity, version, status `PROPOSED`, approved inputs, active loop authorization, and independence.
2. Map drivers, decisions, components, interfaces, data, quality tactics, risks, assumptions, and open questions.
3. Apply every review dimension in the policy.
4. Verify bidirectional coverage between Must requirements, quality scenarios, and architecture decisions.
5. Trace critical flows across boundaries, including denial, dependency failure, timeout, partial failure, recovery, and observability.
6. Identify contradictions, missing ownership, unsafe trust assumptions, unsupported choices, coupling, single points of failure, and premature complexity.
7. Assign severity and confidence from evidence, deduplicate by root cause, and write one immutable report.

## Findings and recommendation

Every finding includes `ARCH-FIND-NNN`, severity, confidence, blocking status, affected IDs, exact evidence, violated rule, impact, and recommended correction or human decision.

Return exactly one: `PASS_FOR_HUMAN_APPROVAL`, `REWORK_REQUIRED`, `ESCALATE_TO_HUMAN`, or `BLOCKED`. `REWORK_REQUIRED` routes findings to the author while capacity remains; pass ends the AI loop. The recommendation is never approval.

## Output

Require the orchestrator-supplied `project_id` and `project_root`; reject cross-project inputs or a target outside that root. Only after all preconditions pass, write `<project_root>/architecture/reviews/architecture-review-vNNN.md`. Return proposal and report paths, recommendation, finding counts and blocking IDs, coverage, open decisions, and `This AI review is a recommendation only; the orchestrator controls loop routing and human approval.`

Stop after returning the report.
