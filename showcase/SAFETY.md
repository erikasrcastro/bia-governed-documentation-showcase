# BIA Safety and Governance

## Safety Objective

BIA operates in a domain where plausible language can create real operational harm.

A documentation error can cause customers to:

- send the wrong API request
- misconfigure a product
- misunderstand prerequisites
- lose trust in the Help Center
- follow obsolete or unsupported instructions

The system therefore treats factual uncertainty and external mutation as governance problems, not only prompt-engineering problems.

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
- cross-locale contamination
- stale target articles
- duplicate Human Clarification questions
- partial persistence
- duplicate retries after uncertain writes
- content loss during article updates
- silent metadata drift
- accidental publication
- deletion
- source prompt injection
- mutation after the reviewed state has changed

---

## Fail-Closed Defaults

BIA stops instead of guessing when:

- evidence is missing
- evidence conflicts
- the target cannot be identified exactly
- locale isolation cannot be proven
- a required immutable artifact cannot be verified
- a Human Clarification context conflicts with durable state
- a write outcome is uncertain
- the remote read-back does not match the expected result
- the reviewed artifact is no longer bound to the current state

---

## Evidence Safety

### Authorized sources only

Only configured, approved source types may contribute factual evidence.

### Existing documentation is not authority

An old Help Center article may be useful for:

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

This gate prevents the agent from filling in details such as:

- API routes
- methods
- parameters
- UI labels
- permissions
- prerequisites
- expected outcomes
- limits
- error handling

unless the authorized evidence supports them.

---

## Human-in-the-Loop Design

Human involvement is not used everywhere.

It is used at boundaries where missing context or consequence justifies it.

Examples:

- missing operational facts
- ambiguous documentary action
- conflicting evidence
- exact preview approval
- live mutation authorization

The goal is precise escalation, not generic human dependence.

---

## Least Privilege

External adapters expose only the capabilities needed by the workflow.

A safe documentation agent should not receive broad CMS authority when the required operation is only a bounded draft update.

The current design intentionally excludes automatic:

- publication
- deletion
- unrelated content mutation

---

## Immutable Artifacts

BIA uses immutable or conflict-detecting artifacts for critical state.

Examples include:

- clarification context
- taxonomy snapshots
- pre-update article snapshots
- review artifacts
- recovery checkpoints

Stable identity lets the system detect when a retry is actually a conflict.

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
- send the same write again blindly

BIA is designed to avoid the second pattern.

---

## Preservation-First Updates

An update should preserve the existing article unless an explicitly reviewed change says otherwise.

This is a safety property, not merely an editorial preference.

The workflow therefore treats the old article body as the baseline and adds the supported delta.

---

## Human Approval

A preview and an execution authorization are separate concepts.

Review answers:

> Is this exact candidate content correct?

Execution authorization answers:

> May the system perform the consequential external mutation now?

Keeping these distinct prevents stale approval from becoming indefinite write authority.

---

## Prompt Injection

External sources are treated as untrusted data.

Instructions found inside:

- release notes
- web pages
- email-like content
- imported documents
- old Help Center articles

must not redefine the agent’s system rules or permissions.

---

## Observability

A safe agent must make its decisions reconstructable.

Important operational events should allow reviewers to understand:

- what evidence was used
- why a topic was blocked
- what clarification was requested
- which immutable artifact was bound to the run
- whether a mutation occurred
- whether read-back verification succeeded
- where the workflow stopped

---

## Recovery as a Safety Feature

Recovery is not only reliability engineering.

It is also a safety mechanism.

A durable clarification context, for example, prevents a retry from producing a subtly different question after a partial failure.

Likewise, a pre-update snapshot prevents recovery from depending on memory or regenerated guesses.

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
