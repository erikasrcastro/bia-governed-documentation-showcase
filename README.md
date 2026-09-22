# BIA — Governed Documentation Agent

**A safety-first, multilingual AI documentation system that turns product changes into evidence-grounded Help Center updates without hallucinating missing operational details or exceeding an approved public scope.**

BIA was designed for a real documentation workflow where product releases, fixes, feature changes, and internal announcements must become accurate customer documentation across multiple languages.

The central engineering problem is simple to state:

> **A source can prove that something changed without proving enough to teach customers how to use it safely.**

BIA treats that distinction as a first-class system constraint.

> **Core principle:** LLM interprets. Software controls. Tools execute.

---

## Why This Project Exists

Documentation automation often fails in one of three ways:

1. it republishes release-note language as if it were customer documentation;
2. it invents plausible operational details when evidence is incomplete; or
3. it ignores a human decision that some operational details are intentionally not public.

BIA is designed to avoid all three.

It separates:

- evidence
- reasoning
- durable state
- Human Clarification
- tutorial-sufficiency evaluation
- documentation-scope control
- localized generation
- semantic guardrails
- factual validation
- review
- external mutation

behind explicit contracts and fail-closed boundaries.

---

## What BIA Demonstrates

- evidence-grounded agentic reasoning
- multilingual documentation workflows
- locale-isolated Help Center retrieval
- documentary decisioning per topic and locale
- tutorial-sufficiency evaluation before generation
- topic-scoped Human Clarification
- crash-safe clarification recovery
- deterministic identity and idempotency boundaries
- immutable evidence and checkpoint artifacts
- documentation-scope redirect from authorized human evidence
- redirect-aware generation
- independent semantic Scope Guard per locale
- preservation-first review previews
- draft-only CMS mutation behind explicit authorization
- independent read-back verification
- fail-closed behavior at consequential boundaries
- structured observability
- full regression testing around live safety gates

---

## Golden Case: From Missing Tutorial Facts to a Safe Scope Redirect

A controlled live validation exposed two related documentation risks.

First, an authorized release announced a new API capability. The source proved that the capability existed, but did **not** contain enough information to write a real operational tutorial.

BIA stopped before generation and created **one topic-scoped Human Clarification** instead of inventing:

- route
- method
- authentication
- request structure
- parameters
- response behavior
- examples
- limits
- prerequisites

Then the human answer introduced a second valid outcome:

> the operational tutorial was intentionally **not** meant to be public.

Instead of repeatedly asking for forbidden details, BIA accepted a narrower authorized documentation goal:

- say that the capability exists;
- direct interested customers to support;
- do not publish operational API instructions.

That became a structured **Documentation Scope Redirect**.

A later controlled E2E validation recovered the exact redirect across runs, generated PT-BR / EN-USA / ES-LATAM content inside the allowed scope, passed a separate semantic Scope Guard for each locale, and produced a preservation-first review preview.

The test was explicitly accepted as:

`TEST_ACCEPTED / NO_PUBLICATION`

No CMS mutation or publication was authorized by that validation.

See [showcase/CASE_STUDY.md](showcase/CASE_STUDY.md).

---

## High-Level Workflow

~~~mermaid
flowchart TD
    A[Authorized Product Change] --> B[Evidence Capture]
    B --> C[Canonical Topic]
    C --> D[Help Center Retrieval]
    D --> E[Coverage + Documentary Decision]
    E --> F{Tutorial Sufficiency}

    F -- SUFFICIENT --> J[Localized Generation]

    F -- INSUFFICIENT --> G[One Topic-Scoped Human Clarification]
    G --> H[Human Answer]
    H --> I[Evidence Revalidation]
    I --> F

    F -- NOT_APPLICABLE --> R[Documentation Scope Redirect]
    R --> J

    J --> K{Scope Redirect Active?}
    K -- Yes --> S[Per-Locale Semantic Scope Guard]
    K -- No --> L[Factual Validation]
    S --> L

    L --> M[Immutable Pre-Update Snapshot]
    M --> N[Preservation-First Review Preview]
    N --> O{Explicit Human Approval}
    O -- No --> P[Stop / Revise]
    O -- Yes --> Q[Bounded Draft Write]
    Q --> T[Independent Read-Back Verification]
~~~

---

## Tutorial Sufficiency Has Three Outcomes

### `SUFFICIENT`

Authorized evidence can support the intended public tutorial.

### `INSUFFICIENT`

A tutorial is still intended, but required operational facts are missing.

### `NOT_APPLICABLE`

An authorized Human Clarification explicitly states that the operational tutorial is not the intended public outcome and supplies a narrower replacement scope.

The third outcome is deliberately strict. It cannot be inferred from silence, missing data, a release note, or model preference.

---

## Documentation Scope Redirect

A scope redirect defines three things:

- **public documentation goal** — what may be communicated publicly
- **customer next step** — what the customer should do
- **prohibited public content** — what must not be exposed or taught

Generation may localize wording, but it may not expand the authorized public scope.

A separate semantic Scope Guard evaluates generated content before it can continue.

---

## Supported Documentation Locales

The production design isolates three Help Center locales:

- PT-BR
- EN-USA
- ES-LATAM

Factual evidence may be topic-scoped. Localization may change language and editorial form, but it may not change facts or expand authority.

When one factual gap affects all locales, BIA asks **one** Human Clarification question rather than duplicating it three times.

---

## Safety Model

Key invariants include:

- authorized evidence only
- no silent promotion of existing documentation into factual truth
- no tutorial generation from insufficient evidence
- no human-scope redirect inferred without explicit authorized human evidence
- no generation beyond a validated scope redirect
- no unsupported operational claims
- no cross-locale writes
- no automatic publication
- no content deletion
- immutable critical artifacts
- no blind retry after uncertain mutation
- explicit authorization before consequential writes
- independent read-back after mutation
- structured observability without leaking blocked content

See [showcase/SAFETY.md](showcase/SAFETY.md).

---

## Human Clarification as an Agent Capability

Human Clarification is not a generic “ask the user” fallback.

It is a governed workflow stage with:

- deterministic question identity
- immutable clarification context
- explicit human-owned answer field
- bounded software-owned lifecycle state
- semantic re-evaluation
- evidence promotion only after validation
- crash-safe recovery
- cross-run reuse only when source semantics match

This lets BIA stop exactly where human knowledge or authority is required and resume without inventing new context.

---

## Preservation-First Documentation Updates

BIA treats an existing article as content that must be preserved unless a reviewed change explicitly requires otherwise.

The update path is:

1. verify the current target article
2. capture an immutable pre-update snapshot
3. preserve the existing article body
4. synthesize only the supported delta
5. present the combined result for review
6. require explicit approval before a bounded draft mutation
7. read the remote article back and verify the expected state

---

## Observability

Observability is treated as an engineering prerequisite, not an optional add-on.

Operational diagnostics are designed to expose safe structured metadata such as:

- stage
- topic
- locale
- stable result/failure code
- retry state
- whether an external mutation occurred

Blocked or sensitive generated content should not be dumped into logs merely for convenience.

---

## Engineering Stack

The private implementation uses a Python-based runtime with typed contracts and provider-neutral structured model boundaries.

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

Exact prompts, credentials, identifiers, private schemas, internal operational rules, and production infrastructure are intentionally excluded.

---

## Repository Contents

- [showcase/ARCHITECTURE.md](showcase/ARCHITECTURE.md) — architecture and control boundaries
- [showcase/WORKFLOW.md](showcase/WORKFLOW.md) — sanitized end-to-end lifecycle
- [showcase/SAFETY.md](showcase/SAFETY.md) — safety and governance model
- [showcase/CASE_STUDY.md](showcase/CASE_STUDY.md) — full Golden Case from insufficiency to scope redirect
- [examples/sample_clarification_event.json](examples/sample_clarification_event.json) — fictionalized clarification example
- [examples/sample_scope_redirect_event.json](examples/sample_scope_redirect_event.json) — fictionalized scope-redirect example
- [SECURITY.md](SECURITY.md) — disclosure and sanitization policy

---

## Project Status

**Portfolio showcase of an actively engineered private system.**

The private system has progressed through deterministic core development, live integration hardening, Human Clarification, tutorial-sufficiency gating, crash-safe reprocessing, documentation-scope redirect, multilingual guarded generation, preservation-first preview, and bounded CMS write safety.

A controlled scope-redirect E2E test has been accepted successfully with **no publication**.

The system is **not presented as an unattended fully released production service**. Consequential live actions remain bounded by explicit authorization and human review.

---

## What Is Intentionally Not Public

This repository does **not** contain:

- production system prompts
- private source code
- credentials or OAuth material
- private Help Center URLs or article IDs
- internal account or document identifiers
- exact production schemas
- proprietary prompt packs
- internal release-channel identifiers
- private Google Drive or Sheets structures
- customer data
- operational secrets
- exact private deployment parameters

The purpose of this repository is to demonstrate **agent architecture, safety reasoning, workflow design, and engineering quality** without weakening the private system.

---

## Author

**Éricka Cahstro**

AI Automation · Agentic Systems · LLM Operations · AI Quality
