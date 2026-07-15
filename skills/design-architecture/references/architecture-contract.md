# Architecture proposal contract

## Required sections

1. Metadata and approved inputs
2. Context, scope, and key drivers
3. Key decisions and alternatives
4. Components, interfaces, and data ownership
5. Quality, security, and operational approach
6. Risks and open decisions
7. Revision summary for revised versions only

## Metadata and status

Include project ID, artifact ID, version, status `PROPOSED`, author role, authorization approval ID, approved requirements baseline, creation timestamp, and superseded version. Never set an approval field.

## Decision records

Each `DEC-*` states the decision, status, drivers, meaningful alternatives, rationale, consequences, affected components, relevant FR/NFR/CON references, validation method, and unresolved risks. Do not maintain a duplicate `ADR-*` item set.

## Boundary contracts

Each `CMP-*` has one owner and clear responsibilities. Each `INT-*` identifies producer, consumers, inputs and outputs, necessary protocol semantics, authentication and authorization, compatibility expectations, failure behavior, timeout or retry behavior where applicable, and observability.

Reference FR, NFR, and constraints locally from the decisions or components that address them. Do not create a separate traceability matrix. Every Must requirement must still be covered or explicitly blocked with an owner and open decision.

Use status `PROPOSED`, `PROPOSED_NEEDS_INPUT`, or `RETIRED`. Only the orchestrator may record `HUMAN_APPROVED` after explicit baseline approval.
