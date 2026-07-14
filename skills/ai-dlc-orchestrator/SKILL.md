---
name: ai-dlc-orchestrator
description: Coordinate an approval-gated AI software development lifecycle with bounded independent AI author-review loops before human artifact approval. Use when starting, continuing, inspecting, pausing, approving, or resuming an AI-DLC project; routing requirements, architecture, implementation-plan, coding, review, or verification roles; or maintaining `.ai-dlc` state, versioned artifacts, findings, traceability, evidence, loop records, and approvals.
---

# AI-DLC Orchestrator

Coordinate specialists and state. Never replace an author or reviewer. AI review is quality control, never human approval.

## Required references

Read [references/workflow-contract.md](references/workflow-contract.md) before acting. Use [assets/state-template.yaml](assets/state-template.yaml) when initializing state.

## Invariants

1. Read `.ai-dlc/state.yaml` before action. If absent, propose initialization and wait for `APPROVE INTAKE`.
2. Require human approval to start each document phase or restart a loop after human rejection, escalation, or exhausted iterations.
3. A document phase follows `AI author -> independent AI reviewer -> AI rework` until pass or stop condition. Do not ask the human to accept an unreviewed artifact.
4. One phase-start approval authorizes only the named phase, input baselines, scope, and configured iteration limit. It does not authorize baseline approval, implementation, merge, push, deploy, or release.
5. Invoke one author and one distinct reviewer sequentially per iteration. Never run parallel authors, expose private author reasoning, or let a reviewer edit the artifact.
6. On `REWORK_REQUIRED`, automatically route accepted correctable findings to the author only when another iteration remains. On pass, stop for human approval. On escalation, blocked result, ambiguous finding, or exhausted limit, stop for human intervention.
7. Preserve every draft and review. Never overwrite a reviewed version. Keep stable IDs for unchanged semantics.
8. Only a human can approve a baseline, accept a work packet, defer a material finding, expand scope, or extend an iteration limit.
9. Record every author invocation, review, automatic routing decision, human decision, pause, and transition.
10. Do not design architecture before requirements baseline approval, plan implementation before architecture baseline approval, or change code before implementation-plan approval.
11. Keep implementation, code review, and verification distinct. Never merge, push, deploy, or release by implication.

## Document author-review loop

Apply this algorithm to requirements, architecture, and implementation plan:

1. Present approved inputs, scope, author skill, reviewer skill, starting version, maximum iterations, and files that may be created. Obtain the exact phase-loop approval.
2. Record a unique loop authorization and invoke the author for iteration `n`.
3. Validate the versioned proposal and author recommendation `READY_FOR_AI_REVIEW`.
4. Invoke a distinct reviewer for the same version without another human approval.
5. Persist the immutable review and route its recommendation:
   - `PASS_FOR_HUMAN_APPROVAL`: close the loop as passed and stop for human approval.
   - `REWORK_REQUIRED`: if `n < maximum`, increment the version, send only artifact evidence and findings to the author, and repeat.
   - `ESCALATE_TO_HUMAN` or `BLOCKED`: stop for human intervention.
   - `REWORK_REQUIRED` at the limit: stop as `AI_REVIEW_LIMIT_REACHED`.
6. Never treat absence of blocking findings outside a completed review as pass.

## Requirements phase

- Start with `APPROVE REQUIREMENTS AI LOOP vNNN` and use `$draft-requirements` plus `$review-requirements`.
- Write drafts and reviews under `.ai-dlc/requirements/drafts/` and `.ai-dlc/requirements/reviews/`.
- After pass, present the exact draft, review evidence, resolved and deferred findings, assumptions, open questions, and traceability. Ask for `APPROVE REQUIREMENTS BASELINE vNNN`, rejection with feedback, or pause.
- On approval, establish `.ai-dlc/requirements/baseline.md`; on rejection, require a new loop approval using the human feedback as an input.

## Architecture phase

- Start with `APPROVE ARCHITECTURE AI LOOP vNNN` and use `$design-architecture` plus `$review-architecture`.
- Require the approved requirements baseline. Store versioned proposals and reviews under `.ai-dlc/architecture/`.
- After pass, ask for `APPROVE ARCHITECTURE BASELINE vNNN`, rejection with feedback, or pause.
- On approval, establish `.ai-dlc/architecture/baseline.md`; on rejection, require a new loop approval.

## Implementation-plan phase

- Start with `APPROVE IMPLEMENTATION PLAN AI LOOP vNNN` and use `$plan-implementation` plus `$review-implementation-plan`.
- Require approved requirements and architecture baselines. Store plans under `.ai-dlc/implementation/plans/` and reviews under `.ai-dlc/implementation/plan-reviews/`.
- After pass, ask for `APPROVE IMPLEMENTATION PLAN BASELINE vNNN`, rejection with feedback, or pause.
- On approval, record the accepted plan and transition to work-packet authorization.

## Work-packet execution

1. Present one dependency-ready `WP-NNN` and ask for `APPROVE IMPLEMENTATION WP-NNN vNNN`.
2. Invoke `$implement-change`; then ask for `APPROVE CODE REVIEW WP-NNN vNNN` and invoke a distinct `$review-code` agent.
3. Route blocking findings to a separately approved implementation rework. Permit verification only after `PASS_FOR_VERIFICATION` and human acceptance of review disposition.
4. Ask for `APPROVE VERIFICATION WP-NNN vNNN`, invoke `$verify-change`, and present evidence.
5. Ask the human to accept, reject, request rework, or pause for that exact packet. Mark only explicitly accepted packets complete.
6. Transition to `IMPLEMENTATION_VERIFIED` only when every required packet is accepted. Do not claim release readiness.

## State and approval records

Update state atomically. Preserve `previous_status`, increment `revision`, update current stage/status/artifacts/loop counters, append history, and set `next_required_human_action`. Approval records contain ID, checkpoint, artifact or loop scope, version, decision, user note, environment timestamp, and actor `human-user` unless verified otherwise.

## Completion

Completion for this plugin version means all required work packets from the human-approved implementation plan have scoped executions, accepted independent code reviews, passing authorized verification, explicit human acceptance, and no unresolved blocking findings. Report all baseline, loop, review, evidence, and approval paths without claiming release readiness.
