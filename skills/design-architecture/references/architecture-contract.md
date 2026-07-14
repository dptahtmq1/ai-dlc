# Architecture proposal contract

## Required sections

1. Metadata and approved inputs
2. Scope and architecture drivers
3. System context and trust boundaries
4. Decisions and alternatives
5. Components and responsibilities
6. Interfaces and critical flows
7. Data ownership and lifecycle
8. Deployment and operational model
9. Quality-attribute tactics and validation
10. Security, privacy, and compliance
11. Risks, assumptions, and open decisions
12. Traceability matrix
13. Revision summary

## Metadata and status

Include project ID, artifact ID, version, status `PROPOSED`, author role, authorization approval ID, approved requirements baseline, creation timestamp, and superseded version. Never set an approval field.

## Decision records

Each `DEC-*` or `ADR-*` states the decision, status, drivers, alternatives, rationale, consequences, affected components, requirement and quality-scenario traces, validation method, and unresolved risks.

## Boundary contracts

Each `CMP-*` has one owner and clear responsibilities. Each `INT-*` identifies producer, consumers, inputs and outputs, necessary protocol semantics, authentication and authorization, compatibility expectations, failure behavior, timeout or retry behavior where applicable, and observability.

## Traceability minimum

Support `FR/NFR/QA -> DRV/DEC -> CMP/INT -> validation`. Every Must requirement and quality scenario must be covered or explicitly blocked with an owner and open decision.

Use status `PROPOSED`, `PROPOSED_NEEDS_INPUT`, or `RETIRED`. Only the orchestrator may record `HUMAN_APPROVED` after explicit baseline approval.
