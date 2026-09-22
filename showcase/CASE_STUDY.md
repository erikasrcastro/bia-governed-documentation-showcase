# Case Study — From Release Note to Real Tutorial

## Summary

This case study documents a controlled live validation that changed BIA’s architecture.

The system received an authorized release note describing a newly added API capability.

The source was factually valid, but operationally incomplete.

The first documentation candidate exposed a critical quality problem: the generated update was too close to the release note and did not actually teach the user how to use the capability.

That failure became a design input.

BIA was changed so that **tutorial sufficiency is now evaluated before generation**.

The resulting behavior is the strongest public example of the system’s purpose:

> When evidence is insufficient, BIA stops, identifies what is missing, asks one precise human question, and leaves the CMS untouched.

---

## The Original Failure Mode

The authorized source essentially said:

> A new broadcast capability was added and can create and launch a regular campaign in one call.

That statement supports an existence claim.

It does **not** support a tutorial.

A useful API tutorial may require information such as:

- HTTP method
- route
- authentication
- request body
- required fields
- optional fields
- response format
- errors
- prerequisites
- examples
- operational limits

The source did not provide those facts.

A text-generation-only system could easily produce fluent but low-value documentation by paraphrasing the announcement.

That is exactly what BIA was redesigned to prevent.

---

## Why Prompt Changes Were Not Enough

The problem was not simply:

> “The prompt should ask for a better tutorial.”

If the evidence itself is incomplete, demanding more detailed output increases hallucination pressure.

The correct fix had to happen **before generation**.

BIA therefore introduced a new architectural stage:

**Tutorial Sufficiency**

This stage asks whether the authorized evidence is rich enough to support the intended instructional artifact.

---

## New Decision Flow

~~~mermaid
flowchart TD
    A[Authorized Release] --> B[Evidence]
    B --> C[Existing Documentation Comparison]
    C --> D[Documentary Decision]
    D --> E{Tutorial Evidence Sufficient?}
    E -- Yes --> F[Generate Tutorial]
    E -- No --> G[Identify Missing Operational Facts]
    G --> H[Create One Human Clarification]
    H --> I[Wait for Human Answer]
    I --> J[Revalidate Evidence]
    J --> E
~~~

---

## Topic-Scoped Clarification

BIA supports three documentation locales.

The missing API facts in this case were not language-specific.

Creating three clarification questions would have produced duplicated work and introduced opportunities for inconsistent answers.

The architecture therefore uses:

> **one clarification question per topic when the factual gap is shared across locales**

The validated human answer can later support all locales after evidence revalidation.

---

## The Generated Human Question

The live gate identified a set of missing operational facts, including:

- method
- endpoint
- request structure
- required and optional parameters
- authentication and permissions
- successful response format
- error behavior
- request/response examples
- rate limits or usage restrictions
- prerequisites

The human-facing question consolidated these into one actionable request rather than asking a vague question such as “Can you provide more information?”

This is important because Human Clarification quality directly affects the quality of the next evidence state.

---

## Crash-Safe Recovery

During validation, the clarification workflow also exercised a partial-failure scenario.

The durable clarification context was successfully persisted before the human-facing row was fully verified.

A naive retry could have:

- regenerated the question
- produced a different wording
- created a duplicate
- recaptured unrelated state

Instead, BIA recovered the exact immutable clarification context using stable identity and resumed materialization.

This behavior is now part of the workflow contract.

---

## Validation Result

At the validated milestone:

- the focused clarification/safety suite passed
- the full private repository regression suite passed with **1,870 tests**
- exactly one topic-scoped clarification was materialized
- the clarification remained in a waiting-for-human-answer state
- the operator language was PT-BR
- no CMS mutation occurred
- no CMS write capability was required for the clarification step
- recovery used the previously persisted immutable context

The exact production identifiers are intentionally omitted from this public case study.

---

## What Changed Architecturally

The incident produced several durable design rules.

### 1. Release notes are evidence, not documentation

They may establish that a change exists without being suitable as customer-facing prose.

### 2. Tutorial sufficiency must be evaluated before generation

The system should not discover missing operational facts only after producing an article.

### 3. Shared factual gaps should produce one shared clarification

Localization happens after evidence is sufficient.

### 4. Human Clarification must be specific

The agent should identify concrete missing facts instead of handing the human an undefined research task.

### 5. Durable context must survive partial failure

Retries should recover the same question rather than regenerate it.

### 6. CMS mutation must remain unreachable while evidence is insufficient

The safest failed documentation update is one that never reaches the write boundary.

---

## Why This Case Matters

This case illustrates a broader pattern for agentic systems.

The difficult question is often not:

> “Can the model generate something good?”

It is:

> “Does the system know when it should not generate yet?”

BIA’s value is not only the ability to write.

It is the ability to:

- detect incomplete evidence
- stop at the correct boundary
- preserve state
- ask a high-quality question
- resume safely
- keep consequential tools inaccessible until the workflow is ready

That is what makes the system an agentic workflow rather than a changelog copywriter.
