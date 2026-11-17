# ADR-0009: Interoperability policy

Every format is supported at a stated fidelity level per direction, imports keep the original and
report every loss, exports are validated independently, and format support comes only from published
specifications and permissively licensed libraries.

- **Status:** Accepted
- **Scope:** Suite

## Context

Design work depends on exchange with other parties and other software. Users must know what an
exchange preserves; silent losses destroy trust and cause design errors. Some widely used formats have
published specifications, some are served only by libraries with restrictive licenses, and some have no
published specification at all. The project's ability to distribute its code depends on how format
support is obtained.

## Decision

- Three fidelity levels are stated for every format and direction: **view** (geometry and appearance),
  **data** (semantics, properties, relationships) and **edit** (native, parametric editing).
- Imports keep the original file, keep unmodeled content as preserved foreign data, map source
  identifiers to Arc identifiers, and produce a loss report.
- Exports are validated by an independent reader in CI.
- Format support comes only from published specifications and permissively licensed libraries. A
  library under a restrictive or uncertain license is isolated behind a byte-level bridge in its own
  process and is never required for a core workflow.
- Proprietary formats without a published specification (for example RVT) are exchanged through open
  formats; they are never reverse engineered.
- Format crates in `arc-core` produce a neutral model of the format; each product owns the mapping into
  its domain model.

## Consequences

- Each product publishes a fidelity table, and corpus tests back every claimed level
  ([interoperability](../architecture/interoperability.md)).
- Users always see what an exchange changed, and re-imports update rather than duplicate.
- Documents grow by the preserved original and foreign data; content addressing limits duplication.
- Some formats are supported only through open-format routes, which requires clear user guidance.
- CI needs independent readers for validation, run as separate processes and never shipped.

## Alternatives considered

- **Best-effort import without loss reporting.** Simpler, but losses go unnoticed until they cause
  errors.
- **Linking restrictive-license libraries for broader format coverage.** Would make the suite
  undistributable under its license or bind core workflows to an external license.
- **Reverse engineering undocumented formats.** Legal and contractual risk, and fragile across format
  revisions.

## Trigger to reopen

The owner of a proprietary format publishes a specification under terms that permit implementation,
or a permissively licensed implementation becomes available.
