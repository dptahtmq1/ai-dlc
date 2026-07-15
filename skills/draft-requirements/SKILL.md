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
3. Summarize purpose, scope, users, exclusions, constraints, dependencies, and domain terms without creating separately managed item types.
4. Draft only functional requirements, non-functional requirements with embedded quality measures, and constraints.
5. Keep each category between 5 and 7 items by default. When a category needs more than 7, state the consolidation considered and the concrete reason each additional item must remain separate.
6. Run the self-check below. Repair defects within the same authorized draft only; do not invent missing business decisions.
7. Write exactly one versioned draft to the authorized path and return a concise summary to the orchestrator.

## Authoring rules

- Use identifiers only for managed specification items: `FR-NNN`, `NFR-NNN`, and `CON-NNN`.
- Keep existing IDs for semantically unchanged items. Retire IDs rather than reusing them for different meanings.
- Write one independently testable obligation per requirement.
- Use `Must`, `Should`, or `Could` priority. Include a brief rationale only when it adds decision value.
- Use normative language: "The system must..." for mandatory behavior.
- State behavior, observable result, error behavior, and relevant authorization boundary where applicable. Keep verification conditions inline instead of creating acceptance-criterion items.
- Cite an approved input only where source fidelity is material. Keep assumptions and open questions as concise prose, not managed ID sets.
- Include no programming language, framework, database, topology, or vendor choice unless the human-approved brief explicitly makes it a constraint.
- Convert uncertain facts into `OPEN-*` questions. Do not resolve them by preference.
- Never quote or fabricate hidden stakeholder statements.
- Do not include chain-of-thought. Record only concise rationale and evidence references.

## Non-functional requirements

Avoid words such as fast, scalable, secure, intuitive, reliable, and easy without measures. When an exact target is unavailable:

1. create an `OPEN-*` question;
2. state why the missing value affects design or validation;
3. leave the related requirement in `PROPOSED_NEEDS_INPUT` state.

Embed quality-attribute context and measures directly in each `NFR-*`. Do not create separate `QA-*` items. Include only the context, required response, and measurable target needed to validate the NFR.

## Revision rules

- Modify only the AI-review findings routed by the orchestrator, human comments, and consistency repairs.
- Include a change summary mapping each routed finding or human comment to its resolution.
- Mark unresolved findings explicitly.
- Never overwrite the prior draft or review.
- Every revised draft requires a new independent AI review; it does not require another human approval while the loop authorization remains active.

## Self-check

Before writing, verify:

- all NFRs are measurable or explicitly blocked on an open question;
- every FR states an observable outcome and relevant failure behavior;
- FR, NFR, and constraints each contain 5 to 7 items, or the document gives a concrete over-limit justification;
- no requirement contradicts the scope or another requirement;
- exclusions are not silently reintroduced;
- permissions, failure paths, data handling, and external dependencies are covered where relevant;
- no architecture decision was made without an approved constraint;
- all assumptions and open questions are visible;
- the document says `PROPOSED`, never `APPROVED`.

## Output

Require the orchestrator-supplied `project_id` and `project_root`; reject a target outside that root. Write `<project_root>/requirements/drafts/requirements-vNNN.md`. Return:

- artifact path and version;
- counts for FR, NFR, and constraints, including any over-limit justification;
- open questions and assumptions;
- changed, added, retired, and unchanged IDs for revisions;
- self-check result;
- recommendation `READY_FOR_AI_REVIEW` or `BLOCKED`.

Stop after returning the proposal. Do not start review.
