# Documentation standards

The documents every Arc repository carries, how they are written, how capabilities are identified,
the templates for ADRs and RFCs, and how documents stay true.

## Document set

Every product repository carries:

| Path | Contents |
|---|---|
| `README.md` | Status banner during the design phase; what the product is; users; capability areas; suite contracts used; architecture diagram; roadmap summary; documentation map; contributing; license; trademark notice last |
| `AGENTS.md` | Contributor and agent reference with the suite rules block ([agent rules](agent-rules.md)) |
| `docs/README.md` | Documentation map |
| `docs/product/vision.md` | Purpose, users, principles, scope, non-goals, positioning, differentiators |
| `docs/product/capabilities/README.md` | Capability index and legend |
| `docs/product/capabilities/NN-<area>.md` | One file per functional area: `ID | Capability | Behavior | Tier | Phase` |
| `docs/product/workflows.md` | Reference end-to-end workflows used as acceptance scenarios |
| `docs/product/open-questions.md` | Behaviors that need verification or a decision, as `OQ-nn` entries |
| `docs/architecture/overview.md` | Crates, layering, run-time flow, use of `arc-core`, threading, performance budgets |
| `docs/architecture/domain-model.md` | Object types, relationships, invariants, regeneration rules |
| `docs/architecture/interoperability.md` | Formats by fidelity level and direction, validation, loss reporting |
| `docs/roadmap.md` | Milestones mapped to suite phases with exit criteria |
| `docs/adr/README.md` and `docs/adr/NNNN-<slug>.md` | Product decisions |
| `docs/glossary.md` | Product vocabulary |

`arc-core` carries the architecture, ADR, roadmap and glossary documents, the specifications of the
contracts it owns, and the technology baseline. `arc-suite` carries the suite documents listed in its
[documentation map](../README.md).

The single positioning paragraph of a product, naming the professional scope it covers, lives in that
product's `docs/product/vision.md`; no other document compares Arc with other products.

## Style rules

- American English; plain, declarative, present tense for design statements ("the model stores").
- No marketing adjectives, emojis or exclamation marks.
- One H1 per file, followed by a one- or two-sentence purpose statement, then sections. No
  introduction that restates the title and no closing summary.
- Tables for inventories; numbered lists for procedures; Mermaid for diagrams.
- Wrap prose at about 100 characters.
- No dates, benchmarks, user numbers, prices or sponsors. Performance targets are stated as targets
  with a rationale.
- Documents never claim that something is implemented. Specifications use "the product provides" or
  "is designed to"; READMEs carry the status banner until the first release.
- Each fact is stated once; other documents link to it. Links are relative inside a repository and
  absolute (`https://github.com/ACKaraca/<repository>/blob/main/<path>`) across repositories, and only
  to files that exist.
- Other companies' names appear only for file formats, standards and protocols, in factual
  interoperability or migration statements, for libraries in architecture discussions, and once in a
  product's positioning paragraph. Features are never described by naming another product's feature.
- Behavior is described in original words from an understanding of the function. Another product's
  documentation structure, dialog text, option wording, icons and default-value lists are never
  reproduced.
- UI labels in documents are Arc's own labels.

## Capability identifiers

Capabilities are identified as `<PREFIX>-<AREA>-<NNN>`:

| Prefix | Repository |
|---|---|
| `AB` | ArchBuilder |
| `DB` | DraftBuilder |
| `CB` | CivilBuilder |
| `SB` | SolidBuilder |
| `MB` | MeshBuilder |
| `RB` | RenderBuilder |
| `RV` | Arc Review |
| `HB` | Arc Hub |
| `CORE` | Foundation |

- `AREA` is a short upper-case token (`WALL`, `SKETCH`, `CLASH`); `NNN` is numbered from 001 within
  the area.
- Identifiers are permanent and used once. A withdrawn capability keeps its row, marked withdrawn
  with a reason, and its number is never reused.
- Each capability has a tier and the suite phase in which it is planned:

| Tier | Meaning |
|---|---|
| T1 Core | Required for first production use |
| T2 Professional | Required for parity with established professional tools |
| T3 Advanced | Specialized or differentiating |

- Issues reference capability identifiers in their scope. Status is tracked in issues and on the
  project board, never in capability tables.

## ADR template

ADRs record decisions inside one repository (suite-wide decisions in `arc-suite/docs/adr`). The file
is `docs/adr/NNNN-<slug>.md`, numbered sequentially, slugged from the title.

```markdown
# ADR-NNNN: <Title>

<One sentence stating the decision.>

- **Status:** Proposed | Accepted | Superseded by ADR-NNNN | Withdrawn
- **Scope:** <repository or suite>

## Context

<The forces and constraints that require a decision.>

## Decision

<What is decided, precisely enough to check compliance.>

## Consequences

<What becomes easier, what becomes harder, what must now be done.>

## Alternatives considered

<Each alternative and why it was not chosen.>

## Trigger to reopen

<The observable condition that would justify revisiting the decision.>
```

## RFC template

The RFC template and process are defined in [RFCs](../rfcs/README.md).

## Open questions

- Each product keeps `docs/product/open-questions.md`. An entry has an identifier `OQ-nn`, the
  question in neutral words, the capabilities affected, and the evidence or decision that closes it.
- When a question is closed, the entry records the outcome and the document or ADR where the answer
  now lives; the identifier is never reused.
- Work to close a question is tracked as an issue of type research.

## How documents stay true

- A pull request that changes behavior updates the documents describing that behavior in the same
  pull request. Reviewers check this.
- Status, progress and percentages live in issues, milestones and the project board, never in
  documents. Documents state what the design is, not how far implementation has come.
- Decisions are changed by new ADRs or RFCs that supersede old ones; accepted records are not edited
  in substance.
- CI checks every relative link, the Mermaid syntax and the suite-rules block in `AGENTS.md`.
- Each document has an owner by path in `CODEOWNERS`.
