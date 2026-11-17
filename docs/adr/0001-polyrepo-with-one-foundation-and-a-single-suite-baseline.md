# ADR-0001: Polyrepo with one foundation and a single suite baseline

The suite is organized as one repository per product, one shared foundation repository and one suite
repository, and every product in a suite release ships on the same foundation tag.

- **Status:** Accepted
- **Scope:** Suite

## Context

Arc consists of eight products with different domain experts, different contributor communities and
different release cadences. They share hard infrastructure: geometry, document storage, commands,
rendering, UI and interchange. If each product owned its own copy of that infrastructure, the suite
would maintain eight divergent kernels and eight DXF readers. If all products shared one repository,
every product's history, CI load and review queue would be coupled to every other's. A shared
foundation consumed at different revisions by different products produces subtle incompatibilities
in documents and contracts.

## Decision

- One repository per product (`archbuilder`, `draftbuilder`, `civilbuilder`, `solidbuilder`,
  `meshbuilder`, `renderbuilder`, `arc-review`, `arc-hub`), one foundation repository (`arc-core`) and
  one suite repository (`arc-suite`).
- **Dependency rule:** products depend on `arc-core` and on third-party crates only. Products never
  depend on each other at compile time. They cooperate at run time through files and three documented
  contracts: the Arc document package, the Arc Scene contract and the Hub API.
- **Suite baseline:** `arc-core` is released as a whole under one version tag. Every product pins
  exactly one `arc-core` tag. A suite release names the single `arc-core` tag that all its products
  ship on; products never pin different foundation revisions within one suite release.
- Foundation crates are consumed as tagged git dependencies. Publishing to crates.io is deferred until
  an API is declared stable.
- Local development uses side-by-side checkouts with a Cargo `[patch]` override pointing products at
  the local `arc-core`.
- Interchange test files live in separate corpus repositories, created when the first importer of a
  domain lands.

## Consequences

- A product team works in a small repository with its own CI and release cadence.
- Shared code exists once; a fix in `arc-core` reaches every product with its next pin update.
- A change that needs a new foundation API takes two pull requests: one in `arc-core`, released under a
  tag, then one in the product that updates the pin. The baseline rule and local `[patch]` overrides
  keep this manageable.
- CI in each product verifies that all foundation crates resolve to one revision.
- Cross-product behavior must be expressed through the documented contracts, which keeps those
  contracts explicit and tested.
- Run-time cooperation without compile-time dependencies means some integration defects appear only
  in contract conformance and nightly cross-repository tests.

## Alternatives considered

- **One monorepo for the whole suite.** Atomic cross-cutting changes are simplest, but it couples eight
  products' histories, CI and contributor communities, and makes independent releases harder.
- **Fully independent repositories with no shared foundation.** Each product would reimplement the
  hardest parts, and documents and behavior would diverge.
- **Shared foundation pinned at arbitrary commits per product.** Avoids coordination but lets products
  ship on different foundation revisions, which breaks document and contract compatibility inside one
  release.

## Trigger to reopen

The foundation changes more often than weekly in ways that break products, so that pin updates become
a constant cost; or a cross-repository change regularly requires more than two coordinated pull
requests.
