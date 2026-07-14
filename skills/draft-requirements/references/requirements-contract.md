# Requirements contract

## Required document sections

1. Metadata
2. Problem and product purpose
3. Goals and success measures
4. Stakeholders and actors
5. Scope
6. Constraints and dependencies
7. Functional requirements
8. Non-functional requirements
9. Quality-attribute scenarios
10. Acceptance criteria
11. Assumptions
12. Open questions
13. Traceability matrix
14. Revision summary

## Metadata

Include project ID, artifact ID, version, status `PROPOSED`, author role, approved input references, authorization approval ID, creation timestamp, and superseded version when applicable. Do not set an approval field.

## Functional requirement fields

| Field | Required | Rule |
|---|---|---|
| ID | yes | `FR-NNN`, stable across revisions |
| Title | yes | Short behavior name |
| Statement | yes | One testable system obligation |
| Priority | yes | Must, Should, or Could |
| Rationale | yes for Must | Business or user reason |
| Source | yes | Goal, approved input, or assumption ID |
| Actors | when applicable | Actor IDs |
| Preconditions | when applicable | Observable starting conditions |
| Main behavior | yes | Trigger and system response |
| Failure behavior | when applicable | Invalid input, denial, timeout, conflict |
| Acceptance criteria | yes for Must | One or more `AC-*` IDs |
| Status | yes | `PROPOSED` or `PROPOSED_NEEDS_INPUT` |

## Non-functional requirement fields

Each `NFR-*` includes quality attribute, operating context, threshold or target, measurement method, priority, rationale, source, related `QA-*`, and status. If a number would be invented, create an open question instead.

## Quality-attribute scenario fields

| Field | Meaning |
|---|---|
| Source | Entity causing the stimulus |
| Stimulus | Event or condition |
| Environment | Relevant operating state |
| Artifact | Affected system element or whole system |
| Response | Required observable behavior |
| Response measure | Numeric or binary validation target |

## Acceptance criterion format

Prefer Given/When/Then when it improves clarity. Each criterion includes ID, related requirement IDs, setup, action, expected observable result, and verification method. Avoid implementation-specific assertions unless the implementation is an approved constraint.

## Traceability minimum

The matrix must support:

`GOAL → FR/NFR → QA when applicable → AC`

Every Must requirement must have incoming goal trace and outgoing acceptance trace. Mark orphaned items as blocking defects.

## Status vocabulary

- `PROPOSED`: complete enough for human consideration.
- `PROPOSED_NEEDS_INPUT`: cannot be validated without a human decision.
- `RETIRED`: preserved for history but no longer part of the proposal.

Only the orchestrator may later record `HUMAN_APPROVED` after explicit baseline approval.
