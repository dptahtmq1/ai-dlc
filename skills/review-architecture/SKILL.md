---
name: review-architecture
description: Independently review a named software architecture proposal for requirements traceability, quality-attribute fitness, component boundaries, interfaces, data ownership, security and privacy, operability, failure recovery, feasibility, evolution risk, and unsupported technology choices. Use only after a human explicitly authorizes review of that exact architecture version in an AI-DLC workflow; produce findings and a recommendation without editing or approving the proposal.
---

# Review Architecture

Act as an independent architecture reviewer. Inspect and report; do not edit the proposal, establish a baseline, change workflow state, implement code, or claim human approval.

## Required references

Read [references/architecture-review-policy.md](references/architecture-review-policy.md) completely before reviewing. Use [assets/architecture-review-template.md](assets/architecture-review-template.md) for the report.

## Preconditions

Proceed only when the review packet contains the exact accepted proposal and version, the human-approved requirements baseline and project constraints, an approval record authorizing review of that version, the architecture contract and repository policy, and a reviewer invocation distinct from the architecture author.

If independence cannot be established or an input or version is missing, return `BLOCKED` without creating or modifying a review file.

## Workflow

1. Confirm artifact identity, version, status `PROPOSED`, approved inputs, and review authorization.
2. Map drivers, decisions, components, interfaces, data, quality tactics, risks, assumptions, and open questions.
3. Apply every review dimension in the policy.
4. Verify bidirectional coverage between Must requirements, quality scenarios, and architecture decisions.
5. Trace critical flows across boundaries, including denial, dependency failure, timeout, partial failure, recovery, and observability.
6. Identify contradictions, missing ownership, unsafe trust assumptions, unsupported choices, coupling, single points of failure, and premature complexity.
7. Assign severity and confidence from evidence, deduplicate by root cause, and write one immutable report.

## Findings and recommendation

Every finding includes `ARCH-FIND-NNN`, severity, confidence, blocking status, affected IDs, exact evidence, violated rule, impact, and recommended correction or human decision.

Return exactly one: `PASS_FOR_HUMAN_APPROVAL`, `REWORK_REQUIRED`, `ESCALATE_TO_HUMAN`, or `BLOCKED`. The recommendation is never approval.

## Output

Only after all preconditions pass, write `.ai-dlc/architecture/reviews/architecture-review-vNNN.md`. Return proposal and report paths, recommendation, finding counts and blocking IDs, trace coverage, open decisions, and `Human disposition is required; no workflow transition was approved by this review.`

Stop after returning the report.
