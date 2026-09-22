# BIA Architecture

## System Goal

BIA is a governed documentation agent designed to transform authorized product changes into accurate Help Center updates without allowing language-model confidence to substitute for evidence.

The architecture separates semantic interpretation from deterministic control.

> **LLM interprets. Software controls. Tools execute.**

The production implementation is private. This document describes the public architecture at a systems level.

---

## Core Design

BIA is not a single prompt connected directly to a CMS.

It is a staged workflow with explicit contracts between:

- source ingestion
- evidence construction
- topic resolution
- Help Center retrieval
- documentary decisioning
- tutorial-sufficiency evaluation
- Human Clarification
- localized generation
- factual validation
- immutable snapshots
- human review
- bounded external mutation
- read-back verification

Each stage owns a narrow responsibility.

---

## Control Plane vs. Reasoning Plane

### Reasoning plane

The model is used for tasks where semantic interpretation is useful, including:

- understanding release-note meaning
- resolving topic-level relationships
- comparing evidence against existing documentation
- deciding what information is still missing
- generating natural-language documentation
- validating semantic consistency

### Control plane

Deterministic software owns:

- stable identities
- locale boundaries
- state transitions
- allowed actions
- persistence rules
- retry behavior
- idempotency
- mutation authorization
- precondition checks
- postcondition checks
- artifact hashing
- snapshot immutability

The model cannot grant itself new authority.

---

## Architecture Overview

~~~mermaid
flowchart TB
    subgraph Sources
        A[Authorized Release Sources]
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
        I[Human Clarification]
        J[Human Answer]
        K[Evidence Revalidation]
    end

    subgraph Authoring
        L[Localized Generation]
        M[Factual Validation]
        N[Preservation-First Composition]
    end

    subgraph SafetyAndExecution
        O[Immutable Pre-Update Snapshot]
        P[Review Preview]
        Q[Explicit Human Approval]
        R[Bounded Draft Write]
        S[Independent Read-Back]
    end

    A --> C
    B --> C
    C --> D
    D --> E
    E --> F
    F --> G
    G --> H

    H -->|insufficient| I
    I --> J
    J --> K
    K --> C

    H -->|sufficient| L
    L --> M
    M --> N
    N --> O
    O --> P
    P --> Q
    Q --> R
    R --> S
~~~

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

## Canonical Topic Model

BIA resolves related source claims into a topic-level unit before downstream decisions.

This allows the system to reason about a product change once while still evaluating locale-specific documentation independently.

Topic identity also supports:

- deduplication
- Human Clarification
- state persistence
- retries
- auditability
- evidence promotion

---

## Documentary Decision Layer

A topic may require different outcomes depending on existing documentation.

Examples include:

- create new documentation
- update an existing article
- take no action
- request clarification
- stop on conflict
- ignore non-public or development-only information

The decision is not equivalent to “generate text.”

Generation is permitted only after the workflow has enough evidence to support the intended documentation.

---

## Tutorial Sufficiency Gate

This gate answers a different question from factual support.

**Factual support:** Do we know that the product change happened?

**Tutorial sufficiency:** Do we know enough to teach the user how to use it correctly?

A release note may pass the first and fail the second.

That distinction prevents BIA from converting changelog prose directly into pseudo-documentation.

When operational details are missing, the workflow stops before generation.

---

## Human Clarification Boundary

When one topic is insufficient across multiple locales, BIA asks one topic-scoped question.

The human-facing interaction is designed to be:

- specific
- answerable
- tied to known missing facts
- stable across retries
- reusable across locales after validation

The clarification context is persisted before the human-facing question is exposed.

This creates a crash-safe recovery path if persistence succeeds but downstream materialization is interrupted.

---

## Localization Model

BIA supports multiple Help Center locales while keeping factual evidence stable.

The factual core may be shared when appropriate, but each locale has an independent generation and validation path.

This prevents:

- cross-locale contamination
- mixed-language article bodies
- silent factual drift during translation

Localization changes language and editorial form, not factual meaning.

---

## Preservation-First Update Model

Existing articles are treated as content that must be preserved unless a reviewed update explicitly requires otherwise.

The update path is therefore:

~~~mermaid
flowchart LR
    A[Verified Existing Article] --> B[Immutable Snapshot]
    B --> C[Preserved Baseline]
    D[Supported New Evidence] --> E[Generated Delta]
    C --> F[Combined Candidate]
    E --> F
    F --> G[Human Review Preview]
    G --> H[Authorized Draft Mutation]
~~~

This protects against a common failure mode in document automation: replacing a full article with only the newly generated fragment.

---

## External Mutation Boundary

External write capability is deliberately narrow.

The public architecture assumes:

- draft-only updates
- stable article identity
- preserved slug
- bounded metadata/category changes
- no automatic publication
- no deletion
- explicit authorization before consequential mutation
- independent remote read-back after mutation

If the system cannot establish whether a write succeeded, it does not blindly repeat the write.

---

## State and Persistence

BIA uses durable external state because ephemeral workflow runners cannot be trusted to preserve operational history.

Persisted artifacts include:

- source cursors
- source revisions
- taxonomy snapshots
- evidence records
- clarification context
- clarification lifecycle state
- pre-update article snapshots
- review artifacts
- recovery checkpoints

Identity-bound artifacts are designed to be immutable or conflict-detecting.

---

## Reliability Strategy

BIA favors:

- idempotent reads
- deterministic identities
- explicit retries
- bounded loops
- read-back verification
- conflict detection
- stable failure codes
- recovery from durable artifacts

The goal is not “never fail.”

The goal is to fail in a way that is visible, explainable, and recoverable without creating duplicate or destructive side effects.

---

## Why This Architecture Matters

The important design choice is not the specific LLM provider.

The important design choice is that **the LLM does not own authority**.

The system can use a model for semantic reasoning while still keeping:

- permissions deterministic
- side effects narrow
- missing information explicit
- human judgment available
- recovery auditable

That is the central engineering pattern demonstrated by BIA.
