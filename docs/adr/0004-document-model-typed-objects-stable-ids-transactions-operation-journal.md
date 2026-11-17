# ADR-0004: Document model: typed objects, stable IDs, transactions, operation journal

A document is a store of typed objects with stable 128-bit identifiers, changed only through
transactions whose element-level operations are recorded in an operation journal.

- **Status:** Accepted
- **Scope:** Suite

## Context

Every product needs undo and redo, history, autosave recovery, model comparison and, later,
multi-user editing. References between objects, IFC GlobalIds, issues and links from Arc Hub require
identifiers that never change. Multi-user editing and model comparison require knowing exactly which
elements changed and how. Performance requires dense in-memory access and cheap snapshots for
background work.

## Decision

- A document is a store of typed objects. Each object has a stable 128-bit identifier (time-ordered
  UUID) that never changes and is never reused; in memory, objects are addressed through dense handles
  for speed.
- All changes happen inside transactions produced by commands. A transaction either commits as a whole
  or leaves the document unchanged.
- A transaction records element-level operations: create, modify and delete, with before and after
  state. The operation journal drives undo and redo, history, change comparison, autosave recovery
  and, later, multi-user change sets.
- Object tables use structural sharing, so snapshots for background work, undo and comparison are
  cheap.
- The store is not an entity-component system; objects are typed domain objects with explicit schemas.

## Consequences

- Undo, history, recovery, comparison and collaboration share one mechanism.
- Every reference, issue and external link uses the stable identifier and survives editing, saving and
  exchange.
- Workers read immutable snapshots without locking the editing thread.
- Every object type needs a schema with a version, and changes to it need migrations.
- Journal size must be managed: history compaction and bounds on retained undo depth are part of
  `arc-doc`.
- Commands must express their changes as operations on objects, which rules out hidden side effects.

## Alternatives considered

- **Whole-document snapshot undo only.** Simple, but it gives no element-level change information for
  comparison or collaboration and scales poorly with model size.
- **CRDT documents.** Automatic merging at data-structure level does not preserve cross-element design
  invariants; see [collaboration](../architecture/collaboration.md#what-is-deliberately-not-done).
- **Ad-hoc mutation with observers.** No reliable undo, recovery or change record.
- **An entity-component system.** Suited to simulation loops, not to typed domain objects with
  schemas, migrations and references.

## Trigger to reopen

A product's representative workflow cannot meet its performance budget with structural sharing and
dense handles, or element-level operations prove insufficient to express a required change.
