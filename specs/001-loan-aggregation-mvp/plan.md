# Implementation Plan: Loan Aggregation MVP Platform

**Branch**: `001-loan-aggregation-mvp` | **Date**: 2026-04-26 | **Spec**: `specs/001-loan-aggregation-mvp/spec.md`
**Input**: Feature specification from `specs/001-loan-aggregation-mvp/spec.md`

## Summary

Build a lead-generation-first loan aggregation MVP with authenticated user journeys,
credit score check (simulation or integration toggle), lender comparison, application-intent
capture, EMI calculator, and admin lead operations. The implementation prioritizes data
quality, consent, and manual processing readiness over automated loan decisioning.

## Technical Context

### Product and Domain Context

- Domain: Financial lead aggregation (not direct lending).
- Primary outcome: Capture qualified leads and route to internal operations.
- Compliance sensitivity: PAN + financial data + consent obligations.

### Delivery Constraints

- Phase 1 excludes direct bank/NBFC integration and auto approvals.
- Comparison data can be static or semi-dynamic.
- Credit score may run in simulation mode initially.

### Quality Attributes

- Security baseline for sensitive PII collection.
- Reliability for lead submission and persistence.
- Auditability for admin actions and lead lifecycle transitions.
- Clear user disclaimers to avoid promise of guaranteed approvals.

## Constitution Check

- **Lead-Capture-First Scope**: Plan prioritizes credit-check and application lead funnels.
- **Data Minimization & Consent**: Lead schema captures only required MVP fields;
  consent checkpoints included at form submission boundaries.
- **Transparent Recommendations**: Score, EMI, and comparison outputs include indicative
  disclaimers and data-basis labeling.
- **Human-in-the-Loop**: No auto approval pipeline in architecture.
- **Spec Traceability**: Each FR block mapped to one or more workstreams below.
- **Security/Observability Gates**: Included as dedicated release criteria.

## Workstreams

### WS1 - Identity, Access, and Session Security

**Covers**: FR-001, FR-002, FR-003

- Implement user auth: email/password signup/login and social login connectors.
- Define roles: `customer`, `admin`.
- Protect admin routes and lead-management APIs with role enforcement.
- Enforce session expiry, secure cookie/token handling, and brute-force mitigation.

**Exit Criteria**
- Users and admins authenticate independently.
- Non-admin access attempts to admin surfaces are denied and logged.

---

### WS2 - Credit Check Flow + Lead Capture Integration

**Covers**: FR-004 to FR-010, FR-024 to FR-026

- Build credit-check form with PAN validation + basic profile inputs.
- Add provider abstraction with `simulation` and `integration` modes.
- Display score output with mandatory indicative disclaimer.
- Persist/merge lead records from credit-check path.
- Capture explicit consent and retention acknowledgment at submission.
- Mask PAN in display contexts except privileged views.

**Exit Criteria**
- Credit-check submission persists lead reliably with source tagging.
- Consent event present for each successful sensitive-data submission.

---

### WS3 - Loan Content, Comparison, and Application Intent

**Covers**: FR-011 to FR-016, FR-022, FR-023

- Publish product information pages (personal, business/Mudra, credit cards).
- Build lender comparison dataset and display layer with filter controls.
- Add campaign-ready landing page variants and attribution persistence.
- Implement application-intent form with lender preference + financial inputs.
- Persist application leads and issue acknowledgment.

**Exit Criteria**
- Users can move from information → comparison → intent submission in one flow.
- Lead records contain source + attribution + preferred lender fields where applicable.

---

### WS4 - EMI Calculator + Guidance Layer

**Covers**: FR-017, FR-018

- Implement EMI calculator inputs and formula output module.
- Validate ranges and show errors for invalid values.
- Show assumptions and non-guarantee disclaimer adjacent to result.

**Exit Criteria**
- Calculator returns deterministic values for valid inputs.
- Error handling prevents invalid calculations from being displayed as valid.

---

### WS5 - Admin Lead Operations Console

**Covers**: FR-019, FR-020, FR-021

- Build admin dashboard list with pagination/search/filter/sort.
- Build lead detail panel with operational fields and contextual history.
- Add status/note mutation workflow with actor/timestamp audit events.

**Exit Criteria**
- Admin can view/filter all leads and update lifecycle state.
- Every state change is audit logged and attributable.

---

### WS6 - Cross-Cutting Hardening and Analytics

**Covers**: FR-025, FR-026 + SC readiness

- Rate limit and abuse-detect public lead submission paths.
- Add structured logs and error instrumentation on critical user funnels.
- Add event metrics for conversion funnel stages.
- Add data-quality checks for mandatory lead fields.

**Exit Criteria**
- Critical flows emit traceable events and error signals.
- Data validation failures are measurable and observable.

## Data and State Model Plan

### Core Entities and Required Fields

- **Lead**
  - identity: lead_id, user_id (nullable for pre-auth captures if ever allowed)
  - personal: full_name, phone, pan_masked, pan_hash/tokenized_reference
  - financial: income_range, employment_type, loan_amount_requested, purpose
  - sourcing: source_type, campaign_source, utm_medium, utm_campaign, referrer
  - processing: status, assigned_operator, notes_latest, created_at, updated_at

- **CreditCheckEvent**
  - lead_id, mode(simulation/integration), score_band/value, result_summary,
    disclaimer_shown(boolean), consent_snapshot_id, created_at

- **ApplicationIntent**
  - lead_id, preferred_lender_id, product_type, requested_amount, tenure_preference,
    supporting_details, created_at

- **LeadAuditEvent**
  - lead_id, actor_id, event_type, from_status, to_status, note, timestamp

### Data Governance Decisions

- Store only required sensitive values; avoid unnecessary duplication.
- PAN displayed masked by default; full PAN restricted to privileged operations.
- Consent artifacts linked by immutable consent snapshot reference.

## Risk Register and Mitigations

1. **Regulatory/Privacy misalignment**
   - Mitigation: consent text gate, retention policy documentation, minimal data scope.
2. **Low lead quality due to weak validation**
   - Mitigation: strong form validation, mandatory fields, enrichment prompts.
3. **Misleading user expectations from static comparison data**
   - Mitigation: timestamped indicative labels and data-basis disclosure.
4. **Operational overload for manual team**
   - Mitigation: lead prioritization tags and filterable admin queue.

## Release Slicing (2–2.5 Month MVP)

### Slice A (Weeks 1-3): Foundation
- Auth + role access (WS1)
- Lead schema + storage + audit skeleton (WS2/WS5)
- Baseline consent and validation framework (WS2)

### Slice B (Weeks 4-6): Core User Conversion Flows
- Credit-check flow with simulation mode (WS2)
- Loan pages + comparison + application intent (WS3)
- EMI calculator (WS4)

### Slice C (Weeks 7-9): Operations + Hardening
- Full admin console workflows (WS5)
- Attribution, analytics, and observability (WS3/WS6)
- Security hardening, release readiness validation (WS6)

### Buffer (Week 10)
- UAT fixes, content polishing, compliance sign-off, go-live checklist.

## Testing Strategy

- **Specification-level acceptance tests** for each P1/P2 user story.
- **Validation tests** for PAN format, mandatory fields, consent gates.
- **Role-access tests** for admin-only surfaces.
- **Data integrity tests** ensuring lead creation from both capture paths.
- **Audit tests** confirming state transitions produce traceable events.
- **Performance smoke checks** aligned with SC-003 and SC-004.

## Deployment and Readiness Gates

MVP is launch-ready only when:

1. FR-001 to FR-026 have acceptance coverage.
2. SC-001 to SC-005 have measurement instrumentation.
3. Consent capture and masking rules are verified in production-like environment.
4. Admin team sign-off confirms manual workflow completeness.

## Open Decisions for Next Command (`/tasks`)

- Final status taxonomy labels and SLA thresholds for lead handling.
- Exact scoring simulation model behavior before provider onboarding.
- Campaign landing page variants to include in first launch wave.
