# Glossary

Suite vocabulary used across Arc repositories. Product-specific terms are in each product's glossary.

| Term | Meaning |
|---|---|
| ADR | Architecture decision record: a numbered document recording one decision, its context, consequences, alternatives and reopen trigger |
| Arc Automation Protocol | JSON-RPC 2.0 protocol over stdio or a local socket through which scripts, tools and assistants run commands in an Arc application |
| Arc document package | The single-file SQLite format of every native Arc document |
| Arc Scene | The renderer-neutral scene description and session protocol through which products obtain photoreal rendering from RenderBuilder |
| `arc-core` | The foundation repository holding all shared crates |
| Assistant panel | Optional panel through which a user-configured AI provider proposes commands for preview and explicit apply |
| B-rep | Boundary representation: exact solids and surfaces described by faces, edges and vertices |
| Borrowing | Obtaining from Arc Hub the exclusive right to modify specific elements during multi-user editing |
| Capability | A specified, testable product behavior with a permanent identifier, tier and phase |
| Change set | The element-level operations of one committed transaction, exchanged through Arc Hub |
| Check-out | An exclusive edit lock on a whole document version in Arc Hub |
| Command | A typed, validated, undoable operation with a stable identifier; the only way a document changes |
| Conformance suite | Tests published by a contract owner that every producer and consumer of the contract must pass |
| Corpus | A repository of interchange test files with a ledger of ownership, license and checksum |
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
| Headless | Running without windowing, GPU or UI; applies to CLI tools, Hub workers and tests |
| Hub API | The HTTPS and WebSocket API of Arc Hub |
| Layer (crate) | One of the levels L0 to L6 of `arc-core`; a crate depends only on lower layers |
| Loss report | Structured list of what an import or export dropped, approximated, converted or preserved |
| Operation journal | The record of element-level operations of every transaction, driving undo, history, recovery and change sets |
| Patch ledger | `PATCHES.md`: the record of every patch applied to a forked dependency, with its removal condition |
| Persistent naming | Identification of B-rep faces and edges that survives regeneration so references remain valid |
| Phase | One of the suite roadmap stages 0 to 5, ended by exit criteria |
| Preserved foreign data | Imported content the product does not model, kept for round trips |
| Prompt machine | The interactive part of a command that collects parameters from the user step by step |
| Recovery journal | Continuously written record of unsaved changes used to restore work after an unexpected exit |
| Regeneration | Recomputation of stale derived data by the dependency graph on worker threads |
| Revision | A numbered state of a document; derived results carry the revision they were computed from |
| RFC | Request for comments: a proposal for a suite-wide change, decided after a comment period |
| Stable identifier | The permanent 128-bit identifier of an object |
| Suite baseline | The single `arc-core` tag on which every product of a suite release ships |
| Suite release | A set of product versions that ship together on one suite baseline |
| Tier | T1 Core, T2 Professional or T3 Advanced: the importance class of a capability |
| Transaction | The unit of change in a document, produced by commands, committed or rolled back as a whole |
| Work area | A user-defined subset of a model used to borrow, load or display elements together |
