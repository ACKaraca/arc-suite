# Glossary

Suite vocabulary used across Arc repositories. Product-specific terms are in each product's glossary.

| Term | Meaning |
|---|---|
| ADR | Architecture decision record: a numbered document recording one decision, its context, consequences, alternatives and reopen trigger |
| Arc Automation Protocol | JSON-RPC 2.0 protocol over stdio or a local socket through which scripts, tools and assistants run commands in an Arc application |
| Arc document package | The single-file SQLite format of every native Arc document |
| Arc exchange package | The open, documented container through which connectors and Arc products exchange data with other applications in both directions |
| Arc Scene | The renderer-neutral scene description and session protocol through which products obtain photoreal rendering from RenderBuilder |
| `arc-core` | The foundation repository holding all shared crates |
| Assistance level | One of A1 explain and search, A2 propose with preview, A3 act under approval, A4 batch and agent runs; it bounds what an assistant may do in a session ([assistance](architecture/assistance.md#levels-of-assistance)) |
| Assistance log | The local record of assistant requests, data sent, tools called, proposals and outcomes |
| Assistance task suite | A product's set of assistance tasks built from its reference workflows, each with a fixture, a request in English and Turkish and a deterministic acceptance check |
| Assistant host | The part of an application that runs the assistant's conversation and tool loop and enforces its level, scope, data envelope and budgets |
| Assistant panel | Optional panel through which a user-configured AI provider proposes commands for preview and explicit apply |
| B-rep | Boundary representation: exact solids and surfaces described by faces, edges and vertices |
| Borrowing | Obtaining from Arc Hub the exclusive right to modify specific elements during multi-user editing |
| Browser build | A product's Rust crates and UI kit compiled to WebAssembly and run in a web browser |
| Capability | A specified, testable product behavior with a permanent identifier, tier and phase |
| Change set | The element-level operations of one committed transaction, exchanged through Arc Hub |
| Check-out | An exclusive edit lock on a whole document version in Arc Hub |
| Checker | A deterministic validation engine exposed as a read-only command, such as a rule set, clash test, constraint status or geometry audit; the checker of record for assistant proposals |
| Command | A typed, validated, undoable operation with a stable identifier; the only way a document changes |
| Conformance suite | Tests published by a contract owner that every producer and consumer of the contract must pass |
| Connector | An add-in in `arc-connectors` that runs inside the user's licensed copy of another application, uses only its official public API, and reads or writes the Arc exchange package |
| Corpus | A repository of interchange test files with a ledger of ownership, license and checksum |
| Data envelope | The classes of project data a user has approved to send to an assistant provider for a project, after redaction |
| DCO | Developer Certificate of Origin; certified by a `Signed-off-by` trailer on each commit |
| Dependency graph | The graph in `arc-doc` that records which derived data depends on which objects and schedules regeneration |
| Derivation record | The assumptions, source log, frozen specification and verification committed for behavior derived from observation |
| Derived data | Geometry, views, schedules, scenes and exports computed from the document model; never a source of truth |
| Document | One native Arc file holding one semantic model |
| Element | An object in a document with a stable identifier |
| Enabled predicate | The part of a command definition that decides whether it can run in the current state and why not |
| FFI crate | A crate named in an ADR that binds a native library and is the only kind of crate allowed to contain `unsafe` |
| Fidelity level | View, data or edit: the extent to which a format is supported in one direction |
| Golden model | A small project-owned model with independently computed expected results, used to test geometry and quantities |
| Grounding | Tying an assistant's statements to sources the user can open: objects, derived results, help, standards and attachments, cited in the answer |
| Headless | Running without windowing, GPU or UI; applies to CLI tools, Hub workers and tests |
| Hub API | The HTTPS and WebSocket API of Arc Hub |
| Layer (crate) | One of the levels L0 to L6 of `arc-core`; a crate depends only on lower layers |
| Licensed bridge | A conversion library that the user installs and licenses, called by an Arc product as a separate process; never distributed with Arc |
| Loss report | Structured list of what an import or export dropped, approximated, converted or preserved |
| Mobile build | A product's Rust crates and UI kit built for tablets and phones on iOS and Android |
| Open reader | Native reading of a proprietary format through a permissively licensed open-source reader or a published specification |
| Operation journal | The record of element-level operations of every transaction, driving undo, history, recovery and change sets |
| Patch ledger | `PATCHES.md`: the record of every patch applied to a forked dependency, with its removal condition |
| Persistent naming | Identification of B-rep faces and edges that survives regeneration so references remain valid |
| Phase | One of the suite roadmap stages 0 to 5, ended by exit criteria |
| Preserved foreign data | Imported content the product does not model, kept for round trips |
| Prompt machine | The interactive part of a command that collects parameters from the user step by step |
| Proposal | An ordered, scoped set of command invocations prepared by an assistant, previewed and checked against a base revision, and applied in whole or in part as one undoable step |
| Provenance mark | The record on an object that an assistant-originated transaction created or changed it, cleared when a person reviews or edits the object |
| Readiness class | R1, R2 or R3: whether an assistance capability works with general models through the command surface, needs product engines or data first, or is research-grade |
| Recovery journal | Continuously written record of unsaved changes used to restore work after an unexpected exit |
| Regeneration | Recomputation of stale derived data by the dependency graph on worker threads |
| Revision | A numbered state of a document; derived results carry the revision they were computed from |
| RFC | Request for comments: a proposal for a suite-wide change, decided after a comment period |
| Send preview | The panel's display of exactly what an assistant request will send off the machine, shown before it is sent |
| Stable identifier | The permanent 128-bit identifier of an object |
| Suite baseline | The single `arc-core` tag on which every product of a suite release ships |
| Suite release | A set of product versions that ship together on one suite baseline |
| Tier | T1 Core, T2 Professional or T3 Advanced: the importance class of a capability |
| Transaction | The unit of change in a document, produced by commands, committed or rolled back as a whole |
| Work area | A user-defined subset of a model used to borrow, load or display elements together |
