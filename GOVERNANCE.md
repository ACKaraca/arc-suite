# Governance

This document defines the roles in the Arc project, how decisions are made and how the governance
itself changes. It applies to every Arc repository.

## Roles

| Role | Scope | Responsibilities | Permissions |
|---|---|---|---|
| Contributor | Anyone | Opens issues, submits pull requests, reviews, comments on ADRs and RFCs | Read; triage of own issues |
| Reviewer | One repository or one area of it | Reviews pull requests in the area and approves them for merge | Approval counts toward merge in the area |
| Maintainer | One repository | Owns the repository's direction, triage, merges, ADRs and releases | Write and merge; release |
| Suite maintainers | All repositories | Own `arc-suite`, the suite ADRs, the RFC process, the suite baseline, the dependency allowlist, governance and the trademarks | Administer the organization |

A person can hold several roles. The suite maintainers are a group of maintainers; until it has three
members, the owners of the [ACKaraca organization](https://github.com/ACKaraca) act as the suite
maintainers. Current role holders are listed in each repository's `CODEOWNERS` file and in the
organization's teams.

## How decisions are made

| Decision | Instrument | Who decides |
|---|---|---|
| Day-to-day change within existing decisions | Pull request | A reviewer or maintainer other than the author |
| Architecture decision inside one repository | ADR in that repository's `docs/adr/` | The repository's maintainers |
| Change that affects more than one repository, a public contract, a file format, the layer table, a suite ADR, the agent rules or the dependency allowlist | [RFC](docs/rfcs/README.md) in `arc-suite` | Suite maintainers other than the author |
| Governance change | Pull request to this file, after an RFC | Suite maintainers, unanimous among those voting |

**Lazy consensus.** ADRs and RFCs are decided by lazy consensus after a comment period: seven days
for an ADR, fourteen days for an RFC. If no unresolved objection remains at the end of the period, a
maintainer other than the author accepts the proposal. An objection must state a technical reason and,
where possible, a change that would resolve it.

**Unresolved objections.** If objections remain, the maintainers responsible for the decision
discuss them in the proposal and decide by simple majority of those voting; the suite maintainers
decide for RFCs and for disputes that a repository's maintainers cannot settle. The decision and its
reasoning are recorded in the ADR or RFC.

**Reopening.** Every ADR names the trigger that reopens it. Reopening a decision requires a new ADR
or RFC that supersedes the old one; accepted records are never edited in substance.

## Release authority

- A repository's maintainers decide when the repository is released and sign off its release notes.
- The suite maintainers name the `arc-core` tag that forms each suite baseline and approve a suite
  release once every participating product passes its release checks on that baseline.
- Signing keys and release credentials are held by suite maintainers only and used through the
  release pipeline; see [docs/engineering/release-process.md](docs/engineering/release-process.md).

## Gaining and losing roles

- **Reviewer:** nominated by a maintainer of the repository after sustained, high-quality
  contributions or reviews in the area; confirmed when no maintainer objects within seven days.
- **Maintainer:** nominated by a maintainer of the repository; confirmed by the suite maintainers
  when no suite maintainer objects within fourteen days.
- **Suite maintainer:** nominated by a suite maintainer from among maintainers; confirmed by the
  existing suite maintainers unanimously.
- **Stepping down:** anyone may step down at any time by notice in the repository.
- **Inactivity:** a reviewer or maintainer without project activity for twelve months moves to
  emeritus status after a notice period of thirty days. Emeritus members can return through the
  normal nomination path.
- **Removal:** a role can be removed by the suite maintainers for a breach of the
  [code of conduct](CODE_OF_CONDUCT.md), of the clean-room policy or of release-key custody, by
  majority excluding the person concerned.

## Conflict of interest

A maintainer discloses any employment, contract or financial interest that bears on a decision they
take part in, and abstains from decisions where the interest is direct. No one approves their own
pull request, ADR or RFC. Decisions about trademarks, release keys and the dependency allowlist
require approval by at least one suite maintainer without a disclosed interest in the matter.

## Changes to governance

Governance changes follow the RFC process with a fourteen-day comment period and are accepted by
unanimous vote of the suite maintainers who vote, with at least two votes. The change is merged as a
pull request to this file that links the RFC.
