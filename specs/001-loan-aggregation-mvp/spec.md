# Feature Specification: Loan Aggregation MVP Platform

**Feature Branch**: `001-loan-aggregation-mvp`  
**Created**: 2026-04-26  
**Status**: Draft (Ready for planning)  
**Input**: User description: "Loan aggregation website MVP for lead generation, eligibility guidance, CIBIL check/simulation, loan comparison, application flow, and admin lead management."

## User Scenarios & Testing

### User Story 1 - Check eligibility and credit profile quickly (Priority: P1)

A visitor wants to understand whether they are likely eligible for a loan and wants a quick
credit profile check so they can decide whether to apply.

**Why this priority**: Credit-check-driven entry is a core lead acquisition gateway and an
explicit business requirement.

**Independent Test**: A new user can register/login, provide required identity and profile
inputs, receive a simulated/connected credit score output with disclaimers, and produce a
stored lead record.

**Acceptance Scenarios**:

1. **Given** an unauthenticated visitor, **when** they click "Check Credit Score", **then**
   they are prompted to authenticate before entering PAN/basic profile details.
2. **Given** an authenticated user, **when** required credit-check form fields are valid,
   **then** system returns score result and explanatory text (indicative only), and stores
   lead data with timestamp and source = `credit_check`.
3. **Given** invalid PAN format or missing required input, **when** user submits, **then**
   system blocks submission with clear field-level errors.

---

### User Story 2 - Compare loan options and submit application intent (Priority: P1)

A user wants to compare high-level lender options (interest range, tenure, eligibility) and
submit a loan application intent for manual follow-up.

**Why this priority**: Comparison + application intent is the primary conversion path.

**Independent Test**: User can browse loan category pages, compare lender cards, choose a
preferred lender, submit loan requirements and income/employment details, and create a lead
record tagged to lender preference.

**Acceptance Scenarios**:

1. **Given** loan comparison page is loaded, **when** user filters by product type,
   **then** lender entries show indicative range fields with updated content state.
2. **Given** user selects a lender and opens apply flow, **when** all mandatory details are
   provided, **then** application lead is stored with source = `loan_application` and
   preferred lender metadata.
3. **Given** user exits mid-form, **when** they return in same session, **then** partially
   entered values are recoverable until explicit cancel/submit.

---

### User Story 3 - Internal team manages and actions leads (Priority: P1)

An admin/back-office user needs to review, filter, and process captured leads to contact
customers and manually route them to suitable lenders.

**Why this priority**: MVP business model depends on internal lead operations.

**Independent Test**: Admin can login, view all leads, filter by source/status/date/product,
open lead detail, and update operational status notes.

**Acceptance Scenarios**:

1. **Given** authenticated admin user, **when** they access lead dashboard, **then** they
   can view paginated lead list with key fields and source tags.
2. **Given** lead list is visible, **when** admin applies filters, **then** list updates to
   matching results and can be reset.
3. **Given** lead detail view, **when** admin updates status/note, **then** update is saved
   with actor and timestamp for audit trace.

---

### User Story 4 - Estimate EMI before applying (Priority: P2)

A user wants to estimate EMI using amount, rate, and tenure before submitting loan interest.

**Why this priority**: Improves confidence and conversion but does not block core lead capture.

**Independent Test**: User enters principal, interest, tenure and sees monthly EMI estimate.

**Acceptance Scenarios**:

1. **Given** calculator page, **when** valid numeric inputs are entered, **then** EMI value
   is computed and displayed instantly.
2. **Given** invalid numeric ranges (negative, zero tenure), **when** submitted, **then**
   calculator returns validation guidance and no result.

## Functional Requirements

### Authentication and Access

- **FR-001**: System MUST support user registration and login via email/password.
- **FR-002**: System MUST support social login via Google and Microsoft.
- **FR-003**: System MUST enforce role-based access for public users vs admin users.

### Credit Score Intake and Output

- **FR-004**: System MUST provide a credit score flow accepting PAN and minimum required
  personal/profile fields.
- **FR-005**: System MUST support configurable mode: simulated score OR integrated provider.
- **FR-006**: System MUST display score outputs as indicative and non-binding.
- **FR-007**: Every completed credit check MUST create or update a lead record.

### Lead Capture and Lifecycle

- **FR-008**: System MUST capture leads from both credit-check and loan application flows.
- **FR-009**: Lead data model MUST include: name, phone, PAN, financial summary, source,
  preferred product, preferred lender (if chosen), status, timestamps.
- **FR-010**: System MUST track lead lifecycle states (e.g., new, contacted, qualified,
  rejected, converted, closed).

### Loan Information and Comparison

- **FR-011**: System MUST publish product pages for personal loans, business (Mudra) loans,
  and credit cards with eligibility, key benefits, and approximate rates.
- **FR-012**: System MUST provide lender comparison views with at least rate range, tenure
  range, and eligibility summary fields.
- **FR-013**: Comparison data MUST support static or semi-dynamic updates by admin/editor.

### Loan Application Flow

- **FR-014**: System MUST collect loan amount, purpose, employment type, and income details.
- **FR-015**: System MUST allow user to select a preferred lender during application intent.
- **FR-016**: Application submission MUST create lead record and acknowledgment response.

### EMI Calculator

- **FR-017**: System MUST calculate EMI from principal, annual interest rate, and tenure.
- **FR-018**: EMI calculator MUST expose formula assumptions and non-guarantee disclaimer.

### Admin Lead Management

- **FR-019**: Admin interface MUST display captured leads with sortable/filterable list view.
- **FR-020**: Admin interface MUST support lead detail inspection and status/note updates.
- **FR-021**: System MUST record admin action audit trail for lead lifecycle changes.

### Marketing and Campaign Support

- **FR-022**: System MUST support campaign landing pages mapped to lead source attribution.
- **FR-023**: System MUST persist UTM/referrer metadata where available for lead analytics.

### Security and Compliance Baseline

- **FR-024**: System MUST capture explicit user consent for collecting PAN and financial data.
- **FR-025**: System MUST apply input validation and rate limiting on lead capture endpoints.
- **FR-026**: System MUST avoid exposing full PAN in non-privileged UI views.

## Key Entities

- **User**: End-customer account (auth credentials, profile, consent flags).
- **AdminUser**: Internal operator account with lead operations privileges.
- **Lead**: Canonical record for captured prospect data and processing lifecycle.
- **CreditCheckEvent**: Credit score attempt/result payload tied to a lead.
- **LoanApplicationIntent**: Application details and lender preference tied to a lead.
- **LenderProfile**: High-level lender comparison metadata and display configuration.
- **LoanProductContent**: Category content blocks (eligibility, rates, benefits).
- **CampaignAttribution**: Source/referrer/UTM metadata associated with lead creation.

## Success Criteria

- **SC-001**: At least 85% of successfully submitted credit checks create valid lead records
  with all mandatory fields.
- **SC-002**: At least 80% of completed application-intent submissions include preferred
  lender selection when user reaches comparison step.
- **SC-003**: Admin team can retrieve and filter leads with median query-to-result latency
  under 2 seconds in normal operating load.
- **SC-004**: At least 95% of valid EMI calculations return result in under 1 second.
- **SC-005**: Less than 1% of stored lead records fail validation audits for required fields.

## Assumptions

- Manual backend processing remains the final decision authority in MVP.
- Static/semi-dynamic lender data is acceptable and does not claim real-time rate accuracy.
- Credit score integration can initially run in simulation mode pending provider contracts.
- Regulatory/privacy policy text will be finalized before production launch.

## Dependencies

- Availability of social-auth app credentials (Google/Microsoft).
- Defined PAN handling and consent language approved by legal/compliance owner.
- Admin operations workflow agreement (status taxonomy and follow-up SLA).
- Initial lender/product content dataset from business stakeholders.

## Out of Scope (Phase 1)

- Direct lender API integrations for instant offer/approval.
- Automated lender matching and automated loan decisioning.
- Real-time partner onboarding portals.
- Fully dynamic market interest rate feeds.
