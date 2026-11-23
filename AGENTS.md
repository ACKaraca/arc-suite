# AGENTS.md

`arc-suite` is the suite repository of Arc, an open-source, Rust-native suite of design applications.
It holds vision, product map, suite architecture, engineering standards, governance, the suite ADRs
and the roadmap. It contains documentation only; every other Arc repository links here.

## Status and where to start

The suite is in the design phase: no code and no builds exist anywhere. Read, in order:
`README.md`, `docs/vision.md`, `docs/products.md`, `docs/architecture/overview.md`,
`docs/adr/README.md`, then the engineering standard for the task at hand under `docs/engineering/`.

## Repository layout

```text
README.md  CONTRIBUTING.md  GOVERNANCE.md  CODE_OF_CONDUCT.md  TRADEMARKS.md  SECURITY.md
AGENTS.md  CLAUDE.md (imports this file)  LICENSE  NOTICE
docs/
  vision.md  products.md  roadmap.md  glossary.md
  architecture/   overview, contracts, interoperability, collaboration, automation, assistance, user experience
  engineering/    agent rules and the suite engineering standards
  adr/            suite ADRs 0001-0021, index and template
  rfcs/           RFC process, template, accepted RFCs
.github/workflows/  reusable CI workflows for all repositories (target; added with phase 0)
```

## Commands

No build exists. This repository has no Rust code; its checks are documentation checks: every
relative link resolves, Mermaid blocks parse, and no third-party product name appears outside the
README trademark notice. In code repositories the commands that will apply are:

```text
cargo fmt --check
cargo clippy --workspace --all-targets -- -D warnings
cargo nextest run --workspace
cargo xtask layers
```

## Suite rules

These rules apply in every Arc repository. The canonical text is
[`docs/engineering/agent-rules.md`](https://github.com/ACKaraca/arc-suite/blob/main/docs/engineering/agent-rules.md)
in `arc-suite`; change it there first.

1. **A person is accountable.** Whoever runs an agent answers for what it submits.
2. **Start from an issue.** Search issues and pull requests, check that nobody else is assigned,
   assign yourself and set the status to *In Progress* before changing files. When you finish,
   comment with what changed, the commit or pull request link and open items, then update the status.
   *Done* requires met and verified acceptance criteria.
3. **Verify, do not guess.** Open a definition before using it. Confirm a cause in the code.
4. **The document is the authority and everything is a command.** No change reaches a document
   except through a validated command inside a transaction. Programmatic paths never open dialogs.
5. **Respect the layers.** A crate depends only on lower layers. Nothing below the UI layer depends
   on windowing, GPU or UI crates. Products never depend on other products.
6. **Never crash, never lose work.** No `unwrap`, `expect`, `panic!`, `todo!` or `unreachable!` in
   production code. No `unsafe` outside the FFI crates named in an ADR. Treat every file and message
   as hostile: bound recursion, allocation and counts. A crash fix ships with a regression test.
7. **Clean room.** Implement from published specifications, observed behavior and original
   reasoning. Never copy or consult another product's code, decompiled output, file-format reverse
   engineering, documentation text, icons, fonts, templates or sample files. Do not name our
   features after another company's features.
8. **Licenses.** Add a dependency only if its license is on the allowlist in
   `docs/engineering/dependency-policy.md` in `arc-suite`. Record every asset's origin and license.
9. **Rust only in desktop applications.** No web view, no JavaScript or TypeScript.
10. **Small, tested changes.** One concern per pull request, with the test that fails without it.
    Update the documents that describe the behavior you change in the same pull request.
11. **Report only what ran.** Say what you tested and what you could not run.
12. **Ask a human before** adding a license outside the allowlist, publishing or releasing,
    changing a file format or public protocol, adding a new repository or remote, spending money,
    handling secrets, or any destructive git operation. Otherwise keep working.
13. **Keep secrets and personal data out** of repositories, logs and requests.
14. **The maintainer commits directly to `main` for now.** Until the project has a second maintainer
    and this repository has reached its MVP release (for `arc-suite` and `arc-core`: the first
    product MVP), the maintainer works on `main` without branches or pull requests; for that work,
    read "commit" wherever the documents say "pull request". Other contributors work on a branch and
    open a pull request, which the maintainer reviews and merges. Run the checks before every push
    and name the issue in the commit message. This rule is temporary and is deleted from a
    repository when that point is reached.

## Repository invariants

- This repository is the single source of suite-wide rules. Other repositories link to it; they do
  not restate it. The one copy allowed is the suite rules block in each `AGENTS.md`.
- The suite rules block above is identical, byte for byte, to the block in
  `docs/engineering/agent-rules.md`. Change both in one pull request and follow the synchronization
  procedure in that file.
- Suite ADRs are numbered, never renumbered, and never edited in substance once accepted; a change
  is a new ADR that supersedes the old one. `docs/adr/README.md` lists every ADR with its status.
- A change to a contract, file-format policy, the layer table, the dependency allowlist, the agent
  rules or governance requires an accepted RFC first (`docs/rfcs/README.md`).
- Documents describe design; they never claim that anything is implemented and never carry status.
- `README.md` keeps the status banner at the top and the trademark notice as its last section,
  verbatim.
- `LICENSE`, `NOTICE`, `SECURITY.md` and `CLAUDE.md` change only on a maintainer's request.

## Documentation rules

- American English; plain, declarative, present tense. No marketing words, emojis or exclamation
  marks. One H1 per file, a one- or two-sentence purpose statement, then sections.
- No trace of how a document was produced. Never describe Arc by comparison with, substitution for
  or resemblance to another product, and never describe a feature by naming another product's
  feature.
- Other companies' names appear only as file formats, standards and protocols, in factual
  interoperability statements, and for libraries in architecture discussions. This repository has no
  positioning statement.
- Use Arc's own descriptive vocabulary; generic industry terms are fine.
- Each fact once: link instead of copying. Relative links inside the repository; other repositories
  as `https://github.com/ACKaraca/<repository>/blob/main/<path>`, only to paths that exist.
- Describe behavior in original words. Do not reproduce another product's documentation structure,
  dialog text, option wording or default-value lists.
- Every README ends with the suite trademark notice, verbatim.

## Issue workflow

Work starts from an issue: search issues and pull requests, confirm nobody is assigned, assign
yourself, set *In Progress*. Statuses: Backlog, Ready, In Progress, In Review, Blocked, Done. On
finishing, comment with what changed, the commit or pull request link and open items; update the
status; record follow-up work as new issues after searching for duplicates. *Done* only when the
acceptance criteria are met and verified. Commits follow Conventional Commits and carry a DCO
`Signed-off-by` trailer. Details: `docs/engineering/issue-workflow.md`.

## Verification

Run the narrowest relevant check: for a document change, resolve every relative link you touched
and scan the changed files for third-party product names. Report only what ran and what you could
not run.

## Ask a human about

- any change to a suite ADR, contract, layer rule, dependency allowlist, agent rule or governance;
- product scope boundaries between repositories;
- trademark, licensing or clean-room questions;
- creating repositories, labels, milestones or releases in the organization;
- anything listed in suite rule 12.
