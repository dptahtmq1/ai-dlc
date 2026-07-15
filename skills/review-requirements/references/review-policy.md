# Requirements review policy

## Review dimensions

### 1. Authorization and identity

- Review authorization names the exact draft version.
- Draft metadata, path, version, and approved inputs agree.
- Draft author and reviewer invocations are distinct.

### 2. Completeness

- Product purpose, users, scope, exclusions, dependencies, assumptions, and open questions are present at the minimum useful level.
- Main flows, error flows, authorization denials, external failures, data lifecycle, and recovery are covered where relevant.
- Every Must behavior has concise inline verification conditions.

### 3. Correctness and source fidelity

- Requirements do not contradict approved inputs.
- Inferences are marked as assumptions.
- No unsupported stakeholder statement or policy is invented.

### 4. Atomicity and clarity

- One requirement expresses one independently testable obligation.
- Terms are defined and used consistently.
- Pronouns, vague qualifiers, optionality, and conditional behavior are unambiguous.

### 5. Consistency

- Requirements do not conflict with each other, exclusions, constraints, or inline verification conditions.
- Duplicate requirements are consolidated or deliberately distinguished.
- Priority and status are consistent with blocking open questions.

### 6. Testability

- Expected behavior is observable.
- FR verification text identifies the observable result and relevant failure condition.
- NFR targets specify context, threshold, and measurement method.

### 7. Concision and item discipline

- FR, NFR, and constraints each contain 5 to 7 items, or a concrete exception rationale explains why consolidation would lose essential meaning.
- Quality measures are embedded in NFRs; no separate QA or acceptance-criterion item sets are introduced.
- Repeated prose, empty sections, and duplicate matrices are removed.
- Fewer than 5 genuine items are allowed only with an explanation; reviewers never request filler items.

### 8. Scope and solution neutrality

- Requirements remain within approved scope.
- Frameworks, vendors, databases, topology, and implementation techniques appear only as approved constraints.
- Design decisions are identified as future decision points rather than silently fixed.

### 9. Risk coverage

As applicable, inspect authentication, authorization, privacy, secrets, auditability, data retention, destructive actions, concurrency, failure recovery, availability, performance, accessibility, observability, migration, compatibility, and external dependency behavior. Evaluate this internally; do not add sections or findings for irrelevant dimensions.

## Severity

| Severity | Criterion | Blocking |
|---|---|---|
| Critical | Baseline could authorize unsafe, unlawful, destructive, or fundamentally wrong behavior; or artifact identity/approval integrity is compromised | yes |
| High | Must behavior is missing, contradictory, untestable, outside scope, or likely to cause major redesign or failure | yes |
| Medium | Material quality gap with a feasible workaround or a human decision that can be explicitly deferred | human decides |
| Low | Local clarity or maintainability improvement with low delivery risk | no by default |

## Confidence

- `HIGH`: directly supported by artifact evidence and a clear rule.
- `MEDIUM`: strong inference but some context may be missing.
- `LOW`: plausible concern needing a human or domain expert to validate.

Low confidence does not lower severity. It changes the recommended next evaluator.

## Gate calculation

Recommend `PASS_FOR_HUMAN_APPROVAL` only when:

- Critical findings = 0;
- High findings = 0;
- Must FR inline-verification coverage = 100%;
- measurable-or-explicitly-blocked NFR coverage = 100%;
- constraint source coverage = 100%;
- item-count policy compliance or justified exceptions = 100%;
- all review dimensions were evaluated;
- any deferred Medium finding names an owner and proposed disposition.

The human may still reject a passing review. The reviewer may never accept risk for the human.
