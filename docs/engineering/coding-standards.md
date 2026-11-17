# Coding standards

Rules for Rust code in every Arc repository. They are enforced by the workspace lint table, the CI
gate and review. "Production code" means everything compiled into a shipped library, application,
CLI or server; tests, benchmarks, fuzz targets and `xtask` are not production code.

## Toolchain and style

- Stable Rust, edition 2024, pinned exactly in `rust-toolchain.toml` and updated deliberately, at
  most once per stable release. Foundation crates support the current stable version minus two.
- `rustfmt` with the default configuration; formatting is checked in CI.
- `clippy` runs with warnings as errors on all targets.
- No nightly features in production code.

## Workspace lint table

Every repository declares its lints once, in `[workspace.lints]` of the root manifest, and every crate
inherits them with `[lints] workspace = true`. The table contains:

| Group | Setting | Lints |
|---|---|---|
| No-panic policy | deny | `clippy::unwrap_used`, `clippy::expect_used`, `clippy::panic`, `clippy::todo`, `clippy::unimplemented`, `clippy::unreachable` |
| Indexing in parsers | deny in `arc-io-*`, product `-io` crates and protocol decoders | `clippy::indexing_slicing`, `clippy::arithmetic_side_effects` |
| Unsafe | forbid, except FFI crates (deny, with documented module-level allowances) | `unsafe_code` |
| Diagnostics output | deny in libraries | `clippy::print_stdout`, `clippy::print_stderr`, `clippy::dbg_macro` |
| Documentation | warn, deny in `arc-core` public APIs | `missing_docs`, `rustdoc::broken_intra_doc_links` |
| Hygiene | deny | `unused_must_use`, `clippy::wildcard_imports`, `clippy::mem_forget`, `clippy::exit` |
| Complexity | warn | `clippy::cognitive_complexity`, `clippy::too_many_arguments`, `clippy::too_many_lines` |
| Casting | warn, deny in parsers | `clippy::cast_possible_truncation`, `clippy::cast_sign_loss`, `clippy::cast_possible_wrap` |

Test modules may allow `expect_used` with a message that states what the test assumed. An
`#[allow]` in production code requires a comment stating why the lint does not apply and is
reviewed by a maintainer.

## Unsafe code

- `unsafe` is forbidden outside FFI crates named in an ADR; the initial register is in
  [ADR-0015](../adr/0015-robustness.md).
- An FFI crate exposes a safe API; no `unsafe` signature crosses its public boundary.
- Every `unsafe` block carries a `// SAFETY:` comment stating the invariant that makes it sound.
- FFI crates run their tests under Miri where the foreign code permits, and under address sanitizer
  in nightly CI.
- Foreign exceptions and aborts are caught at the boundary and converted to errors.

## Error handling

- Fallible functions return `Result`. Libraries define typed error enums per crate (deriving with
  `thiserror` is permitted); the shared error vocabulary lives in `arc-base`.
- Errors carry context: what was attempted, on which object or file, and the underlying cause.
- User-facing messages are produced at the application boundary from error values through message
  identifiers; library errors are never shown raw.
- Errors are never silently discarded. `let _ =` on a `Result` requires a comment.
- A command that fails leaves the document unchanged; the transaction is rolled back. Command
  execution is additionally guarded so that a fault restores the document state.
- Out-of-memory, cancellation and timeouts are expected outcomes with their own error variants.

## Naming

- Rust API naming guidelines apply: types `UpperCamelCase`, functions and modules `snake_case`,
  constants `SCREAMING_SNAKE_CASE`.
- Domain names follow the glossary of the repository and of the suite; one concept has one name.
- Units appear in names when a value is not a typed quantity: `offset_m`, `angle_rad`. Typed
  quantities from `arc-base` are preferred.
- Feature, type and command names are descriptive and original; they never reuse another company's
  feature names.

## Modules and crates

- A crate is created for a real boundary with a second user, not for organization alone.
- A module holds one concept; files above roughly 1,000 lines and functions above roughly 80 lines
  are candidates for splitting and need a reason in review to stay.
- Public APIs are minimal. Items are private by default; `pub(crate)` before `pub`.
- No cyclic dependencies between modules of one crate where a layering exists inside it.
- Generated code is marked as generated, committed with the script that produces it, and never
  edited by hand.

## Documentation comments

- Every public item in `arc-core` has a doc comment stating what it does, its units, its error
  conditions and its panics policy (always "does not panic" in production code).
- Examples in doc comments compile and run as tests.
- Comments explain why; code states what. Commented-out code is not committed.
- Each crate has a crate-level doc comment stating its responsibility and what it does not do.

## Concurrency

- The UI thread never blocks on I/O, regeneration, network or locks held by worker threads; work
  that may exceed a frame budget runs on workers.
- Data parallelism uses `rayon`; asynchronous I/O in the server and clients uses `tokio`. Desktop
  applications keep the async runtime off the UI thread.
- Shared state is minimized: workers receive immutable snapshots of the document (cheap through
  structural sharing) and return results tagged with the revision they were computed from; stale
  results are discarded ([ADR-0007](../adr/0007-regeneration-dependency-graph-and-revision-tagged-background-work.md)).
- Every long-running task is cancellable and reports progress.
- No lock is held across an `.await` or a call into foreign code. Lock order is documented where more
  than one lock exists.
- Determinism: given the same document revision, regeneration produces the same result regardless
  of thread count. Tests run with varied thread counts.

## Performance practice

- Performance budgets are stated in product architecture documents as targets with rationale and
  checked by benchmarks ([testing strategy](testing-strategy.md#performance-budgets)).
- Measure before optimizing; a performance change cites the benchmark it improves.
- Model coordinates are `f64`; GPU data is `f32` relative to a camera origin.
- Avoid per-object heap allocation in hot paths; use dense handles and arenas in stores.
- Regeneration is incremental: only stale nodes recompute.
- Large documents load lazily from the package; memory use is bounded and measured.

## Parsers and hostile input

Every file, network message, clipboard payload and plug-in result is hostile until validated.

- Readers bound recursion depth, nesting, total allocation, element counts, string lengths and
  decompressed size; limits are named constants with documented defaults and can be raised by the
  user for a specific file.
- Lengths and offsets read from input are checked before use; arithmetic on them is checked.
- No indexing without a bounds check in parsers; use iterators and `get`.
- Decompression, archive extraction and path handling reject absolute paths, parent-directory
  traversal and links that leave the target.
- A malformed part of a file produces a diagnostic and, where possible, partial import with a loss
  report; it never aborts the application.
- Every parser has a fuzz target; a crash found by fuzzing becomes a regression test.

## Logging

- `tracing` is the logging and instrumentation facade in every crate.
- Levels: `error` for failures that affect the user, `warn` for recovered problems, `info` for
  lifecycle events, `debug` and `trace` for diagnosis.
- Logs never contain secrets, tokens, document content or personal data beyond what is needed to
  diagnose; file paths are logged relative to the project where possible.
- Logs are written locally with rotation; nothing is sent anywhere without explicit opt-in.
- Performance-relevant spans are instrumented so traces can be recorded on demand.

## Localization of user-facing strings

- No user-facing string literal in code. Every label, message, tooltip and error text is a message
  identifier resolved through Fluent catalogues; English is the source.
- Messages use Fluent variables for values; strings are never assembled by concatenation.
- Each message has a translator comment when its meaning is not obvious from the identifier.
- Log messages, identifiers and file-format keys are not localized.
- CI checks that every identifier used in code exists in the English catalogue and that catalogues
  parse.
