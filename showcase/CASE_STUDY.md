# Case Study — From Release Note to Safe Documentation Scope

## Summary

This case study documents a controlled live validation that changed BIA’s architecture twice.

The system received an authorized release note describing a newly added API capability.

The source was factually valid, but operationally incomplete.

The first candidate exposed a quality problem: a release announcement can prove that a capability exists without containing enough information to teach customers how to use it.

BIA was changed so that **tutorial sufficiency is evaluated before generation**.

Then the Human Clarification answer exposed a second valid scenario:

> the missing operational details were intentionally not meant to become public tutorial documentation.

BIA therefore added a governed **Documentation Scope Redirect** path.

The final controlled validation demonstrated that BIA could:

- stop before hallucinating
- ask one precise topic-scoped question
- preserve clarification history
- reinterpret the answer without rewriting old checkpoints
- recover the approved scope across runs
- generate three locales inside the allowed public boundary
- block scope expansion with a separate semantic guard
- produce a review preview
- stop before CMS mutation

The final operator disposition was:

`TEST_ACCEPTED / NO_PUBLICATION`

---

## Stage 1 — The Original Failure Mode

The authorized source essentially said:

> A new broadcast capability was added and can create and launch a regular campaign in one call.

That supports an existence claim.

It does **not** support a full operational tutorial.

A useful API tutorial may require:

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

A text-generation-only system could easily create fluent but unsupported documentation by filling the gaps.

BIA was redesigned to prevent that.

---

## Stage 2 — Tutorial Sufficiency Before Generation

The fix was architectural, not cosmetic.

Instead of telling the model to “write a better tutorial,” BIA added a pre-generation question:

> Is the authorized evidence sufficient to support the intended instructional artifact?

If not, generation stops.

~~~mermaid
flowchart TD
    A[Authorized Release] --> B[Evidence]
    B --> C[Existing Documentation Comparison]
    C --> D[Documentary Decision]
    D --> E{Tutorial Sufficiency}
    E -- SUFFICIENT --> F[Generate]
    E -- INSUFFICIENT --> G[Identify Missing Facts]
    G --> H[One Human Clarification]
~~~

---

## Stage 3 — Topic-Scoped Human Clarification

BIA supports three documentation locales:

- PT-BR
- EN-USA
- ES-LATAM

The missing API facts were topic-level, not language-specific.

Creating three clarification questions would have duplicated work and increased inconsistency risk.

BIA therefore uses:

> **one clarification question per topic when the factual gap is shared across locales**

The question consolidates the concrete missing facts into one actionable request.

---

## Stage 4 — Crash-Safe Clarification Recovery

The clarification workflow persists durable context before exposing the human-facing question.

That matters because a partial failure could otherwise produce:

- a regenerated question
- slightly different wording
- a duplicate row
- inconsistent later evidence

Instead, BIA recovers the exact immutable clarification context and resumes the same question identity.

This behavior became part of the workflow contract.

---

## Stage 5 — The Human Answer Changed the Documentation Goal

The Human Clarification answer did **not** provide the missing operational API details.

Instead, it established a narrower public policy:

- acknowledge that the capability exists
- route interested customers to support
- do not publish operational API instructions

This was not ordinary `SUFFICIENT`.

It was also not ordinary `INSUFFICIENT`.

The intended public artifact had changed.

BIA therefore introduced a third tutorial outcome:

`NOT_APPLICABLE`

This means:

> the public operational tutorial is not the intended documentation outcome, and an authorized human source has defined a narrower replacement scope.

---

## Stage 6 — Documentation Scope Redirect

The redirect is structured around three concepts:

### Public documentation goal

What may be communicated publicly.

### Customer next step

What the customer should do.

### Prohibited public content

What the documentation must not expose or teach.

The redirect is deliberately restrictive.

It cannot be inferred from missing evidence, silence, or model preference.

It must come from authorized Human Clarification.

---

## Stage 7 — Preserve History Instead of Rewriting It

The earlier clarification attempt had already produced an immutable tutorial checkpoint under the previous semantics.

BIA did **not** rewrite that historical artifact.

Instead, it created a separate immutable semantic-resolution artifact bound to the old checkpoint.

This allows the system to evolve interpretation while preserving the truth of what happened previously.

The design principle is:

> **new semantics may resolve old state, but they must not erase old history.**

---

## Stage 8 — Cross-Run Recovery

The next E2E run needed to recover the previously validated redirect.

A naive implementation could match only on a transient runtime ID and fail to recognize the same source semantics later.

BIA instead binds cross-run reuse to stable source meaning.

A previous redirect is reusable only when the current source evidence still matches the original authorized source semantics.

Ambiguity or mismatch fails closed.

---

## Stage 9 — Redirect-Aware Generation

Once the redirect is recovered, the normal tutorial objective is skipped for that topic.

Generation receives the redirect as a **maximum public scope**.

The model may localize wording, but it may not:

- teach the forbidden operation
- reconstruct missing API details
- expand the public claim
- expose internal case history

---

## Stage 10 — Independent Semantic Scope Guard

Generation does not get to declare itself safe.

Each localized artifact passes through a separate semantic Scope Guard.

The guard checks whether the public output remains inside the redirect.

If the draft exceeds the allowed scope, the workflow blocks before downstream acceptance.

This separation matters because:

> generation and policy enforcement should not be the same decision.

---

## Stage 11 — Multilingual Review Preview

The controlled validation generated candidate updates for:

- PT-BR
- EN-USA
- ES-LATAM

The accepted public delta was narrow:

- capability exists
- access/details require contacting support
- no operational API tutorial is exposed

The workflow then produced a preservation-first review preview.

The existing article content remained the baseline.

No CMS mutation was performed.

---

## Final Validation Result

The final validation confirmed:

- the clarification lifecycle completed successfully
- the Human Clarification became authorized evidence
- historical checkpoint state remained immutable
- the semantic-resolution path recovered safely
- the scope redirect was reused across runs
- all three locales were generated under the redirect
- the Scope Guard passed
- factual validation passed
- a multilingual review preview was persisted
- the full private regression suite passed
- no CMS mutation occurred

The operator explicitly accepted the test and explicitly rejected publication of the test article.

Final disposition:

`TEST_ACCEPTED / NO_PUBLICATION`

The exact production identifiers, URLs, prompts, private schemas, and infrastructure details are intentionally omitted.

---

## What Changed Architecturally

This case produced several durable design rules.

### 1. Release notes are evidence, not documentation

A source may establish that something exists without teaching safe use.

### 2. Tutorial sufficiency belongs before generation

Missing facts should be detected before hallucination pressure begins.

### 3. Shared factual gaps should create one shared clarification

Localization happens after factual resolution.

### 4. Human Clarification can change the intended artifact

Sometimes the correct answer is not “here are the missing tutorial details.”

Sometimes the correct answer is “those details are intentionally not public.”

### 5. Scope restriction must be structured

A vague instruction such as “don’t say too much” is not enough.

The system needs an explicit public goal, next step, and prohibited content boundary.

### 6. History should be append-only at semantic boundaries

Later interpretation should not rewrite earlier immutable evidence of what happened.

### 7. Cross-run reuse requires semantic identity

Transient runtime IDs are not enough.

### 8. Generation needs an independent scope guard

The authoring model should not be the sole judge of whether it respected policy.

### 9. Preview is not publication authority

A successful test preview can end explicitly in `NO_PUBLICATION`.

### 10. Observability is part of safety

The system should expose enough structured information to explain where and why it stopped without dumping blocked content into logs.

---

## Why This Case Matters

This case illustrates a broader pattern for agentic systems.

The difficult question is often not:

> “Can the model generate something good?”

It is:

> “Does the system know what it is allowed to generate, when it should stop, and how to preserve that decision across time?”

BIA’s value is not only the ability to write.

It is the ability to:

- detect incomplete evidence
- distinguish fact support from tutorial support
- ask for human knowledge at the right boundary
- accept a human restriction as a first-class system constraint
- recover exact state across retries and runs
- guard generated output independently
- preserve existing content
- keep consequential tools inaccessible until explicitly authorized

That is what makes the system an agentic workflow rather than a changelog copywriter.
