# BIA Architecture

## System Goal

BIA is a governed documentation agent designed to transform authorized product changes into accurate Help Center updates without allowing language-model confidence to substitute for evidence or authority.

> **LLM interprets. Software controls. Tools execute.**

The production implementation is private. This document describes the public architecture at a systems level.

---

## Core Design

BIA is not a prompt connected directly to a CMS.

It is a staged workflow with explicit contracts between:

- source ingestion
- evidence construction
- canonical topic resolution
- Help Center retrieval
- coverage analysis
- documentary decisioning
- tutorial-sufficiency evaluation
- Human Clarification
- documentation-scope redirect
- localized generation
- semantic Scope Guard
- factual validation
- immutable snapshots
- preservation-first preview
- explicit external mutation authorization
- independent read-back verification

Each stage owns a narrow responsibility.

---

## Control Plane vs. Reasoning Plane

### Reasoning plane

The model is used where semantic interpretation is useful, including:

- understanding source meaning
- comparing evidence against existing documentation
- determining whether operational information is missing
- evaluating a Human Clarification answer
- generating localized documentation
- checking whether generated content stays inside an authorized public scope
- validating semantic consistency

### Control plane

Deterministic software owns:

- stable identities
- locale boundaries
- lifecycle state
- allowed actions
- persistence rules
- retry behavior
- idempotency
- mutation authorization
- artifact identity
- snapshot immutability
- fail-closed transitions
- external side-effect boundaries

The model cannot grant itself new authority.

---

## Architecture Overview

~~~mermaid
flowchart TB
    subgraph Sources
        A[Authorized Product Sources]
        B[Approved Human Clarifications]
    end

    subgraph Evidence
        C[Evidence Records]
        D[Canonical Topic]
    end

    subgraph Documentary Intelligence
        E[Help Center Retrieval]
        F[Coverage Analysis]
        G[Documentary Decision]
        H[Tutorial Sufficiency]
    end

    subgraph HumanLoop
        I[Topic-Scoped Human Clarification]
        J[Human Answer]
        K[Evidence Revalidation]
        R[Documentation Scope Redirect]
    end

    subgraph Authoring
        L[Localized Generation]
        SG[Per-Locale Semantic Scope Guard]
        M[Factual Validation]
        N[Preservation-First Composition]
    end

    subgraph SafetyAndExecution
        O[Immutable Pre-Update Snapshot]
        P[Review Preview]
        Q[Explicit Human Approval]
        W[Bounded Draft Write]
        X[Independent Read-Back]
    end

    A --> C
    B --> C
    C --> D
    D --> E
    E --> F
    F --> G
    G --> H

    H -->|INSUFFICIENT| I
    I --> J
    J --> K
    K --> H

    H -->|NOT_APPLICABLE| R
    H -->|SUFFICIENT| L
    R --> L

    L --> SG
    SG --> M
    M --> N
    N --> O
    O --> P
    P --> Q
    Q --> W
    W --> X
~~~

When no scope redirect exists, the Scope Guard path is effectively bypassed and factual validation remains the next semantic gate.

---

## Evidence Layer

BIA distinguishes between:

1. **authorized factual evidence**
2. **existing documentation used for comparison**
3. **human clarification evidence**
4. **generated prose**

These are not interchangeable.

Existing Help Center content may inform structure, editorial pattern, or retrieval relevance, but it is not silently treated as factual truth.

Generated prose never becomes evidence merely because BIA produced it.

---

## Tutorial Sufficiency Is Not Factual Support

BIA separates two questions:

**Factual support:** Do we know that the change happened?

**Tutorial sufficiency:** Do we know enough to teach the customer how to use it safely?

A release note may pass the first and fail the second.

That distinction prevents changelog text from becoming pseudo-documentation.

---

## Three Tutorial Outcomes

### `SUFFICIENT`

Authorized evidence supports the intended public tutorial.

### `INSUFFICIENT`

The tutorial is still intended, but required operational evidence is missing.

### `NOT_APPLICABLE`

An authorized Human Clarification explicitly states that the public operational tutorial is not the intended outcome and supplies a narrower public documentation scope.

The third outcome cannot be inferred from missing information, source silence, or model preference.

---

## Human Clarification Boundary

When one topic-level gap affects multiple locales, BIA asks one topic-scoped question.

The interaction is designed to be:

- specific
- answerable
- tied to known missing facts
- stable across retries
- reusable across locales after validation

The clarification context is persisted before the human-facing question is exposed.

This creates a crash-safe recovery path.

---

## Documentation Scope Redirect

A scope redirect is a structured human-authorized boundary containing:

- public documentation goal
- customer next step
- prohibited public content

It does not say “the tutorial is now complete.”

It says “the intended public artifact is different and narrower.”

This matters because a system should not keep requesting details that an authorized human has explicitly said must remain non-public.

---

## Cross-Run Recovery

A validated redirect can be reused by a later workflow run only when the current source semantics still match the source package that produced the original clarification.

Operational IDs that are intentionally ephemeral do not define semantic equality.

The system instead binds reuse to stable source meaning and persisted immutable artifacts.

Ambiguity or mismatch fails closed.

---

## Semantic Scope Guard

When a redirect exists, generation receives it as a **maximum allowed public scope**.

A separate semantic guard evaluates each locale after generation.

The guard checks whether the generated public artifact:

- follows the allowed documentation goal
- includes only the permitted next step
- avoids prohibited operational content
- does not expand scope during localization
- does not expose private case history

A violation blocks the topic before downstream acceptance.

---

## Localization Model

BIA supports:

- PT-BR
- EN-USA
- ES-LATAM

The factual core may be topic-scoped, but each locale has an independent generation and validation path.

Localization may change language and editorial form.

It may not change facts or authority.

---

## Preservation-First Update Model

Existing articles are treated as content that must be preserved unless a reviewed update explicitly requires otherwise.

~~~mermaid
flowchart LR
    A[Verified Existing Article] --> B[Immutable Snapshot]
    B --> C[Preserved Baseline]
    D[Supported New Evidence] --> E[Generated Delta]
    C --> F[Combined Candidate]
    E --> F
    F --> G[Human Review Preview]
    G --> H[Explicitly Authorized Draft Mutation]
~~~

This protects against replacing a complete article with only a newly generated fragment.

---

## External Mutation Boundary

External write capability is deliberately narrow.

The architecture assumes:

- draft-only updates
- stable article identity
- preserved slug
- bounded metadata/category changes
- no automatic publication
- no deletion
- explicit authorization before consequential mutation
- independent remote read-back after mutation

If the system cannot establish whether a write succeeded, it does not blindly repeat it.

---

## State and Persistence

BIA uses durable external state because ephemeral runners cannot be trusted to preserve operational history.

Durable artifacts may include:

- source cursors
- source revisions
- taxonomy snapshots
- evidence records
- clarification context
- clarification lifecycle state
- immutable reprocess checkpoints
- semantic-resolution artifacts
- pre-update article snapshots
- review artifacts
- recovery checkpoints

Identity-bound artifacts are immutable or conflict-detecting.

---

## Observability

Observability is part of the architecture, not an afterthought.

Operational stages should expose safe structured metadata such as:

- stage
- topic
- locale
- stable result/failure code
- retry state
- mutation state

Sensitive generated content should not be emitted merely to make debugging easier.

---

## Reliability Strategy

BIA favors:

- deterministic identities
- idempotent reads
- bounded retries
- bounded loops
- durable state
- conflict detection
- stable failure codes
- read-back verification
- safe recovery from persisted artifacts

The goal is not “never fail.”

The goal is to fail in a way that is visible, explainable, recoverable, and non-destructive.

---

## Why This Architecture Matters

The important design choice is not the specific LLM provider.

The important design choice is that **the LLM does not own authority**.

BIA demonstrates how semantic model reasoning can coexist with:

- deterministic permissions
- explicit evidence
- human escalation
- scope control
- narrow side effects
- durable recovery
- observable safety gates

That is the core engineering pattern behind the system.
