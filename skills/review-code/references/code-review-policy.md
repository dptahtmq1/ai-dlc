# Code review policy

Evaluate authorization and diff identity, packet scope, baseline traceability, functional correctness, negative and failure paths, security and privacy, data and concurrency behavior, compatibility and migration, maintainability, tests, observability, rollback, and repository policy.

Use Critical for unsafe or approval-integrity failures, High for incorrect Must behavior or major security/data/recovery gaps, Medium for material but containable risk, and Low for local quality issues. Critical and High block verification. Recommend `PASS_FOR_VERIFICATION` only with no blocking findings and complete packet trace coverage.
