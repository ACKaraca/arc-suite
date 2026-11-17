# Agent rules

The canonical suite rules for every contributor and every automated coding agent in every Arc
repository, the reason for each rule, and the procedure for changing them. Each repository's
`AGENTS.md` carries the block below byte for byte.

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
14. **Commit directly to `main` for now.** Until the project has a second maintainer and this
    repository has reached its MVP release (for `arc-suite` and `arc-core`: the first product MVP),
    work on `main`: no feature branches and no pull requests. Wherever these rules or the documents
    say "pull request", read "commit". Run the checks before every push and name the issue in the
    commit message. This rule is temporary and is deleted from a repository when that point is
    reached.

## Rationale

| Rule | Why it exists |
|---|---|
| 1. A person is accountable | Review capacity is the scarcest resource in an open project. Automated output without a responsible person shifts all verification onto reviewers. The DCO sign-off identifies that person; licensing and provenance certifications can only be made by a person. |
| 2. Start from an issue | Unclaimed parallel work produces duplicate and conflicting pull requests that nobody can merge. Claiming before changing files, and reporting back on the issue, keeps the issue tracker the single record of who does what and what is left. *Done* means verified, so the board can be trusted. |
| 3. Verify, do not guess | Plausible code that calls a function with the wrong contract, or fixes a symptom instead of a cause, costs more review time than it saves. Reading the definition and confirming the cause is cheaper than any later correction. |
| 4. Document is the authority; everything is a command | Undo, history, multi-user change sets, automation and auditing all depend on every change passing through a validated command in a transaction ([ADR-0004](../adr/0004-document-model-typed-objects-stable-ids-transactions-operation-journal.md), [ADR-0006](../adr/0006-one-command-model-one-automation-protocol.md)). One side door breaks all of them. A dialog on a programmatic path blocks headless jobs. |
| 5. Respect the layers | Headless builds (CLI, Hub workers, WebAssembly viewer, tests) exist only if lower layers stay free of UI, GPU and windowing dependencies. Product independence keeps the polyrepo releasable ([ADR-0001](../adr/0001-polyrepo-with-one-foundation-and-a-single-suite-baseline.md)). |
| 6. Never crash, never lose work | Files come from outside parties and professional documents represent weeks of work. A panic is a crash and a crash is lost trust ([ADR-0015](../adr/0015-robustness.md)). Regression tests keep a fixed crash fixed. |
| 7. Clean room | The project's right to distribute its code depends on every part being original or properly licensed. Tainted code must be removed and rewritten, which is far more expensive than not writing it ([clean-room policy](clean-room-policy.md)). |
| 8. Licenses | The suite is distributed under Apache-2.0; a single incompatible dependency can make a release undistributable. Asset provenance is required for the same reason ([dependency policy](dependency-policy.md)). |
| 9. Rust only in desktop applications | Principle 1 of the [vision](../vision.md). A second language in the desktop stack doubles the toolchain, splits the safety model and blocks code reuse between crates. |
| 10. Small, tested changes | Small pull requests are reviewed faster and reverted safely. A test that fails without the change proves the change does something. Documents updated in the same pull request stay true. |
| 11. Report only what ran | A reviewer must know which claims were checked. "Tests pass" when they did not run is worse than "not run". |
| 12. Ask a human before | These actions are irreversible, legally significant, cost money or affect users outside the change. They need a decision by an accountable person. Everything else should not wait for one. |
| 13. Keep secrets and personal data out | Repositories, logs and requests to external services are public or long lived; a leaked secret or personal datum cannot be recalled. |
| 14. Commit directly to `main` for now | With one maintainer and no released product, a pull request has no second reviewer and only adds steps. Issues, checks before every push and Conventional Commits keep the history traceable. The rule is removed per repository at its MVP release or when a second maintainer joins, and the branch and review rules in the [contribution guide](../../CONTRIBUTING.md) apply from then on. |

## Changing the rules

1. Open an [RFC](../rfcs/README.md) in `arc-suite` with the proposed text, the reason and the expected
   effect. Changes to wording that do not change meaning may use a pull request with suite-maintainer
   approval instead.
2. After acceptance, one pull request in `arc-suite` changes the block in this file and in
   `arc-suite/AGENTS.md` together. The rationale table is updated in the same pull request.
3. The merge commit of that pull request is the new canonical revision.

## Synchronizing every repository

- Each repository's `AGENTS.md` contains the block from `## Suite rules` to the end of the last rule, byte
  for byte, followed by the repository's own sections.
- After a change, a synchronization pull request is opened in every Arc repository, replacing only
  the block. It is labeled `type: chore` and links the RFC. One synchronization pull request per
  repository is open at a time; a newer change supersedes an older open one.
- A CI check in each repository compares the block with the canonical text at the latest `arc-suite`
  revision. A mismatch with a canonical revision that is older than the latest produces a warning;
  any other difference fails the check.
- `CLAUDE.md` in each repository contains only `@AGENTS.md` and is never a copy.
- `AGENTS.md` stays under 10 KB including the block; repository sections link to documents instead
  of restating them.
