---
name: srs-writer
description: "Turns a PRD (and its design spec, where one exists) into a full Software Requirements Specification: introduction, overall description, EARS-form functional and non-functional requirements with Given/When/Then examples, external interfaces, business rules, database, legal, i18n, FMEA risk analysis, traceability matrix, TBD list, and approval block. Use when the user has a PRD and needs the software requirements before engineering design, or asks for an SRS, a software requirements specification, an IEEE-style requirements document, or a sign-off-ready spec for a client, auditor, regulator, or vendor."
---

# SRS Writer

Stage 5 of the spec-writer chain. Reads a PRD and, where one exists, a Design Spec; feeds `trd-writer` and `qa-test-plan-writer`.

Turn product requirements into a Software Requirements Specification: what the software shall do, under what constraints, and how each requirement will be verified. The PRD says what the product does for its users. The SRS states that as testable software requirements — the contract a client, auditor, tester, or vendor can sign. The TRD, downstream, then decides how engineering builds it.

This is where the chain's functional and non-functional requirements are minted. Every `FR-xx` and `NFR-xx` starts here, and the TRD and test plan carry them under the same IDs.

## Reading the upstream documents

This stage reads the PRD and, where one exists, the design spec. In a repository the documents are on disk, versioned, and possibly split per platform, so resolve them deliberately rather than taking the first file that matches.

| Upstream | Where it lives |
|---|---|
| PRD | `docs/prd-writer/<slug>-prd-v*.md` |
| design spec | `docs/design-spec-writer/<slug>-design-spec-v*.md` |

1. **Look in the producing skill's folder**, or in the project's documentation directory where one replaces `docs/`.
2. **Take the highest version.** Compare `v<major>.<minor>.<patch>` numerically, so `v0.0.10` is newer than `v0.0.9`. Never read an older version just because it came up in conversation. If the user names an older one deliberately, use it and say so in the header block.
3. **Match the platform.** Where you are producing a per-platform document, read the upstream file for *that* platform — the iOS document is built from the iOS upstream, not from the set. Where the upstream was written as a single file with no platform token, every platform reads that one.
4. **Cite the exact filename** in `**Upstream:**`, version and platform included — `invoice-tracker-prd-ios-v0.2.0.md`. Where this stage reads several documents, list them all, comma-separated; a missing one reads as "written without it". That line is what makes the staleness check mechanical: a reviewer compares it against the newest upstream file on disk.
5. **On a re-run, diff rather than redraft.** Your own previous version file names the upstream version it was built from. Compare that against the upstream you just resolved, and change only what the difference requires.

Outside a repository — a pasted document, an attachment, or a document drafted earlier in this conversation — apply the same rules to what you were given, and where the version genuinely isn't knowable write `**Upstream:** <name> (version unknown)` rather than inventing one.

## Step 1 — Get the source material

Resolve the PRD by the rules above — highest version in `docs/prd-writer/`, matching platform, exact filename cited. A pasted description or a PRD drafted earlier in this conversation counts. If there is no PRD at all, work from whatever the user describes and state explicitly in the header that it's derived from an informal brief.

Read the whole PRD before drafting. Pull out the problem statement, user classes, `PR-xx` stories and requirements with their acceptance criteria, the Section 8 quality requirements, `MET-xx` metrics and guardrails, priorities, constraints, and everything the PRD explicitly excludes. If the BRD carries constraints the PRD doesn't repeat — regulatory, contractual — read those too.

If a Design Spec exists, read it too — its screens, flows, states, and validation rules are functional requirements you'd otherwise have to re-derive, and its `SCR-xx` and `FLOW-xx` IDs are cited alongside the `PR-xx` ones.

**Scope and platforms:** carry both from the PRD header into this document's header verbatim, and do not ask again.

## Step 2 — Fill the gaps the PRD won't have

A PRD rarely holds everything an SRS needs. Identify what is missing and ask the user in one batched question:

- Who approves this document, and for what purpose — internal engineering handoff, contract, regulatory submission, vendor handoff, or formal acceptance
- Quality targets the PRD leaves open: response time, availability, data retention, recovery time, accessibility conformance level
- Security and privacy: user roles and permissions, data sensitivity, audit needs
- Applicable regulations and standards, with the clauses that matter
- Supported locales, languages, and currencies
- External systems, hardware, and communications the software must interface with
- The FMEA risk threshold: the RPN at or above which a mitigation is mandatory

If the user is unavailable, state reasonable assumptions in Section 2.5 and Section 6 rather than baking in silent defaults.

## Step 3 — Write the requirements in EARS

Derive the software requirements from the PRD and number them: `FR-xx` for functional, `NFR-xx` for non-functional. These IDs are global — the TRD refines them and the test plan tests them under the same numbers, so never renumber an existing one on a re-run.

1. **One EARS pattern per requirement** — ubiquitous, event-driven, state-driven, unwanted behavior, optional feature, or complex — with *shall*. Describe observable behavior only: no table names, frameworks, endpoints, or status codes. Those belong to the TRD.
2. **Keep it singular.** A requirement with "and" that can fail two ways is two requirements.
3. **Add specification by example.** Each rule, calculation, or edge case gets at least one Given/When/Then scenario with concrete values.
4. **Assign priority** — Must, Should, Could, or Won't — carried from the PRD's MoSCoW where it exists.
5. **Trace every one.** Each `FR-xx` cites the `PR-xx` it implements, plus `SCR-xx` where a design spec exists. Each `NFR-xx` cites a `MET-xx`, a Section 8 `PR-xx`, or a BRD constraint. Never invent a parent.
6. **Give every NFR a number and a verification method**, classified against the ISO/IEC 25010:2023 quality model. Walk all nine characteristics, and mark the ones that don't apply rather than skipping them.

## Step 4 — Assess risk with FMEA

For each feature, ask what can fail, what the user experiences, and why. Score each failure mode for severity (S), occurrence (O), and detection difficulty (D), each 1–10, and compute RPN = S × O × D. Sort by RPN. Every failure mode above the threshold from Step 2, and every one with S ≥ 9, names a mitigation that is a requirement ID in this document. Number failure modes `FM-xx`. They are document-local, and nothing downstream traces to them.

Scores are judgment, not measurement. State who scored them, and mark scores made without the team as assumptions.

## Step 5 — Link stories to requirements

Build the traceability matrix: every PRD `PR-xx` maps to the SRS requirements that satisfy it and the design surfaces that show it. A `PR-xx` with no requirement is a coverage gap. A requirement with no `PR-xx` carries `[no upstream — new in this document]`. Both go in Section 6.

## Step 6 — Structure the document

<!-- include-start: references/template.md -->
Read `references/template.md` in this skill folder before drafting, and follow it exactly — section list, header block, Revision History, and the absent-section markers.
<!-- include-end -->

## Step 7 — Quality bar before delivering

- `**Upstream:**` names the exact PRD filename, and the design spec where one was read, version and platform included, and each is the newest on disk.
- Revision History carries a row for this version, with the `FR-xx`, `NFR-xx`, and `FM-xx` IDs added, changed, and removed since the previous one.
- Every requirement uses exactly one EARS pattern, with *shall*, and carries a priority and acceptance criteria.
- Every rule, calculation, or edge case has at least one Given/When/Then example with concrete values.
- Every `FR-xx` cites the `PR-xx` it implements. Every `NFR-xx` cites a `MET-xx`, a `PR-xx`, or a BRD constraint. Every orphan is marked and raised in Section 6.
- Every NFR has a number and a verification method. Performance and security appear once, in 3.2.
- No implementation detail: no architecture, schema, framework, endpoint, or status code. Those are the TRD's.
- Every `PR-xx` appears in the traceability matrix.
- Every FMEA row above the threshold, or with S ≥ 9, has a mitigation that is a requirement ID.
- Every `TBD` in the body is in Appendix 5.3 with an owner and a date. Status is not Approved while the list is non-empty.
- Legal, internationalization, database, hardware, and communications sections are each filled or marked `Not applicable — <reason>`.
- The Approval block names every approver from Step 2.

## Standards this follows

Content and structure follow **ISO/IEC/IEEE 29148:2018**, clause 9.6 (software requirements specification), which superseded IEEE 830-1998. Requirements use the **EARS** syntax (Easy Approach to Requirements Syntax) and are judged against the nine 29148 characteristics: necessary, appropriate, unambiguous, complete, singular, feasible, verifiable, correct, conforming. Examples use Gherkin Given/When/Then. Non-functional requirements are classified against the **ISO/IEC 25010:2023** quality model. Risk analysis follows **FMEA** as defined in **IEC 60812:2018**.

## Output format

An SRS is a document that approvers sign and the team revises, so it has to exist as a file. Draft the substance first, then format it.

**This document set always ends in a Markdown file. Never deliver it as ordinary chat prose.**

Deliver by the highest tier the environment supports:

1. **Filesystem available** (coding agent, IDE, terminal, code interpreter) — write `docs/srs-writer/<slug>-srs-v<version>.md`. Create `docs/` only if it is missing — never recreate or replace an existing one — then create the `srs-writer/` subfolder inside it if that is missing too, so each skill's output stays in its own folder. Where the project already has a documentation directory, use that in place of `docs/`, with the same `srs-writer/` subfolder inside it. Report the path.
2. **Files or a document surface, but no repo** (downloadable file, canvas, doc, notebook) — create it there named `<slug>-srs-v<version>.md` and hand over the download or link.
3. **Chat only** — put the entire document in one fenced code block tagged `markdown`, with nothing else inside the fence, and name the file the user should save it as: `<slug>-srs-v<version>.md`. Everything you want to say goes before the fence, never interleaved.

Use one consistent `<slug>` across every document in the chain for a given product — the same slug the first document in the chain started with — so the files read as one set. At feature scope the slug is the feature, not the product. The folder is per skill, the slug is per product: `docs/srs-writer/<slug>-srs-v<version>.md`.

**Versioning.** Every write creates a new file; an existing version file is never overwritten or deleted.

Before drafting, list `docs/srs-writer/` and find the highest `v<major>.<minor>.<patch>` among files matching `<slug>-srs-v*.md`. Compare the three numbers numerically, so `v0.0.10` is newer than `v0.0.9`. That file is the previous version — read it first, so the new document is a revision rather than a restart.

- No previous file — start at `v0.0.1`.
- **Patch** (`v0.0.x`) — edits, corrections, gaps filled, wording.
- **Minor** (`v0.x.0`) — new sections or requirements, or a re-run against a revised upstream document.
- **Major** (`vx.0.0`) — **Status** reaches Approved, or a rewrite that invalidates the documents downstream of this one.

The `**Version:**` line in the header block always carries the same number as the filename, and every version appends a row to the document's **Revision History** — what changed, and which IDs were added, changed, and removed. Report the new path and that summary, not just that a file was written.

**The changelog is mandatory.** After writing the file, append one bullet to `docs/CHANGELOG.md` — at the root of `docs/`, beside the per-skill subfolders — under today's ISO date heading, creating the file or the heading if either is missing:

```markdown
- **SRS** `srs-writer/<filename>` — <one-line summary>. +<IDs added>, ~<IDs changed>, -<IDs removed>. Upstream `<upstream path>` (every upstream, comma-separated, or "informal brief" where there is none).
```

The IDs must match the Revision History row for this version. Never edit a past entry — a correction is a new version with a new entry. Per-platform writes get one bullet each — and where this stage writes a single file whatever the platform set, that file gets one bullet. The document is not delivered until this entry exists.

**Per-platform documents.** Where the header block declares more than one platform, produce one complete SRS per platform — not a shared document with a platform column doing the work. Each file is standalone and whole: every section filled for that platform, shared behavior written out in full rather than cross-referenced.

`docs/srs-writer/<slug>-srs-<platform>-v<version>.md`, one file per declared platform. The `<platform>` token comes from this fixed vocabulary, so filenames stay predictable: `web`, `ios`, `android`, `macos`, `windows`, `linux`, `api`.

- One platform, or platform-neutral — a single file with no platform token, exactly as before.
- Each file's `**Platforms:**` header carries that one platform, and names the sibling files it was split from.
- Each file versions independently: read the highest version of *that platform's* file and bump from it.
- **IDs are global, not per file.** A requirement that exists on several platforms keeps the same ID in every file — an ID means one thing across the whole set. A platform-only requirement takes the next ID from the same sequence and appears only in its own file. Never renumber per platform; that is what makes the set reviewable side by side.
- **A change to shared content is a change to every file.** When a shared requirement moves, update every platform file in the same run and bump each one. Producing a new iOS file while the Android file still carries the old wording is the failure this structure invites, so guard against it deliberately.
- Every file states the parity intent explicitly — full parity, reduced scope here, or capability unique to this platform — and lists what the other platform files have that this one doesn't.

Report every path written, not just the first.

Whatever the tier:

- The document is Markdown: header block first, then every section. A section with nothing to say carries `Not applicable — <reason>` or `Insufficient evidence — <what would resolve it>`, never an omission.
- Report the **filename plus a 3–5 line summary** — what it covers, the biggest assumption, the open questions that need a human. Don't restate the body in prose.
- Other formats — PDF, Google Doc, Word, spreadsheet — are exports *from* the Markdown, never replacements for it.
- Re-running against a revised upstream document produces the next version file — see the versioning rule above — rather than editing the previous one in place.

Open the file with the document template — title, header block, Revision History, then sections 1–7 in order.

## Reviewing an existing SRS

When asked to review rather than write, report against the template's section numbers so every gap is addressable, and work the Step 7 quality bar as the checklist. Say which sections are sign-off-ready, which are stated but unverifiable (a requirement in no EARS pattern, an NFR with an adjective instead of a number, a TBD with no owner), and which are missing outright. Name the smallest concrete edit that closes each gap. Check the four most often skipped: the FMEA table, the traceability matrix, the TBD list, and whether the SRS has leaked architecture that belongs in the TRD.

On handoff, point at `trd-writer`: it carries every `FR-xx` and `NFR-xx` under the same ID and decides how to build them. Then `qa-test-plan-writer`: the Given/When/Then examples and FMEA mitigations are test cases waiting to be written, and an NFR without a number is one that can't be tested.
