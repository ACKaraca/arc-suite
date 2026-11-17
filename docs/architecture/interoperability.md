# Interoperability

The suite-wide view of file-format support, the policy every importer and exporter follows, how
formats without a published specification are exchanged, and how support is validated. Each
product's `docs/architecture/interoperability.md` is authoritative for that product's details.

## Fidelity levels

Every format is stated per product and per direction at one of three levels
([ADR-0009](../adr/0009-interoperability-policy.md)):

| Level | Code | Meaning on import | Meaning on export |
|---|---|---|---|
| View | V | Geometry and appearance are shown and can be measured, sectioned and referenced | Geometry and appearance are written |
| Data | D | Semantics, properties, classifications and relationships are kept and queryable | Semantics, properties and relationships are written as well as geometry |
| Edit | E | Content becomes native objects that can be edited parametrically | The format is written as a native working format with no loss of the product's own data |

## Suite format matrix

Target fidelity, as `R:` (import) and `W:` (export). A dash means the product does not handle the
format directly; the product documents name the route instead.

| Format | ArchBuilder | DraftBuilder | CivilBuilder | SolidBuilder | MeshBuilder | RenderBuilder | Arc Review | Arc Hub |
|---|---|---|---|---|---|---|---|---|
| IFC 2x3, 4, 4.3 | R:E W:D | R:V | R:D W:D | W:D | R:V | — | R:D | R:V |
| DWG | R:V W:D | R:E W:E | R:D W:D | R:V W:D | R:V | — | R:D | R:V |
| DXF | R:V W:D | R:E W:E | R:D W:D | R:V W:D | R:V | — | R:D | R:V |
| STEP AP203/214/242 | R:V W:V | R:D W:D | — | R:E W:E | R:V | — | R:D | R:V |
| IGES | — | R:V | — | R:D W:D | — | — | R:V | — |
| glTF 2.0 | R:V W:V | R:V W:V | R:V W:V | R:V W:V | R:E W:E | R:V | R:V W:V | R:V |
| OBJ, STL, PLY, 3MF | R:V W:V | R:V W:V | R:V W:V | R:V W:V | R:E W:E | R:V | R:V | R:V |
| OpenUSD | W:V | — | W:V | W:V | R:E W:E | R:D W:D | R:V | — |
| MaterialX | — | — | — | — | R:E W:E | R:E W:E | — | — |
| DGN V7 | — | R:D | — | — | — | — | — | — |
| 3DM | — | R:V | — | — | — | — | — | — |
| FBX (open reader) | — | — | — | — | R:D | — | — | — |
| LandXML | R:V | R:V | R:E W:E | — | — | — | R:D | — |
| GIS vector (GeoPackage, Shapefile, FlatGeobuf) | R:V | R:D W:D | R:D W:D | — | — | — | R:V | — |
| GIS raster (GeoTIFF) | R:V | R:V | R:D | — | — | — | R:V | — |
| Point clouds (LAS/LAZ, E57) | R:V | R:V | R:D | — | R:V | — | R:V | — |
| BCF | R:D W:D | R:D W:D | R:D W:D | R:D W:D | — | — | R:D W:D | R:D W:D |
| IDS | R:D | — | R:D | — | — | — | R:D | R:D |
| PDF | R:V W:V | R:D W:V | R:V W:V | R:V W:V | — | — | R:V W:V | R:V |
| SVG | W:V | R:V W:V | W:V | W:V | — | — | W:V | — |
| Images (OpenEXR, PNG, JPEG, TIFF) | R:V W:V | R:V W:V | R:V W:V | R:V W:V | R:D W:D | R:D W:D | R:V W:V | R:V |
| Machine programs (G-code via post-processors) | — | — | — | W:D | — | — | — | — |
| Arc Scene | W:D | W:D | W:D | W:D | W:D | R:E | W:D | — |
| Arc document packages of other products | — | — | R:V | — | R:V | R:V | R:D | R:V |
| Arc exchange package | R:D W:D | R:D W:D | R:D W:D | R:D W:D | R:D W:D | R:D | R:D W:D | R:V |

Interchange formats are read and written by the `arc-io-*` crates in `arc-core`, which produce a
neutral model of each format. Each product maps that model to and from its own domain model.

## Interoperability policy

1. **Fidelity is stated.** Each format and direction has a target level in the product's
   interoperability document. A claim of a level is backed by corpus tests.
2. **The original is kept.** An imported file is stored unchanged alongside the document (as a
   content-addressed asset or a referenced file), so the import can be repeated or audited.
3. **Unmodeled content is preserved.** Content the product cannot represent is kept as preserved
   foreign data, attached to the nearest native object, and written back on export to the same
   format where the format allows.
4. **Identifiers are mapped.** Source identifiers (IFC GlobalIds, DWG handles, STEP entity names)
   are recorded against Arc identifiers, so re-imports update objects instead of duplicating them and
   issues stay attached.
5. **Every loss is reported.** Each import and export produces a loss report: objects dropped,
   approximated, converted or preserved without interpretation, with counts and identifiers. The
   report is shown to the user, written by the CLI as structured data, and stored with the document.
6. **Exports are validated independently.** CI checks exported files with an independent reader:
   a schema validator, a separate open-source reader run as a separate process, or a second
   implementation. An export that only Arc can read does not count as supported.
7. **Sources are published.** Format support is implemented only from published specifications,
   permissively licensed libraries and the official public APIs of other applications. A library
   under a restrictive or uncertain license is isolated behind a byte-level bridge in its own process
   and is never required for a core workflow.
8. **No reverse engineering.** Proprietary formats without a published specification are exchanged
   through connectors, licensed bridges or open formats
   ([ADR-0017](../adr/0017-exchange-connectors-and-licensed-bridges.md)). Reverse engineering
   undocumented formats or binaries, running compiled or protected script files of other
   applications, and codecs whose license forbids open distribution stay excluded because each is a
   legal problem ([clean-room policy](../engineering/clean-room-policy.md)).
9. **Units and coordinates are explicit.** Every import records the source units, axis convention
   and coordinate reference; conversions happen once, at the boundary
   ([ADR-0008](../adr/0008-units-precision-and-coordinates.md)).
10. **Hostile input is assumed.** Importers bound recursion, allocation and entity counts and are
    fuzzed ([coding standards](../engineering/coding-standards.md#parsers-and-hostile-input)).

## Exchange routes for proprietary formats

Proprietary formats reach Arc by four routes
([ADR-0017](../adr/0017-exchange-connectors-and-licensed-bridges.md)). Each route produces the same
identifier mapping, preserved foreign data and loss report as any other import or export.

| Route | How it works | Fidelity |
|---|---|---|
| Connector | An add-in in the `arc-connectors` repository runs inside the user's own licensed copy of the other application, uses only that application's official public API, and reads or writes the Arc exchange package | Data in both directions; edit where the host API exposes parametric definitions |
| Licensed bridge | An Arc product calls a conversion library that the user has installed and licensed, as a separate process; the library is never distributed with Arc and never required for a core workflow | As stated by the product for each bridge |
| Open reader | A format with a permissively licensed open-source reader or a published specification (for example 3DM, FBX through an open reader, DGN V7) is read natively behind an `arc-io-*` crate | View or data, stated per product |
| Open format | The other application exports an open format and Arc imports it, or the reverse | As stated for the open format |

### Arc exchange package

The Arc exchange package is an open, documented container for exchange between Arc and other
applications. It carries geometry, semantics, parameters, types, identifiers and relationships,
together with units, coordinate reference and the source application's identifiers, so that
re-exchanges update instead of duplicating. It is a contract owned by `arc-core`
([contracts](contracts.md#arc-exchange-package)); its specification is in the
[arc-core interchange architecture](https://github.com/ACKaraca/arc-core/blob/main/docs/architecture/interchange.md).
Products read and write it through the `arc-io-*` layer and map it into their own domain models;
connectors read and write it inside the other application.

### Routes by source data

| Source data | Route into Arc | Route out of Arc |
|---|---|---|
| Proprietary BIM authoring files (for example RVT) and their loadable components | Connector; IFC 4 or 4.3 export with an IDS file describing the required information; DWG for 2D views | Connector; IFC, DWG, PDF |
| Proprietary mechanical CAD part and assembly files | Connector, with feature trees where the host API exposes them; STEP AP242 with product structure and, where available, semantic tolerancing | Connector; STEP AP242, PDF drawings |
| Proprietary animation interchange (for example FBX) | Open reader; OpenUSD or glTF | OpenUSD, glTF |
| Proprietary scene files of content-creation tools | Connector; OpenUSD with MaterialX materials | Connector; OpenUSD |
| Freeform surface models (3DM) and DGN V7 drawings | Open reader | Open formats |
| Proprietary review and federation files | The underlying model files (IFC, DWG, STEP) plus BCF for issues; export from authoring applications through connectors | BCF, IFC, PDF reports; switch-back to the authoring application through its connector |
| Proprietary point-cloud project formats | E57 or LAS/LAZ | E57, LAS/LAZ |
| Proprietary civil project files | Connector for design objects; LandXML and IFC 4.3, plus DWG for drawing content | Connector; LandXML, IFC 4.3, DWG |

The receiving product documents which exporter settings and connector options preserve the most
information; those notes are written from the open format's specification, the host's public API
reference and observed results, never from vendor documentation text.

## Validation and corpora

**Corpus repositories.** Interchange test files live in separate repositories named
`arc-corpus-<domain>` (for example `arc-corpus-dxf`, `arc-corpus-ifc`), created when the first
importer of the domain lands. Each file has a ledger row: path, owner, SPDX license, origin, SHA-256
and a description of what it exercises. Admission rules are in the
[clean-room policy](../engineering/clean-room-policy.md#corpus-admission); vendor sample files are never admitted.

**Pinning.** Each consuming repository pins a corpus commit. Tests locate the corpus by a configured
path; locally they skip with a clear message when it is absent, and in CI its presence is required.

**Round-trip tests.** For each format and direction at level D or E, the corpus round-trip test
imports, exports, re-imports and compares: object counts by type, identifiers, properties and
geometry within tolerance. The resulting loss report is stored as a snapshot; any change to it is
reviewed like code.

**Oracles.** Independent readers used to validate exports run as separate processes in CI and are
never linked or shipped. Their source code is never consulted.

**Fuzzing.** Every parser has a fuzz target seeded from the corpus; crashes found become corpus or
unit test cases.

Test layers and CI scheduling are in [testing strategy](../engineering/testing-strategy.md).
