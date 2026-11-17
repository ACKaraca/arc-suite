# ADR-0010: Rendering through the Arc Scene contract

Every product has the real-time viewport from `arc-gpu`; photoreal rendering is provided by
RenderBuilder as a separate process, driven through the Arc Scene contract.

- **Status:** Accepted
- **Scope:** Suite

## Context

Every Arc product needs interactive viewports, and most need photoreal output for presentation,
lighting analysis or final frames. A production path tracer is a large body of code with heavy
dependencies (ray-intersection libraries, denoisers, color management). Linking it into every
application would multiply binary size, build complexity and crash exposure, and would invite products
to diverge in how they render.

## Decision

- Every product uses the real-time viewport from `arc-gpu`.
- Photoreal rendering is provided by RenderBuilder as a separate process. A product exports an Arc
  Scene, aligned with OpenUSD, MaterialX and OpenPBR, and streams updates; frames return over local IPC.
- Products never link the path tracer.
- The same contract drives RenderBuilder's command-line renderer and render-farm workers, including
  those run by Arc Hub.
- The contract is owned by `arc-core` (`arc-scene`) and versioned as described in
  [contracts](../architecture/contracts.md#arc-scene-contract).

## Consequences

- One renderer is maintained for the whole suite, and every product benefits from its improvements.
- A renderer fault never crashes a host application or affects its document.
- Applications stay free of heavy rendering dependencies.
- Products must map their domain materials and lights to the Arc Scene vocabulary; the mapping is
  tested with conformance scenes.
- Interactive rendering depends on efficient delta updates keyed by stable identifiers.
- Scene export introduces latency for interactive rendering, which the live session protocol keeps
  small.

## Alternatives considered

- **Linking a path tracer into every product.** Duplicated heavy dependencies and shared crash domain.
- **A renderer per product.** Divergent results and duplicated effort.
- **Third-party renderers through plug-in interfaces as the primary path.** Would make photoreal output
  depend on software outside the project; third-party renderers can still consume exported OpenUSD.

## Trigger to reopen

Measured IPC overhead prevents interactive rendering from meeting its budget on reference scenes after
optimization of the live session protocol.
