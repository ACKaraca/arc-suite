# ADR-0008: Units, precision and coordinates

Model coordinates are `f64`, stored in SI units for 3D domains, with explicit drafting units, typed
quantities at boundaries, shared tolerances, camera-relative rendering and a georeference record in
every document.

- **Status:** Accepted
- **Scope:** Suite

## Context

Arc documents range from micrometer machining tolerances to georeferenced corridors hundreds of
kilometers long with coordinates in the millions of meters. Drafting data must preserve the drawing
units of DWG files exactly. Unit errors and ad-hoc tolerances are among the most common sources of
silent geometric defects. GPU hardware renders efficiently in `f32`, which loses precision far from the
origin.

## Decision

- Model coordinates are `f64`.
- BIM, civil, solid and mesh documents store lengths in meters and angles in radians. Display units are
  a document setting and never change stored values.
- Drafting spaces declare their unit: drawing units for DWG fidelity, millimeters on sheets.
  Conversion happens only at explicit boundaries, using typed quantities from `arc-base`.
- Tolerances come from `arc-base` (length, angle, area and domain-specific tolerances), never from
  numeric literals in code.
- Rendering is camera-relative: GPU data is `f32` relative to an origin near the camera, so
  georeferenced coordinates keep their precision on screen.
- Every document has a local origin, a survey or geodetic reference, and a coordinate reference system
  record. Imports record their source units, axis convention and coordinate reference.

## Consequences

- Unit conversions are explicit and testable; a raw number crossing a module boundary is a review
  finding.
- DWG drawings round-trip in their own units without rescaling.
- Large georeferenced models render without jitter.
- Every import and export must handle units and coordinate systems explicitly, including axis
  conventions (Y-up and Z-up).
- Tolerances can be tuned per domain in one place.

## Alternatives considered

- **`f32` model coordinates.** Insufficient precision for civil and georeferenced work.
- **Storing values in display units.** Every unit change would rewrite the model and accumulate
  rounding.
- **Millimeters as the internal unit for all domains.** Convenient for mechanical work but awkward for
  civil scales and inconsistent with SI-based exchange formats; drafting spaces still choose their unit
  explicitly.
- **Fixed-point or arbitrary-precision coordinates.** Slower and incompatible with the geometry
  libraries the suite builds on.

## Trigger to reopen

A domain demonstrates a precision requirement that `f64` with camera-relative rendering and the
shared tolerances cannot meet.
