---
name: design-architecture
description: Draft or revise a traceable software architecture proposal inside a human-authorized AI author-review loop from an approved requirements baseline. Use when an orchestrator supplies active loop authorization, target version, and independent AI architecture-review findings; produce system context, decisions, components, interfaces, data ownership, quality tactics, risks, and traceability without approval or implementation.
---

# Design Architecture

Produce an architecture proposal for independent AI review. Never approve architecture, advance workflow state, invoke the reviewer, or implement code.

## Required references

Read [references/architecture-contract.md](references/architecture-contract.md) completely before drafting. Use [assets/architecture-template.md](assets/architecture-template.md) as the output structure.

## Preconditions

Proceed only when the input packet contains:

- the exact human-approved requirements baseline and its approval record;
- approved project scope, constraints, and applicable repository guidance;
- the target architecture version `vNNN`;
- an active architecture-loop authorization established by a human phase-start approval;
- the prior AI review report and routed findings when revising.

If any precondition is missing, versions disagree, or the loop limit is exceeded, return `BLOCKED` without writing a draft.

## Workflow

1. Read the approved FR, NFR with embedded quality measures, constraints, and accepted findings.
2. Identify architecture drivers and decisions that must be made now; leave unrelated decisions open.
3. Define system context, trust boundaries, components, responsibilities, interfaces, data ownership, and deployment constraints at the minimum useful level.
4. Reference the affected FR, NFR, or constraint directly from each material decision when useful; do not create a separate traceability matrix.
5. Record alternatives, rationale, consequences, risks, validation approach, and unresolved decisions.
6. Check consistency, feasibility, operability, security, privacy, evolvability, and requirement coverage.
7. Write exactly one versioned proposal to the authorized path and return a concise summary.

## Design rules

- Use `DRV-NNN`, `DEC-NNN`, `CMP-NNN`, `INT-NNN`, `DATA-NNN`, `RISK-NNN`, `ADR-NNN`, and `OPEN-ARCH-NNN` identifiers.
- Keep stable IDs for semantically unchanged items; retire rather than reuse IDs.
- Treat approved technology choices as constraints, not preferences. Do not invent vendors, frameworks, databases, or topology.
- Give every component one clear responsibility and every interface an owner, consumers, contract, failure behavior, and security boundary where relevant.
- Identify data classification, ownership, lifecycle, consistency, recovery, and migration concerns where applicable.
- Express quality tactics with the NFR IDs they satisfy and how they will be verified.
- Record meaningful alternatives and why they were rejected. Do not manufacture alternatives for trivial decisions.
- Convert unresolved product intent or risk acceptance into `OPEN-ARCH-*`; do not decide it on behalf of the human.
- Include no chain-of-thought. Record concise rationale and evidence only.
- Mark the artifact `PROPOSED`, never `APPROVED`.

## Revision rules

- Modify only routed AI-review findings, human comments, and consistency repairs.
- Map every routed finding or human comment to its resolution.
- Preserve prior drafts and reviews; never overwrite them.
- Require a new independent AI review for every revision; no new human approval is required inside the active loop.

## Output

Require the orchestrator-supplied `project_id` and `project_root`; reject cross-project baselines or a target outside that root. Write `<project_root>/architecture/drafts/architecture-vNNN.md`. Return the artifact path and version, ID counts, coverage, revision changes, and recommendation `READY_FOR_AI_REVIEW` or `BLOCKED`.

Stop after returning the proposal. Do not start review or implementation.
