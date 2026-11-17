# ADR-0014: Assistance is optional and provider neutral

An assistant panel may propose and run the same commands a user can, through the automation protocol,
with preview and explicit apply; the provider is configured by the user, no feature depends on it, and
no document content leaves the machine without an explicit user action.

- **Status:** Accepted
- **Scope:** Suite

## Context

Language-model assistants can help users with multi-step tasks, queries over models and automation.
They also introduce dependencies on external services, risks of unwanted changes, and risks of
disclosing confidential design data. Principle 5 requires full functionality without a network or an
account.

## Decision

- An assistant panel calls the same commands a user can, through the automation protocol.
- Every proposed change is previewed as a dry run and applied only on explicit user confirmation, as
  one undoable transaction.
- The provider is user configured: any endpoint implementing a supported, documented API, including
  locally running models.
- No product feature requires an AI service.
- No document content leaves the machine without an explicit user action; the panel shows what will be
  sent.
- Hosted assistant services are outside the open-source repositories.

## Consequences

- Assistants are bounded by the command registry, validation and session permissions like any other
  automation client ([security boundaries](../architecture/automation-and-assistance.md#security-boundaries)).
- Users and organizations choose providers according to their own data policies, including fully local
  operation.
- Products cannot rely on an assistant for any workflow; every assisted task is also possible manually
  or by script.
- The provider interface must be maintained as provider APIs evolve; supported APIs are documented in
  `arc-core`.

## Alternatives considered

- **A built-in hosted assistant.** Would require accounts and network access and send data to one
  provider.
- **Assistants that edit documents directly.** Bypass validation, undo and permissions.
- **No assistance at all.** Leaves a useful capability unavailable to users who want it, when it can be
  added safely on top of the command model.

## Trigger to reopen

A documented assistance workflow cannot be expressed through commands and previewed changes, or
provider APIs converge on an interface that changes the integration approach.
