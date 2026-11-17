# ADR-0005: Document package: one SQLite file per document

A native Arc document is a single SQLite database with a documented schema, saved atomically on top of
a crash-safe recovery journal.

- **Status:** Accepted
- **Scope:** Suite

## Context

Native documents hold large models, binary assets, history and preserved foreign data. They must
survive crashes and power loss, load partially for large models, save atomically, and remain readable
for decades, including without Arc (principle 6). Design projects routinely mix software versions, so
compatibility across versions is essential.

## Decision

- A native document is a single SQLite database with a documented schema containing: metadata, object
  tables (typed payloads, each with its own schema version), the operation journal, content-addressed
  assets, thumbnails and preserved foreign data.
- Applications use explicit-save semantics on top of a crash-safe recovery journal. Saves are atomic:
  after a save, the file holds either the previous or the new state, never a mixture.
- **Compatibility promise:** a document written by any released version opens in every later version.
  An older application opens a newer minor format read-only where possible and refuses a newer major
  format without touching the file.
- DraftBuilder is the exception in spirit: its native model is the DWG/DXF drawing database and it
  saves `.dwg` and `.dxf` directly. It uses the package for sidecar data the drawing database cannot
  hold.
- The package schema and conformance suite are specified in `arc-core` with `arc-doc`
  ([contracts](../architecture/contracts.md#arc-document-package)).

## Consequences

- Saves are atomic and partial loading is possible for large models.
- Anyone can inspect a document with standard SQLite tools and the published schema.
- Assets are deduplicated across versions of the same document by content addressing.
- Every schema change needs a forward migration and a test from every frozen version.
- Very large binary assets need chunking to keep the database efficient; `arc-doc` provides it.
- Concurrent writers on one file are not supported; collaboration goes through Arc Hub.

## Alternatives considered

- **Zipped trees of JSON or XML files.** Easy to inspect but slow, with all-or-nothing saves and no
  partial loading.
- **A custom binary container.** Full control, but every guarantee (atomicity, recovery, indexing)
  would have to be built and documented, and no standard tool could read it.
- **A directory of files per document.** Fragile to copy, sync and email; atomic saves across files
  are difficult.

## Trigger to reopen

SQLite cannot meet a product's load, save or size budget on its reference models after schema and
chunking optimization.
