# ADR-0011: Collaboration: offline first, Hub optional, staged multi-user

Collaboration is optional and delivered by Arc Hub in two stages: file-level versioning and review
first, element-level multi-user editing second, without CRDTs and without ever discarding work.

- **Status:** Accepted
- **Scope:** Suite

## Context

Design teams share models, review them, track issues and approvals and, on larger projects, edit the
same model concurrently. Principle 5 requires every application to be complete with no network and no
account. Organizations often require that project data stays on their own infrastructure. Concurrent
editing of design models must preserve design invariants that span many elements.

## Decision

- **Stage 1:** file-level versioning with check-out locks, lifecycle states, reviews, issues and web
  viewing through Arc Hub.
- **Stage 2:** element-level multi-user editing. Users borrow elements; transactions travel as change
  sets from the operation journal; the server orders change sets.
- No CRDT for model data.
- A conflict never discards work: both versions are kept and compared, and the user resolves.
- Arc Hub is a single Rust server: axum, PostgreSQL, S3-compatible or file-system blob storage, OpenID
  Connect. Its web client is server-rendered and embeds the Arc Review viewer compiled to WebAssembly;
  this browser client is the single documented exception to the no-JavaScript principle for desktop
  applications.
- Self-hosting is the default deployment.

## Consequences

- Collaboration value arrives early (stage 1) without changes to the document model.
- Stage 2 reuses the operation journal ([ADR-0004](0004-document-model-typed-objects-stable-ids-transactions-operation-journal.md))
  and keeps every accepted model state valid.
- Borrowing requires a connection to modify unborrowed elements; offline edits to unborrowed elements
  may become alternates that need resolution.
- The Hub API must version change-set and borrowing endpoints carefully, since clients of different
  versions may meet in one project.
- The design is detailed in [collaboration](../architecture/collaboration.md).

## Alternatives considered

- **Mandatory cloud service.** Conflicts with principle 5 and with organizations' data requirements.
- **CRDT-based model documents.** Automatic merges produce well-formed but invalid designs silently.
- **File-level locking only, permanently.** Insufficient for large projects where many people work on
  one model.
- **Peer-to-peer collaboration without a server.** No single ordering authority, harder permissions
  and audit.

## Trigger to reopen

Element borrowing proves unworkable for a documented, frequent workflow, or users of stage 2 regularly
face conflicts that borrowing was meant to prevent.
