# ADR-0021: Name comparison

Names in documents are stored exactly as entered and compared by Unicode case folding with the four
letters i, ı, İ and I treated as one equivalence class, independent of the interface language and of
the machine's locale.

- **Status:** Proposed
- **Scope:** Suite
- **Relationship:** Extends [ADR-0016](0016-localization-and-accessibility.md); changes none of its
  decisions. It affects more than one repository, so it needs an accepted RFC before it is accepted.
- **Approval:** Approved in principle by the maintainer on 2026-11-20.

## Context

Casing rules differ by language: Turkish distinguishes dotted and dotless i, and a comparison that
follows the machine's locale gives different results on different machines. Documents are exchanged
between machines and users, and uniqueness of names (levels, views, sheets, layers) and name search
must give the same answer everywhere.

## Decision

- Names are stored exactly as entered; no normalization changes the stored text.
- Every comparison and search of names, including uniqueness checks, uses Unicode case folding with
  the letters i, ı, İ and I forming one equivalence class.
- The comparison does not depend on the interface language or on the machine's locale.
- One implementation of the comparison lives in `arc-base` and every product uses it.

## Consequences

- A document behaves the same on every machine; two names that differ only in the casing of these
  letters are duplicates.
- Names that differ only in case are rejected where names must be unique.
- Products test the comparison with English and Turkish names.

## Alternatives considered

- **Compare by the machine's locale.** The same document would accept a name on one machine and
  reject it on another.
- **Compare byte for byte.** Names that look the same to a user would not be duplicates.
- **Normalize names when stored.** It loses what the user typed.

## Trigger to reopen

A language needs a different equivalence class for names, or the Unicode case folding rules change
in a way that alters existing comparisons.
