# Architecture review policy

## Review dimensions

1. Authorization and identity: exact proposal, baseline, approval, and independent invocation agree.
2. Requirements coverage: every Must FR, NFR, and constraint has justified design and validation coverage through local references.
3. Boundary quality: components have cohesive responsibilities, explicit ownership, and controlled coupling.
4. Interfaces and flows: contracts cover authorization, compatibility, timeout, failure, recovery, and observability where applicable.
5. Data architecture: ownership, classification, lifecycle, consistency, retention, migration, backup, and recovery are addressed as applicable.
6. Quality attributes: tactics are plausible, measurable, and do not contradict other drivers.
7. Security and privacy: trust boundaries, least privilege, secrets, abuse paths, auditability, and privacy obligations are covered.
8. Operability and resilience: deployment, configuration, diagnostics, dependency failure, rollback, and disaster recovery are credible.
9. Feasibility and evolution: decisions fit constraints, expose risks, preserve required changeability, and avoid unsupported technology choices.
10. Simplicity and consistency: no contradiction, needless mechanism, premature distribution, or unowned cross-cutting concern exists.

## Severity and gate

- `Critical`: unsafe, unlawful, destructive, fundamentally wrong, or approval integrity compromised; blocking.
- `High`: Must requirement, critical NFR, trust boundary, recovery path, or feasibility missing or contradictory; blocking.
- `Medium`: material risk or ambiguity deferrable only with owner and disposition.
- `Low`: local clarity or maintainability improvement.

Use confidence `HIGH`, `MEDIUM`, or `LOW`. Recommend `PASS_FOR_HUMAN_APPROVAL` only when Critical and High findings are zero, Must FR/NFR/constraint coverage is 100%, all dimensions were evaluated, and deferred Medium findings name an owner and disposition.
