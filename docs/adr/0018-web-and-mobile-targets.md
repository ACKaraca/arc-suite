# ADR-0018: Web and mobile targets from the same Rust code

Products may ship a browser build and tablet and phone builds compiled from the same Rust crates and
UI kit as their desktop applications; desktop applications stay native Rust with no web view.

- **Status:** Accepted
- **Scope:** Suite
- **Clarifies:** [ADR-0002](0002-native-ui-egui-on-wgpu-and-winit.md) and principle 1 of the
  [vision](../vision.md#principles)

## Context

Principle 1 and [ADR-0002](0002-native-ui-egui-on-wgpu-and-winit.md) define the desktop
applications: native Rust, egui on wgpu and winit, no web-view shell, no JavaScript or TypeScript.
They do not say whether a product can reach a browser, a tablet or a phone. Users need to view,
measure and mark up drawings and models on site and away from a workstation, to sketch building
masses on a tablet, and to open Arc Hub documents and issues in the field, often without a
connection.

The foundation is already shaped for more targets: layers L0 to L4 build headless and for
WebAssembly, wgpu runs on browser and mobile graphics interfaces, and Arc Hub's browser client already
embeds the Arc Review viewer compiled to WebAssembly
([ADR-0011](0011-collaboration-offline-first-hub-optional-staged-multi-user.md)).

## Decision

- Principle 1 is unchanged for desktop applications: no web-view shell and no JavaScript or
  TypeScript user interface.
- A product may, in addition, ship a **browser build**: the same Rust crates compiled to WebAssembly,
  with the same UI kit (`arc-ui` on egui and wgpu) drawing into a browser canvas. The page that loads
  it contains only the minimal loader the browser platform requires; no product logic or user
  interface is written in JavaScript or TypeScript.
- A product may ship **tablet and phone builds**: the same crates and UI kit built for iOS and
  Android, with the platform integration each operating system requires kept in `arc-app`.
- These are targets of the same code, not separate applications. A browser or mobile build uses the
  same document model, commands, file formats and Hub API as the desktop application; it may expose a
  subset of the product's commands, chosen per product.
- In scope: DraftBuilder in the browser and on mobile (viewing, measuring, markup and light editing
  first, then full 2D editing); ArchBuilder concept massing on tablets and in the browser; Arc Review
  on mobile; native Arc Hub mobile clients for documents, issues and markups with offline field use.
  Browser authoring through Arc Hub opens the browser build of the product that owns the document.
- Touch and small-screen interaction follows one set of conventions in `arc-ui`
  ([user experience](../architecture/user-experience.md#touch-and-small-screens)).

## Consequences

- Users reach drawings, models and issues from a browser, a tablet or a phone without a second
  implementation of any product behavior.
- Crates below the UI layer must keep building for WebAssembly and for mobile targets; CI adds those
  builds for every product that declares such a target.
- `arc-ui` and `arc-app` must cover touch input, small screens, platform lifecycles, sandboxed file
  access and offline storage on browser and mobile platforms.
- Browser builds are bounded by browser memory and threading limits; each product states which
  workflows its browser build supports.
- Release pipelines gain browser bundles and store packages for mobile platforms.
- Accessibility and localization requirements apply to every target, through the same AccessKit and
  Fluent layers.

## Alternatives considered

- **Desktop only.** Leaves field and remote work to other tools and keeps users from their own data
  away from a workstation.
- **Separate web and mobile applications in JavaScript, TypeScript or platform toolkits.** A second
  implementation of every behavior, a second language and a second safety model; behavior drifts
  between targets.
- **Web-view shells around the desktop user interface.** Excluded by principle 1 and by
  [ADR-0002](0002-native-ui-egui-on-wgpu-and-winit.md).
- **Remote rendering of a desktop application streamed to the device.** Requires a connection and a
  server for every user, which conflicts with principle 5.

## Trigger to reopen

A required browser or mobile capability (text input methods, accessibility on a mobile platform,
memory for representative models) cannot be met by the shared UI kit without a permanent fork, or a
browser build cannot run without product logic in JavaScript.
