# ADR-0016: Localization and accessibility

All UI strings are message identifiers resolved through Fluent catalogues, with English as the source
and Turkish as the first complete translation, and every application meets an accessibility baseline
verified by tests.

- **Status:** Accepted
- **Scope:** Suite

## Context

Arc serves professionals worldwide, many of whom work in languages other than English and some of whom
rely on assistive technologies. Localization and accessibility retrofitted late are expensive and
incomplete: hard-coded strings, concatenated messages and controls without accessible names spread
through every panel. Languages differ in plural rules, grammatical case, casing rules (Turkish dotted
and dotless i) and direction.

## Decision

- All UI strings are message identifiers resolved through Fluent catalogues.
- English is the source language; Turkish is the first complete translation.
- Right-to-left support is designed in from the start.
- Accessibility baseline: full keyboard operation, accessible names for every control, scalable UI and
  high-contrast themes, verified by tests. The detailed baseline is in
  [user experience](../architecture/user-experience.md#accessibility-baseline).
- These are release criteria for every application's first release.

## Consequences

- No user-facing string literal appears in code; CI checks catalogues and identifiers.
- Message composition uses Fluent variables, never concatenation.
- Every widget in `arc-ui` exposes name, role and state through AccessKit; UI tests use the
  accessibility tree, so missing names fail tests.
- Layouts must tolerate longer strings and mirrored direction; a pseudo-locale tests both.
- A second complete translation from the first release validates the whole pipeline, including casing
  rules that differ from English.

## Alternatives considered

- **English only at first.** Faster initially, but every later translation would require auditing every
  string in every product.
- **Keying catalogues by English text.** Simpler for translators, but any wording change breaks every
  translation and identical English words with different meanings collide.
- **Accessibility as a later milestone.** Retrofitting accessible names and keyboard operation across
  all panels costs more than building them in.

## Trigger to reopen

The localization system cannot express a required language's grammar, or the accessibility layer
cannot meet a platform's assistive-technology requirements.
