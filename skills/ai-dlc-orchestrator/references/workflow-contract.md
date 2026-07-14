# Workflow contract

## State machine

| Current status | Required human decision | Allowed action | Next status |
|---|---|---|---|
| `NOT_INITIALIZED` | `APPROVE INTAKE` | Initialize project state | `AWAITING_DRAFT_AUTHORIZATION` |
| `AWAITING_DRAFT_AUTHORIZATION` | `APPROVE DRAFT vNNN` | Invoke requirements author | `AWAITING_DRAFT_ACCEPTANCE` |
| `AWAITING_DRAFT_ACCEPTANCE` | Accept or reject named draft | Accept for review or create human finding | `AWAITING_REVIEW_AUTHORIZATION` or `AWAITING_REWORK_AUTHORIZATION` |
| `AWAITING_REVIEW_AUTHORIZATION` | `APPROVE REVIEW vNNN` | Invoke independent reviewer | `AWAITING_REVIEW_DISPOSITION` |
| `AWAITING_REVIEW_DISPOSITION` | Accept or reject named review | Route to rework or baseline preparation | `AWAITING_REWORK_AUTHORIZATION` or `AWAITING_BASELINE_APPROVAL` |
| `AWAITING_REWORK_AUTHORIZATION` | `APPROVE REWORK vNNN` | Invoke author for one revision | `AWAITING_DRAFT_ACCEPTANCE` |
| `AWAITING_BASELINE_APPROVAL` | `APPROVE REQUIREMENTS BASELINE vNNN` | Establish requirements baseline | `AWAITING_ARCHITECTURE_AUTHORIZATION` |
| `AWAITING_ARCHITECTURE_AUTHORIZATION` | `APPROVE ARCHITECTURE DRAFT vNNN` | Invoke architecture author | `AWAITING_ARCHITECTURE_ACCEPTANCE` |
| `AWAITING_ARCHITECTURE_ACCEPTANCE` | Accept or reject named proposal | Accept for review or create human finding | `AWAITING_ARCHITECTURE_REVIEW_AUTHORIZATION` or `AWAITING_ARCHITECTURE_REWORK_AUTHORIZATION` |
| `AWAITING_ARCHITECTURE_REVIEW_AUTHORIZATION` | `APPROVE ARCHITECTURE REVIEW vNNN` | Invoke independent architecture reviewer | `AWAITING_ARCHITECTURE_REVIEW_DISPOSITION` |
| `AWAITING_ARCHITECTURE_REVIEW_DISPOSITION` | Accept or reject named review | Route to rework or baseline preparation | `AWAITING_ARCHITECTURE_REWORK_AUTHORIZATION` or `AWAITING_ARCHITECTURE_BASELINE_APPROVAL` |
| `AWAITING_ARCHITECTURE_REWORK_AUTHORIZATION` | `APPROVE ARCHITECTURE REWORK vNNN` | Invoke architecture author for one revision | `AWAITING_ARCHITECTURE_ACCEPTANCE` |
| `AWAITING_ARCHITECTURE_BASELINE_APPROVAL` | `APPROVE ARCHITECTURE BASELINE vNNN` | Establish architecture baseline | `AWAITING_IMPLEMENTATION_PLAN_AUTHORIZATION` |
| `AWAITING_IMPLEMENTATION_PLAN_AUTHORIZATION` | `APPROVE IMPLEMENTATION PLAN vNNN` | Invoke implementation planner | `AWAITING_IMPLEMENTATION_PLAN_ACCEPTANCE` |
| `AWAITING_IMPLEMENTATION_PLAN_ACCEPTANCE` | Accept or reject named plan | Establish plan baseline or request revision | `AWAITING_WORK_PACKET_AUTHORIZATION` or `AWAITING_IMPLEMENTATION_PLAN_AUTHORIZATION` |
| `AWAITING_WORK_PACKET_AUTHORIZATION` | `APPROVE IMPLEMENTATION WP-NNN vNNN` | Implement one work packet | `AWAITING_CODE_REVIEW_AUTHORIZATION` |
| `AWAITING_CODE_REVIEW_AUTHORIZATION` | `APPROVE CODE REVIEW WP-NNN vNNN` | Invoke independent code reviewer | `AWAITING_CODE_REVIEW_DISPOSITION` |
| `AWAITING_CODE_REVIEW_DISPOSITION` | Accept review for verification or request rework | Prepare verification or rework | `AWAITING_VERIFICATION_AUTHORIZATION` or `AWAITING_WORK_PACKET_REWORK_AUTHORIZATION` |
| `AWAITING_WORK_PACKET_REWORK_AUTHORIZATION` | `APPROVE IMPLEMENTATION REWORK WP-NNN vNNN` | Revise one packet execution | `AWAITING_CODE_REVIEW_AUTHORIZATION` |
| `AWAITING_VERIFICATION_AUTHORIZATION` | `APPROVE VERIFICATION WP-NNN vNNN` | Invoke verifier | `AWAITING_WORK_PACKET_DISPOSITION` |
| `AWAITING_WORK_PACKET_DISPOSITION` | Accept, reject, or request rework for named packet | Record packet decision | `AWAITING_WORK_PACKET_AUTHORIZATION`, `AWAITING_WORK_PACKET_REWORK_AUTHORIZATION`, or `IMPLEMENTATION_VERIFIED` |
| `IMPLEMENTATION_VERIFIED` | New release-specific approval | No release or deployment action in current plugin version | unchanged |

Any status may transition to `PAUSED` or `BLOCKED` after a human pause, missing input, unavailable independent reviewer, state inconsistency, or policy conflict. Resumption requires a human decision naming the intended checkpoint.

## Invariants

1. `current_status` has exactly one value.
2. Each artifact version is immutable after it enters review.
3. Every transition references one approval or an explicit blocking event.
4. An approval applies to one checkpoint and one artifact version only.
5. A later edit invalidates prior draft acceptance, review disposition, and baseline approval for that artifact.
6. Reviewer recommendation is never represented as human approval.
7. A requirements baseline requires all of:
   - a human-accepted draft;
   - an independent review;
   - no open Critical or High finding;
   - a human-accepted review disposition;
   - explicit human baseline approval.
8. `ARCHITECTURE_BASELINED` additionally requires an accepted architecture proposal, an independent architecture review, no open Critical or High architecture finding, human-accepted review disposition, and explicit architecture baseline approval.
9. `IMPLEMENTATION_VERIFIED` requires every required plan packet to have an authorized execution, independent code review, authorized passing verification, explicit human packet acceptance, and no unresolved blocking finding.

## File layout in the target repository

```text
.ai-dlc/
  project.yaml
  state.yaml
  traceability.yaml
  findings.yaml
  approvals.yaml
  requirements/
    drafts/requirements-v001.md
    reviews/review-v001.md
    baseline.md
  architecture/
    drafts/architecture-v001.md
    reviews/architecture-review-v001.md
    baseline.md
  implementation/
    plans/implementation-plan-v001.md
    executions/WP-001-v001.md
    reviews/WP-001-review-v001.md
    verifications/WP-001-verification-v001.md
  runs/run-YYYYMMDD-NNN.md
```

Create missing directories only after `APPROVE INTAKE`.

## Iteration limits

- Default maximum requirements revisions: 3.
- Default maximum architecture revisions: 3.
- Default maximum execution revisions per work packet: 3.
- Every revision requires a new human authorization.
- On the third rejected revision, transition to `BLOCKED` and request a human decision to change scope, extend the limit, or stop.
- Never silently reset a counter.

## Human gate presentation

At every gate, show:

1. completed action and resulting artifact;
2. material changes and open findings;
3. proposed next action;
4. exact files that may be created or changed;
5. the explicit approval phrase expected;
6. alternatives: reject with reason, edit, or pause.
