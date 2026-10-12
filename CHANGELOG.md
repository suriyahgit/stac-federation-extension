# Changelog

## v0.4.0

- Simplified `federation:provenance` to a minimal shape: `profile`
  (`name@version`) + the plain-language `changes` list only.
- Removed `source` and `harmonized` (`stac_version`, `upstream_stac_version`,
  `profile_version`): they duplicated the collection's own `id`,
  `summaries["federation:backends"]` and top-level `stac_version`.
- The origin of an upstream pass-through is now a standard STAC `via` link.
- No-op rule: no `federation:provenance` object is emitted when nothing changed.
- `additionalProperties: false` on the provenance object.

## v0.3.0

- Simplified `federation:provenance` to a production shape: `source` + `harmonized`
  + a plain-language `changes` summary.
- Removed internal/implementation detail (`transformations`, `software`, `sha256`,
  `retrieved_at`, `issue`) and the `federation:source_*` fields.
- README rewritten as production documentation.

## v0.2.0

- Restructured the schema around explicit `item` / `collection` definitions (`oneOf` on `type`).
- Added `software` (the harmonization agent).
- `source`: added `retrieved_at` and `sha256` (reproducibility).
- `transformations[]`: added `value` (the new value) and `issue` (the `UP-n` upstream defect a
  workaround addresses), alongside `target` / `operation` / `source`.
- Richer titles/descriptions throughout; `source.kind` is now an `enum`.
- Documented the operation reference in the README.

## v0.1.0

- Initial extension: `federation:backends`, `federation:provenance`, `federation:source_*`.
