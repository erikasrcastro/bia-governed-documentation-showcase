# BIA Safety and Governance

## Safety Objective

BIA operates in a domain where plausible language can create real operational harm.

A documentation error can cause customers to:

- send the wrong API request
- misconfigure a product
- misunderstand prerequisites
- follow unsupported instructions
- rely on information that was never meant to be public
- lose trust in the Help Center

The system therefore treats factual uncertainty, public-scope authority, and external mutation as governance problems, not only prompt-engineering problems.

---

## Primary Safety Principle

> **More consequence requires less autonomy without external validation.**

BIA uses model reasoning where it is useful, but consequential permissions remain deterministic.

---

## Threat Model

The public safety model considers risks such as:

- hallucinated operational instructions
- release-note paraphrasing presented as a tutorial
- unsupported inference from existing documentation
- disclosure of intentionally non-public operational details
- scope expansion during localization
- cross-locale contamination
- stale target articles
- duplicate Human Clarification questions
- partial persistence
- duplicate retries after uncertain writes
- content loss during updates
- accidental publication
- deletion
- source prompt injection
- mutation after the reviewed state has changed
- diagnostic logs leaking blocked content

---

## Fail-Closed Defaults

BIA stops instead of guessing when:

- evidence is missing
- evidence conflicts
- tutorial evidence is insufficient
- the target cannot be identified exactly
- locale isolation cannot be proven
- a required immutable artifact cannot be verified
- Human Clarification context conflicts with durable state
- a persisted semantic resolution does not match its source checkpoint
- a reused scope redirect no longer matches current source semantics
- a Scope Guard reports a violation
- a write outcome is uncertain
- remote read-back does not match the expected result
- a reviewed artifact is no longer bound to the current state

---

## Evidence Safety

### Authorized sources only

Only configured, approved source types may contribute factual evidence.

### Existing documentation is not authority

An old Help Center article may help with:

- structure
- tone
- terminology
- continuity

But it does not silently become factual proof.

### Generated text is not evidence

A sentence created by the model cannot later support itself.

### Human answers require revalidation

Human Clarification answers enter the evidence pipeline through an explicit evaluation step.

---

## Tutorial Sufficiency

BIA distinguishes:

- **factually supported change**
- **sufficiently documented operation**

A change can be fully supported while still being unsafe to document as a tutorial.

This gate prevents the agent from filling in unsupported details such as:

- API routes
- methods
- parameters
- UI labels
- permissions
- prerequisites
- expected outcomes
- limits
- error handling

---

## Documentation Scope Redirect

A Human Clarification can explicitly state that a public operational tutorial is **not** the intended outcome.

In that case BIA may accept a narrower public scope.

The redirect must come from authorized human evidence and define:

- public documentation goal
- customer next step
- prohibited public content

It may **not** be inferred from:

- silence
- missing evidence
- release-note wording
- model preference
- convenience

A redirect is a restriction, not permission to improvise.

---

## Scope Guard

When a redirect exists, generated content is checked by a separate semantic guard before downstream acceptance.

The guard is designed to catch:

- operational instructions beyond the allowed scope
- API details that should remain non-public
- translation that broadens the public claim
- internal case history leaking into public documentation
- unsupported next steps

A violation blocks the topic.

---

## Human-in-the-Loop Design

Human involvement is used at boundaries where missing context or consequence justifies it.

Examples:

- missing operational facts
- ambiguous documentary action
- explicit public-scope decisions
- conflicting evidence
- exact preview approval
- live mutation authorization

The goal is precise escalation, not generic human dependence.

---

## Least Privilege

External adapters expose only the capabilities required by the workflow.

A safe documentation agent should not receive broad CMS authority when the required action is only a bounded draft update.

The design intentionally excludes automatic:

- publication
- deletion
- unrelated content mutation

---

## Immutable Artifacts

BIA uses immutable or conflict-detecting artifacts for critical state.

Examples include:

- clarification context
- reprocessing checkpoints
- semantic-resolution artifacts
- taxonomy snapshots
- pre-update article snapshots
- review artifacts
- recovery checkpoints

Stable identity lets the system detect when a retry is actually a conflict.

Historical checkpoints are preserved rather than rewritten when later semantics evolve.

---

## Cross-Run Safety

A previous Human Clarification outcome should not be reused merely because the topic name looks similar.

Reuse is allowed only when the later workflow can bind the current source semantics to the exact earlier clarification context and persisted artifacts.

Ambiguity fails closed.

---

## Idempotency and Retry Safety

Retries are allowed only when their semantics are known.

### Safe pattern

- deterministic identity
- read current state
- compare expected state
- perform one bounded mutation
- read back
- verify

### Unsafe pattern

- API call times out
- assume it failed
- repeat the write blindly

BIA is designed to avoid the second pattern.

---

## Preservation-First Updates

An update should preserve the existing article unless an explicitly reviewed change says otherwise.

This is a safety property, not merely an editorial preference.

The workflow treats the previous article as the baseline and adds only the supported delta.

---

## Human Approval

A preview and an execution authorization are separate concepts.

Review answers:

> Is this exact candidate content correct?

Execution authorization answers:

> May the system perform the consequential external mutation now?

Keeping these distinct prevents stale approval from becoming indefinite write authority.

A successful test preview may explicitly end in **NO_PUBLICATION**.

---

## Prompt Injection

External sources are treated as untrusted data.

Instructions found inside:

- release notes
- web pages
- email-like content
- imported documents
- old Help Center articles

must not redefine:

- system instructions
- permissions
- credential policy
- mutation authority
- safety constraints

---

## Observability

Observability is a safety prerequisite.

Important operational events should allow reviewers to reconstruct:

- what stage ran
- what evidence path was used
- why a topic was blocked
- which locale was affected
- what stable failure/result code occurred
- whether a mutation occurred
- whether read-back verification succeeded
- where the workflow stopped

Diagnostics should be structured and allow-listed.

Blocked or sensitive generated content should not be logged merely for debugging convenience.

---

## Recovery as a Safety Feature

Recovery is not only reliability engineering.

It is also a safety mechanism.

A durable clarification context prevents a retry from producing a subtly different question after partial failure.

An immutable semantic-resolution artifact lets the system evolve interpretation without rewriting historical checkpoints.

A pre-update snapshot prevents recovery from depending on memory or regenerated guesses.

---

## Public Disclosure Boundary

This repository intentionally omits:

- live credentials
- OAuth material
- internal IDs
- production prompts
- exact production schemas
- private Help Center content
- private article identifiers
- internal source locations
- proprietary operational heuristics

The public project documents engineering patterns without weakening the private system.
