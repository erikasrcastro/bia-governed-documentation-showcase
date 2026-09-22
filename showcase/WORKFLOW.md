# BIA Workflow

## End-to-End Lifecycle

BIA uses a staged workflow rather than a single generate-and-publish action.

~~~mermaid
flowchart TD
    A[Collect Authorized Change] --> B[Build Evidence]
    B --> C[Resolve Canonical Topic]
    C --> D[Retrieve Existing Docs]
    D --> E[Analyze Coverage]
    E --> F[Choose Documentary Action]
    F --> G{Enough Evidence for a Tutorial?}

    G -- No --> H[Generate One Human Clarification]
    H --> I[Wait for Human Answer]
    I --> J[Reprocess Answer]
    J --> K[Validate as Evidence]
    K --> G

    G -- Yes --> L[Generate Locale Drafts]
    L --> M[Validate Facts]
    M --> N[Capture Pre-Update Snapshot]
    N --> O[Build Preservation-First Preview]
    O --> P{Human Approves Exact Preview?}
    P -- No --> Q[Stop / Revise]
    P -- Yes --> R[Explicitly Authorize Draft Write]
    R --> S[Write Bounded Draft Update]
    S --> T[Read Back and Verify]
~~~

---

## 1. Source Collection

BIA starts from explicitly authorized product-change sources.

External text is treated as data, not as authority over the agent.

The source layer may identify:

- new features
- fixes
- API changes
- changed procedures
- release announcements
- other approved product updates

The fact that a source is authorized does not mean it is complete enough to become documentation.

---

## 2. Evidence Construction

Source claims are converted into evidence records with provenance.

The system keeps the distinction between:

- what the source explicitly supports
- what is unknown
- what is inferred
- what existing documentation says

Unsupported inference is not allowed to become factual evidence.

---

## 3. Topic Resolution

Related claims are grouped into one canonical topic.

This prevents duplicate work and allows one clarification answer to serve all locales when the missing fact is topic-level rather than language-specific.

---

## 4. Help Center Retrieval

BIA retrieves existing Help Center content for each locale.

The retrieved article can answer questions such as:

- Is this topic already documented?
- What article structure already exists?
- Which parts appear to require updates?
- What editorial pattern should be preserved?

It cannot answer:

- Is this old article still factually correct?
- Can missing release details be inferred from historical prose?

Existing content is comparison material, not an evidence source by default.

---

## 5. Documentary Decision

For each topic and locale, BIA decides whether the documentation path should continue.

Possible outcomes include:

- new article
- update
- no action
- clarification required
- conflict
- ignored non-public content

A positive documentary decision is not yet permission to generate.

---

## 6. Tutorial Sufficiency

Before tutorial-style generation, BIA asks:

> Do the authorized facts contain enough operational detail to teach the customer how to use the change correctly?

This is intentionally stricter than asking whether the release is true.

If essential operational information is absent, generation stops.

Examples of missing information may include:

- where the user starts
- API method and endpoint
- required fields
- optional parameters
- authentication
- prerequisites
- expected output
- error states
- limitations
- complete examples

---

## 7. Human Clarification

If a topic-level factual gap affects all locales, BIA creates one clarification question rather than duplicating it by language.

The question is expected to identify the concrete missing information.

The human answer is not immediately trusted as final documentation.

It enters a reprocessing path where the answer is:

1. accepted into the clarification lifecycle
2. evaluated for sufficiency
3. checked for conflict
4. promoted into authorized evidence only when valid

If the answer is still incomplete, the system remains blocked.

---

## 8. Localized Generation

Only after evidence is sufficient does BIA generate customer-facing content.

Generation must:

- remain inside the requested locale
- preserve supported facts
- use the existing article only as editorial/structural guidance
- produce instructional content rather than changelog prose
- avoid inventing operational details

---

## 9. Factual Validation

Generated content is checked against the authorized evidence set.

The generation stage is not allowed to validate itself implicitly.

The workflow expects a separate validation boundary before content can approach a write path.

---

## 10. Immutable Pre-Update Snapshot

Before an approved mutation, BIA captures the exact current article state needed for auditability and recovery.

The snapshot is identity-bound and conflict-detecting.

Its purpose is to answer:

- What existed before the change?
- Can the prior state be reconstructed?
- Did the target drift between review and execution?

---

## 11. Preservation-First Preview

BIA creates a reviewable candidate update that combines:

- preserved existing content
- the supported new instructional delta
- intended metadata/category changes
- provenance context

The preview is a review artifact, not a write authorization.

Human review should be able to see what is:

- preserved
- added
- altered
- removed

---

## 12. Explicit Approval

The operator approves the exact reviewed artifact.

A later write should be bound to that reviewed state rather than to a vague statement such as “go ahead.”

If the source, target article, or preview identity changes materially, the system should fail closed and require review again.

---

## 13. Bounded Draft Write

The CMS write path is intentionally narrower than the read path.

The current safety model centers on:

- draft status
- exact target article
- locale isolation
- slug preservation
- bounded metadata
- no delete
- no automatic publish

---

## 14. Independent Read-Back

After mutation, BIA does not assume success because an API returned a 2xx response.

It reads the target back and verifies the expected state.

This protects against:

- serialization changes
- partial writes
- stale responses
- uncertain mutation outcomes
- unintended content loss

---

## Crash Recovery

BIA persists durable context before exposing consequential workflow states.

For Human Clarification, the pattern is:

~~~mermaid
sequenceDiagram
    participant B as BIA
    participant D as Durable Context Store
    participant S as Human-Facing Sheet

    B->>D: Persist immutable question context
    D-->>B: Verified
    B->>S: Materialize question
    alt Sheet step fails
        B--xS: Failure
        Note over B,D: Exact context remains durable
        B->>D: Recover same question identity
        D-->>B: Original context
        B->>S: Retry idempotent materialization
    end
~~~

The system recovers the original question instead of generating a new one.

---

## Operational Principle

At every stage, BIA should answer one of three things clearly:

1. **I have enough evidence to continue.**
2. **I do not have enough evidence, and here is exactly what is missing.**
3. **I cannot prove that the next action is safe, so I will stop.**

That is the behavior the workflow is designed to enforce.
