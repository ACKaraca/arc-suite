# ADR-0002: Native UI: egui on wgpu and winit

All desktop user interface is built with egui, rendered through wgpu, with windows from winit, and
Arc-specific widgets live in `arc-ui`.

- **Status:** Accepted
- **Scope:** Suite

## Context

Arc applications are dense professional tools: docking workspaces, large property grids, trees,
command lines and, at their center, custom GPU viewports that render millions of objects. The UI
toolkit must embed those viewports without copying frames, run identically on Windows, macOS and
Linux, expose accessibility information, and be written in Rust (principle 1). The project cannot
afford to write and maintain its own toolkit.

## Decision

- Desktop UI uses egui in immediate mode, rendering through wgpu, with windowing through winit.
- Arc-specific widgets (docking workspace integration, viewport widget, property grid, outliner, command
  line and palette, tool options, numeric and unit input, node editor, theming) live in `arc-ui`.
  Products use these widgets rather than building their own variants.
- Accessibility is provided through AccessKit; native menus and file dialogs use platform integration
  crates.
- Upstream crates are used unmodified. Any fork requires a patch ledger entry with an upstream link and
  a removal condition ([dependency policy](../engineering/dependency-policy.md#forks-and-the-patch-ledger)).

## Consequences

- Custom GPU viewports are first-class: the viewport renders with the same wgpu device as the UI.
- One code path serves all three desktop platforms.
- The UI layer has a small API surface and is easy to test through the accessibility tree.
- Large tables, virtualized trees and rich text are not provided adequately by the toolkit; `arc-ui`
  must build virtualized widgets for them.
- Immediate-mode layout needs discipline for complex panels; `arc-ui` provides layout helpers so
  products do not each solve it.
- Toolkit releases with breaking changes are absorbed in `arc-ui`, not in every product.

## Alternatives considered

- **Web-view shells.** Excluded by principle 1; they also add a second language and a browser engine to
  every application.
- **Retained-mode toolkits that own their own renderer.** Embedding a model viewport is difficult or
  requires copying frames between renderers.
- **Writing an Arc toolkit.** Years of work that does not advance the products.
- **Platform-native toolkits per operating system.** Three UI implementations for every panel.

## Trigger to reopen

A required accessibility or text capability (screen-reader support, input methods, bidirectional
text) cannot be met without a permanent fork of the toolkit.
