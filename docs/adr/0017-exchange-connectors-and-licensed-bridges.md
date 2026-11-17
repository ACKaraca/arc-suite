# ADR-0017: Exchange connectors and licensed bridges

Data held in proprietary applications is exchanged through connectors that run inside the user's
licensed copy of that application, through licensed bridges the user installs, and through open
readers, all meeting at the Arc exchange package; reverse engineering stays excluded.

- **Status:** Accepted
- **Scope:** Suite
- **Amends:** [ADR-0009](0009-interoperability-policy.md)

## Context

[ADR-0009](0009-interoperability-policy.md) routes every proprietary format without a published
specification through open formats such as IFC, STEP and OpenUSD. Those routes depend on the other
application's exporter: parameters, types, relationships and identifiers that the exporter drops are
lost, and nothing returns to the source application except another open-format file. Many users
keep working in other applications alongside Arc and need both directions at data fidelity.

Most of those applications publish an official API for add-ins that runs inside the application. Some
vendors and consortia license conversion libraries for their formats. Some proprietary formats have
a permissively licensed open-source reader or a published specification. None of these routes
requires reverse engineering, which remains a legal problem the project does not take on.

## Decision

- **Connectors.** Data in a proprietary format without a published specification (for example RVT
  and other native BIM, CAD and content-creation project files) is exchanged by a connector: a small
  add-in that runs inside the user's own licensed copy of the other application, uses only that
  application's official public API, and reads or writes the Arc exchange package. Connectors carry
  geometry, semantics, parameters, types, identifiers and relationships in both directions, at data
  fidelity and, where the host API allows, edit fidelity.
- **Arc exchange package.** An open, documented container for exchange with other applications,
  owned by `arc-core` as a contract ([contracts](../architecture/contracts.md#arc-exchange-package))
  and specified in its
  [interchange architecture](https://github.com/ACKaraca/arc-core/blob/main/docs/architecture/interchange.md).
  Arc products read and write it through an `arc-io-*` crate like any other format, with the same
  identifier mapping, preserved foreign data and loss report.
- **The `arc-connectors` repository.** Connectors live in one separate repository, created with the
  first connector. Each connector is written in the language its host application requires. Connectors
  are not Arc desktop applications, so the rule that desktop applications are written in Rust does
  not apply to them; they contain no Arc product logic beyond mapping to and from the exchange
  package.
- **Licensed bridges.** Where a vendor or consortium licenses a conversion library for a format, an
  Arc product can call a copy that the user has installed and licensed, as a separate process. The
  library is never distributed with Arc and is never required for a core workflow.
- **Open readers.** A proprietary format for which a permissively licensed open-source reader or a
  published specification exists (for example 3DM, FBX through an open reader, DGN V7) is read
  natively behind an `arc-io-*` crate.
- **Still excluded.** Reverse engineering undocumented formats or binaries, running compiled or
  protected script files of other applications, and codecs whose license forbids open distribution
  remain outside the project, because each of them is the legal problem.

## Consequences

- Users exchange data with proprietary applications in both directions without depending on another
  vendor's exporter, and see a loss report for every exchange as with any other format.
- The open-format routes of [ADR-0009](0009-interoperability-policy.md) remain available and remain
  the default for formats with a published specification.
- `arc-core` owns one more contract, with a version, a changelog and a conformance suite that every
  connector and every product reader runs.
- Each connector depends on a host API that changes with the host's releases; the `arc-connectors`
  repository tests each connector against the host versions it declares and states them.
- Connector code is written against official public API documentation only; no vendor SDK code or
  samples are copied into Arc repositories ([clean-room policy](../engineering/clean-room-policy.md)).
- Licensed bridges need user guidance for installing and locating the licensed library, and every
  workflow that can use one also works without it.
- Product consequences: ArchBuilder, CivilBuilder, SolidBuilder and MeshBuilder gain connectors for
  native project files of their domains; DraftBuilder needs no connector for DWG and gains open
  readers for DGN V7 and 3DM; RenderBuilder gains host integrations, including a Hydra render
  delegate, so it renders inside other content-creation applications; Arc Review exports from
  authoring tools through connectors and switches back to them; Arc Hub builds its pipeline
  integrations inside other applications on the connectors ([products](../products.md)).

## Alternatives considered

- **Open formats only, as before.** Keeps the project simplest, but leaves parameters, types and
  identifiers to the other application's exporter and gives no route back.
- **Reverse engineering undocumented formats.** Legal and contractual risk, fragile across format
  revisions, and excluded by the [clean-room policy](../engineering/clean-room-policy.md).
- **Distributing licensed conversion libraries with Arc.** Their terms do not permit distribution
  under the Apache License and would bind core workflows to an external license.
- **Connectors inside each product repository.** Spreads host-language code and host-version testing
  across eight Rust repositories; one repository keeps that toolchain in one place.

## Trigger to reopen

A host application withdraws or restricts the public API a connector depends on, or the owner of a
proprietary format publishes a specification under terms that permit implementation, which makes a
native reader preferable to the connector.
