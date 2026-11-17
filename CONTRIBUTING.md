# Contributing to Arc

This is the contribution guide for every Arc repository. Repository-specific details (crate layout,
commands, invariants) are in each repository's `AGENTS.md` and `docs/` folder.

## Ways to contribute

| Contribution | Where it goes |
|---|---|
| Report a defect | An issue of type bug in the affected repository, with a minimal reproduction |
| Propose a capability or change a behavior | An issue in the product repository; it references capability identifiers where they exist |
| Review architecture and decisions | Comments on ADRs, RFCs and design issues |
| Professional domain expertise | Comments on capability files, workflows and open questions in the product repositories |
| Code | A pull request linked to a *Ready* issue |
| Documentation | A pull request; behavior changes and the documents that describe them travel together |
| Test files for interchange corpora | Files you own, under an open license, following the [corpus admission rules](docs/engineering/clean-room-policy.md#corpus-admission) |
| Translations | Fluent catalogues, written from the meaning of the English source string |

Security problems are never reported in public issues; follow [SECURITY.md](SECURITY.md).

## Issue-first workflow

Every change starts from an issue. The full procedure is in
[docs/engineering/issue-workflow.md](docs/engineering/issue-workflow.md); in short:

1. Search open and closed issues and pull requests for the same work.
2. If an issue exists and nobody is assigned, ask to be assigned or assign yourself. If someone is
   assigned, coordinate in the issue instead of starting a competing change.
3. If no issue exists, open one stating the problem and the acceptance criteria. Small fixes such as
   typos may skip this step.
4. Set the status to *In Progress* when you start.
5. When you finish, comment on the issue with what changed, the pull request link and open items,
   and move the issue to *In Review*. Maintainers move it to *Done* when the acceptance criteria are
   met and verified.

## Development setup

No repository contains buildable code during the design phase. The setup below applies once
implementation starts.

**Toolchain.** Stable Rust, edition 2024, installed with `rustup`. Each repository pins its exact
toolchain in `rust-toolchain.toml`; `rustup` selects it automatically. Additional tools:
`cargo-nextest`, `cargo-deny`, `cargo-audit`; fuzzing uses `cargo-fuzz` on a nightly toolchain.
Repositories that link native libraries (the B-rep FFI crate, renderer libraries) document the
prebuilt artifacts they need in their own `AGENTS.md`.

**Side-by-side checkout.** Clone the repositories you work on into one parent folder:

```text
arc/
  arc-core/
  archbuilder/
  draftbuilder/
  ...
```

A product depends on one tagged `arc-core` release. To develop against a local `arc-core`, add a
Cargo `[patch]` override in your user-level or untracked Cargo configuration, pointing the `arc-core`
git source at `../arc-core`. Do not commit the override. A change that needs a new `arc-core` API
lands in `arc-core` first, then the product updates its pin in a second pull request; see
[repository standards](docs/engineering/repository-standards.md#suite-baseline-and-the-arc-core-pin).

**Checks.** Each repository runs one gate locally and in CI. The individual commands are:

```text
cargo fmt --check
cargo clippy --workspace --all-targets -- -D warnings
cargo nextest run --workspace
cargo xtask layers
```

## Branches and pull requests

> **Current working mode.** Until the project has a second maintainer and this repository has
> reached its MVP release, changes are committed directly to `main` (suite rule 14): no feature
> branches and no pull requests. Where this document says "pull request", read "commit". The
> branch, review and merge rules below apply again when rule 14 is removed.

- `main` is protected. All changes arrive by pull request.
- Branch names: `<type>/<issue-number>-<short-slug>`, for example `fix/412-dxf-hatch-bounds`.
- One concern per pull request. A refactoring and a behavior change are separate pull requests.
- The pull request description links the issue (`Closes #412` only when it completes the issue),
  states what changed and why, lists the tests added, and states what could not be verified.
- Keep pull requests small enough to review in one sitting. Large features land as a sequence of
  independently correct pull requests behind the issue's task list.
- Pull requests are squash-merged. The title becomes the commit subject and must follow Conventional
  Commits; every `Signed-off-by` trailer from the branch is kept in the squashed message.
- Draft pull requests are welcome for early feedback; they are not reviewed for merge.

## Commit conventions

Commit subjects follow [Conventional Commits](https://www.conventionalcommits.org/en/v1.0.0/):

```text
<type>(<scope>): <summary in the imperative, lower case, no period>
```

Types: `feat`, `fix`, `docs`, `test`, `refactor`, `perf`, `build`, `ci`, `chore`. The scope is a
crate or area (`arc-doc`, `dxf`, `governance`). A breaking change to a public API, file format or
protocol adds `!` after the scope and a `BREAKING CHANGE:` footer.

Every commit carries a [Developer Certificate of Origin](https://developercertificate.org/) sign-off
with your real name and an e-mail address you control:

```text
Signed-off-by: Jane Doe <jane.doe@example.com>
```

`git commit -s` adds the trailer. Commits without a sign-off cannot be merged. There is no
contributor license agreement and no copyright assignment.

## Review expectations

- Every pull request needs approval from a reviewer or maintainer of the repository other than the
  author, and a green CI gate. Changes to file formats, public protocols, the layer table or the
  dependency allowlist need a maintainer.
- Reviewers check correctness, tests, layering, error handling, hostile-input handling, documentation
  and clean-room provenance, in that order of priority.
- Reviewers respond within seven days; authors respond to review within fourteen days. A pull
  request inactive for thirty days may be closed; the issue returns to *Ready*.
- Disagreements are resolved in the pull request on technical grounds. Unresolved disagreements go
  to the repository maintainers as described in [GOVERNANCE.md](GOVERNANCE.md).

## Tests required

A change ships with the test that fails without it. Required tests by change type, snapshot and
corpus practice, and the CI gate are defined in
[docs/engineering/testing-strategy.md](docs/engineering/testing-strategy.md). In particular:

- every bug fix adds a regression test; every crash fix adds the crashing input as a test case;
- every new command has headless tests for its validation and its effect, including undo;
- every importer change keeps the corpus round-trip and fuzz targets passing.

## Documentation changes

A pull request that changes behavior updates the documents that describe that behavior in the same
pull request. Status lives in issues and on the project board, never in documents. Writing rules
and the document set are in
[docs/engineering/documentation-standards.md](docs/engineering/documentation-standards.md).

## Clean-room obligations

Arc is implemented from published specifications, observed behavior of legitimately obtained
software and original reasoning. Contributors must not:

- copy or consult the source code or decompiled output of other products;
- reverse engineer binaries or undocumented file formats;
- copy documentation text, dialog wording, icons, fonts, patterns, templates or sample files of
  other products;
- contribute material under a license incompatible with the dependency policy;
- name Arc features after another company's features.

If you have been exposed to material that makes your contribution to a specific area unclean, say so
in the issue before you start. The full policy, including the derivation record required for
behavior derived from observation, is [docs/engineering/clean-room-policy.md](docs/engineering/clean-room-policy.md).

## Licensing of contributions

Every Arc repository is licensed under the Apache License, Version 2.0. By signing off a commit you
certify the Developer Certificate of Origin and license your contribution under the same license.
Third-party code, assets and data may be added only if their license is on the allowlist in
[docs/engineering/dependency-policy.md](docs/engineering/dependency-policy.md), with their origin and
license recorded. The project's names and logos are not licensed with the code; see
[TRADEMARKS.md](TRADEMARKS.md).

## Automated coding agents

Contributions prepared with automated coding agents are accepted under the same rules as any other
contribution:

- A person is accountable. The person who runs the agent signs off the commits, answers review
  comments and is responsible for correctness, licensing and provenance.
- The agent follows the repository's `AGENTS.md`, which contains the suite rules defined in
  [docs/engineering/agent-rules.md](docs/engineering/agent-rules.md).
- The issue-first workflow applies: an agent claims an issue only on behalf of a person who is
  assigned to it.
- The pull request states what was tested and what could not be run.
- Pull requests do not carry agent attribution lines; the sign-off identifies the accountable person.
- Maintainers may close pull requests that show no evidence of human review.
