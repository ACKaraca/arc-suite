# Vision

Why the Arc suite exists, whom it serves, the principles every product follows, what the suite will
not do, and how success is recognized.

## Why the suite exists

Buildings, infrastructure, manufactured products and 3D content are designed with software that
professionals use for decades and that holds the most valuable record of their work. That record
should belong to the people who make it: readable without a particular vendor's software, usable
without a network connection or an account, and safe across many years of software versions.

Arc is a suite of native desktop applications and one collaboration server, open source under the
Apache License 2.0, built on one shared Rust foundation. It covers the design professions end to end:
authoring, drafting, engineering, visualization, review and data management. The products share one
document architecture, one command model, one viewport, one user-interface kit and one set of
interchange libraries, so a capability built once is available to every product, and a user who
learns one product finds the others familiar. The same code reaches the browser, tablets and
phones, and connectors carry data in both directions between Arc and the other applications a team
already uses.

## Who it serves

| Profession | Primary products | Typical deliverables |
|---|---|---|
| Architects and interior designers | ArchBuilder, DraftBuilder, RenderBuilder, Arc Review | Building models, drawings and sheets, schedules, visualizations, IFC exchange |
| Structural engineers and steel detailers | ArchBuilder, Arc Review | Structural models, analytical models, reinforcement and connection details, fabrication outputs |
| Building-services engineers | ArchBuilder, DraftBuilder | Duct, pipe and electrical systems, sizing, coordinated services drawings |
| Drafters and CAD technicians in all disciplines | DraftBuilder | 2D drawings, details, layouts, plotted sets, DWG exchange |
| Civil engineers and surveyors | CivilBuilder, Arc Review | Survey data, terrain models, road and rail alignments, corridors, earthworks, networks, plan production |
| Mechanical engineers and product designers | SolidBuilder, RenderBuilder | Parts, assemblies, drawings with tolerances, manufacturing programs, simulation results |
| 3D artists, animators and technical directors | MeshBuilder, RenderBuilder | Models, rigs, animation, effects, rendered images and sequences |
| Look-development and lighting artists | RenderBuilder | Materials, lighting setups, final frames |
| BIM coordinators and construction managers | Arc Review, Arc Hub | Federated models, clash reports, 4D sequences, quantities, issues |
| Project, document and data managers | Arc Hub | Controlled document sets, approvals, transmittals, revisions, bills of materials |
| Production coordinators | Arc Hub | Task tracking, review sessions, version history |
| Developers and automation engineers | All products | Scripts, node graphs, plug-ins, headless pipelines |

## Principles

**1. Native Rust end to end.** Every desktop application is a native program written in Rust, with
no web view shell and no JavaScript or TypeScript. One language across the foundation and products
means one toolchain, one set of safety guarantees and code that moves freely between crates. The
same Rust crates and UI kit also build for the browser, through WebAssembly, and for tablets and
phones; these are targets of the same code, not separate applications
([ADR-0018](adr/0018-web-and-mobile-targets.md)). Arc Hub's server-rendered browser client is the
single documented exception to the no-JavaScript rule, because a web browser is its delivery
platform; it reuses the Rust viewer compiled to WebAssembly. Connectors that run inside other
applications are written in the language their host requires, because they are not Arc
applications ([ADR-0017](adr/0017-exchange-connectors-and-licensed-bridges.md)).

**2. One foundation, many products.** Geometry, document storage, commands, rendering, the UI kit
and interchange live once, in `arc-core`. A product repository contains only its domain. A defect in
the DXF reader is fixed once for every product, and a product team spends its effort on domain
behavior rather than on infrastructure.

**3. The document is the authority.** Each document has one semantic model. Geometry, views, sheets,
schedules, render scenes and exports are derived from it and can be rebuilt at any time. Derived data
is never edited as a source of truth, caches can be discarded safely, and two outputs from the same
document never disagree.

**4. Everything is a command.** Every change to a document is a typed, validated, undoable command.
The user interface, the command line, scripts, the automation protocol, assistants and Hub jobs all
invoke the same commands, and programmatic execution never opens a dialog. One surface is secured,
documented and tested; macro recording, scripting and headless pipelines come at no extra cost.

**5. Offline first, account free.** Every application is fully functional with no network, no
account and no license server. Collaboration is added by connecting to an Arc Hub, never required. A
project archived today opens in ten years without asking anybody's permission.

**6. Open formats first.** IFC, DWG/DXF, STEP, glTF, OpenUSD, LandXML, BCF and PDF are first-class.
Native formats are documented and readable without Arc. Imports keep what they cannot model and
report every loss, so a user always knows what an exchange changed.

**7. Never lose work, never crash.** Production code does not panic, every input is treated as
hostile, storage is crash safe, and files from older versions always open. A fault inside a command
restores the document instead of ending the session.

**8. Fast by construction.** Regeneration is multi-threaded and incremental, viewports are
GPU-driven, and work is cancellable. Performance budgets are part of acceptance: a feature that
misses its budget is not done.

**9. Automatable everywhere.** Every application has one automation protocol, a headless CLI,
Python scripting through the protocol, visual node graphs and sandboxed plug-ins. Anything a user can
do interactively, a pipeline can do unattended.

**10. Accessible and localized from the first release.** Applications are keyboard complete, expose
accessible names for screen readers, scale, and offer high-contrast themes. English is the source
language and Turkish the first complete translation; right-to-left layout is designed in. These are
release criteria, not later additions.

**11. Simple until proven otherwise.** A crate, a service or an abstraction is created when its
second concrete user exists, not before. Every decision names the trigger that would reopen it, so
simplicity today does not become a trap tomorrow.

## What the suite will not do

| Non-goal | Reason |
|---|---|
| Require an account, subscription, activation or license server | Conflicts with principle 5 |
| Ship desktop applications built on web technology | Conflicts with principle 1; browser and mobile builds compile the same Rust code instead ([ADR-0018](adr/0018-web-and-mobile-targets.md)) |
| Reverse engineer undocumented formats or binaries, run compiled or protected script files of other applications, or ship codecs whose license forbids open distribution | Legal reason; proprietary data is exchanged through connectors inside the user's licensed application, licensed bridges, open readers and open formats instead ([ADR-0017](adr/0017-exchange-connectors-and-licensed-bridges.md)) |
| Collect telemetry by default | Crash reports and usage data are opt-in only |
| Make any feature depend on a hosted AI service | Assistance is optional and provider neutral ([ADR-0014](adr/0014-assistance-is-optional-and-provider-neutral.md)) |
| Operate a hosted service or metered cloud inside the open-source repositories | Arc Hub is self-hostable; hosting is outside the project's repositories |
| Load native-code plug-ins | Plug-ins are sandboxed WebAssembly ([ADR-0012](adr/0012-extensibility.md)) |
| Build, in the first two years, a B-rep kernel from scratch, a GUI toolkit, a GPU abstraction, a CRDT engine, a DWG or IFC parser from scratch, an installer or updater, or a scripting language | Deferred, not excluded: proven components exist and effort goes to product behavior first ([technology baseline](https://github.com/ACKaraca/arc-core/blob/main/docs/architecture/technology-baseline.md)). Native finite-element, meshing, slicing and multi-axis toolpath kernels replace wrapped external engines once they pass the same validation suites ([roadmap](roadmap.md#phase-5-depth-and-scale)) |

## Success criteria

The suite succeeds when the following can be observed:

1. A professional completes a reference project in their discipline (a building permit set, a road
   corridor design, a machined part with drawings, an animated shot) using Arc products only, with
   no network connection.
2. A document saved by any released version opens in every later version; a document from a newer
   major format is refused by an older version without being modified.
3. An IFC, DWG, STEP or glTF file imported and exported again produces a loss report that lists
   every change, and the exported file passes an independent reader in CI.
4. A native document can be inspected with standard SQLite tools and its schema documentation alone.
5. Every user command can be run from the headless CLI and the automation protocol, and every
   interactive command can be completed with the keyboard alone.
6. A crash report or crashing input file leads to a release that handles the input without crashing
   and carries a regression test for it.
7. A team self-hosts Arc Hub from published artifacts and runs check-out, review, issue and approval
   workflows across products without any third-party service.
8. Fuzzing of every importer runs continuously without unresolved crashes older than one release.
9. Each product's performance budgets are measured in CI and met on the reference models.
10. A contributor who reads `CONTRIBUTING.md` and a repository's `AGENTS.md` lands a reviewed change
    without further instructions.
