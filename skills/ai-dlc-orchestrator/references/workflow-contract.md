# Workflow contract

## State machine

| Status | Event or decision | Next status |
|---|---|---|
| `NOT_INITIALIZED` | `APPROVE INTAKE` | `AWAITING_REQUIREMENTS_LOOP_AUTHORIZATION` |
| `AWAITING_REQUIREMENTS_LOOP_AUTHORIZATION` | `APPROVE REQUIREMENTS AI LOOP vNNN` | `REQUIREMENTS_AI_LOOP` |
| `REQUIREMENTS_AI_LOOP` | reviewer pass | `AWAITING_REQUIREMENTS_HUMAN_APPROVAL` |
| `REQUIREMENTS_AI_LOOP` | correctable rework with capacity | `REQUIREMENTS_AI_LOOP` |
| `REQUIREMENTS_AI_LOOP` | escalation, blocked, or limit | `AWAITING_HUMAN_INTERVENTION` |
| `AWAITING_REQUIREMENTS_HUMAN_APPROVAL` | approve baseline | `AWAITING_ARCHITECTURE_LOOP_AUTHORIZATION` |
| `AWAITING_REQUIREMENTS_HUMAN_APPROVAL` | reject with feedback | `AWAITING_REQUIREMENTS_LOOP_AUTHORIZATION` |
| `AWAITING_ARCHITECTURE_LOOP_AUTHORIZATION` | `APPROVE ARCHITECTURE AI LOOP vNNN` | `ARCHITECTURE_AI_LOOP` |
| `ARCHITECTURE_AI_LOOP` | reviewer pass | `AWAITING_ARCHITECTURE_HUMAN_APPROVAL` |
| `ARCHITECTURE_AI_LOOP` | correctable rework with capacity | `ARCHITECTURE_AI_LOOP` |
| `ARCHITECTURE_AI_LOOP` | escalation, blocked, or limit | `AWAITING_HUMAN_INTERVENTION` |
| `AWAITING_ARCHITECTURE_HUMAN_APPROVAL` | approve baseline | `AWAITING_IMPLEMENTATION_PLAN_LOOP_AUTHORIZATION` |
| `AWAITING_ARCHITECTURE_HUMAN_APPROVAL` | reject with feedback | `AWAITING_ARCHITECTURE_LOOP_AUTHORIZATION` |
| `AWAITING_IMPLEMENTATION_PLAN_LOOP_AUTHORIZATION` | `APPROVE IMPLEMENTATION PLAN AI LOOP vNNN` | `IMPLEMENTATION_PLAN_AI_LOOP` |
| `IMPLEMENTATION_PLAN_AI_LOOP` | reviewer pass | `AWAITING_IMPLEMENTATION_PLAN_HUMAN_APPROVAL` |
| `IMPLEMENTATION_PLAN_AI_LOOP` | correctable rework with capacity | `IMPLEMENTATION_PLAN_AI_LOOP` |
| `IMPLEMENTATION_PLAN_AI_LOOP` | escalation, blocked, or limit | `AWAITING_HUMAN_INTERVENTION` |
| `AWAITING_IMPLEMENTATION_PLAN_HUMAN_APPROVAL` | approve plan baseline | `AWAITING_WORK_PACKET_AUTHORIZATION` |
| `AWAITING_IMPLEMENTATION_PLAN_HUMAN_APPROVAL` | reject with feedback | `AWAITING_IMPLEMENTATION_PLAN_LOOP_AUTHORIZATION` |
| `AWAITING_WORK_PACKET_AUTHORIZATION` | approve named packet | `AWAITING_CODE_REVIEW_AUTHORIZATION` |
| `AWAITING_CODE_REVIEW_AUTHORIZATION` | approve independent review | `AWAITING_CODE_REVIEW_DISPOSITION` |
| `AWAITING_CODE_REVIEW_DISPOSITION` | accept for verification or request rework | `AWAITING_VERIFICATION_AUTHORIZATION` or `AWAITING_WORK_PACKET_REWORK_AUTHORIZATION` |
| `AWAITING_VERIFICATION_AUTHORIZATION` | approve verification | `AWAITING_WORK_PACKET_DISPOSITION` |
| `AWAITING_WORK_PACKET_DISPOSITION` | accept/rework named packet | `AWAITING_WORK_PACKET_AUTHORIZATION`, `AWAITING_WORK_PACKET_REWORK_AUTHORIZATION`, or `IMPLEMENTATION_VERIFIED` |
| `AWAITING_HUMAN_INTERVENTION` | resolve and approve named restart | corresponding loop authorization state |
| `IMPLEMENTATION_VERIFIED` | new release approval | unchanged; release is out of scope |

Any status may move to `PAUSED` or `BLOCKED`. Resume only with a human decision naming the intended checkpoint.

## AI loop rules

1. Default maximum is 3 author-review iterations per loop authorization.
2. One iteration contains exactly one versioned author artifact followed by one independent review of that version.
3. `REWORK_REQUIRED` automatically starts the next iteration only for correctable artifact findings and while capacity remains.
4. Product intent, risk acceptance, conflicting sources, scope expansion, cross-phase baseline change, or ambiguous findings require human intervention.
5. A loop pass requires the reviewer-specific pass criteria. Self-check success is insufficient.
6. Human rejection after a pass closes that loop. A new authorization is required to revise.
7. Store authorization ID, phase, approved inputs, scope, starting version, iteration and maximum, author and reviewer identities, artifacts, reviews, routing decisions, and outcome.

## Baseline invariants

- AI pass is not human approval.
- Only the exact AI-reviewed version may be presented for human approval.
- Any edit after pass invalidates the pass and requires a new AI review.
- A baseline requires AI review pass, explicit human approval, no unresolved Critical or High finding, and dispositions for deferred Medium findings.
- Architecture requires requirements baseline; implementation plan requires architecture baseline; code requires implementation-plan baseline.

## File layout

```text
.ai-dlc/
  registry.yaml
  projects/
    <project-id>/
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
        plan-reviews/implementation-plan-review-v001.md
        executions/WP-001-v001.md
        reviews/WP-001-review-v001.md
        verifications/WP-001-verification-v001.md
      runs/run-YYYYMMDD-NNN.md
```

`registry.yaml` is workspace-scoped. Every other workflow artifact is project-scoped. The resolved project root is `.ai-dlc/projects/<project-id>`. Reject writes that normalize outside this root.

## Registry and selection

- Registry schema fields: `schema_version`, optional `active_project_id`, and `projects` keyed by project ID with `path`, `status`, and `created_at`.
- A project ID is an immutable lowercase slug matching `[a-z0-9][a-z0-9-]{0,62}`. Renaming requires a separately approved migration.
- Explicit project context overrides `active_project_id`. If neither resolves exactly one project, stop for user selection.
- Creating, selecting, archiving, or migrating a project is a human gate and must be recorded in that project's approvals and registry history where applicable.
- Legacy top-level `.ai-dlc/state.yaml` is supported as one read/write root until an explicitly approved migration. Do not mix legacy and namespaced paths in one project.

## Human gate presentation

Show the project ID, completed loop or action, exact artifacts and review evidence, material changes and findings, iteration usage, proposed decision, files affected by the next action, exact project-qualified approval phrase, and reject/edit/pause alternatives.
