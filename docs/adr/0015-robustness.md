# ADR-0015: Robustness

Production code does not panic, `unsafe` is confined to named FFI crates, every importer bounds its
resource use, command execution is guarded, every crash fix ships a regression test, and importers are
fuzzed.

- **Status:** Accepted
- **Scope:** Suite

## Context

Arc opens files produced by other parties and other software, receives messages from servers and
scripts, and holds documents that represent weeks of professional work. A crash loses work and trust;
a malformed file that crashes an application is also a security defect. Rust prevents many memory
errors but still allows panics, unbounded allocation and unsafe code.

## Decision

- Production code does not panic: `unwrap`, `expect`, `panic!`, `todo!` and `unreachable!` are denied
  by lint ([coding standards](../engineering/coding-standards.md#workspace-lint-table)).
- `unsafe` is forbidden outside named FFI crates. Adding an FFI crate requires an ADR in the owning
  repository that names it.
- Every importer bounds recursion, allocation and counts.
- Command execution is guarded: a fault inside a command restores the document to its state before the
  command, and the user is told what failed.
- Every crash fix ships a regression test.
- Importers and protocol decoders are fuzzed.

The initial register of FFI crates:

| FFI crate | Repository | Native library |
|---|---|---|
| B-rep kernel binding under `arc-brep` | `arc-core` | Open CASCADE ([ADR-0003](0003-geometry-owned-api-two-tiers.md)) |
| PDF import binding under `arc-io-pdf` | `arc-core` | PDFium |
| Ray-intersection binding | `renderbuilder` | Embree |
| Denoiser binding | `renderbuilder` | Open Image Denoise |
| Color-management binding | `renderbuilder` | OpenColorIO |

## Consequences

- Error paths are explicit in every API; code must handle every failure case.
- Reviewers reject panicking constructs and unbounded reads as defects.
- Fuzzing infrastructure and corpora are maintained continuously.
- A panic-hunt test calls every registered command with adversarial parameters on every pull request
  ([testing strategy](../engineering/testing-strategy.md)).
- FFI crates carry extra verification: sanitizer runs, safe wrappers, and conversion of foreign
  failures into errors.

## Alternatives considered

- **Allowing panics for "impossible" states.** Every such state has eventually been reached by a
  crafted or corrupted file; an error is always recoverable, a panic is not.
- **Catching panics as the main strategy.** Retained only as a last-resort guard around commands; it
  does not replace error handling.
- **Unrestricted `unsafe` with review.** Does not scale; confinement makes audits possible.

## Trigger to reopen

A required capability cannot be implemented within these constraints at acceptable performance, for
example a hot loop where bounds checks provably dominate and cannot be eliminated safely.
