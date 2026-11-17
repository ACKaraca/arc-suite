# Release process

How Arc applications, the foundation and the server are released: release trains and the suite
baseline, pre-release stages, signing, platforms and packaging, updates, the file-format promise,
long-term support and security fixes.

## Release trains and the suite baseline

- `arc-core` releases under one tag for all its crates. Products release on their own cadence, each
  pinned to one `arc-core` tag ([repository standards](repository-standards.md#suite-baseline-and-the-arc-core-pin)).
- A **suite release** is a set of product versions that all ship on the same `arc-core` tag. The
  suite maintainers name the tag, each participating product passes its release checks on it, and the
  suite release record in `arc-suite` lists the `arc-core` tag and every product version.
- Products join a suite release when they are at least in alpha. A product not ready for a suite
  release stays on its previous release.
- Releases are cut from a green commit on `main`. The version bump happens on a release branch
  `release/<version>`; fixes for the release land on `main` first and are cherry-picked.
- A nightly dry run builds, signs with test keys and packages every product, so release failures are
  found before a release.

## Pre-release stages

| Stage | Entry criteria | Promise to users |
|---|---|---|
| Design phase | Documentation set published | No builds |
| Alpha | The product's phase exit criteria for its first milestone are met; CI gate, fuzzing and corpus tests run | Builds for evaluation; documents may need migration or may not open in later alphas; feedback wanted |
| Beta | Capabilities of tier T1 planned for the release are complete; native format frozen at a `1.0` candidate; no known data-loss defects | Documents written by a beta open in every later beta and in the stable release |
| Stable | Beta exit: no open defects that lose data or crash on the corpus; performance budgets met; accessibility and localization criteria met; format frozen | The full compatibility promise below |

Version numbers follow semantic versioning with pre-release identifiers: `0.y.z` during alpha,
`1.0.0-beta.n` during beta.

## Signing and checksums

- Every release artifact is listed in a `SHA256SUMS` file; the checksum file is signed with the
  project's release key, and the public key is published in `arc-suite` and in each repository's
  release notes.
- Windows executables and installers carry an Authenticode signature. macOS applications are signed
  with a Developer ID and notarized. Linux packages are signed by their repository mechanism.
- Release tags are signed. Signing keys are used only by the release pipeline from protected
  environments that require approval by a suite maintainer.
- Release notes state how to verify checksums and signatures.
- Builds are reproducible from the tag and the lock file; the build environment is recorded with the
  release.

## Platforms and packaging

| Platform | Support level | Desktop packaging | CLI and server packaging |
|---|---|---|---|
| Windows x86-64 | Primary | MSI installer | Archive; installer through the CLI packager |
| macOS arm64 | Primary | Signed and notarized DMG | Archive |
| Linux x86-64 | Primary | Flatpak | Archive; container image for Arc Hub |
| Windows arm64 | Secondary | MSI installer | Archive |
| Linux arm64 | Secondary | Flatpak | Archive; container image for Arc Hub |
| macOS x86-64 | Secondary | DMG | Archive |

Primary platforms block a release when tests fail; secondary platforms are built and tested, and a
failure is documented in the release notes. Desktop installers and updates use Velopack; CLI and server
artifacts use `cargo-dist`. Arc Hub is also published as an OCI container image with a documented
configuration.

## Update channel

- Desktop applications check for updates only if the user allows it, never before the first run
  question is answered; the check sends the product, version, platform and channel, and nothing else.
- Channels: stable, beta and nightly; the user chooses. Updates are downloaded, verified against the
  signed checksums and installed when the user agrees.
- Updates never run in the middle of unsaved work; a failed update leaves the installed version
  working.
- Organizations can disable update checks by policy and distribute packages themselves.

## File-format compatibility promise

- A document written by any released version opens in every later version.
- An older application opens a document of a newer minor format read-only where possible and refuses
  a newer major format without modifying the file.
- Each native format version is frozen by a tag and documented; frozen versions are never changed.
- Migrations are forward only and tested from every frozen version in CI.
- A major format change requires an RFC and is announced in the release notes of the release before
  it ships.
- Interchange formats (IFC, DWG, STEP and others) are written in the versions the user selects among
  those the product supports.

## Long-term support

Design projects outlive software releases. Once a product reaches stable, the project intends to
designate long-term-support lines: a release line that receives security fixes and fixes for data
loss and crashes for a support period announced with the line, with a target of at least two years.
Long-term-support lines are aligned with suite releases, so all products in a suite release share
the same support period.

## Security fixes

- Vulnerabilities are reported privately as described in [SECURITY.md](../../SECURITY.md).
- A fix is prepared in a private fork, reviewed by a maintainer, and released for the latest stable
  release, every supported long-term-support line and `main` together.
- A security fix release contains only the fix and what it requires; it is released before details are
  published, and an advisory is published with the release.
- A fix in `arc-core` triggers patch releases of every product in supported suite releases, on a
  patch tag of the same `arc-core` line.
