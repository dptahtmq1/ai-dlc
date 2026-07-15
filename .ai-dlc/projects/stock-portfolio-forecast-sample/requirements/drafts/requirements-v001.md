# Software Requirements Specification

## 1. Metadata

| Field | Value |
|---|---|
| Project ID | `stock-portfolio-forecast-sample` |
| Artifact ID | `SRS-001` |
| Version | `v001` |
| Status | `PROPOSED` |
| Author role | `requirements-author` |
| Authorization approval | `APR-20260714-002` |
| Approved inputs | `.ai-dlc/projects/stock-portfolio-forecast-sample/project.yaml` (`intake-v001`); `.ai-dlc/projects/stock-portfolio-forecast-sample/approvals.yaml` (`APR-20260714-002`); `docs/system-specification.md` (workflow context only) |
| Supersedes | none |
| Created at | `2026-07-14T15:12:00+09:00` |

## 2. Problem and purpose

An investor needs a reproducible way to understand the outlook and risk of their existing stock holdings and to consider a target portfolio restricted to large-cap stocks. The system will accept existing holdings, produce explicitly uncertain forecasts, evaluate portfolios, and propose rebalancing candidates without guaranteeing returns, presenting personalized financial advice, or placing trades. This document defines the product obligations while leaving unresolved market, data, horizon, eligibility, and portfolio-policy decisions visible for human resolution.

## 3. Goals and success measures

| ID | Goal | Success measure | Source |
|---|---|---|---|
| GOAL-001 | Forecast future stock returns or price ranges with explicit uncertainty. | Every displayed forecast identifies its horizon, as-of time, point or range estimate, uncertainty measure, and data/model version. | Approved project brief, goal 1 |
| GOAL-002 | Analyze existing holdings and propose a target portfolio and rebalancing candidates. | For every valid portfolio request, the output reconciles current weights and shows target weights and proposed changes whose weights each total 100% within the configured rounding tolerance. | Approved project brief, goal 2 |
| GOAL-003 | Restrict proposed investments to large-cap stocks. | 100% of securities assigned a positive proposed target weight satisfy the human-approved large-cap eligibility policy as of the evaluation time. | Approved project brief, goal 3 and constraint 1 |
| GOAL-004 | Make forecasts and portfolio proposals reproducible and auditable. | Every result can be traced to input holdings, market-data snapshot, model version, backtest version, policy version, and generation time. | Approved project brief, goal 4 |

## 4. Stakeholders and actors

| ID | Actor | Need or responsibility |
|---|---|---|
| ACTOR-001 | Portfolio user | Supplies holdings, reviews forecasts and proposed changes, and retains responsibility for investment decisions. |
| ACTOR-002 | Product owner | Resolves the investment universe, horizon, large-cap definition, portfolio constraints, disclosures, and success thresholds. |
| ACTOR-003 | Data/model operator | Makes approved data and model versions available and investigates validation or freshness failures. |
| ACTOR-004 | Auditor/reviewer | Verifies provenance, backtest controls, eligibility, calculations, and non-advisory behavior. |

## 5. Scope

### Included

- Capture a user's existing stock holdings and quantities.
- Value and analyze the current portfolio using an identified market-data snapshot.
- Generate horizon-specific forecasts with uncertainty for eligible securities.
- Propose target weights anchored in the existing holdings under an approved eligibility and portfolio policy.
- Show rebalancing candidates, portfolio concentration, risk, and forecast uncertainty.
- Backtest forecasts and proposal logic using time-ordered validation and leakage controls.
- Preserve result provenance and versions for audit and reproduction.

### Excluded

- Automatic submission or execution of brokerage orders.
- Guarantees of returns or presentation of output as personalized financial advice.
- Autonomous approval of requirements, findings, workflow phases, or baselines.
- Tax, legal, or suitability advice unless introduced by a future approved scope change.

## 6. Constraints and dependencies

| ID | Type | Statement | Source |
|---|---|---|---|
| CON-001 | Eligibility | Only securities satisfying a human-approved, measurable large-cap policy may receive positive proposed target weight. | Approved project brief, constraint 1 |
| CON-002 | Safety | The system must not guarantee returns or characterize forecasts or proposals as personalized financial advice. | Approved project brief, constraint 2 |
| CON-003 | Trading boundary | The system must not place or submit trades. | Approved project brief, constraint 3 |
| CON-004 | Validation | Backtests must use time-ordered evaluation and controls against use of information unavailable at the simulated decision time. | Approved initial scope |
| CON-005 | External dependency | Forecasts, valuations, and eligibility depend on approved market and fundamental data whose provider and permitted usage remain unresolved. | `OPEN-003` |
| CON-006 | Policy dependency | Portfolio construction depends on human-approved risk, turnover, diversification, tax, and position policies. | `OPEN-005` |

## 7. Functional requirements

### FR-001: Capture existing holdings

- Priority: Must
- Statement: The system must accept a portfolio containing security identifiers and quantities and preserve the submitted values as the input snapshot for that analysis.
- Rationale: Existing holdings are the required anchor for portfolio analysis and proposal generation.
- Source: GOAL-002; approved initial scope
- Actors: ACTOR-001
- Preconditions: The user starts a new portfolio analysis.
- Main behavior: The system validates each identifier and quantity, records the accepted holdings and as-of time, and reports the accepted input snapshot.
- Failure behavior: The system rejects missing, ambiguous, duplicate, unsupported, non-finite, or non-positive quantities with item-specific errors and does not generate a proposal from invalid input.
- Acceptance criteria: AC-001
- Status: `PROPOSED`

### FR-002: Calculate current portfolio composition

- Priority: Must
- Statement: The system must calculate each accepted holding's market value and current portfolio weight from a single identified valuation snapshot.
- Rationale: Current weights are necessary to compare the user's portfolio with a target portfolio.
- Source: GOAL-002; approved initial scope
- Actors: ACTOR-001
- Preconditions: Valid holdings and usable valuation data exist.
- Main behavior: The system shows per-security quantity, valuation price, market value, weight, valuation currency, and total portfolio value.
- Failure behavior: The system identifies securities with missing or stale valuation data and does not present a complete current-weight calculation as valid.
- Acceptance criteria: AC-002
- Status: `PROPOSED_NEEDS_INPUT`

### FR-003: Determine large-cap eligibility

- Priority: Must
- Statement: The system must evaluate every candidate security against the human-approved large-cap policy using data available as of the evaluation time.
- Rationale: Large-cap-only eligibility is an explicit project constraint.
- Source: GOAL-003; CON-001
- Actors: ACTOR-001, ACTOR-003
- Preconditions: A large-cap policy, investment universe, and required eligibility data are available.
- Main behavior: The system records eligible or ineligible status, the policy version, evaluation time, and evidence used for each candidate.
- Failure behavior: The system treats a security as not eligible for positive proposed weight when eligibility cannot be established and explains the missing evidence.
- Acceptance criteria: AC-003
- Status: `PROPOSED_NEEDS_INPUT`

### FR-004: Generate security forecasts

- Priority: Must
- Statement: The system must generate a forecast for each eligible security included in an analysis for the configured forecast target and horizon.
- Rationale: Security forecasts are the evidence base for the proposed portfolio.
- Source: GOAL-001
- Actors: ACTOR-001, ACTOR-003
- Preconditions: Approved data, a valid model version, forecast target, and horizon are available.
- Main behavior: The system reports the security, as-of time, horizon, target definition, point or range estimate, and direction where applicable.
- Failure behavior: The system marks unavailable forecasts and their reason and excludes them from proposal calculations that require a valid forecast.
- Acceptance criteria: AC-004
- Status: `PROPOSED_NEEDS_INPUT`

### FR-005: Report forecast uncertainty

- Priority: Must
- Statement: The system must display a defined uncertainty measure with every forecast and identify the interpretation and coverage or confidence level of that measure.
- Rationale: Users must be able to distinguish uncertain estimates from guaranteed outcomes.
- Source: GOAL-001; CON-002
- Actors: ACTOR-001
- Preconditions: A forecast is displayed.
- Main behavior: The system presents uncertainty adjacent to the estimate and retains it in exported or audited result data.
- Failure behavior: The system does not display a forecast as complete when its required uncertainty measure is unavailable.
- Acceptance criteria: AC-005
- Status: `PROPOSED_NEEDS_INPUT`

### FR-006: Propose a target portfolio

- Priority: Must
- Statement: The system must calculate proposed target weights from the accepted existing-holdings snapshot, eligible forecast set, and approved portfolio policy.
- Rationale: A target allocation is the primary portfolio decision-support output.
- Source: GOAL-002; GOAL-003
- Actors: ACTOR-001
- Preconditions: Current weights, eligibility results, forecasts, and a complete portfolio policy are available.
- Main behavior: The system reports target weights totaling 100% within the configured rounding tolerance and identifies whether each proposed security was already held.
- Failure behavior: The system does not issue a target portfolio when inputs or constraints are infeasible and reports the conflicting or missing constraints.
- Acceptance criteria: AC-006
- Status: `PROPOSED_NEEDS_INPUT`

### FR-007: Enforce eligibility in proposals

- Priority: Must
- Statement: The system must assign zero proposed target weight to every security that is ineligible or whose large-cap eligibility is unresolved.
- Rationale: This prevents portfolio proposals from violating the large-cap-only constraint.
- Source: GOAL-003; CON-001
- Actors: ACTOR-001
- Preconditions: A proposal is being calculated.
- Main behavior: The system filters or zero-weights ineligible and unresolved securities and reports the reason.
- Failure behavior: If an existing holding is ineligible, the system flags the conflict and applies the approved treatment rather than silently retaining a positive target weight.
- Acceptance criteria: AC-007
- Status: `PROPOSED_NEEDS_INPUT`

### FR-008: Explain rebalancing candidates

- Priority: Must
- Statement: The system must show the difference between current and target weight for each relevant security and label the corresponding increase, decrease, retain, or exit candidate action.
- Rationale: Users need observable changes rather than only a target allocation.
- Source: GOAL-002; approved initial scope
- Actors: ACTOR-001
- Preconditions: A valid target portfolio exists.
- Main behavior: The system shows current weight, target weight, percentage-point difference, candidate action, and relevant forecast and constraint evidence.
- Failure behavior: The system does not label any candidate as an executable order.
- Acceptance criteria: AC-008
- Status: `PROPOSED`

### FR-009: Report portfolio risk and concentration

- Priority: Must
- Statement: The system must calculate and display the approved portfolio risk and concentration measures for both current and proposed portfolios over the defined measurement basis.
- Rationale: A proposal cannot be evaluated only by forecast return.
- Source: GOAL-002; approved initial scope
- Actors: ACTOR-001
- Preconditions: A current portfolio is valid; a proposed portfolio is required only for proposed metrics.
- Main behavior: The system names each metric, its time basis, current value, proposed value, and material input assumptions.
- Failure behavior: The system marks a metric unavailable rather than substituting an unlabeled or partial calculation.
- Acceptance criteria: AC-009
- Status: `PROPOSED_NEEDS_INPUT`

### FR-010: Perform time-ordered backtesting

- Priority: Must
- Statement: The system must evaluate forecast and portfolio performance using time-ordered training, selection, and test periods in which each simulated decision uses only information available at that time.
- Rationale: Time-order violations and leakage would make performance evidence misleading.
- Source: GOAL-004; CON-004
- Actors: ACTOR-003, ACTOR-004
- Preconditions: Historical data and a candidate model or proposal policy exist.
- Main behavior: The system records period boundaries, as-of cutoffs, prediction targets, evaluated observations, and performance metrics.
- Failure behavior: The system fails the backtest and identifies the violation when a feature, label, eligibility decision, or selection step uses future information.
- Acceptance criteria: AC-010
- Status: `PROPOSED`

### FR-011: Compare against reference performance

- Priority: Should
- Statement: The system should compare backtest results with human-approved reference portfolios or forecasting baselines over identical evaluation periods.
- Source: ASM-002
- Actors: ACTOR-003, ACTOR-004
- Main behavior: The system reports matched-period results and differences for each approved metric.
- Failure behavior: The system identifies unavailable reference data and does not claim comparative outperformance.
- Status: `PROPOSED_NEEDS_INPUT`

### FR-012: Preserve result provenance

- Priority: Must
- Statement: The system must associate every forecast, backtest, and portfolio proposal with the holdings snapshot, market-data snapshot, eligibility policy, portfolio policy, model version, backtest version, and generation time used to produce it.
- Rationale: Provenance is necessary for reproduction, audit, and investigation.
- Source: GOAL-004
- Actors: ACTOR-003, ACTOR-004
- Preconditions: A result is generated.
- Main behavior: The system exposes stable references to all required inputs and versions in the result record.
- Failure behavior: The system does not mark a result complete when a required provenance reference is missing.
- Acceptance criteria: AC-011
- Status: `PROPOSED`

### FR-013: Reproduce a prior result

- Priority: Should
- Statement: The system should allow an authorized operator to request regeneration of a prior result from its recorded versions and report whether the regenerated values match within the approved numeric tolerance.
- Source: GOAL-004
- Actors: ACTOR-003, ACTOR-004
- Failure behavior: The system reports unavailable or changed dependencies and does not claim successful reproduction when comparison cannot be completed.
- Status: `PROPOSED_NEEDS_INPUT`

### FR-014: Present non-advisory disclosure

- Priority: Must
- Statement: The system must present a clear disclosure with every forecast and portfolio proposal that results are uncertain decision-support information, are not a guarantee, and are not personalized financial advice.
- Rationale: This enforces an explicit safety boundary and reduces misleading interpretation.
- Source: CON-002
- Actors: ACTOR-001
- Preconditions: A forecast or proposal is displayed or exported.
- Main behavior: The disclosure remains visible or included with the result.
- Failure behavior: The system does not release or export a result without the disclosure.
- Acceptance criteria: AC-012
- Status: `PROPOSED`

### FR-015: Prevent trade execution

- Priority: Must
- Statement: The system must limit portfolio actions to informational candidates and must not submit, route, schedule, or execute a brokerage order.
- Rationale: Automatic order execution is explicitly out of scope.
- Source: CON-003
- Actors: ACTOR-001
- Preconditions: A proposal or candidate action exists.
- Main behavior: The system provides reviewable information without an execution action or execution-side effect.
- Failure behavior: Any attempted order-execution request is rejected and recorded as outside system scope.
- Acceptance criteria: AC-013
- Status: `PROPOSED`

## 8. Non-functional requirements

### NFR-001: Forecast and proposal trace completeness

- Quality attribute: Auditability
- Operating context: Every completed forecast, backtest, and proposal
- Target: 100% of completed results contain all provenance references required by FR-012.
- Measurement method: Automated completeness check over result records plus sampled reference resolution.
- Priority: Must
- Rationale: Missing provenance prevents reliable audit and reproduction.
- Source: GOAL-004
- Quality scenarios: QA-001
- Status: `PROPOSED`

### NFR-002: Calculation integrity

- Quality attribute: Accuracy
- Operating context: Current and proposed portfolio calculations
- Target: Displayed component weights total 100% within the human-approved rounding tolerance, and displayed differences reconcile to displayed current and target weights within that tolerance.
- Measurement method: Deterministic calculation tests across zero, single-holding, multi-holding, and rounding-boundary cases.
- Priority: Must
- Rationale: Internally inconsistent allocations are unusable and misleading.
- Source: GOAL-002
- Quality scenarios: QA-002
- Status: `PROPOSED_NEEDS_INPUT`

### NFR-003: Eligibility enforcement coverage

- Quality attribute: Policy compliance
- Operating context: Every generated target portfolio
- Target: 0 positive-weight proposed securities lack a passing eligibility determination under the recorded policy version.
- Measurement method: Automated invariant check over every proposal and eligibility record.
- Priority: Must
- Rationale: The large-cap constraint permits no exceptions unless the approved scope changes.
- Source: GOAL-003; CON-001
- Quality scenarios: QA-003
- Status: `PROPOSED`

### NFR-004: Temporal leakage prevention

- Quality attribute: Validity
- Operating context: Every completed backtest
- Target: 0 features, labels, universe selections, eligibility decisions, model-selection decisions, or portfolio decisions use information timestamped after the simulated decision cutoff.
- Measurement method: Automated timestamp-boundary assertions and an auditable sample review of data lineage and period definitions.
- Priority: Must
- Rationale: Leakage invalidates reported forecast and portfolio performance.
- Source: GOAL-004; CON-004
- Quality scenarios: QA-004
- Status: `PROPOSED`

### NFR-005: Result reproducibility

- Quality attribute: Reproducibility
- Operating context: Regeneration of a retained result while all recorded dependencies remain available
- Target: 100% of tested regenerated outputs match the retained output within the human-approved numeric tolerance.
- Measurement method: Repeat selected result generation from recorded references and compare all numeric and categorical outputs.
- Priority: Should
- Source: GOAL-004
- Quality scenarios: QA-005
- Status: `PROPOSED_NEEDS_INPUT`

### NFR-006: Data freshness visibility

- Quality attribute: Timeliness and transparency
- Operating context: Every valuation, forecast, eligibility result, and proposal shown to a user
- Target: 100% display the relevant data as-of time and visibly identify data exceeding the human-approved freshness limit.
- Measurement method: UI/export inspection and boundary tests around the configured freshness limit.
- Priority: Must
- Rationale: Users must know whether decision-support output relies on stale information.
- Source: GOAL-001; GOAL-002; CON-005
- Quality scenarios: QA-006
- Status: `PROPOSED_NEEDS_INPUT`

## 9. Quality-attribute scenarios

### QA-001: Missing provenance blocks completion

- Source: Data/model operator
- Stimulus: A result is generated without one required input or version reference.
- Environment: Normal result generation
- Artifact: Forecast, backtest, or portfolio-proposal record
- Response: The system marks the result incomplete and identifies the missing reference.
- Response measure: 0 results marked complete with a missing FR-012 provenance field.

### QA-002: Weight rounding remains consistent

- Source: Portfolio user
- Stimulus: A portfolio containing values near rounding boundaries is analyzed.
- Environment: Normal current and target portfolio calculation
- Artifact: Portfolio calculation and presentation
- Response: The system applies the approved rounding policy consistently and reconciles totals and differences.
- Response measure: Totals and differences remain within the approved rounding tolerance in 100% of boundary tests.

### QA-003: Unresolved eligibility is excluded

- Source: Market-data dependency
- Stimulus: Eligibility evidence is missing for a candidate security.
- Environment: Proposal generation
- Artifact: Eligibility evaluator and target portfolio
- Response: The system assigns no positive target weight and reports the unresolved evidence.
- Response measure: 0 unresolved candidates receive positive target weight.

### QA-004: Future information is detected

- Source: Data/model operator
- Stimulus: A backtest input or selection decision is timestamped after its simulated decision cutoff.
- Environment: Backtest validation
- Artifact: Backtest and data lineage
- Response: The system fails the affected run and reports the offending item and timestamps.
- Response measure: 100% of injected future-information violations are detected; 0 affected runs are reported as valid.

### QA-005: Retained result is regenerated

- Source: Auditor/reviewer
- Stimulus: The reviewer requests reproduction of a retained result whose dependencies remain available.
- Environment: Audit operation
- Artifact: Result and recorded provenance
- Response: The system regenerates the result and reports field-level comparison against the retained output.
- Response measure: All compared fields match within the approved numeric tolerance or the reproduction is reported as failed.

### QA-006: Stale data is visible

- Source: Passage of time or delayed data provider
- Stimulus: A required data point exceeds the approved freshness limit.
- Environment: User views or exports a result
- Artifact: Result presentation
- Response: The system labels the affected data and result as stale and does not imply current validity.
- Response measure: 100% of test data beyond the freshness boundary is labeled stale.

## 10. Acceptance criteria

### AC-001

- Related requirements: FR-001
- Given: A portfolio submission containing valid identifiers and positive finite quantities
- When: The user submits it for analysis
- Then: The system records and returns the same identifiers and quantities with an input-snapshot identifier and as-of time; an invalid item instead produces an item-specific error and no proposal.
- Verification method: Input validation and persistence tests

### AC-002

- Related requirements: FR-002
- Given: Accepted holdings and a complete valuation snapshot
- When: Current composition is calculated
- Then: Each market value equals quantity multiplied by identified valuation price, each weight equals its share of total value, and all weights reconcile within the approved rounding tolerance.
- Verification method: Deterministic calculation tests with independently computed expected values

### AC-003

- Related requirements: FR-003
- Given: An approved large-cap policy and candidates with complete, cutoff-valid evidence
- When: Eligibility is evaluated
- Then: Each candidate receives an eligible or ineligible result with policy version, as-of time, and evidence; missing evidence produces an unresolved result that is not treated as eligible.
- Verification method: Policy boundary and missing-data tests

### AC-004

- Related requirements: FR-004
- Given: Approved data and a valid model configured for the approved target and horizon
- When: Forecast generation completes
- Then: Every returned forecast identifies the security, as-of time, target, horizon, estimate, and model/data references; failed securities carry a reason and are excluded where a valid forecast is required.
- Verification method: Forecast contract and partial-failure tests

### AC-005

- Related requirements: FR-005
- Given: A forecast is available for display or export
- When: The result is rendered
- Then: A labeled uncertainty measure, its interpretation, and coverage or confidence level accompany the estimate; absence of uncertainty prevents complete status.
- Verification method: Result schema and presentation tests

### AC-006

- Related requirements: FR-006
- Given: Valid current weights, eligibility results, forecasts, and a feasible approved portfolio policy
- When: A target portfolio is requested
- Then: The result identifies existing versus introduced securities, obeys all recorded constraints, and target weights total 100% within the approved rounding tolerance; infeasibility produces no target and identifies conflicts.
- Verification method: Feasible, infeasible, and rounding-boundary scenario tests

### AC-007

- Related requirements: FR-007
- Given: A proposal candidate set containing eligible, ineligible, and unresolved securities
- When: Target weights are calculated
- Then: Only eligible securities may receive positive weight, and all excluded securities have a recorded reason; an ineligible existing holding follows the approved treatment.
- Verification method: Eligibility invariant and existing-holding conflict tests

### AC-008

- Related requirements: FR-008
- Given: Valid current and target portfolios
- When: Rebalancing candidates are displayed
- Then: Every relevant security shows current weight, target weight, their reconciled difference, a candidate action, and supporting evidence without an order-execution control.
- Verification method: Calculation and interaction-boundary tests

### AC-009

- Related requirements: FR-009
- Given: A valid current portfolio and, where applicable, a valid proposal
- When: Risk analysis is requested
- Then: Each approved risk and concentration metric is labeled with its time basis, assumptions, current value, and proposed value or an explicit unavailable reason.
- Verification method: Metric contract, reference-data, and missing-data tests

### AC-010

- Related requirements: FR-010
- Given: Historical data containing timestamped observations and a configured time-ordered evaluation
- When: A backtest runs
- Then: The result records all period boundaries and cutoffs, and any injected future-dated input or decision causes failure with evidence rather than a valid performance report.
- Verification method: Temporal boundary, leakage injection, and audit-record tests

### AC-011

- Related requirements: FR-012, NFR-001
- Given: A forecast, backtest, or proposal completes
- When: Its result record is inspected
- Then: All FR-012 provenance references are present and resolvable; removing any required reference prevents complete status.
- Verification method: Automated provenance completeness and reference-resolution tests

### AC-012

- Related requirements: FR-014
- Given: A forecast or proposal is displayed or exported
- When: The user receives the result
- Then: The result includes the approved uncertainty, no-guarantee, and non-advice disclosure; suppressing it prevents release or export.
- Verification method: Presentation and export gating tests

### AC-013

- Related requirements: FR-015
- Given: A proposed increase, decrease, retain, or exit candidate exists
- When: The user interacts with the result or requests execution
- Then: No brokerage order is submitted, routed, scheduled, or executed, and an execution request is rejected as out of scope.
- Verification method: Interface inspection, integration-boundary test, and side-effect audit

### AC-014

- Related requirements: NFR-002
- Given: Portfolios at rounding boundaries and the approved tolerance
- When: Values, weights, and differences are calculated and displayed
- Then: All totals and differences reconcile within that tolerance.
- Verification method: Automated numeric boundary tests

### AC-015

- Related requirements: NFR-003
- Given: Any generated target portfolio
- When: Positive-weight securities are joined to their eligibility results
- Then: Every such security has a passing result under the proposal's recorded policy version.
- Verification method: Automated invariant query over proposal fixtures and generated results

### AC-016

- Related requirements: NFR-004
- Given: A backtest fixture containing one future-information violation of each covered category
- When: Validation runs
- Then: Every violation is detected and no affected run is marked valid.
- Verification method: Timestamp mutation and leakage-category test suite

### AC-017

- Related requirements: NFR-006
- Given: Data immediately before, at, and after the approved freshness boundary
- When: A result is displayed or exported
- Then: Its as-of time is always shown and every value beyond the boundary is visibly labeled stale.
- Verification method: Freshness boundary and presentation tests

## 11. Assumptions

| ID | Assumption | Impact if false | Confirmation owner |
|---|---|---|---|
| ASM-001 | The initial users can provide security identifiers and quantities without direct brokerage-account integration. | Scope would need secure account connectivity, identity, consent, and additional failure requirements. | ACTOR-002 |
| ASM-002 | At least one meaningful reference portfolio or naive forecast baseline will be available for comparative backtesting. | FR-011 would need removal or deferral and performance interpretation would be weaker. | ACTOR-002 |
| ASM-003 | Historical point-in-time data sufficient to reconstruct eligibility and features at each backtest cutoff can be obtained. | Large-cap and leakage-valid historical evaluation may be infeasible. | ACTOR-003 |
| ASM-004 | The system is decision support for one portfolio user per analysis and does not initially require household or multi-account aggregation. | Holdings, authorization, and aggregation requirements would need expansion. | ACTOR-002 |

## 12. Open questions

| ID | Question | Why it matters | Required decision owner | Blocking? |
|---|---|---|---|---|
| OPEN-001 | Which market, exchanges, currencies, and security types define the investment universe? | Determines identifier validation, calendars, valuation, corporate actions, and eligible candidates. | ACTOR-002 | Yes, for FR-002 through FR-010 validation |
| OPEN-002 | What forecast target and horizon are required, such as next-day return, five-day return, or price range? | Defines labels, uncertainty interpretation, backtest periods, and user meaning. | ACTOR-002 | Yes, for FR-004 and FR-005 validation |
| OPEN-003 | Which market and fundamental data providers and usage rights are approved, and how are corporate actions handled? | Determines availability, legality, point-in-time validity, and reproducibility. | ACTOR-002, ACTOR-003 | Yes, for data-dependent requirements |
| OPEN-004 | What measurable large-cap rule applies: capitalization threshold, percentile, named index membership, or another rule; and how often is it evaluated? | The core eligibility constraint cannot be tested without an exact rule and cutoff semantics. | ACTOR-002 | Yes, for FR-003, FR-007, and NFR-003 |
| OPEN-005 | What risk tolerance, turnover, diversification, tax, liquidity, cash, minimum/maximum position, and transaction-cost constraints govern proposals? | Determines feasibility and whether a target portfolio is suitable under the intended policy. | ACTOR-002 | Yes, for FR-006 and FR-009 |
| OPEN-006 | May proposals introduce eligible large-cap securities not currently held, or only reweight and exit existing holdings? | Changes the candidate universe and interpretation of being anchored in existing holdings. | ACTOR-002 | Yes, for FR-006 |
| OPEN-007 | How must an existing holding that fails or lacks large-cap eligibility be treated in the target portfolio? | FR-007 requires an explicit exit, zero-weight, grandfathering, or exception policy; grandfathering would conflict with the current strict constraint. | ACTOR-002 | Yes, for FR-007 |
| OPEN-008 | Which forecast, risk, concentration, and backtest metrics and minimum acceptable performance thresholds are required? | Exact calculations and success evaluation cannot be validated without named metrics and thresholds. | ACTOR-002 | Yes, for FR-009 through FR-011 |
| OPEN-009 | What numeric rounding and reproduction tolerances apply? | Needed for deterministic acceptance of weight totals, differences, and regenerated results. | ACTOR-002 | Yes, for NFR-002 and NFR-005 |
| OPEN-010 | What data freshness limits apply during market hours and outside them? | Needed to distinguish current from stale valuations and forecasts. | ACTOR-002, ACTOR-003 | Yes, for NFR-006 |
| OPEN-011 | What uncertainty method, coverage level, and calibration acceptance threshold must be used? | Required to make uncertainty measurable and meaningful rather than decorative. | ACTOR-002 | Yes, for FR-005 validation |
| OPEN-012 | What retention period and authorization rules apply to holdings snapshots and generated results? | Holdings may be sensitive financial information and audit/reproduction needs must be balanced with retention. | ACTOR-002 | Yes, before operational use |
| OPEN-013 | What exact disclosure wording and jurisdictions apply? | Determines whether the non-advice boundary is communicated appropriately for intended users. | ACTOR-002 | Yes, before operational use |

## 13. Traceability matrix

| Goal | Requirement | Quality scenario | Acceptance criteria |
|---|---|---|---|
| GOAL-001 | FR-004 | - | AC-004 |
| GOAL-001 | FR-005 | - | AC-005 |
| GOAL-001 | FR-014 | - | AC-012 |
| GOAL-001 | NFR-006 | QA-006 | AC-017 |
| GOAL-002 | FR-001 | - | AC-001 |
| GOAL-002 | FR-002 | - | AC-002 |
| GOAL-002 | FR-006 | - | AC-006 |
| GOAL-002 | FR-008 | - | AC-008 |
| GOAL-002 | FR-009 | - | AC-009 |
| GOAL-002 | NFR-002 | QA-002 | AC-014 |
| GOAL-003 | FR-003 | - | AC-003 |
| GOAL-003 | FR-006 | - | AC-006 |
| GOAL-003 | FR-007 | - | AC-007 |
| GOAL-003 | NFR-003 | QA-003 | AC-015 |
| GOAL-004 | FR-010 | - | AC-010 |
| GOAL-004 | FR-012 | QA-001 | AC-011 |
| GOAL-004 | NFR-001 | QA-001 | AC-011 |
| GOAL-004 | NFR-004 | QA-004 | AC-016 |
| CON-002 | FR-014 | - | AC-012 |
| CON-003 | FR-015 | - | AC-013 |

No Must requirement is orphaned: each traces to an approved goal or explicit approved constraint and to at least one acceptance criterion. Should requirements FR-011, FR-013, and NFR-005 remain intentionally outside the mandatory acceptance-coverage gate pending human decisions.

## 14. Revision summary

| Source finding/comment | Resolution | Affected IDs | Status |
|---|---|---|---|
| Initial authorized draft `APR-20260714-002` | Created the first requirements proposal from the approved intake; no prior IDs existed to retain or retire. | GOAL-001 through GOAL-004; ACTOR-001 through ACTOR-004; CON-001 through CON-006; FR-001 through FR-015; NFR-001 through NFR-006; QA-001 through QA-006; AC-001 through AC-017; ASM-001 through ASM-004; OPEN-001 through OPEN-013 | Added |

Changed IDs: none. Retired IDs: none. Unchanged IDs: none. This artifact remains `PROPOSED` and requires human draft acceptance and an independent review before any requirements baseline decision.
