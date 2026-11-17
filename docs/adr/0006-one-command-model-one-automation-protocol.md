# ADR-0006: One command model, one automation protocol

Every change is a command defined once in `arc-command`, and every product exposes its command registry
through the same surfaces, including the Arc Automation Protocol.

- **Status:** Accepted
- **Scope:** Suite

## Context

Users change documents through menus, toolbars, a command line, macros and scripts; pipelines change
them headlessly; Arc Hub runs jobs; assistants propose changes. If each surface had its own way of
changing documents, each would need its own validation, undo integration, security checks,
documentation and tests, and the surfaces would drift apart.

## Decision

- `arc-command` defines a command as: a stable identifier, a label, parameters with a machine-readable
  schema, an enabled predicate, a non-interactive `run`, and an optional interactive prompt machine.
- Every product exposes its registry through menus and toolbars, the command line and palette, macro
  recording, the **Arc Automation Protocol** (JSON-RPC over stdio or a local socket), a headless CLI,
  and a Model Context Protocol endpoint derived from the same registry.
- Programmatic execution never opens a dialog.
- Scripting (Python) is a client of the protocol and runs out of process. No interpreter is embedded in
  the first releases.

## Consequences

- One surface is secured, documented and tested. The command catalogue for documentation, CLI help and
  tool lists is generated from the registry.
- Every command is usable headlessly, which makes automated testing of product behavior direct.
- Command authors must separate parameter collection (the prompt machine) from execution (`run`).
- Out-of-process scripting adds call overhead; batch operations and transaction grouping in the
  protocol keep it acceptable.
- A product that needs a compatibility interpreter for an established customization language of its
  domain records that in its own ADR; the interpreter calls the same registry and permission model.

## Alternatives considered

- **A separate scripting API per product.** Duplicates validation and drifts from the UI.
- **An embedded interpreter as the primary automation path.** Couples applications to an interpreter
  runtime, enlarges the attack surface in-process, and splits automation between in-process and
  external tools.
- **UI automation for scripting.** Brittle and impossible headlessly.

## Trigger to reopen

Out-of-process scripting cannot meet interactive performance needs for a documented workflow, which
would justify embedding an interpreter.
