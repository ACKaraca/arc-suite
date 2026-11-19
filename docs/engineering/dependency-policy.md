# Dependency policy

Which third-party code, tools, assets and fonts Arc may use, how a dependency is added, and how the
supply chain is protected ([ADR-0013](../adr/0013-licensing-and-contribution-terms.md)). This is
engineering practice, not legal advice.

## License allowlist

The allowlist is enforced by `cargo deny` in every repository. "In-process" means linked into an Arc
binary, statically or dynamically. "Out-of-process" means a separate executable that Arc starts and
talks to through files, pipes or sockets.

| SPDX identifier | In-process | Out-of-process | Conditions |
|---|---|---|---|
| `MIT` | Yes | Yes | Notice reproduced in `THIRD_PARTY_NOTICES.md` |
| `Apache-2.0` | Yes | Yes | Notice and any `NOTICE` file content reproduced |
| `BSD-2-Clause` | Yes | Yes | Notice reproduced |
| `BSD-3-Clause` | Yes | Yes | Notice reproduced; no endorsement use of names |
| `ISC` | Yes | Yes | Notice reproduced |
| `Zlib` | Yes | Yes | Notice kept in source distributions |
| `BSL-1.0` | Yes | Yes | Notice kept in source distributions |
| `Unicode-3.0` | Yes | Yes | Notice reproduced |
| `CC0-1.0` | Yes | Yes | None |
| `0BSD` | Yes | Yes | None |
| `MPL-2.0` | Yes, unmodified crates only | Yes | Used unmodified; if a patch is unavoidable, the patched files stay MPL-2.0, are published in a public fork and are recorded in the patch ledger |
| `LGPL-2.1`, `LGPL-3.0` (including variants with linking exceptions) | Only native libraries named in an ADR, dynamically linked, replaceable by the user | Yes | Library shipped as a separate shared library with its license and source offer; never statically linked |
| `GPL-2.0`, `GPL-3.0` | No | Only as separate programs that are never required for a core workflow | See [separate-process tools](#tools-that-may-run-only-as-separate-processes) |
| `AGPL-3.0` | No | No | Not used, including on servers |
| Proprietary, source-available, non-commercial or field-of-use restricted licenses | No | No | Not used |
| No license or unclear license | No | No | Treated as all rights reserved |

A crate offered under several licenses is acceptable when at least one of them is on the list; the
chosen license is recorded by `cargo deny`.

LGPL libraries currently named: Open CASCADE, behind the B-rep FFI crate
([ADR-0003](../adr/0003-geometry-owned-api-two-tiers.md)).

## Adding a dependency

1. **Check need.** Prefer the standard library, an existing dependency or a small amount of Arc code.
   A dependency is not added for a few lines.
2. **Check license.** The crate and every new transitive dependency must be on the allowlist.
3. **Check health.** Maintenance activity, number of maintainers, release history, open security
   advisories, use of `unsafe` and build scripts, and transitive dependency count.
4. **Propose.** The pull request states purpose, license, alternatives considered, health findings,
   added transitive crates and binary size impact.
5. **Approve.** Foundation dependencies are approved by an `arc-core` maintainer and recorded in the
   [technology baseline](https://github.com/ACKaraca/arc-core/blob/main/docs/architecture/technology-baseline.md);
   product dependencies by a maintainer of the product. Duplicating a capability that `arc-core`
   already provides needs an ADR.
6. **Wrap if critical.** A dependency on a critical path (geometry kernel, format library, renderer
   building block) is used only behind an Arc-owned API so it can be replaced.

## FFI crates and dynamic linking

- Bindings to native libraries live in dedicated FFI crates named in an ADR; only those crates may
  contain `unsafe` ([coding standards](coding-standards.md#unsafe-code)).
- An FFI crate exposes a safe Rust API with Arc-owned types; no foreign type crosses it.
- LGPL libraries are dynamically linked from a separate shared library file that users can replace.
  The build documents how to rebuild or substitute the library.
- Native sources or prebuilt binaries are fetched by pinned version and checksum, built reproducibly
  in CI and cached; no build script downloads from an unpinned location.

## Tools that may run only as separate processes

Some useful programs carry licenses that do not permit linking. They may be used only as separate
processes:

| Use | Rule |
|---|---|
| Solvers (for example finite-element solvers and meshers under GPL) | Started as separate executables behind an adapter that exchanges files; installed separately by the user or distributed as unmodified separate programs with their license and source; never required for a core workflow |
| Test oracles (independent readers used to validate exports) | Run in CI containers only, never shipped; their source code is never consulted |
| Development tools | Any tool that does not become part of a distributed artifact may be used if its license permits that use |

Adapters for such tools speak only documented file formats and command-line interfaces.

## Forks and the patch ledger

- Upstream crates are used unmodified. A fork is a last resort, recorded in an ADR when it affects
  `arc-core` or more than one product.
- A repository that applies a `[patch]` to a fork keeps `PATCHES.md` with one row per patch:

| Column | Content |
|---|---|
| Patch id | Stable identifier, for example `egui-001` |
| Upstream | Crate and version patched |
| Change | What the patch does, in one sentence |
| Reason | The Arc requirement that needs it |
| Upstream status | Link to the upstream issue or pull request |
| Remove when | The condition under which the patch is dropped |
| Owner | Maintainer responsible |

- One commit per patch in the fork, each with its own test. The fork is public and keeps the
  upstream license.
- Patches are reviewed at every upstream release; a patch whose removal condition is met is
  removed in the next pin update.

## Supply-chain checks

- `Cargo.lock` is committed in every repository, libraries included.
- `cargo deny` checks licenses, banned crates, duplicate versions above a threshold and sources: only
  crates.io and git repositories of the ACKaraca organization or forks listed in a patch ledger.
- `cargo audit` runs on every pull request and nightly; a new advisory against a shipped dependency
  opens an issue automatically.
- New versions of dependencies are adopted only after a cooldown of seven days from their release,
  except for fixes of security advisories that affect Arc.
- Git dependencies are pinned to tags or commits, never branches.
- Dependency updates arrive as separate pull requests, never mixed with feature work.
- Release builds run in clean CI environments from the lock file, with network access limited to
  pinned sources.

## Assets and fonts

Assets (icons, images, materials, textures, hatch patterns, line types, symbols, templates, sample
documents) and fonts follow the same discipline as code:

| License | Allowed for |
|---|---|
| Project-original work under Apache-2.0 | Everything |
| `CC0-1.0` | Assets |
| `CC-BY-4.0` | Assets, with attribution shown where the asset is credited |
| `OFL-1.1` | Fonts |
| Code licenses on the allowlist | Assets distributed with a library under that license |

- Every asset file has a row in `ATTRIBUTION.md`: path, author, origin, SPDX license and, for
  derived assets, the source asset.
- Icons are drawn by the project. Drafting fonts, hatch patterns, line types and templates are
  original; none is copied or traced from another product.
- Large asset libraries live in separate repositories consumed by pinned commit, never as Cargo
  dependencies; applications work, with a reduced library, when they are absent.
- Fonts that are not shipped are taken from the operating system at run time.
