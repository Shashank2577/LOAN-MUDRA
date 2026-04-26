<!--
Sync Impact Report
Version change: N/A → 1.0.0
Modified principles: Initial adoption
Added sections: Core Principles, Delivery Workflow, Governance
Removed sections: None
Templates requiring updates:
- ✅ specs/001-loan-aggregation-mvp/spec.md
- ✅ specs/001-loan-aggregation-mvp/plan.md
Follow-up TODOs:
- TODO(RATIFICATION_CONFIRMED_BY): Confirm ratification approver (founder/legal owner)
-->

# Loan Mudra Constitution

## Metadata

- Project: Loan Mudra Lead Aggregation Platform
- Constitution Version: 1.0.0
- Ratification Date: 2026-04-26
- Last Amended Date: 2026-04-26

## Core Principles

### I. Lead-Capture-First Product Scope

Every feature MUST improve one of these measurable outcomes: qualified lead capture rate,
lead data completeness, or lead-to-contact turnaround readiness. Features that are
"nice-to-have" but do not move these outcomes MUST be deferred to future phases.

**Rationale**: Phase 1 is an MVP focused on lead generation, not end-to-end loan disbursal.

### II. Compliance and Data Minimization by Default

The product MUST only collect personal and financial data that is explicitly required for
MVP use-cases (credit check simulation, loan eligibility intake, loan interest expression,
and backend follow-up). PAN and contact data MUST be treated as sensitive and MUST have
clear consent capture, purpose disclosure, and retention expectations documented.

**Rationale**: Financial lead platforms process regulated data and face legal, trust, and
reputational risks if collection scope expands without controls.

### III. Explainable Eligibility and Transparent Comparison

Eligibility hints, score-driven suggestions, lender comparisons, and EMI outputs MUST be
presented as indicative (not guaranteed approval). Any displayed ranges or recommendations
MUST include the data basis (static table, configured ranges, or simulation assumptions).

**Rationale**: Phase 1 uses static/semi-dynamic data and manual backend underwriting;
transparency prevents user misinterpretation and reduces disputes.

### IV. Human-in-the-Loop Decision Integrity

No automated approval or lender assignment is permitted in MVP. Final lender routing,
customer contact, and profile judgment MUST remain manual and auditable by internal teams.
System automation MAY prioritize leads, but MUST not finalize loan outcomes.

**Rationale**: Business requirements explicitly exclude direct lender APIs and auto approvals.

### V. Spec-Driven Delivery and Traceability

All work MUST follow: `/constitution` → `/specify` → `/plan` before `/tasks` or coding.
Every functional requirement in `spec.md` MUST map to at least one implementation track in
`plan.md` and later to executable tasks. Ambiguities MUST be resolved in spec artifacts, not
inside implementation-only discussions.

**Rationale**: This repository relies on GitHub Spec Kit discipline to keep agents aligned.

### VI. Security, Reliability, and Observability as Launch Gates

No feature is considered MVP-ready unless it includes: authentication/authorization checks,
input validation and abuse prevention, baseline event/audit logging for lead lifecycle, and
error monitoring hooks for user-facing critical flows.

**Rationale**: Lead funnels are high-risk for fraud, data misuse, and silent conversion loss.

## Delivery Workflow Rules

1. `/constitution` updates governance and operating constraints.
2. `/specify` defines user outcomes, testable requirements, and measurable success criteria.
3. `/plan` defines implementation architecture, data model boundaries, risk controls, and
   release sequencing.
4. `/tasks` may only begin after spec and plan pass constitution checks.
5. Any change impacting compliance, data handling, or approval semantics requires spec and
   plan amendment before code changes.

## Governance

### Amendment Process

- Amendments require a written proposal in PR form referencing impacted principles.
- At least one business owner and one technical owner review is required before merge.
- Emergency amendments are allowed only for regulatory, legal, or security incidents and
  MUST be backfilled with full rationale within 48 hours.

### Versioning Policy

- MAJOR: Principle removal/redefinition that changes non-negotiable behavior.
- MINOR: New principle or materially expanded governance section.
- PATCH: Clarifications, wording improvements, and non-semantic refinements.

### Compliance Review Cadence

- Weekly check: lead data collection fields vs approved purpose list.
- Sprint check: spec/plan/task traceability for all active features.
- Release check: security, consent, and audit-log readiness for MVP critical flows.

### Enforcement

Any PR violating a MUST statement in this constitution is non-compliant and cannot be
merged until corrected or the constitution is amended first.
