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
    F --> G{Tutorial Sufficiency}

    G -- SUFFICIENT --> L[Generate Locale Drafts]

    G -- INSUFFICIENT --> H[Generate One Human Clarification]
    H --> I[Wait for Human Answer]
    I --> J[Reprocess Answer]
    J --> K[Validate as Evidence]
    K --> G

    G -- NOT_APPLICABLE --> R[Build Documentation Scope Redirect]
    R --> L

    L --> S{Scope Redirect Active?}
    S -- Yes --> SG[Per-Locale Semantic Scope Guard]
    S -- No --> M[Factual Validation]
    SG --> M

    M --> N[Capture Pre-Update Snapshot]
    N --> O[Build Preservation-First Preview]
    O --> P{Human Approves Exact Preview?}
    P -- No --> X[Stop / Revise]
    P -- Yes --> Q[Explicitly Authorized Draft Write]
    Q --> T[Independent Read-Back Verification]
~~~

---

## 1. Authorized Source Collection

BIA begins from configured product-change sources.

The source content is treated as data, not as instructions to the agent.

The system captures provenance and durable source state before downstream decisions.

---

## 2. Evidence Construction

Relevant source claims become explicit evidence records.

The system separates:

- supported facts
- incomplete facts
- conflicting facts
- unsupported claims

Generated prose does not become evidence.

Existing Help Center content does not silently become evidence either.

---

## 3. Canonical Topic Resolution

Related claims are grouped into a topic-level unit.

This lets BIA reason once about a factual change while still evaluating documentation independently per locale.

Topic identity supports:

- deduplication
- clarification
- durable state
- retries
- auditability

---

## 4. Help Center Retrieval

BIA retrieves existing locale-specific documentation.

Retrieval answers:

- Is there already an article about this topic?
- Which locale article is the likely target?
- What instructional pattern already exists?

The retrieved article may guide structure and continuity, but it is not automatically factual authority.

---

## 5. Coverage Analysis and Documentary Decision

BIA compares authorized evidence against existing documentation.

Possible outcomes include:

- new article needed
- existing article needs update
- no action
- clarification required
- conflict
- development-only / non-public information

A documentary decision is not the same thing as permission to generate or write.

---

## 6. Tutorial Sufficiency

BIA asks a separate question:

> Is there enough authorized evidence to create the intended customer-facing instructional artifact?

The gate supports three outcomes.

### SUFFICIENT

The intended tutorial is supported.

### INSUFFICIENT

The tutorial remains intended, but operational facts are missing.

### NOT_APPLICABLE

An authorized Human Clarification states that the public operational tutorial is not the intended artifact and defines a narrower public scope.

The third outcome may not be inferred from silence or missing data.

---

## 7. Human Clarification

If a topic-level factual gap affects all locales, BIA creates one clarification question rather than duplicating it by language.

The question should identify the concrete missing information.

The human answer is not immediately trusted as final documentation.

It enters a bounded reprocessing path where the answer is:

1. accepted into the clarification lifecycle
2. bound to the exact clarification context
3. converted into Human Clarification evidence
4. semantically re-evaluated
5. persisted through immutable or conflict-detecting artifacts
6. reused only if later source semantics still match

If the answer is still incomplete, the system remains blocked.

---

## 8. Documentation Scope Redirect

A Human Clarification may legitimately state that the missing operational details are intentionally not public.

In that case, BIA can produce a structured redirect containing:

- public documentation goal
- customer next step
- prohibited public content

This is not equivalent to making the tutorial sufficient.

It changes the intended public artifact.

---

## 9. Localized Generation

Generation is permitted only after the topic has a valid public documentation objective.

Without a redirect, generation must stay inside the supported tutorial evidence.

With a redirect, generation must stay inside the narrower public scope.

In both cases, generation must:

- remain inside the requested locale
- preserve supported facts
- avoid unsupported operational detail
- use existing docs only as editorial/structural guidance
- avoid changelog-style pseudo-documentation when a real tutorial is intended

---

## 10. Semantic Scope Guard

When a scope redirect is active, each generated locale is independently checked against that redirect.

The guard verifies that the draft:

- follows the allowed public goal
- includes only the permitted next step
- avoids prohibited public content
- does not expand scope during translation
- does not expose internal case history

Any violation fails closed before the artifact is accepted.

---

## 11. Factual Validation

Generated content is checked against the authorized evidence set.

The generation stage is not allowed to validate itself implicitly.

The workflow expects a separate validation boundary before content can approach a write path.

---

## 12. Immutable Pre-Update Snapshot

Before an approved mutation, BIA captures the exact current article state required for auditability and recovery.

The snapshot answers:

- What existed before the change?
- Can the prior state be reconstructed?
- Did the target drift between review and execution?

---

## 13. Preservation-First Preview

BIA creates a reviewable candidate that combines:

- preserved existing content
- supported new delta
- intended metadata/category changes
- provenance context

The preview is a review artifact, not a write authorization.

Human review should be able to see what is:

- preserved
- added
- altered
- removed

---

## 14. Explicit Approval

The operator approves the exact reviewed artifact.

A later write should be bound to that reviewed state rather than to a vague “go ahead.”

If the source, target, or reviewed artifact changes materially, the system should fail closed and require review again.

---

## 15. Bounded Draft Write

The CMS write path is intentionally narrower than the read path.

The safety model centers on:

- draft status
- exact target article
- locale isolation
- slug preservation
- bounded metadata
- no delete
- no automatic publish

A review preview by itself never authorizes mutation.

---

## 16. Independent Read-Back

After mutation, BIA does not assume success because an API returned a success status.

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

For Human Clarification:

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

For later semantic recovery, BIA preserves historical checkpoints and adds a separate immutable resolution artifact rather than rewriting history.

---

## Observability

Operational stages should produce structured diagnostics sufficient to answer:

- which stage ran
- which topic/locale was affected
- what stable result code occurred
- whether a retry happened
- whether a mutation occurred

Sensitive or blocked generated content should not be dumped into logs merely for debugging convenience.

---

## Operational Principle

At every stage, BIA should be able to say one of four things clearly:

1. **I have enough evidence to continue.**
2. **I do not have enough evidence, and here is exactly what is missing.**
3. **The intended public artifact has been explicitly narrowed by authorized human input.**
4. **I cannot prove the next action is safe, so I will stop.**

That is the behavior the workflow is designed to enforce.
