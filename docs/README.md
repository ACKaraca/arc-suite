# Documentation map

The suite documents, one line each. Product-specific documentation lives in each product repository.

## Suite definition

| Document | Contents |
|---|---|
| [vision.md](vision.md) | Why the suite exists, who it serves, principles, non-goals, success criteria |
| [products.md](products.md) | Product scopes, native documents, boundaries, hand-offs and the responsibility matrix |
| [roadmap.md](roadmap.md) | Suite phases 0 to 5 with goals, cross-repository dependencies and exit criteria |
| [glossary.md](glossary.md) | Suite vocabulary |

## Architecture

| Document | Contents |
|---|---|
| [architecture/overview.md](architecture/overview.md) | System context, repository topology, dependency rule, `arc-core` layers, product template, workstation run-time picture |
| [architecture/contracts.md](architecture/contracts.md) | Document package, Arc Scene contract, Hub API and Arc Automation Protocol: ownership, versioning, compatibility |
| [architecture/interoperability.md](architecture/interoperability.md) | Suite format matrix, interoperability policy, exchange routes, validation and corpora |
| [architecture/collaboration.md](architecture/collaboration.md) | Offline-first collaboration in two stages, branching and merging, identity and permissions |
| [architecture/automation-and-assistance.md](architecture/automation-and-assistance.md) | Command model, automation protocol, scripting, node graphs, plug-ins, optional assistance, security boundaries |
| [architecture/user-experience.md](architecture/user-experience.md) | Interaction standards shared by every application |

## Engineering standards

| Document | Contents |
|---|---|
| [engineering/agent-rules.md](engineering/agent-rules.md) | Canonical suite rules for contributors and coding agents, rationale, change procedure |
| [engineering/repository-standards.md](engineering/repository-standards.md) | Layout, naming, required files, branch protection, suite baseline, versioning, labels |
| [engineering/coding-standards.md](engineering/coding-standards.md) | Rust style, lints, errors, concurrency, performance, parsers, logging, localization |
| [engineering/testing-strategy.md](engineering/testing-strategy.md) | Test pyramid, required tests, snapshots, corpora, golden models, fuzzing, budgets, CI gate |
| [engineering/dependency-policy.md](engineering/dependency-policy.md) | License allowlist, adding dependencies, FFI, forks, supply chain, asset attribution |
| [engineering/clean-room-policy.md](engineering/clean-room-policy.md) | Permitted and prohibited inputs, derivation records, corpus admission |
| [engineering/documentation-standards.md](engineering/documentation-standards.md) | Document set, style, capability identifiers, ADR and RFC templates, keeping documents true |
| [engineering/issue-workflow.md](engineering/issue-workflow.md) | Issue types, statuses, labels, milestones, definition of done |
| [engineering/release-process.md](engineering/release-process.md) | Release trains, stages, signing, packaging, compatibility promise, support |

## Decisions

| Document | Contents |
|---|---|
| [adr/README.md](adr/README.md) | Index of the suite ADRs and the ADR template |
| [rfcs/README.md](rfcs/README.md) | RFC process, status vocabulary and template |
