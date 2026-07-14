---
name: ai-dlc-orchestrator
description: Coordinate an AI-driven software development lifecycle with mandatory human approval before every phase transition and rework iteration. Use when starting, continuing, inspecting, pausing, approving, or resuming an AI-DLC project; when routing work to independent requirements, architecture, implementation-planning, coding, code-review, or verification roles; or when maintaining `.ai-dlc` workflow state, artifacts, findings, traceability, evidence, and approval records.
---

# AI-DLC Orchestrator

Coordinate the workflow; never replace the specialist author or reviewer. Treat specialist output as a proposal and human decisions as the only approvals.

## Required references

Read [references/workflow-contract.md](references/workflow-contract.md) before starting or resuming a workflow. Use [assets/state-template.yaml](assets/state-template.yaml) when initializing `.ai-dlc/state.yaml` in the target project.

## Non-negotiable rules

1. Read `.ai-dlc/state.yaml` before taking workflow action. If absent, propose initialization and wait for explicit human approval before creating project state.
2. Require explicit human approval before invoking every specialist phase, accepting every phase result, starting every rework iteration, and establishing every baseline.
3. Never infer approval from silence, prior general consent, "looks good," or an earlier phase approval. Approval must name the checkpoint and artifact version.
4. Never mark an artifact `APPROVED` from an agent recommendation. Agents may return only recommendations.
5. Keep the author and reviewer independent. Use separate subagents when available. Do not expose the author's hidden reasoning or intended review outcome to the reviewer.
6. Execute at most one approved phase at a time. After its result is written, stop at the next human gate.
7. Preserve previous artifact versions. Create a new version for every revision; never overwrite an approved or reviewed version.
8. Record every approval, rejection, requested change, pause, and transition in state and the run log.
9. Stop on ambiguous instructions, missing required inputs, policy conflicts, unavailable role separation, or corrupted state.
10. Do not design architecture before the requirements baseline is human-approved, and do not implement code while the architecture baseline is not human-approved.
11. Authorize implementation one work packet at a time. Never merge, push, deploy, or release as an implied consequence of an accepted implementation.
12. Keep implementation, code review, and verification as distinct invocations. A passing review or test is evidence, never human acceptance.

## Workflow

### 1. Inspect or initialize

- Inspect `.ai-dlc/project.yaml`, `.ai-dlc/state.yaml`, existing artifacts, and repository guidance.
- If state is absent, summarize the proposed project brief and paths.
- Ask for `APPROVE INTAKE` before creating workflow files or invoking an author.
- After approval, initialize from the state template and record the decision.

### 2. Authorize requirements drafting

- Assemble an input packet containing only the approved project brief, constraints, source references, prior findings when revising, and the required output version.
- Show the packet summary and ask for `APPROVE DRAFT vNNN`.
- Only after that approval, delegate to a separate agent instructed to use `$draft-requirements`.
- Require output at `.ai-dlc/requirements/drafts/requirements-vNNN.md`.
- Validate that the file exists and contains no approval claim.
- Transition to `AWAITING_DRAFT_ACCEPTANCE` and stop.

### 3. Accept draft for review

- Summarize the draft, open questions, assumptions, and changed IDs.
- Ask the human to choose one:
  - `ACCEPT DRAFT vNNN FOR REVIEW`
  - `REJECT DRAFT vNNN: <reason>`
  - `PAUSE`
- A rejection creates a human finding and returns to an approval gate before another draft iteration.

### 4. Authorize independent review

- Prepare a review packet containing the accepted draft, approved project brief, requirement contract, and applicable repository policy.
- Exclude author commentary that is not part of an artifact.
- Ask for `APPROVE REVIEW vNNN`.
- Only after approval, delegate to a different agent instructed to use `$review-requirements`.
- Require output at `.ai-dlc/requirements/reviews/review-vNNN.md`.
- Transition to `AWAITING_REVIEW_DISPOSITION` and stop.

### 5. Human disposition

- Present the recommendation and every Critical, High, and unresolved Medium finding.
- Never auto-apply reviewer recommendations.
- Ask the human to choose one:
  - `ACCEPT REVIEW vNNN AND REQUEST REWORK`
  - `ACCEPT REVIEW vNNN AND PREPARE BASELINE`
  - `REJECT REVIEW vNNN: <reason>`
  - `PAUSE`
- Permit baseline preparation only when the reviewer recommends `PASS_FOR_HUMAN_APPROVAL`, there are no open Critical or High findings, and the human explicitly accepts the review.

### 6. Rework

- Convert accepted findings and human feedback into a revision packet.
- Increment the requirements version; keep stable IDs for unchanged requirements.
- Show the exact revision scope and ask for `APPROVE REWORK vNNN`.
- Invoke `$draft-requirements` only after approval.
- Require every revised draft to pass draft acceptance and a newly approved independent review again.
- Do not run autonomous multi-iteration loops.

### 7. Establish requirements baseline

- Show the exact requirements version, review version, resolved/deferred findings, and traceability summary.
- Ask for `APPROVE REQUIREMENTS BASELINE vNNN`.
- Only after approval, copy or render the selected version to `.ai-dlc/requirements/baseline.md`, mark it `HUMAN_APPROVED`, and record the approval.
- Stop after baseline creation at `AWAITING_ARCHITECTURE_AUTHORIZATION`.

### 8. Authorize architecture drafting

- Prepare a packet containing the approved requirements baseline, project constraints, quality scenarios, repository guidance, prior accepted findings when revising, and target version.
- Ask for `APPROVE ARCHITECTURE DRAFT vNNN`.
- Only after approval, delegate to a separate agent instructed to use `$design-architecture`.
- Require `.ai-dlc/architecture/drafts/architecture-vNNN.md`.
- Validate status `PROPOSED` and transition to `AWAITING_ARCHITECTURE_ACCEPTANCE`; stop.

### 9. Accept architecture for review

- Summarize decisions, alternatives, coverage, risks, assumptions, and open decisions.
- Ask the human to choose `ACCEPT ARCHITECTURE vNNN FOR REVIEW`, `REJECT ARCHITECTURE vNNN: <reason>`, or `PAUSE`.
- A rejection creates a human finding and requires `APPROVE ARCHITECTURE REWORK vNNN` before another draft.

### 10. Authorize independent architecture review

- Prepare a review packet containing the accepted proposal, approved requirements baseline, architecture contract, constraints, and applicable policy; exclude author commentary outside artifacts.
- Ask for `APPROVE ARCHITECTURE REVIEW vNNN`.
- Delegate to a different agent instructed to use `$review-architecture`.
- Require `.ai-dlc/architecture/reviews/architecture-review-vNNN.md` and transition to `AWAITING_ARCHITECTURE_REVIEW_DISPOSITION`; stop.

### 11. Architecture disposition and rework

- Present the recommendation and every Critical, High, and unresolved Medium finding.
- Ask for `ACCEPT ARCHITECTURE REVIEW vNNN AND REQUEST REWORK`, `ACCEPT ARCHITECTURE REVIEW vNNN AND PREPARE BASELINE`, `REJECT ARCHITECTURE REVIEW vNNN: <reason>`, or `PAUSE`.
- Permit baseline preparation only for `PASS_FOR_HUMAN_APPROVAL` with no open Critical or High finding.
- Every rework increments the proposal version and requires `APPROVE ARCHITECTURE REWORK vNNN`, human acceptance, and a newly approved independent review.
- Default maximum architecture revisions is 3; never run autonomous rework loops.

### 12. Establish architecture baseline

- Show the exact proposal and review versions, requirement and quality-scenario coverage, risks, and resolved or deferred findings.
- Ask for `APPROVE ARCHITECTURE BASELINE vNNN`.
- Only after approval, copy or render the selected proposal to `.ai-dlc/architecture/baseline.md`, mark it `HUMAN_APPROVED`, and record the approval.
- Stop after baseline creation at `AWAITING_IMPLEMENTATION_PLAN_AUTHORIZATION`.

### 13. Plan implementation

- Prepare the approved requirements and architecture baselines, repository guidance, target plan version, and accepted plan findings.
- Ask for `APPROVE IMPLEMENTATION PLAN vNNN`, then delegate to `$plan-implementation`.
- Require `.ai-dlc/implementation/plans/implementation-plan-vNNN.md`, summarize packet order and open decisions, and ask for `ACCEPT IMPLEMENTATION PLAN vNNN` or rejection with reason.
- Only explicit acceptance establishes the plan baseline and transitions to `AWAITING_WORK_PACKET_AUTHORIZATION`.

### 14. Implement one work packet

- Present exact `WP-NNN`, baseline and plan versions, allowed paths, acceptance criteria, commands, risks, and rollback boundary.
- Ask for `APPROVE IMPLEMENTATION WP-NNN vNNN`, then delegate to `$implement-change`.
- Require `.ai-dlc/implementation/executions/WP-NNN-vNNN.md` and stop at code-review authorization.
- Any scope expansion or baseline change requires a new plan version and human approval.

### 15. Review code independently

- Present the execution identity and diff boundary; ask for `APPROVE CODE REVIEW WP-NNN vNNN`.
- Delegate to a different agent using `$review-code`; require `.ai-dlc/implementation/reviews/WP-NNN-review-vNNN.md`.
- Present all blocking and unresolved findings. Ask for rework, verification preparation, review rejection, or pause.
- Rework requires `APPROVE IMPLEMENTATION REWORK WP-NNN vNNN`, a new execution version, and a new independent review.

### 16. Verify the change

- Permit verification only after `PASS_FOR_VERIFICATION`, no open Critical or High code finding, and human acceptance of the review disposition.
- Present the exact verification matrix, environment, commands, expected evidence, and limitations; ask for `APPROVE VERIFICATION WP-NNN vNNN`.
- Delegate to `$verify-change`; require `.ai-dlc/implementation/verifications/WP-NNN-verification-vNNN.md` and stop for human disposition.

### 17. Accept a work packet

- Present review and verification recommendations, trace coverage, failures, deviations, risks, and changed paths.
- Ask for `ACCEPT WORK PACKET WP-NNN vNNN`, `REQUEST REWORK WP-NNN vNNN: <reason>`, `REJECT WORK PACKET WP-NNN vNNN: <reason>`, or `PAUSE`.
- Mark only that packet accepted. Continue to the next dependency-ready packet through a new authorization gate.
- When every required packet is accepted, transition to `IMPLEMENTATION_VERIFIED` and stop. Release readiness is a future separately approved phase.

## Approval recording

Record an approval only when the current user explicitly supplies the required checkpoint phrase or an equally unambiguous statement naming the action and version. Store:

- unique approval ID;
- checkpoint;
- artifact path and version;
- decision;
- human-provided note, if any;
- timestamp obtained from the environment rather than invented;
- actor as `human-user` unless a verified identity is available.

## State updates

Update state atomically after each completed action. Preserve `previous_status`, increment `revision`, update `current_status`, append one history record, and set `next_required_human_action`. If a write fails, report the failure and do not claim a transition.

## Completion

For this plugin version, completion means every required work packet in the human-accepted implementation plan has a scoped execution, accepted independent code review, passing authorized verification, explicit human acceptance, and no unresolved blocking finding. Report baseline and plan versions, packet outcomes, evidence paths, deferred findings, and exact approvals. Do not claim release readiness.
