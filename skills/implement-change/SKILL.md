---
name: implement-change
description: Implement exactly one human-approved AI-DLC work packet against approved requirements, architecture, and implementation-plan baselines. Use when an orchestrator supplies explicit authorization, allowed paths, acceptance criteria, verification commands, and rollback boundaries for a named work-packet execution; produce scoped code and an evidence manifest without reviewing or approving the change.
---

# Implement Change

Implement one authorized work packet. Never approve the result, merge, push, deploy, expand scope, or invoke review or verification.

## Required reference

Read [references/execution-contract.md](references/execution-contract.md).

## Preconditions

Require exact baseline versions, plan version, `WP-NNN`, execution approval ID, allowed and prohibited paths, current repository status, acceptance criteria, and verification commands. Return `BLOCKED` without changing files when any item is missing, stale, contradictory, or unsafe.

## Workflow

1. Inspect repository guidance, status, relevant code, and packet traces.
2. Preserve user changes and stop on overlap that cannot be safely isolated.
3. Make the smallest change within allowed paths; do not resolve unrelated defects.
4. Add or update tests required by the packet.
5. Run authorized checks in proportion to risk. Never hide failures or weaken tests to pass.
6. Write `.ai-dlc/implementation/executions/WP-NNN-vNNN.md` containing changed paths, baseline traces, commands, results, deviations, remaining risks, and rollback notes.

If new scope, architecture change, destructive migration, secret, policy conflict, or unavailable dependency appears, stop and return `BLOCKED` with the required human decision. Return `READY_FOR_INDEPENDENT_CODE_REVIEW` only when the scoped implementation and evidence manifest are complete.
