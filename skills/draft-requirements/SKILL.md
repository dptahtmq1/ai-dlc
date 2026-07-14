---
name: draft-requirements
description: Draft or revise traceable functional and non-functional software requirements inside a human-authorized AI author-review loop. Use when an AI-DLC orchestrator supplies an approved project brief, loop authorization, target version, and independent AI review findings for revision; produce a versioned proposal for AI review without making architecture decisions or claiming approval.
---

# Draft Requirements

Produce a proposal for independent AI review. Never approve requirements, advance workflow state, invoke the reviewer, or describe the output as final.

## Required references

Read [references/requirements-contract.md](references/requirements-contract.md) completely before drafting. Use [assets/requirements-template.md](assets/requirements-template.md) as the output structure.

## Preconditions

Proceed only when the input packet contains:

- an approved project brief;
- explicit scope and known constraints;
- the target version `vNNN`;
- an active requirements-loop authorization established by a human phase-start approval;
- the prior AI review report and routed findings when revising.

If any precondition is missing, the target exceeds the authorized loop limit, or versions disagree, return `BLOCKED` without writing a draft.

## Workflow

1. Read the project brief, source material, applicable repository guidance, prior draft, and accepted findings.
2. Separate user-stated facts from inferred assumptions. Never present an inference as an approved fact.
3. Identify goals, stakeholders, actors, in-scope behavior, exclusions, constraints, dependencies, and domain terms.
4. Draft atomic functional requirements and measurable non-functional requirements.
5. Express important non-functional requirements as quality-attribute scenarios.
6. Create observable acceptance criteria for every Must requirement.
7. Add stable trace links from goals to requirements and from requirements to acceptance criteria.
8. Run the self-check below. Repair defects within the same authorized draft only; do not invent missing business decisions.
9. Write exactly one versioned draft to the authorized path and return a concise summary to the orchestrator.

## Authoring rules

- Use `GOAL-NNN`, `ACTOR-NNN`, `FR-NNN`, `NFR-NNN`, `QA-NNN`, `AC-NNN`, `CON-NNN`, `ASM-NNN`, and `OPEN-NNN` identifiers.
- Keep existing IDs for semantically unchanged items. Retire IDs rather than reusing them for different meanings.
- Write one independently testable obligation per requirement.
- Use `Must`, `Should`, or `Could` priority. Explain the rationale for every Must.
- Use normative language: "The system must..." for mandatory behavior.
- State trigger, behavior, observable result, error behavior, and relevant authorization boundary where applicable.
- Give every requirement a source or mark it as an assumption requiring human confirmation.
- Include no programming language, framework, database, topology, or vendor choice unless the human-approved brief explicitly makes it a constraint.
- Convert uncertain facts into `OPEN-*` questions. Do not resolve them by preference.
- Never quote or fabricate hidden stakeholder statements.
- Do not include chain-of-thought. Record only concise rationale and evidence references.

## Non-functional requirements

Avoid words such as fast, scalable, secure, intuitive, reliable, and easy without measures. When an exact target is unavailable:

1. create an `OPEN-*` question;
2. state why the missing value affects design or validation;
3. leave the related requirement in `PROPOSED_NEEDS_INPUT` state.

Quality-attribute scenarios must include source, stimulus, environment, affected artifact, response, and response measure.

## Revision rules

- Modify only the AI-review findings routed by the orchestrator, human comments, and consistency repairs.
- Include a change summary mapping each routed finding or human comment to its resolution.
- Mark unresolved findings explicitly.
- Never overwrite the prior draft or review.
- Every revised draft requires a new independent AI review; it does not require another human approval while the loop authorization remains active.

## Self-check

Before writing, verify:

- each Must requirement traces to at least one goal;
- each Must requirement has at least one acceptance criterion;
- all NFRs are measurable or explicitly blocked on an open question;
- no requirement contradicts the scope or another requirement;
- exclusions are not silently reintroduced;
- permissions, failure paths, data handling, and external dependencies are covered where relevant;
- no architecture decision was made without an approved constraint;
- all assumptions and open questions are visible;
- the document says `PROPOSED`, never `APPROVED`.

## Output

Write `.ai-dlc/requirements/drafts/requirements-vNNN.md`. Return:

- artifact path and version;
- counts by requirement type and priority;
- open questions and assumptions;
- changed, added, retired, and unchanged IDs for revisions;
- self-check result;
- recommendation `READY_FOR_AI_REVIEW` or `BLOCKED`.

Stop after returning the proposal. Do not start review.
