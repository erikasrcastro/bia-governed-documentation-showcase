# BIA — Governed Documentation Agent

**A safety-first, multilingual AI documentation system that turns product changes into evidence-grounded tutorials without hallucinating missing operational details.**

BIA was designed for a real Help Center workflow where product releases, fixes, feature changes, and internal announcements must become accurate customer documentation across multiple languages.

The central engineering problem is simple to describe and surprisingly hard to solve:

> **A release note can prove that something changed without containing enough information to teach a customer how to use it.**

BIA treats that distinction as a first-class system constraint.

Instead of copying changelog text into documentation—or asking an LLM to fill in the gaps—BIA separates **evidence**, **reasoning**, **state**, **human clarification**, **generation**, and **external mutation** behind explicit safety gates.

> **Core principle:** LLM interprets. Software controls. Tools execute.

---

## Why This Project Exists

Documentation automation often fails in one of two ways:

1. it behaves like a text transformer and republishes release-note language as customer documentation; or
2. it generates plausible but unsupported instructions when the source material is incomplete.

Neither behavior is acceptable for operational documentation.

BIA was built to solve a harder problem:

**How can an AI agent decide what changed, determine whether existing documentation needs work, know when the available evidence is insufficient, ask a human exactly what is missing, and update documentation without losing existing content or exceeding its authority?**

---

## What BIA Demonstrates

- Evidence-grounded agentic reasoning
- Multilingual documentation workflows
- Retrieval against existing Help Center content without treating that content as factual authority
- Documentary decisioning per topic and locale
- Tutorial-sufficiency evaluation before generation
- Human-in-the-loop clarification when operational evidence is incomplete
- Deterministic state, identity, idempotency, and retry boundaries
- Immutable evidence and pre-update snapshots
- Preservation-first review previews
- Draft-only CMS mutation behind explicit authorization
- Independent read-back verification after external writes
- Crash-safe recovery for partially completed workflows
- Fail-closed behavior at consequential boundaries
- Full regression testing around live operational gates

---

## Golden Case: Release Note → Clarification Instead of Hallucination

A controlled live validation exposed exactly the failure mode BIA was intended to prevent.

An authorized product release announced a new API capability. The source proved that the capability existed, but it did **not** contain enough operational information to produce a useful tutorial.

A weaker automation could have produced something like:

> “A new endpoint was added. It creates and launches a campaign in a single call.”

That is still a release note. It does not teach the customer how to use the feature.

BIA instead stopped the workflow **before generation**.

Its tutorial-sufficiency gate identified the missing operational information required to write a real tutorial, including:

- HTTP method
- endpoint path
- request structure
- required and optional parameters
- authentication and permissions
- successful response shape
- error behavior
- request/response examples
- usage restrictions or rate limits
- prerequisites

BIA then generated **one topic-scoped Human Clarification question** for the missing facts.

The result:

- no invented documentation
- no duplicated question per language
- one human answer can become evidence for all supported locales after revalidation
- no CMS mutation while evidence is insufficient
- crash-safe recovery if clarification persistence is interrupted
- full regression suite passing at the validated milestone

This behavior is now a reference case for the project:

> **Release notes are evidence. They are not automatically publishable tutorials.**

See [showcase/CASE_STUDY.md](showcase/CASE_STUDY.md).

---

## High-Level Workflow

~~~mermaid
flowchart TD
    A[Authorized Product Change] --> B[Evidence Capture]
    B --> C[Canonical Topic]
    C --> D[Help Center Retrieval]
    D --> E[Coverage + Documentary Decision]
    E --> F{Tutorial Evidence Sufficient?}

    F -- No --> G[One Topic-Scoped Human Clarification]
    G --> H[Human Answer]
    H --> I[Evidence Revalidation]
    I --> F

    F -- Yes --> J[Localized Generation]
    J --> K[Factual Validation]
    K --> L[Immutable Pre-Update Snapshot]
    L --> M[Preservation-First Review Preview]
    M --> N{Human Approval}
    N -- No --> O[Stop / Revise]
    N -- Yes --> P[Explicitly Authorized Draft Write]
    P --> Q[Independent Read-Back Verification]
~~~

---

## Supported Documentation Locales

The production design isolates three Help Center locales:

- PT-BR
- EN-USA
- ES-LATAM

Factual evidence is topic-scoped where appropriate. Localization may change language and editorial form, but it may not change facts.

When one factual gap affects all locales, BIA asks **one** Human Clarification question rather than creating three duplicates.

---

## Architecture

BIA uses a hybrid agent architecture:

~~~mermaid
flowchart LR
    S[Authorized Sources] --> E[Evidence Layer]
    E --> T[Topic Resolution]
    T --> R[Retrieval]
    R --> D[Documentary Decision]
    D --> U[Tutorial Sufficiency]
    U -->|insufficient| H[Human Clarification]
    H --> E
    U -->|sufficient| G[Localized Generation]
    G --> V[Factual Validation]
    V --> P[Preview + Approval]
    P --> W[Bounded Draft Write]

    C[Deterministic Control Layer] --- E
    C --- D
    C --- U
    C --- H
    C --- V
    C --- P
    C --- W
~~~

The LLM is used where semantic interpretation is valuable. Deterministic software owns identity, permissions, transitions, validation, persistence rules, retry behavior, and external side effects.

More detail: [showcase/ARCHITECTURE.md](showcase/ARCHITECTURE.md).

---

## Safety Model

BIA is designed to prefer an explicit stop over plausible invention.

Key invariants include:

- authorized evidence only
- no silent promotion of existing documentation into factual truth
- no tutorial generation from insufficient evidence
- no unsupported operational claims
- no cross-locale writes
- no automatic publication
- no content deletion
- immutable pre-update snapshots
- no blind retry after an uncertain mutation
- explicit authorization before consequential writes
- independent read-back after mutation
- crash-safe recovery using stable identities

See [showcase/SAFETY.md](showcase/SAFETY.md).

---

## Human Clarification as an Agent Capability

Human Clarification is not treated as a generic “ask the user” fallback.

It is a governed workflow stage with:

- deterministic question identity
- immutable clarification context
- explicit human-owned answer field
- bounded software-owned lifecycle state
- semantic re-evaluation after the answer
- evidence promotion only after validation
- recovery without regenerating the original question

This lets BIA preserve context while stopping exactly at the point where human knowledge is required.

---

## Preservation-First Documentation Updates

A documentation agent should not replace an existing article with only the newly generated delta.

BIA therefore uses a preservation-first update model:

1. verify the current target article
2. capture an immutable pre-update snapshot
3. preserve the existing article body as the baseline
4. synthesize only the supported update
5. present the combined result for review
6. require approval before an authorized draft mutation
7. read the remote article back and verify the expected result

This design was hardened after a controlled live incident and recovery exercise. The recovery workflow itself became part of the system’s safety architecture.

---

## Engineering Stack

The private production implementation includes a Python-based runtime with typed contracts and provider-neutral model boundaries.

Publicly relevant technologies include:

- Python
- Pydantic
- structured LLM output
- GitHub Actions
- Google Drive
- Google Sheets
- Discord source ingestion
- WordPress / Help Center adapters
- immutable state and evidence artifacts
- automated regression tests

Exact production prompts, credentials, identifiers, internal schemas, proprietary operational rules, and private infrastructure are intentionally excluded from this repository.

---

## Repository Contents

- [showcase/ARCHITECTURE.md](showcase/ARCHITECTURE.md) — system architecture and control boundaries
- [showcase/WORKFLOW.md](showcase/WORKFLOW.md) — sanitized end-to-end lifecycle
- [showcase/SAFETY.md](showcase/SAFETY.md) — safety and governance model
- [showcase/CASE_STUDY.md](showcase/CASE_STUDY.md) — the tutorial-sufficiency Golden Case
- [examples/sample_clarification_event.json](examples/sample_clarification_event.json) — fictionalized event example
- [SECURITY.md](SECURITY.md) — public repository disclosure policy

---

## Project Status

**Portfolio showcase of an actively engineered private system.**

The private implementation has progressed through architecture, deterministic core development, integration hardening, controlled live validation, incident recovery, Human Clarification, tutorial-sufficiency gating, and multilingual preview work.

The system is **not presented here as an unattended fully released production service**. Consequential live actions remain bounded by explicit authorization and human review.

---

## What Is Intentionally Not Public

This repository does **not** contain:

- production system prompts
- private source code
- credentials or OAuth material
- internal account or document identifiers
- private Help Center URLs or article IDs
- exact production schemas
- proprietary prompt packs
- internal release-channel identifiers
- private Google Drive or Sheets structures
- customer data
- operational secrets
- exact retry timing or deployment parameters

The purpose of this repository is to demonstrate **agent architecture, safety reasoning, workflow design, and engineering quality** without exposing private implementation details.

---

## Author

**Éricka Cahstro**

AI Automation · Agentic Systems · LLM Operations · AI Quality
