---
name: review-requirements
description: Independently review a named requirements draft inside a human-authorized AI author-review loop for completeness, consistency, ambiguity, testability, traceability, measurable quality attributes, scope discipline, security and privacy concerns, and unsupported solution decisions. Use when an orchestrator supplies the exact draft, active loop authorization, and a reviewer invocation distinct from the author; route correctable findings back to AI rework or pass the draft to human approval.
---

# Review Requirements

Act as an independent reviewer. Inspect and report; do not edit the requirements draft, establish a baseline, change workflow state, or claim human approval.

## Required references

Read [references/review-policy.md](references/review-policy.md) completely before reviewing. Use [assets/review-template.md](assets/review-template.md) for the report.

## Preconditions

Proceed only when the review packet contains:

- the exact draft path and version produced in the active loop;
- the approved project brief and scope;
- the active requirements-loop authorization and iteration record;
- the applicable requirements contract and project policies;
- a reviewer identity or invocation distinct from the draft author.

If independence cannot be established, versions disagree, or an input is missing, return `BLOCKED` without producing a substantive review or changing files.

## Independence

- Review only artifact evidence and approved context.
- Do not use the author's private reasoning, intended conclusions, or suggested severity.
- Do not ask the author to defend the draft during the review.
- Do not repair the draft. Describe defects and recommended outcomes precisely enough for a later approved rework.
- If prior reviews exist, use them only to check recurrence and resolution after performing an independent pass on the current artifact.

## Review workflow

1. Confirm artifact identity, version, status `PROPOSED`, active loop authorization, and author-review independence.
2. Map goals, scope, constraints, requirements, quality scenarios, acceptance criteria, assumptions, and open questions.
3. Apply every review dimension in the policy.
4. Verify all Must requirements bidirectionally through the traceability matrix.
5. Inspect High-priority behavior for positive, negative, authorization, failure, boundary, and recovery cases where relevant.
6. Inspect each NFR and quality scenario for an observable measurement.
7. Identify contradictions, duplicates, hidden design decisions, unsupported assumptions, and missing actors or states.
8. Assign severity and confidence using evidence, not prose intensity.
9. Deduplicate findings by root cause while listing all affected IDs.
10. Produce one immutable review report and a routing recommendation to the orchestrator.

## Finding rules

Every finding must include:

- stable finding ID `FIND-NNN` within the review;
- severity and confidence;
- affected requirement or section IDs;
- exact artifact evidence;
- violated review rule;
- practical impact;
- recommended correction or human decision;
- blocking status.

Avoid style-only findings unless wording creates ambiguity or test risk. Do not report speculative defects without identifying the missing evidence or scenario.

## Recommendation

Return exactly one:

- `PASS_FOR_HUMAN_APPROVAL`: no Critical or High finding remains; every Must requirement has valid goal and acceptance traces; unresolved items are clearly visible and do not prevent a responsible baseline decision.
- `REWORK_REQUIRED`: one or more correctable blocking findings exist.
- `ESCALATE_TO_HUMAN`: product intent, risk acceptance, conflicting authoritative sources, independence, or policy cannot be resolved by requirements editing alone.
- `BLOCKED`: required inputs or authorization are missing.

This recommendation is never approval. `REWORK_REQUIRED` routes findings to the author while loop capacity remains. `PASS_FOR_HUMAN_APPROVAL` ends the AI loop and moves to human disposition. `ESCALATE_TO_HUMAN` and `BLOCKED` stop the loop.

## Output

Require the orchestrator-supplied `project_id` and `project_root`; reject cross-project inputs or a target outside that root. Only after all preconditions pass, write `<project_root>/requirements/reviews/review-vNNN.md` without changing the draft. Return:

- reviewed artifact path and version;
- review report path and version;
- recommendation;
- finding counts by severity;
- blocking finding IDs;
- traceability coverage summary;
- open decisions requiring a human;
- explicit statement: `This AI review is a recommendation only; the orchestrator controls loop routing and human approval.`

Stop after returning the report.
