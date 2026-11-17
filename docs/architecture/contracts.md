# Run-time contracts

The contracts through which Arc products, tools and servers cooperate without compile-time
dependencies on each other. Three contracts connect products: the Arc document package, the Arc
Scene contract and the Hub API. A fourth, the Arc Automation Protocol, connects every application to
scripts, pipelines and assistants. A fifth, the Arc exchange package, connects Arc to other
applications through connectors.

## Summary

| Contract | Purpose | Owner repository | Implementation crate | Transport |
|---|---|---|---|---|
| Arc document package | Storage of every native Arc document | `arc-core` | `arc-doc` | SQLite database file |
| Arc Scene | Photoreal rendering of any product's model | `arc-core` | `arc-scene` | Files on disk; streamed updates over local IPC |
| Hub API | Collaboration and data management | `arc-hub` (server), `arc-core` (types and client) | `arc-hub-api` | HTTPS (REST, JSON) and WebSocket |
| Arc Automation Protocol | Programmatic control of an application | `arc-core` | `arc-command` | JSON-RPC 2.0 over stdio or a local socket |
| Arc exchange package | Two-way exchange with other applications | `arc-core` | An `arc-io-*` crate | Files on disk, written and read by connectors and products |

The specification of each contract lives with its owner and is versioned with it. The owner
repository's maintainers are responsible for the specification, a conformance test suite, and
compatibility. A breaking change to any contract requires an [RFC](../rfcs/README.md).

## General compatibility rules

- Every contract carries an explicit version of the form `MAJOR.MINOR`.
- A minor version adds optional elements only: new optional fields, new message types, new object
  types that a reader may ignore or preserve. A reader of minor version *n* reads anything written at
  minor version *n* or lower and preserves or ignores what it does not understand from higher minor
  versions.
- A major version may remove or change meaning. Readers refuse a higher major version with a clear
  message and never modify the data.
- Every element that is removed is deprecated for at least one suite release first; deprecations are
  listed in the contract's changelog.
- Each contract has a conformance suite in its owner repository; every producer and consumer runs it
  in CI against the suite baseline.
- Contract versions are independent of application and crate versions.

## Arc document package

**Purpose.** One storage format for every native Arc document, readable without Arc
([ADR-0005](../adr/0005-document-package-one-sqlite-file-per-document.md)).

**Content.** A single SQLite database with a documented schema:

| Part | Content |
|---|---|
| Metadata | Format version, product and product format version, application version that wrote it, document identifier, units, coordinate reference record |
| Object tables | Typed objects addressed by stable 128-bit identifiers; each payload carries its own schema version |
| Journal | Operation journal: committed transactions with element-level operations |
| Assets | Content-addressed binary assets (textures, embedded images, point-cloud chunks), deduplicated |
| Thumbnails | Preview images for file browsers and Hub |
| Preserved foreign data | Imported content that the product does not model, kept for round trips, with its source identifiers |
| References | External references to other documents and files, by identifier and relative path |

**Versioning.** The package format has its own version, and each product's domain schema has its
own version on top of it. Schema migration code lives in `arc-doc` (package) and in each product's
`-model` crate (domain); migrations are forward only and tested against stored documents from every
released version.

**Compatibility promise.** A document written by any released version opens in every later version.
An older application opens a document of a newer minor format read-only where possible and refuses
a newer major format without touching the file.

**Cross-product use.** Products read each other's packages as references: for example, Arc Review
federates `.archb` documents and MeshBuilder references `.solidb` models. A product reading another
product's package uses the shared package layer and the published domain schema for what it
understands, and treats the rest as opaque. It never writes another product's package.

**Specification.** In `arc-core`, next to `arc-doc`: the package schema, the domain-schema
registration rules, and the conformance suite.

DraftBuilder writes DWG and DXF as its native drawing format and uses the package for sidecar data.

## Arc Scene contract

**Purpose.** A renderer-neutral description of what to render, so every product obtains photoreal
output from RenderBuilder without linking a path tracer
([ADR-0010](../adr/0010-rendering-through-the-arc-scene-contract.md)).

**Content.** Instances and their transforms, meshes and subdivision surfaces, curves and points,
materials aligned with OpenPBR and expressed as MaterialX-compatible graphs, textures with color
space metadata, lights, cameras, environments, render settings and AOV requests. The vocabulary is
aligned with OpenUSD so that a scene can be written as a USD layer without loss.

**Two transports.**

| Mode | Use | Form |
|---|---|---|
| Snapshot | Batch rendering, farm jobs, archival | An Arc Scene written to disk as OpenUSD layers plus assets |
| Live session | Interactive rendering from a host application | A session over local IPC: the host sends an initial scene and then deltas keyed by stable identifiers; the renderer returns progressive frames and AOVs |

**Versioning.** The session handshake exchanges contract versions and capabilities (supported
material features, AOVs, GPU availability). The host downgrades what the renderer does not support
and reports it to the user.

**Ownership.** The scene is derived from the host document and is never edited in RenderBuilder as
a source of truth; looks, overrides and render settings authored in RenderBuilder are stored in its
own document and reference host objects by identifier.

**Specification.** In `arc-core`, next to `arc-scene`, with the conformance scenes used by
RenderBuilder's image-comparison tests.

## Hub API

**Purpose.** Every interaction between an Arc client (desktop application, CLI, browser client,
third-party integration) and Arc Hub ([ADR-0011](../adr/0011-collaboration-offline-first-hub-optional-staged-multi-user.md)).

**Content.** Authentication through OpenID Connect; projects, folders and permissions; files and
versions with check-out locks; lifecycle states, reviews and approvals; issues and markups; items,
revisions and bills of materials; jobs; webhooks; and, in stage 2 of collaboration, element borrowing
and change-set exchange. Where an open standard exists, the Hub implements it alongside its own API:
the BCF API for issues and the openCDE APIs for document exchange.

**Versioning.** REST paths carry the major version (`/api/v1/...`). Minor versions add endpoints and
optional fields. A server supports the previous major version for at least one suite release after a
new major version ships. Clients discover the server's version and capabilities from a public
endpoint before signing in.

**Specification.** An OpenAPI document published by `arc-hub`; `arc-hub-api` in `arc-core` holds the
Rust types and client generated from or checked against it. The conformance suite runs the client
against a test server.

## Arc Automation Protocol

**Purpose.** Programmatic control of any Arc application through the same commands the user invokes
([ADR-0006](../adr/0006-one-command-model-one-automation-protocol.md)).

**Content.** JSON-RPC 2.0 messages:

| Group | Messages |
|---|---|
| Session | `initialize` (protocol version, client name, requested permissions), `shutdown` |
| Discovery | List commands with their parameter schemas, labels and enabled state; describe a command |
| Documents | Open, create, save, close, list open documents, query objects and properties (read only) |
| Commands | Run a command with parameters in a named document; dry-run (validate and preview without commit) |
| Events | Subscriptions to document changes, regeneration completion and progress |
| Transactions | Group several commands into one undoable transaction |

**Transports.** Standard input and output (for a process started by the client, including the
headless CLI) and a local socket (named pipe on Windows, Unix domain socket elsewhere) for a running
application. The protocol is not exposed on a network interface.

**Derived surfaces.** A Model Context Protocol endpoint is generated from the same command registry;
the official Python client is a thin library over the protocol.

**Versioning.** The protocol version is negotiated in `initialize`. Command identifiers are stable;
a command's parameter schema follows the same minor/major rules as the protocol, and a removed
command is deprecated for one suite release first.

**Specification.** In `arc-core`, next to `arc-command`, with a conformance suite that every product
CLI passes. Security boundaries for automated access are in
[automation and assistance](automation-and-assistance.md#security-boundaries).

## Arc exchange package

**Purpose.** An open, documented container through which connectors running inside other
applications and Arc products exchange data in both directions
([ADR-0017](../adr/0017-exchange-connectors-and-licensed-bridges.md)).

**Content.** Objects with their geometry, semantics, parameters, types and relationships; the
identifiers of the source application next to stable Arc identifiers, so that a repeated exchange
updates instead of duplicating; units, axis convention and coordinate reference; the name and version
of the writing application and connector; and the loss report of the exchange that wrote it.

**Producers and consumers.** Connectors in the `arc-connectors` repository write and read it inside
the other application. Arc products read and write it through the `arc-io-*` layer and map it to and
from their domain models like any other format; an import keeps the original package and produces a
loss report ([interoperability](interoperability.md#arc-exchange-package)).

**Versioning.** The general compatibility rules above apply. A connector declares the package
versions it writes and reads; a product refuses a higher major version without modifying the file.

**Specification.** In `arc-core`, in its
[interchange architecture](https://github.com/ACKaraca/arc-core/blob/main/docs/architecture/interchange.md),
with a conformance suite that every connector and every product reader runs.
