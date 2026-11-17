# ADR-0012: Extensibility

Arc is extended in three layers, all on the command model: scripts through the automation protocol,
visual node graphs on `arc-nodes`, and sandboxed WebAssembly plug-ins. Native plug-ins are not
supported.

- **Status:** Accepted
- **Scope:** Suite

## Context

Professional users and firms automate repetitive work, encode design rules and add domain-specific
tools. Extensions run inside applications that hold valuable documents, often come from third parties,
and must keep working across application versions and platforms. Native dynamic-library plug-ins can
crash the host, bypass every security boundary and break with every compiler or API change.

## Decision

- **Scripts** use the automation protocol, with an official Python client; they run out of process.
- **Visual node graphs** run on `arc-nodes`. Parametric design, procedural modeling, materials and
  automation rules use the same engine and editor across products.
- **Plug-ins** are WebAssembly components with no ambient authority, resource limits (memory, fuel,
  wall-clock time) and transactional application of changes.
- No native dynamic-library plug-ins.
- Packages carry a manifest (identifier, version, publisher, host contract version, requested
  capabilities, contributions). The host assigns trust; a package cannot grant itself trust.

## Consequences

- Every extension acts through commands, so undo, validation and permissions apply uniformly.
- Plug-ins run identically on every platform and cannot crash or corrupt the host.
- Plug-in authors work within the capabilities the host exposes; operations that need more capability
  require new host interfaces, decided by the product.
- Performance-critical extensions that would need native code must be contributed to the product or
  foundation instead.
- The plug-in contract version follows the same compatibility rules as the other contracts.
- Details are in [automation and assistance](../architecture/automation-and-assistance.md#plug-ins).

## Alternatives considered

- **Native dynamic-library plug-ins.** Maximum power, but unsafe, platform-specific and fragile across
  versions.
- **An embedded scripting language as the plug-in mechanism.** Interpreted in-process with broad
  access; harder to sandbox and limit.
- **Separate node systems per product.** Duplicated engines and editors; skills would not transfer.

## Trigger to reopen

A class of extension that users demonstrably need cannot be built within the WebAssembly sandbox with
reasonable host interfaces and performance.
