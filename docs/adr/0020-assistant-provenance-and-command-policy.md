# ADR-0020: Assistant provenance and command policy

The automation protocol, the operation journal and the command catalog gain optional fields that
record where a change came from, mark objects an assistant produced until a person reviews them,
and state whether an assistant may run each command.

- **Status:** Proposed
- **Scope:** Suite (`arc-core`: `arc-doc`, `arc-command`; the Arc Automation Protocol; the Arc
  document package)
- **Relationship:** Implements the provenance and command-policy rules of
  [ADR-0019](0019-assistance-levels-grounding-and-deterministic-checking.md). Changes a contract
  and a file format, so it needs an accepted RFC before it is accepted.

## Context

[Assistance](../architecture/assistance.md#human-control) requires that every applied proposal is
one undo step labeled with its origin, that objects an assistant created or changed are visible as
such until reviewed, and that some commands are never available to an assistant. Today a protocol
session names its client in `initialize`, but a transaction record does not carry the kind of
client or the proposal it came from, the catalog has no per-command assistant policy, and nothing
in a document marks objects as unreviewed. Products already plan history labeled with its source
(user, script, plug-in, node graph, assistant).

## Decision

Proposed additions, all optional and backward compatible within protocol version 1 and the current
package major version:

1. **Origin on runs.** `command.run` and the transaction-group commands accept an optional
   `origin`: `kind` (`user`, `script`, `plugin`, `graph`, `assistant`, `job`), `client` (the session
   client name), `proposal` (an opaque identifier, at most 64 bytes) and `level` (`A1` to `A4`). A
   session declares a default origin in `arc.initialize`; a run cannot claim a kind other than the
   session's.
2. **Origin in the journal.** A transaction record stores the origin as an optional field;
   `query.history` returns it. History panels label transactions by origin.
3. **Provenance marks.** A transaction whose origin kind is `assistant` gives every object it creates
   or modifies a core-owned provenance record: origin, proposal identifier and revision. A
   reviewing command (`core.provenance.mark_reviewed`, run by a person on a selection) or any later
   user-origin edit of the object clears it. Marks are queryable and filterable, survive save and
   reload, and are not exported to other formats unless a product maps them to a property.
4. **Command policy.** Each catalog entry gains `assistant`: `allowed`, `confirm_each` or `never`.
   The default follows the side-effect class: read-only commands `allowed`; document changes
   `allowed`; file-system and network access `confirm_each`. Commands that change permissions,
   automation or assistance settings, credentials, release or lifecycle state, signatures or
   approvals are `never`. The server enforces the policy for sessions whose origin kind is
   `assistant`, and the Model Context Protocol endpoint maps it to tool annotations.

## Consequences

- The journal record and the document package gain an optional field and a core-owned record type;
  older readers ignore them, and documents without them stay valid.
- History, filters and reports can show what an assistant did, and reviewers can find unreviewed
  objects before issuing a document.
- Command authors set a policy on every command; the catalog test fails for a command whose policy
  is missing and whose side-effect class has no default.
- The origin is declared by the client; it is reliable for honest clients and the in-application
  host, not a security control against a malicious local process, which the session permissions
  already bound.

## Alternatives considered

- **Provenance from the journal alone, with no per-object record.** No format change, but marks
  vanish when history is trimmed and require a journal scan to filter objects.
- **An assistant-only exposure surface.** A separate surface duplicates the catalog and drifts from
  the automation surface; a policy field keeps one catalog.
- **No provenance.** Reviewers could not tell assisted content from authored content, which the
  safety rules of assistance require.

## Trigger to reopen

The RFC review finds that per-object records cost too much in large documents, or a collaboration
stage needs provenance in change sets exchanged through Arc Hub.
