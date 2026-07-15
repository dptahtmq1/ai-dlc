# Requirements contract

## Required document sections

1. Metadata
2. Purpose, success, and scope
3. Functional requirements
4. Non-functional requirements and quality measures
5. Constraints, assumptions, and open questions
6. Revision summary for revised versions only

Do not add standalone actor, goal, quality-scenario, acceptance-criterion, or traceability item sets. Use short prose in the relevant section when that context is needed.

## Metadata

Include project ID, artifact ID, version, status `PROPOSED`, author role, approved input references, authorization approval ID, creation timestamp, and superseded version when applicable. Do not set an approval field.

## Functional requirement fields

| Field | Required | Rule |
|---|---|---|
| ID | yes | `FR-NNN`, stable across revisions |
| Title | yes | Short behavior name |
| Statement | yes | One testable system obligation |
| Priority | yes | Must, Should, or Could |
| Rationale | when useful | Business or user reason that affects prioritization |
| Source | when material | Approved input reference |
| Actors | when applicable | Plain-language actor name |
| Preconditions | when applicable | Observable starting conditions |
| Main behavior | yes | Trigger and system response |
| Failure behavior | when applicable | Invalid input, denial, timeout, conflict |
| Verification | yes | Concise observable success and relevant failure conditions |
| Status | yes | `PROPOSED` or `PROPOSED_NEEDS_INPUT` |

## Non-functional requirement fields

Each `NFR-*` includes the quality attribute, operating context, measurable target, measurement method, priority, and status. This embeds the useful part of a quality-attribute scenario in the NFR. Do not create separate `QA-*` items. If a target would be invented, state the missing decision as an open question instead.

## Constraint fields

Each `CON-*` states one binding business, policy, technology, schedule, compatibility, or operating constraint and cites its approved source. Do not promote assumptions or preferences to constraints.

## Item-count policy

Functional requirements, non-functional requirements, and constraints must each contain 5 to 7 items by default. A category may exceed 7 only when the document states why consolidation would lose an independently testable obligation, measurable quality target, or binding constraint. Never split items merely to populate a template. If fewer than 5 genuine items exist, do not invent filler; state why the category is smaller.

## Status vocabulary

- `PROPOSED`: complete enough for human consideration.
- `PROPOSED_NEEDS_INPUT`: cannot be validated without a human decision.
- `RETIRED`: preserved for history but no longer part of the proposal.

Only the orchestrator may later record `HUMAN_APPROVED` after explicit baseline approval.
