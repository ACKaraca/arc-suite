# Repository standards

How every Arc repository is laid out, named, protected, versioned and organized.

> **Current working mode.** Until the project has a second maintainer and this repository has
> reached its MVP release, changes are committed directly to `main` (suite rule 14): no feature
> branches and no pull requests. Where this document says "pull request", read "commit". The
> branch, review and merge rules below apply again when rule 14 is removed.

## Repository kinds and names

| Kind | Name pattern | Examples |
|---|---|---|
| Suite | `arc-suite` | `arc-suite` |
| Foundation | `arc-core` | `arc-core` |
| Product | Product name in lower case, no separators, or `arc-` plus a word for products named "Arc ..." | `archbuilder`, `draftbuilder`, `arc-review`, `arc-hub` |
| Corpus | `arc-corpus-<domain>` | `arc-corpus-dxf`, `arc-corpus-ifc` |
| Connectors | `arc-connectors` | `arc-connectors` |

A new repository is created only after an RFC and by a suite maintainer.

### The connectors repository

`arc-connectors` holds the add-ins that run inside other applications
([ADR-0017](../adr/0017-exchange-connectors-and-licensed-bridges.md)). It differs from the other
repositories in these points; everything else in this standard applies:

- Each connector lives in its own folder, is written in the language its host application requires,
  and is exempt from the Rust-only rule for desktop applications. Its build and lint commands are
  stated in the repository's `AGENTS.md`.
- A connector uses only the host application's official public API, inside the user's own licensed
  copy. No vendor SDK code, sample code, templates or sample files are copied into any Arc
  repository; SDK headers or assemblies needed to build are obtained by the builder from the vendor
  and are never committed ([clean-room policy](clean-room-policy.md)).
- A connector contains no Arc product logic: it maps between the host's objects and the Arc exchange
  package and passes the package's conformance suite.
- Each connector states the host application versions it supports and is tested against them.

## Layout

The product layout is defined in the [architecture overview](../architecture/overview.md#product-repository-template).
`arc-core` uses the same top level with `crates/` holding the foundation crates by name and no `apps/`
folder other than the reference shell application.

### Required root files

| File | Purpose |
|---|---|
| `README.md` | Status banner during the design phase, purpose, map, license, trademark notice as the last section |
| `AGENTS.md` | Agent and contributor quick reference with the suite rules block ([agent rules](agent-rules.md)) |
| `CLAUDE.md` | The single line `@AGENTS.md` |
| `LICENSE` | Apache License 2.0 |
| `NOTICE` | Copyright "ACKaraca and the Arc contributors", trademark statement, third-party notices pointer |
| `CONTRIBUTING.md` | In product repositories, a short file linking to the canonical guide in `arc-suite` |
| `SECURITY.md` | Private vulnerability reporting through GitHub |
| `.gitignore`, `.gitattributes`, `.editorconfig` | Shared settings: UTF-8, LF line endings, final newline |
| `rust-toolchain.toml` | Exact toolchain pin, in repositories with Rust code |
| `deny.toml` | License allowlist and source rules, in repositories with Rust code |
| `PATCHES.md` | Patch ledger, only in repositories that carry a `[patch]` to a fork |
| `THIRD_PARTY_NOTICES.md` | Generated notices of bundled third-party code, once dependencies exist |
| `ATTRIBUTION.md` | Origin and license of every non-code asset, once assets exist |
| `CODEOWNERS` (in `.github/`) | Reviewers and maintainers by path |

The required document set under `docs/` is defined in
[documentation standards](documentation-standards.md#document-set).

## Naming

| Item | Rule | Example |
|---|---|---|
| Crates | Lower case, hyphen separated; foundation crates start with `arc-`; product crates start with the repository name | `arc-doc`, `arc-io-ifc`, `civilbuilder-model` |
| Binaries | Product name; CLI adds `-cli` | `solidbuilder`, `solidbuilder-cli` |
| Rust modules and files | `snake_case`; one public concept per module | `regeneration.rs` |
| Documents | Lower case, hyphen separated; ADRs and RFCs prefixed with a four-digit number | `clean-room-policy.md`, `0005-document-package-one-sqlite-file-per-document.md` |
| Command identifiers | `<product or core>.<area>.<verb>` in lower case | `civil.alignment.create` |
| Capability identifiers | `<PREFIX>-<AREA>-<NNN>` | `DB-DIM-014` ([documentation standards](documentation-standards.md#capability-identifiers)) |
| Branches | `<type>/<issue>-<slug>` | `feat/88-wall-joins` |
| Tags | `v<semver>` for crates and applications; `format-<name>-<major>.<minor>` for file-format freezes | `v0.4.0`, `format-archb-1.0` |

## Branch protection

Every repository protects `main`:

- changes only by pull request; no direct pushes, no force pushes, no deletion;
- at least one approval from a code owner other than the author; maintainers' approval for paths
  marked in `CODEOWNERS` (formats, protocols, layer table, `deny.toml`, CI workflows);
- the single CI gate must pass; branches must be up to date before merge;
- squash merging only, with Conventional Commit titles and DCO sign-off check;
- stale approvals are dismissed when new commits are pushed;
- signed tags for releases, created only by the release pipeline.

Reusable CI workflows are maintained in `arc-suite` under `.github/workflows/`. Callers pin them by
tag; release and signing workflows are never called by branch name.

## Suite baseline and the arc-core pin

- `arc-core` is released as a whole under one tag `v<major>.<minor>.<patch>`. All its crates share
  that version.
- Each product pins exactly one `arc-core` tag in its workspace manifest; every foundation crate the
  product uses comes from that tag.
- A suite release names one `arc-core` tag; every product in the release ships on it. CI in each
  product fails if any two foundation crates resolve to different revisions.

**Updating the pin.**

1. The `arc-core` change merges and is released under a new tag.
2. The product opens a pull request titled `build(deps): update arc-core to v<x.y.z>` that changes
   only the pin and the code needed to adapt to it.
3. The pull request links the `arc-core` release notes and the issues that required the update.
4. A product updates its pin at least once per suite release cycle so that it can join the suite
   baseline.

**Local development** uses side-by-side checkouts and an uncommitted Cargo `[patch]` override, as
described in [CONTRIBUTING.md](../../CONTRIBUTING.md#development-setup). A pull request that requires
an unreleased `arc-core` change stays in draft until that change is released.

## Versioning

| Item | Scheme | Rules |
|---|---|---|
| Applications | Semantic versioning | Major: a breaking change in user-visible behavior, CLI or automation contract; minor: new capabilities; patch: fixes. Before 1.0, minor versions may break. |
| `arc-core` | Semantic versioning, one version for all crates | Breaking API changes increase the major version (minor before 1.0) and are listed in the release notes with migration notes. |
| Native file formats | `major.minor`, independent of application versions | Minor: additive, older readers can open read-only; major: older readers refuse. A format version is frozen by a tag; frozen versions are never changed. |
| Contracts | `major.minor`, independent | See [contracts](../architecture/contracts.md#general-compatibility-rules). |
| Suite releases | Sequential number | A suite release records the `arc-core` tag and each product version it contains. |

## Labels

Every repository uses the same label set:

| Group | Labels |
|---|---|
| Type | `type: epic`, `type: feature`, `type: bug`, `type: docs`, `type: research`, `type: chore` |
| Tier | `tier: 1`, `tier: 2`, `tier: 3` |
| Phase | `phase: 0` to `phase: 5` |
| Area | `area: <token>`, tokens defined per repository in its `AGENTS.md` or capability index |
| State | `status: blocked`, `needs: verification` |
| Community | `good first issue`, `help wanted` |

Labels are created and changed by maintainers; new area tokens follow the capability areas of the
repository.

## Milestones

- Milestones correspond to the product's roadmap milestones, each mapped to one suite phase, named
  `<phase>.<n> <short name>`, for example `1.2 Annotation`.
- A milestone's description lists its exit criteria by linking the roadmap section.
- Status of a milestone is read from its issues; it is never written into documents.
