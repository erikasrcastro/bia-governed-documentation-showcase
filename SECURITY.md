# Security and Disclosure Policy

## Purpose

This repository is a public architecture and engineering showcase for BIA.

It is intentionally separated from the private production implementation.

---

## Not Included

The public repository must not contain:

- API keys
- OAuth client secrets
- refresh tokens
- passwords
- cookies or sessions
- private CMS credentials
- private Google Drive or Sheets identifiers
- internal article IDs
- private source-channel identifiers
- customer or employee personal data
- internal Help Center content
- production system prompts
- exact private operational schemas
- proprietary deployment configuration
- private recovery artifacts

---

## Sanitization Rule

Examples in this repository must be fictionalized or generalized.

Public examples may demonstrate:

- workflow structure
- state transitions
- safety decisions
- evidence provenance patterns
- Human Clarification behavior
- auditability concepts

They must not contain operational secrets from the private system.

---

## External Content

Any external content ingested by an agent should be treated as untrusted data.

Retrieved text must never be allowed to redefine:

- system instructions
- permissions
- credential policy
- mutation authority
- safety constraints

---

## Responsible Disclosure

If you believe this public repository accidentally exposes sensitive production information, please report it privately to the repository owner rather than opening a public issue containing the sensitive material.

---

## Production Boundary

The complete BIA implementation is maintained separately in a private repository.

This showcase exists to demonstrate architecture and engineering patterns without exposing production intellectual property or credentials.
