# Suite architecture decisions

The architecture decisions that bind every Arc repository, and the template for new records.
Product repositories record their own decisions in their `docs/adr/` folders and link here rather
than restating these.

## Index

| ADR | Title | Status |
|---|---|---|
| [0001](0001-polyrepo-with-one-foundation-and-a-single-suite-baseline.md) | Polyrepo with one foundation and a single suite baseline | Accepted |
| [0002](0002-native-ui-egui-on-wgpu-and-winit.md) | Native UI: egui on wgpu and winit | Accepted |
| [0003](0003-geometry-owned-api-two-tiers.md) | Geometry: owned API, two tiers | Accepted |
| [0004](0004-document-model-typed-objects-stable-ids-transactions-operation-journal.md) | Document model: typed objects, stable IDs, transactions, operation journal | Accepted |
| [0005](0005-document-package-one-sqlite-file-per-document.md) | Document package: one SQLite file per document | Accepted |
| [0006](0006-one-command-model-one-automation-protocol.md) | One command model, one automation protocol | Accepted |
| [0007](0007-regeneration-dependency-graph-and-revision-tagged-background-work.md) | Regeneration: dependency graph and revision-tagged background work | Accepted |
| [0008](0008-units-precision-and-coordinates.md) | Units, precision and coordinates | Accepted |
| [0009](0009-interoperability-policy.md) | Interoperability policy | Accepted |
| [0010](0010-rendering-through-the-arc-scene-contract.md) | Rendering through the Arc Scene contract | Accepted |
| [0011](0011-collaboration-offline-first-hub-optional-staged-multi-user.md) | Collaboration: offline first, Hub optional, staged multi-user | Accepted |
| [0012](0012-extensibility.md) | Extensibility | Accepted |
| [0013](0013-licensing-and-contribution-terms.md) | Licensing and contribution terms | Accepted |
| [0014](0014-assistance-is-optional-and-provider-neutral.md) | Assistance is optional and provider neutral | Accepted |
| [0015](0015-robustness.md) | Robustness | Accepted |
| [0016](0016-localization-and-accessibility.md) | Localization and accessibility | Accepted |
| [0017](0017-exchange-connectors-and-licensed-bridges.md) | Exchange connectors and licensed bridges | Accepted; amends 0009 |
| [0018](0018-web-and-mobile-targets.md) | Web and mobile targets from the same Rust code | Accepted; clarifies 0002 |

## Rules

- A suite ADR is created, superseded or withdrawn only through an accepted [RFC](../rfcs/README.md).
- Numbers are sequential and never reused. Accepted records are not edited in substance; a change is a
  new ADR that supersedes the old one, and both records link to each other.
- Each ADR names the trigger that reopens it.
- Status values: Proposed, Accepted, Superseded by ADR-NNNN, Withdrawn.

## Template

```markdown
# ADR-NNNN: <Title>

<One sentence stating the decision.>

- **Status:** Proposed | Accepted | Superseded by ADR-NNNN | Withdrawn
- **Scope:** <repository or suite>

## Context

<The forces and constraints that require a decision.>

## Decision

<What is decided, precisely enough to check compliance.>

## Consequences

<What becomes easier, what becomes harder, what must now be done.>

## Alternatives considered

<Each alternative and why it was not chosen.>

## Trigger to reopen

<The observable condition that would justify revisiting the decision.>
```
