---
name: design-architecture
description: Draft or revise a traceable software architecture proposal from a human-approved requirements baseline. Use when an AI-DLC orchestrator has explicit human authorization for a named architecture draft or rework version and needs system context, architectural drivers, decisions, components, interfaces, data ownership, deployment constraints, quality-attribute tactics, risks, and requirement traceability without approving the design or implementing code.
---

# Design Architecture

Produce an architecture proposal for human review. Never approve architecture, advance workflow state, invoke another phase, or implement code.

## Required references

Read [references/architecture-contract.md](references/architecture-contract.md) completely before drafting. Use [assets/architecture-template.md](assets/architecture-template.md) as the output structure.

## Preconditions

Proceed only when the input packet contains:

- the exact human-approved requirements baseline and its approval record;
- approved project scope, constraints, and applicable repository guidance;
- the target architecture version `vNNN`;
- an orchestrator record that a human approved this draft or rework invocation;
- accepted findings and human comments when revising.

If any precondition is missing or versions disagree, return `BLOCKED` without writing a draft.

## Workflow

1. Read the approved baseline, quality scenarios, constraints, traceability, and accepted findings.
2. Identify architecture drivers and decisions that must be made now; leave unrelated decisions open.
3. Define system context, trust boundaries, components, responsibilities, interfaces, data ownership, and deployment constraints at the minimum useful level.
4. Connect each material decision and quality tactic to requirement IDs or an explicit assumption.
5. Record alternatives, rationale, consequences, risks, validation approach, and unresolved decisions.
6. Check consistency, feasibility, operability, security, privacy, evolvability, and requirement coverage.
7. Write exactly one versioned proposal to the authorized path and return a concise summary.

## Design rules

- Use `DRV-NNN`, `DEC-NNN`, `CMP-NNN`, `INT-NNN`, `DATA-NNN`, `RISK-NNN`, `ADR-NNN`, and `OPEN-ARCH-NNN` identifiers.
- Keep stable IDs for semantically unchanged items; retire rather than reuse IDs.
- Treat approved technology choices as constraints, not preferences. Do not invent vendors, frameworks, databases, or topology.
- Give every component one clear responsibility and every interface an owner, consumers, contract, failure behavior, and security boundary where relevant.
- Identify data classification, ownership, lifecycle, consistency, recovery, and migration concerns where applicable.
- Express quality tactics with the requirement or quality-scenario IDs they satisfy and how they will be verified.
- Record meaningful alternatives and why they were rejected. Do not manufacture alternatives for trivial decisions.
- Convert unresolved product intent or risk acceptance into `OPEN-ARCH-*`; do not decide it on behalf of the human.
- Include no chain-of-thought. Record concise rationale and evidence only.
- Mark the artifact `PROPOSED`, never `APPROVED`.

## Revision rules

- Modify only the approved rework scope plus consistency repairs.
- Map every accepted finding or human comment to its resolution.
- Preserve prior drafts and reviews; never overwrite them.
- Require new human acceptance and independent review for every revision.

## Output

Write `.ai-dlc/architecture/drafts/architecture-vNNN.md`. Return the artifact path and version, ID counts, requirements and quality-scenario coverage, revision changes, and recommendation `READY_FOR_HUMAN_ARCHITECTURE_ACCEPTANCE` or `BLOCKED`.

Stop after returning the proposal. Do not start review or implementation.
