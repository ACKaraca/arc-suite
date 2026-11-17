# Issue workflow

How work is planned, claimed, tracked and finished in every Arc repository.

> **Current working mode.** Until the project has a second maintainer and this repository has
> reached its MVP release, changes are committed directly to `main` (suite rule 14): no feature
> branches and no pull requests. Where this document says "pull request", read "commit". The
> branch, review and merge rules below apply again when rule 14 is removed.

## Issue types

| Type label | Use | Typical content |
|---|---|---|
| `type: epic` | A body of work spanning several issues, usually a roadmap milestone item | Summary, scope as capability identifiers, child issues as a task list, exit criteria |
| `type: feature` | One capability or part of one | Capability identifiers, behavior, acceptance criteria |
| `type: bug` | Behavior that differs from the documented design | Version or commit, platform, reproduction, expected and actual result |
| `type: docs` | Documentation change without behavior change | Documents affected, what is wrong or missing |
| `type: research` | A question to answer before a decision | The question, the evidence needed, the decision it unblocks (often an `OQ-nn`) |
| `type: chore` | Maintenance: dependencies, CI, tooling, synchronization | What changes and why |

Every issue has a summary, a scope and acceptance criteria written as a task list of observable
results.

## Statuses

Status is a field of the organization's project board; every open issue has one.

| Status | Meaning |
|---|---|
| Backlog | Recorded; not yet ready to start (unclear scope, missing decision or dependency) |
| Ready | Scope and acceptance criteria are clear, dependencies are met, nobody is assigned |
| In Progress | Assigned and being worked on |
| In Review | A pull request or deliverable is waiting for review |
| Blocked | Cannot proceed; the issue states what blocks it and links the blocking issue. Also labeled `status: blocked` |
| Done | Acceptance criteria are met and verified; the issue is closed |

An issue moves to *Done* only when every acceptance criterion is met and has been verified by a
person other than the implementer or by CI evidence linked in the issue. Work that is merged but not
yet verified stays *In Review* and is labeled `needs: verification`.

## Before you start

1. Search open and closed issues and pull requests for the same work, including other repositories
   when the change crosses a contract.
2. Confirm that nobody else is assigned. If someone is, coordinate in the issue; do not open a
   competing pull request.
3. Assign yourself. Contributors without permission to assign comment to claim, and a maintainer
   assigns them.
4. Set the status to *In Progress*.
5. If the scope is unclear, ask in the issue before changing files.

A claim with no visible activity for fourteen days may be released by a maintainer after asking in
the issue; the issue returns to *Ready*.

## When you finish

1. Open the pull request with a link to the issue.
2. Comment on the issue with: what changed, the pull request or commit link, and any open items.
3. Set the status to *In Review*.
4. Record newly discovered work as new issues, after searching for duplicates, and link them from the
   original issue.
5. After merge and verification, a maintainer sets *Done* and closes the issue. If verification is
   still pending, the issue keeps `needs: verification`.

## Labels

The full label set is defined in [repository standards](repository-standards.md#labels): type, tier,
phase, area, `status: blocked`, `needs: verification`, and community labels. Every issue carries
exactly one type label and one phase label; feature issues carry a tier label; issues carry at least
one area label.

## Epics and milestones

- An epic corresponds to a roadmap item. Its body lists child issues as a task list; progress is read
  from the children.
- Each issue belongs to at most one milestone; milestones correspond to the product roadmap and to a
  suite phase.
- Cross-repository work has an epic in the repository that owns the outcome, with child issues in
  each repository that must change, linked by full URL.

## Definition of done

An issue is done when all of the following hold:

- every acceptance criterion is met and verified;
- the code is merged to `main` through a reviewed pull request with a green CI gate;
- the tests required for the change type are in place ([testing strategy](testing-strategy.md#required-tests-by-change-type));
- documents describing the changed behavior are updated in the same pull request;
- loss-report and snapshot changes are reviewed and approved;
- follow-up work is recorded as issues;
- the closing comment states what changed, links the pull request and lists open items.

## Duplicates

- A newer duplicate is closed with a link to the older issue, unless the newer one is clearly better
  specified; then the older one is closed and its discussion linked.
- Useful information from the closed duplicate is copied into the remaining issue as a comment.
- Duplicate pull requests: the one linked to the assigned person's claim proceeds; others are closed
  with thanks and a pointer to where help is useful.

## Follow-up work

- Work found during a change that is outside its scope becomes a new issue, never an unrelated change
  in the same pull request.
- The new issue links to the issue or pull request where it was found and states why it matters.
- Small findings of the same kind in the same area are collected in one issue rather than many.
- `TODO` comments in code are not a tracking mechanism; if a comment refers to future work, it names
  the issue number.
