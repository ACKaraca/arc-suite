# RFCs

The process for proposing and deciding suite-wide changes. Decisions inside one repository use that
repository's ADRs instead ([GOVERNANCE.md](../../GOVERNANCE.md#how-decisions-are-made)).

## When an RFC is needed

An RFC is required for any change that:

- affects more than one repository;
- changes a run-time contract: the Arc document package, the Arc Scene contract, the Hub API or the
  Arc Automation Protocol;
- changes a native file format's major version or the compatibility promise;
- changes the `arc-core` layer table or the dependency rule;
- supersedes or amends a suite ADR;
- changes the agent rules, the dependency allowlist, the clean-room policy or governance;
- creates or retires a repository.

Clarifications that do not change meaning, and changes confined to one repository, do not need an
RFC.

## Process

1. **Proposal.** The author copies the template to `docs/rfcs/NNNN-<slug>.md` using the next free
   number and opens a pull request with status *Draft*. An issue of type research may precede it.
2. **Comment period.** When the author marks the RFC *Proposed*, a suite maintainer announces a
   comment period of fourteen days in the pull request and in the affected repositories. The author
   answers every comment and revises the text; substantial revisions may extend the period.
3. **Decision.** After the comment period, suite maintainers other than the author decide by lazy
   consensus: with no unresolved objection, a suite maintainer accepts the RFC. Unresolved objections
   are decided by majority of the suite maintainers voting, excluding the author, and the reasoning is
   recorded in the RFC.
4. **Merge.** The pull request merges with the final status. Accepted RFCs result in ADRs, document
   changes and issues in the affected repositories, linked from the RFC.
5. **Implementation.** Implementation follows through the normal issue workflow. An RFC is never
   edited in substance after acceptance; a change is a new RFC that supersedes it.

## Status vocabulary

| Status | Meaning |
|---|---|
| Draft | Being written; not open for decision |
| Proposed | Comment period open |
| Accepted | Decided; implementation may proceed |
| Rejected | Decided against; the reasoning is recorded |
| Withdrawn | Abandoned by the author before a decision |
| Superseded | Replaced by a later RFC, which is linked |

Only *Accepted* RFCs constrain work. A *Draft* or *Proposed* RFC is never cited as a rule.

## Index

Each RFC is listed here, in number order with its title and status, by the pull request that adds
it. RFC numbers start at 0001.

## Template

```markdown
# RFC-NNNN: <Title>

<One sentence stating what the RFC proposes.>

- **Status:** Draft | Proposed | Accepted | Rejected | Withdrawn | Superseded by RFC-NNNN
- **Author:** <GitHub handle>
- **Affected repositories:** <list>
- **Comment period:** <opened and closed, filled in by a suite maintainer>

## Motivation

<The problem, who has it and why existing decisions do not solve it.>

## Proposal

<The change, precisely enough to implement and to check compliance.>

## Compatibility and migration

<Effect on contracts, file formats, existing documents and repositories; migration steps.>

## Alternatives considered

<Each alternative and why it was not chosen.>

## Risks and open questions

<What could go wrong; what remains to be decided.>

## Implementation plan

<ADRs to write, documents to change, issues to open, in order.>

## Decision

<Filled in at decision: outcome, date of decision record, deciding maintainers, reasoning for
resolved objections.>
```
