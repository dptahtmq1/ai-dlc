# Implementation plan review policy

Review authorization and identity, baseline coverage, packet atomicity, dependency order, scope boundaries, acceptance evidence, verification coverage, migration and compatibility safety, rollout and rollback, operability, feasibility, and risk ownership.

- `Critical`: unsafe/destructive plan or compromised approval integrity; blocking.
- `High`: missing Must coverage, unsafe ordering, unbounded packet, absent rollback for material risk, or unverifiable completion; blocking.
- `Medium`: material gap deferrable only with owner and disposition.
- `Low`: local clarity or maintainability improvement.

Recommend `PASS_FOR_HUMAN_APPROVAL` only when Critical and High findings are zero, all required baseline items trace to implementation and verification packets, every packet can be authorized independently, all review dimensions are complete, and deferred Medium findings have owners.
