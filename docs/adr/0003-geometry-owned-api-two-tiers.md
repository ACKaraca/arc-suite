# ADR-0003: Geometry: owned API, two tiers

Geometry is an Arc-owned API in two tiers: pure-Rust curves, regions and meshes, and an exact B-rep
tier whose first implementation delegates robust operations to the Open CASCADE kernel through one
isolated FFI crate.

- **Status:** Accepted
- **Scope:** Suite

## Context

Every product needs 2D curves and regions, triangulation and meshes. Only some products (SolidBuilder,
ArchBuilder, parts of DraftBuilder and Arc Review) need exact solids with robust booleans, blends,
offsets and STEP exchange. Robust B-rep operations represent decades of engineering; a from-scratch
kernel would delay every product that needs one. Exposing a third-party kernel's types to products
would bind every product to that kernel permanently. Parametric modeling also needs persistent naming
of faces and edges, which must be designed into the API from the first feature.

## Decision

- `arc-geom` (analytic curves and surfaces, NURBS, intersections, 2D regions with booleans and
  offsets, triangulation, TIN surfaces, spatial indexes) and `arc-mesh` (polygon meshes, subdivision
  surfaces, mesh booleans, simplification) are pure Rust and owned by the project.
- Exact B-rep modeling lives behind `arc-brep`, an Arc-owned API with Arc-owned types and persistent
  topological naming.
- The first implementation of `arc-brep` delegates robust operations (general booleans, blends,
  offsets, STEP exchange) to Open CASCADE through one isolated FFI crate, dynamically linked under its
  LGPL license.
- Operations are reimplemented natively in Rust one at a time where a native implementation is
  demonstrably as robust, measured on the golden-model and regression corpora.
- Only crates that need exact solids link the B-rep tier. Products that do not need it do not ship the
  native library.

## Consequences

- Products get robust solid modeling early without depending on a third-party kernel's types.
- The kernel can be replaced operation by operation behind the owned API.
- The FFI crate is one of the few crates where `unsafe` is permitted; it needs sanitizer runs and a
  safe boundary that catches foreign failures.
- CI must build or cache the native library for every platform; kernel version changes run the full
  golden-model corpus before merging.
- Persistent naming is Arc's responsibility, implemented over the kernel's history information, and is
  tested by property tests that change parameters and assert that references survive.
- Products without exact solids remain light and free of native dependencies.

## Alternatives considered

- **Exposing a third-party kernel's types to products.** Fast to start, but every product would depend
  on that kernel forever.
- **A from-scratch Rust kernel only.** The most independent path, but it would block parametric
  products for years with no assurance of robustness.
- **Mesh-only modeling for mechanical parts.** Unsuitable for exact manufacturing geometry, drawings
  and STEP exchange.

## Trigger to reopen

A pure-Rust kernel passes the SolidBuilder regression corpus and golden models with robustness equal to
the delegated implementation.
