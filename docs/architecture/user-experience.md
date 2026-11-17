# User experience standards

The interaction standards shared by every Arc desktop application and by the browser and mobile
builds of a product. They are implemented once in `arc-ui` and `arc-app` so that a user who knows
one product can work in another. Products add domain tools; they do not change these conventions.

## Workspace layout

- One main window per application, holding one or more documents as tabs. Additional windows can be
  torn off for documents, views and panels on multiple monitors.
- A docking workspace: the viewport area in the center; panels (model browser, properties, layers or
  categories, command history, issues) dock to any edge, stack as tabs, float or auto-hide.
- Fixed regions: menu bar, a contextual tool area showing the options of the active tool, the
  command line, and a status bar with coordinates, units, snap and constraint toggles, background
  task progress and connection state.
- Named workspace layouts are saved per user, can be exported and imported, and include a reset to
  the default.
- Layout state never lives in documents; documents hold only document data such as saved views.

## Command access

Every command is reachable in four ways, all backed by the same registry
([automation and assistance](automation-and-assistance.md#one-command-model)):

| Surface | Behavior |
|---|---|
| Menus | Native menu bar on every platform; every command appears in a menu or in a documented panel. Context menus show commands valid for the current selection. |
| Toolbars and tool palettes | Configurable groups of commands with icons and labels; icons are original and always have a text label available as a tooltip and accessible name. |
| Command line | Accepts command identifiers, short aliases and parameters typed in sequence; shows prompts of the active interactive command, lists options as clickable keywords, and keeps a searchable history. Aliases are user-configurable. |
| Command palette | Opened with one shortcut; fuzzy search over command labels, aliases, recent commands, settings and panels; shows the shortcut of each result. |

Shortcuts are user-configurable, conflicts are reported, and the shortcut map is exportable.

## Selection

- Click selects; Shift adds; Ctrl (Command on macOS) toggles; Escape clears and cancels the active
  command.
- Window selection (left to right) selects fully enclosed objects; crossing selection (right to left)
  selects touched objects. Lasso and polygon selection are available.
- Selection filters by type, layer or category and by property; saved selection sets live in the
  document.
- Pre-selection highlight shows what a click will select; cycling selects among overlapping objects.
- Commands accept a pre-selection or prompt for one; the prompt states what kinds of objects are
  valid.
- Selection is synchronized across the viewport, the model browser and the properties panel.

## Precision input and units

- Every length, angle and quantity field accepts expressions with units (`3.2 m + 150 mm`,
  `45deg`, `=width/2` where parameters exist) and displays values in the document's display units
  with its configured precision.
- Coordinate entry from the command line or a heads-up input accepts absolute, relative and polar
  forms, typed while the pointer moves.
- Object snaps (endpoint, midpoint, center, intersection, perpendicular, tangent, nearest and
  domain-specific points) with visible markers and tooltips; snap and grid toggles are in the status
  bar.
- Tracking and alignment guides from snap points; ortho and angle constraints.
- Units are stored according to [ADR-0008](../adr/0008-units-precision-and-coordinates.md); display
  units never change stored values.

## Property editing

- The properties panel shows the properties of the selection grouped by category, with mixed values
  indicated for multi-selection; editing a mixed value applies to all selected objects.
- Each edit is a command and one undo step; dragging a numeric value is one undo step on release.
- Fields validate as the user types and show the reason for an invalid value; invalid values are
  never applied.
- Read-only and derived properties are visibly distinct and explain where their value comes from.
- Type and instance properties are distinguished wherever the domain has types.

## Undo

- Undo and redo apply to whole commands and are available for every change, whichever surface
  issued it.
- The command history panel lists undoable steps by label and allows undoing back to a chosen step.
- Undo history is per document and survives autosave recovery.
- View changes (pan, zoom, orbit) are not undo steps; a separate view history navigates them.
- Operations that cannot be undone, such as publishing to Hub, say so before they run.

## Keyboard completeness

- Every command, panel, field and dialog is reachable and operable with the keyboard alone.
- Focus order is logical and visible; focus is never trapped.
- The viewport supports keyboard navigation (pan, zoom, orbit, standard views) and keyboard
  selection cycling.
- Interactive commands accept typed input for every point, distance and option they request.
- Drag-and-drop operations have keyboard or command equivalents.

## Theming

- Light, dark and high-contrast themes; the default follows the operating system.
- Themes are data: a set of named color, spacing and typography tokens. Products use tokens, never
  literal colors.
- Viewport background and drafting colors are configurable separately from UI themes.
- Information is never conveyed by color alone; states also use shape, text or pattern.

## Touch and small screens

Browser, tablet and phone builds of a product ([ADR-0018](../adr/0018-web-and-mobile-targets.md))
follow the conventions above, adapted once in `arc-ui`:

- Touch gestures: one finger selects and drags, two fingers pan, pinch zooms and twist orbits;
  a long press opens the context menu. Every gesture has an on-screen control or command
  equivalent.
- Touch targets are at least 44 by 44 device-independent pixels; snap markers and handles grow under
  touch input.
- Pen input is distinguished from touch: the pen draws and marks up, the finger navigates, and
  pressure is available to tools that use it.
- On small screens the docking workspace collapses to one viewport with panels as sheets that slide
  in from an edge; the command palette and the tool options remain one tap away.
- Precision entry uses the same expression fields with an on-screen numeric keypad that includes
  units and operators; coordinate entry is never gesture-only.
- A connected keyboard and pointer restore the desktop conventions, including the command line.
- Offline state, pending synchronization with Arc Hub and storage limits are shown in the status
  area.

## Accessibility baseline

The baseline is a release criterion for every application
([ADR-0016](../adr/0016-localization-and-accessibility.md)):

| Requirement | Standard |
|---|---|
| Keyboard operation | Complete, as above |
| Accessible names and roles | Every control exposes name, role, state and value through AccessKit to the platform's assistive technologies |
| Scaling | UI scale from 100% to 200% at minimum, following the operating system setting, without loss of content or function |
| Contrast | Text and essential graphics meet a contrast ratio of at least 4.5:1 for text and 3:1 for UI components in standard themes; high-contrast themes exceed it |
| Motion | Animations can be disabled; nothing flashes more than three times per second |
| Timing | No operation requires a response within a time limit |
| Status | Progress, errors and notifications are announced to assistive technologies |
| Verification | UI tests query controls through the accessibility tree; every panel has a test that fails if a control lacks an accessible name |

## Localization

- All UI strings are message identifiers resolved through Fluent catalogues; source strings are
  English; Turkish is the first complete translation.
- Plurals, grammatical case and locale-specific casing (for example Turkish dotted and dotless i)
  are handled by the localization layer, never by string concatenation.
- Numbers, dates and units are formatted by locale; model data and file formats are locale
  independent.
- Right-to-left layout is designed in: panels mirror, text fields support bidirectional text, and
  icons with direction are mirrored where meaning requires.
- A pseudo-locale with expanded and accented strings is used in tests to find truncation and
  hard-coded text.
- Translators work from the meaning of the English source and its translator comment, never from
  another product's string tables.
