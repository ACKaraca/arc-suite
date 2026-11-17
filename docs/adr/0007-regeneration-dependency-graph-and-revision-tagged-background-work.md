# ADR-0007: Regeneration: dependency graph and revision-tagged background work

Derived data is computed by a dependency graph owned by `arc-doc`, on cancellable worker threads, and
every result carries the document revision it was computed from.

- **Status:** Accepted
- **Scope:** Suite

## Context

Arc documents derive large amounts of data from their models: element geometry, joins between
elements, views, schedules, toolpaths, corridor sections, render scenes. Recomputing everything after
each change does not scale; recomputing on the UI thread freezes the application; applying a result
computed from an older state corrupts what the user sees. Every product faces this, so it belongs in
the foundation.

## Decision

- Derived data is computed by a dependency graph owned by `arc-doc`. Nodes declare which objects and
  other derived data they read; a committed transaction marks exactly the dependent nodes stale.
- Stale nodes are recomputed incrementally on worker threads. Work is cancellable: a newer revision
  cancels work that has become obsolete.
- Every result carries the document revision it was computed from. A result whose revision is older
  than the revision it would be applied to, for data that has since changed, is never applied.
- Regeneration is deterministic: the same revision yields the same result regardless of thread count
  or scheduling.
- Interactive previews may use a lighter computation, but committed state always comes from the graph.

## Consequences

- The UI stays responsive during large regenerations, and progress is visible.
- Products declare dependencies explicitly, which makes regeneration testable: a test asserts which
  nodes recompute after a change.
- Cycles in the graph are errors detected at registration or when a change introduces them, and are
  reported to the user with the objects involved.
- Workers operate on immutable snapshots, relying on structural sharing in the document store
  ([ADR-0004](0004-document-model-typed-objects-stable-ids-transactions-operation-journal.md)).
- Derived data is never stored as a source of truth; it may be cached in the package for faster
  opening and is discarded when its revision does not match.

## Alternatives considered

- **Recompute everything after each change.** Simple and correct, but unacceptable for large models.
- **Synchronous recomputation on the UI thread.** Freezes the application during large updates.
- **Observer callbacks per object.** Order-dependent, hard to cancel, and prone to applying stale
  results.
- **A general incremental-computation framework as the core.** Considered as an implementation aid; a
  graph owned by `arc-doc` keeps control of revisions, cancellation and persistence.

## Trigger to reopen

A product's dependency structure cannot be expressed without excessive fine-grained nodes that cost
more than the recomputation they save, or determinism cannot be maintained for a required parallel
algorithm.
