Produce the SRS with the sections below, in order. No section is ever deleted: one that genuinely doesn't apply is marked `Not applicable — <reason>`, and one that applies but has nothing behind it yet is marked `Insufficient evidence — <what would resolve it>`. Which of the two you pick is itself information a reviewer needs.

The outline follows the common SRS shape — Introduction, Overall Description, System Features and Requirements, Other Requirements, Appendices — with one correction: performance and security are stated once, under Non-Functional Requirements, rather than repeated under External Interface Requirements, so no requirement has two homes that can drift apart.

```markdown
# Software Requirements Specification: [product / feature name]

**Owner:** <name or role>
**Date:** <today's date>
**Version:** 0.0.1
**Status:** Draft | In review | Approved | Superseded
**Scope:** Product | Feature | Change
**Platforms:** <carried from the PRD>
**Upstream:** <exact upstream filenames, comma-separated — `invoice-tracker-prd-ios-v0.3.0.md`, `invoice-tracker-design-spec-ios-v0.1.0.md` — or "Informal brief">
**Downstream:** TRD, QA Test Plan

## Revision History

| Version | Date | Author | Upstream version | Summary | IDs added | IDs changed | IDs removed |
|---|---|---|---|---|---|---|---|
| 0.0.1 | [today's date] | [author] | `<prd filename>`, `<design spec filename>` | Initial draft | FR-01–FR-12, NFR-01–NFR-06, FM-01–FM-05 (every ID in this draft) | — | — |

One appended row per version, never rewritten. `—` means none; an empty cell means the change wasn't tracked, which is a gap worth fixing.

## 1. Introduction

### 1.1 Purpose
What software this SRS specifies, which release or version of it, and what decision this document exists to support — engineering handoff, contract, regulatory submission, vendor handoff, or acceptance sign-off.

### 1.2 Intended Audience
Each reader and what they take from it: product owner and business analyst (requirements are complete), developers (what to build), testers (what to verify), project manager (scope), client and approvers (what they are signing).

### 1.3 Intended Use
How each audience uses the document, and what it is not for — it is not the architecture (that is the TRD, which is built from this document) and not the test plan.

### 1.4 Product Scope
What the software does, the benefit it delivers, and the business objective it serves, cited to the PRD rather than re-argued. **In scope** and **Out of scope**, each explicit — out-of-scope carried from what the PRD explicitly excludes.

### 1.5 Definitions and Acronyms
Every domain term and abbreviation used below, defined once. Where the list is long, keep it here and cross-reference it from Appendix 5.1.

### 1.6 References
Every upstream document by exact filename and version, plus external standards, regulations, and interface specifications this SRS conforms to.

## 2. Overall Description

### 2.1 Product Perspective
Why the product is needed, whether it is new, a replacement, or an add-on to an existing system, and what it integrates with. A context diagram — the system as one box, every external actor and system around it — where a picture helps.

### 2.2 User Needs
Each user class from the PRD: who they are, what they need from the software, how often they use it, and their technical proficiency. Cite the PRD's `PR-xx` stories; do not invent personas.

### 2.3 Operating Environment
Platforms, OS and browser versions, hardware, and the other software the product must coexist with — carried from the PRD's platforms and constraints.

### 2.4 Design and Implementation Constraints
Hard limits the implementation must honor: mandated technologies, regulatory rules, corporate standards, hardware limits. Constraints only — the architecture chosen inside them belongs to the TRD.

### 2.5 Assumptions and Dependencies
**Assumptions** — what is taken as true and would change requirements if false. **Dependencies** — existing technology, third-party services, reused components, and other teams this relies on, each with the requirement that breaks if it fails.

## 3. System Features and Requirements

### 3.1 Functional Requirements
Grouped by system feature. For each feature: a one-line description, its priority, and its requirements.

| ID | Requirement (EARS) | Priority | Platforms | Acceptance criteria | Upstream |
|---|---|---|---|---|---|
| FR-01 | When an authenticated user requests overdue invoices, the system shall return that user's overdue invoices sorted by days overdue, descending. | Must | Shared backend | Returns only the caller's invoices; sorted descending; empty list, not an error, when none; unauthenticated request rejected | PR-01, SCR-01 |
| FR-12 | While the device has no connectivity, when the user captures a receipt, the app shall persist the capture locally. | Must | iOS, Android | Capture survives app restart offline; uploaded in capture order on reconnect | PR-08 |

**EARS patterns** — every requirement uses exactly one:

| Pattern | Template |
|---|---|
| Ubiquitous | The <system> shall <response>. |
| Event-driven | When <trigger>, the <system> shall <response>. |
| State-driven | While <state>, the <system> shall <response>. |
| Unwanted behavior | If <unwanted condition>, then the <system> shall <response>. |
| Optional feature | Where <feature is included>, the <system> shall <response>. |
| Complex | While <state>, when <trigger>, the <system> shall <response>. |

**Specification by example** — for every requirement with a business rule, edge case, or calculation, add at least one Given/When/Then scenario with concrete values beneath the feature's table:

    Scenario: FR-01 — no overdue invoices
      Given a signed-in user with 3 invoices, none past due
      When they request overdue invoices
      Then the system returns an empty list

This is where `FR-xx` IDs are minted. The TRD refines each one technically under the same ID, and the test plan tests it under the same ID, so an existing ID is never renumbered. Where a `PR-xx` is too ambiguous to turn into an EARS requirement, log the question in Section 6 rather than guessing.

### 3.2 Non-Functional Requirements

| ID | Category | Requirement (EARS) | Measure | Verification method | Upstream |
|---|---|---|---|---|---|
| NFR-01 | Performance | The system shall return the overdue-invoice list within 150 ms at p95 under 200 requests/s. | p95 ≤ 150 ms | Measurement — staging load test | MET-02 |

Classify each against the **ISO/IEC 25010:2023** quality model. Cover, at minimum: **performance**, **security** (authentication, authorization, data protection, audit), **usability and accessibility**, **reliability and availability**, **safety** where harm is possible, and **compliance**. Every NFR has a number and a verification method — measurement, test, demonstration, analysis, or inspection. A category that does not apply is still listed, marked `Not applicable — <reason>`.

### 3.3 External Interface Requirements
- **User interfaces** — screen-level expectations, cited to the design spec's `SCR-xx`; layout detail stays in the design spec.
- **Hardware interfaces** — devices, sensors, peripherals, and the supported interaction with each.
- **Software interfaces** — every external system and third-party integration: what is exchanged, format, direction, and the `FR-xx` it serves. The TRD turns each into an `API-xx` contract with fields and status codes.
- **Communications interfaces** — protocols, encryption, message formats, and synchronization.

### 3.4 Business Rules
Every rule the software enforces — eligibility, calculation, validation, workflow, retention — stated once, each tied to the `FR-xx` that enforces it.

## 4. Other Requirements

### 4.1 Database Requirements
Logical data the system must retain: entities, retention periods, integrity rules, and backup or recovery expectations. Physical schema stays in the TRD.

### 4.2 Legal and Regulatory Requirements
Each regulation, license, or contractual obligation, with the requirement IDs that satisfy it. For regulated domains — medical, financial, automotive — name the standard and the clause.

### 4.3 Internationalization and Localization
Supported locales, languages, currencies, time zones, date and number formats, and right-to-left support, or `Not applicable — <reason>`.

### 4.4 Risk Management (FMEA)

| ID | Function / requirement | Failure mode | Effect | Cause | S | O | D | RPN | Mitigation | Owner |
|---|---|---|---|---|---|---|---|---|---|---|
| FM-01 | FR-12 | Queued capture lost on app update | User loses receipt | Local store migrated without the queue | 8 | 3 | 6 | 144 | Migration test for the queue store; FR added | Mobile lead |

S, O, and D are each scored 1–10: S for severity, O for likelihood of occurrence, and D for difficulty of detection, where 10 means it escapes detection. RPN = S × O × D. Sort by RPN, descending. Every row with RPN at or above the agreed threshold, or with S ≥ 9 whatever its RPN, names a mitigation that is a requirement ID in this document. A mitigation that is not a requirement is only intent.

### 4.5 Traceability Matrix

| PRD story / requirement | SRS requirement | Design surface |
|---|---|---|
| PR-01 | FR-01, NFR-01 | SCR-01 |

Every `PR-xx` appears at least once. A `PR-xx` with no SRS requirement is a coverage gap. A requirement with no `PR-xx` is `[no upstream — new in this document]`. The test plan extends this chain to `TC-xxx`; it is not filled here.

## 5. Appendices

### 5.1 Glossary
Business vocabulary for non-technical readers, and anything not already in 1.5.

### 5.2 Use Cases and Diagrams
Use cases for the main flows (actor, preconditions, main path, alternates, postconditions), cited to the design spec's `FLOW-xx` where one exists. Context, state, and sequence diagrams, in Mermaid where possible.

### 5.3 To Be Determined (TBD) List
Every `TBD` used in the body: where it is, what resolves it, owner, and due date. An SRS cannot reach Approved while this list is non-empty.

## 6. Assumptions, Open Questions, and Next Steps
Mandatory in every document in this chain. **Assumptions and Open Questions:** every gap you filled with a guess, every `PR-xx` too ambiguous to turn into a requirement, and every requirement with no upstream parent. **Next Steps:** the review and approval path, with named approvers, who owns each open question, and what has to be true before sign-off.

## 7. Approval

| Role | Name | Decision | Date |
|---|---|---|---|
| Product owner | | Pending | |
| Engineering lead | | Pending | |
| QA lead | | Pending | |
| Client / sponsor | | Pending | |
```
