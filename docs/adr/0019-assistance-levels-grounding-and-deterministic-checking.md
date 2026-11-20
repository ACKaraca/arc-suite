# ADR-0019: Assistance levels, grounding and deterministic checking

Assistance runs at one of four declared levels, answers from the project with citations, has every
proposal checked by the product's deterministic engines before the user sees it, and ships a
capability only after it passes a task suite.

- **Status:** Proposed
- **Scope:** Suite
- **Relationship:** Extends [ADR-0014](0014-assistance-is-optional-and-provider-neutral.md); changes
  none of its decisions.

## Context

[ADR-0014](0014-assistance-is-optional-and-provider-neutral.md) makes assistance optional, provider
neutral and bound to the command model with preview and explicit apply. It leaves open what an
assistant may do short of applying a change, what it may do in unattended runs, how its answers are
tied to the project, who checks its proposals, how data is limited, and how a capability is judged
ready. Language models produce plausible but unverified values, follow instructions embedded in the
content they read, and change behavior between versions. Engineering documents carry liability:
a wrong value presented as checked is worse than no assistance.

## Decision

- **Levels.** A session runs at one of four levels: A1 explain and search (read-only permission
  class only), A2 propose with preview, A3 act under approval, A4 batch and agent runs with a
  declared scope and budget whose results land in a copy, branch or draft for review. The rules of
  each level are specified in [assistance](../architecture/assistance.md#levels-of-assistance).
- **One surface.** The assistant's tools are the automation protocol's methods and the product's
  commands, queries and checkers. No product adds an assistant-only path to change a document.
- **Grounding.** Statements about the project cite objects, derived results at a revision, help
  pages, standards and rules, or user attachments. Numbers about the project come from queries and
  checkers, not from model arithmetic. Unsourced statements are labeled as general knowledge.
- **Checker of record.** Every proposal is validated against its schemas, previewed as a dry run and
  checked by the product's deterministic checkers before it is shown. The assistant never certifies
  validity or compliance; the panel states which checkers ran at which revision.
- **Human control.** Proposals are scoped, bound to a base revision, reviewable item by item,
  selectively accepted after a fresh preview of the accepted subset, and applied as one undoable
  step with a provenance mark ([ADR-0020](0020-assistant-provenance-and-command-policy.md)).
  Signing, releasing, approving and issuing are never available to an assistant.
- **Data envelope.** Data leaves the machine only in the classes the user approves for the project
  and provider, after redaction, with a send preview; the whole document is never sent by the host.
  Content read from documents and tools is untrusted data.
- **Budgets.** Every request has bounds on tool calls, size, revisions, time and changed objects; A4
  runs also carry a money bound. Exceeding a bound stops the request without unapproved changes.
- **Evaluation gate.** Each product keeps a task suite built from its reference workflows, runnable
  in a replay mode without network access. A capability is offered only when its tasks meet the
  product's threshold; scope violations and unit errors have a threshold of zero.
- **Open host.** The assistant host, provider adapters, the host's instructions to the model, tool
  definitions and task suites are open. Hosted services remain outside the repositories.
- **Readiness classes.** Product plans classify assistance capabilities as R1 (general models
  through the command surface), R2 (needs product engines or data first) or R3 (research-grade, not
  promised).

## Consequences

- Products must expose their validation engines as read-only checker commands with findings that
  name objects and rules; these serve scripts and continuous integration as well.
- Products must keep reference workflows that can be turned into fixtures with deterministic
  acceptance checks.
- The panel needs a review interface for proposals, a send preview and a local assistance log.
- A capability that a model performs well but the product cannot check is not offered until a
  checker exists; this delays some features in exchange for trust in the others.
- Recorded model responses make assistance testable in continuous integration without cost.

## Alternatives considered

- **Leave the assistant's scope to each product.** Products would diverge in what an assistant may
  do and in how data leaves the machine; users could not rely on one set of rules.
- **Let the model validate its own output.** Self-assessment by the model is not reproducible and
  cannot carry engineering responsibility.
- **Ship assistance features without measured evaluation.** Quality would vary with provider and
  model versions without anyone noticing.
- **Keep the host's instructions private.** Users of local models and their own providers need the
  same host; private instructions would make the open product depend on a service.

## Trigger to reopen

A level cannot express a documented assisted workflow, a checker family cannot be built for a
capability users need, or task-suite results show that the evaluation gate blocks capabilities
that users accept in practice.
