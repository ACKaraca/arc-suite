# ADR-0013: Licensing and contribution terms

Every Arc repository is licensed under Apache-2.0, contributions are accepted with a Developer
Certificate of Origin sign-off, dependencies are restricted to an SPDX allowlist, and the project's
names and logos are covered by a separate trademark policy.

- **Status:** Accepted
- **Scope:** Suite

## Context

Arc is meant to be used, modified and embedded by individuals, firms and institutions without legal
friction. Contributors need clear, light terms. A single incompatible dependency can make a release
undistributable. The project's names must remain identifiable with official builds while the code is
freely forkable.

## Decision

- Apache License 2.0 for every public repository. Copyright line: "ACKaraca and the Arc contributors".
- Developer Certificate of Origin sign-off on every commit. No contributor license agreement, no
  copyright assignment; contributions are licensed inbound under the same license as outbound.
- Dependency licenses are restricted to an SPDX allowlist enforced by `cargo deny`: MIT, Apache-2.0,
  BSD-2-Clause, BSD-3-Clause, ISC, Zlib, BSL-1.0, Unicode-3.0, CC0-1.0, and MPL-2.0 for unmodified
  crates. LGPL is permitted only for dynamically linked native libraries named in an ADR.
- Names and logos are covered by a separate [trademark policy](../../TRADEMARKS.md) and are not
  licensed with the code.

## Consequences

- Users and firms can adopt, modify and redistribute Arc, including in proprietary products, under a
  well-understood license with an explicit patent grant.
- Contributors keep their copyright; relicensing the project would require consent of every
  contributor, which is accepted as a deliberate constraint.
- Each dependency addition is checked against the allowlist in CI; the procedure is in the
  [dependency policy](../engineering/dependency-policy.md).
- GPL and AGPL code cannot be linked; tools under such licenses can be used only as separate processes
  that are never required for a core workflow.
- Modified distributions must be renamed and unbranded; the trademark policy states how.

## Alternatives considered

- **A copyleft license for the suite.** Would allow linking GPL libraries but would restrict adoption
  by firms that embed or extend the software.
- **Dual MIT or Apache-2.0 licensing.** Common in the Rust ecosystem; Apache-2.0 alone is chosen for its
  explicit patent grant and to keep one license text.
- **A contributor license agreement.** Allows relicensing but raises the barrier to contribution and
  concentrates rights.
- **No allowlist, case-by-case review.** Error-prone and unenforceable at scale.

## Trigger to reopen

A dependency that is essential to a core workflow and has no permissive alternative requires a license
outside the allowlist.
