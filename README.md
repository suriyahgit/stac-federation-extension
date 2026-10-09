# STAC Federation Extension (EODC)

A small **third-party STAC extension** formally defining the `federation:*`
fields used by the EODC federated STAC catalogue
([`catalogue.services.eodc.eu`](https://catalogue.services.eodc.eu)).

The federation is **search-time only**: collections are **not merged**, so every
harmonized object maps to exactly one source. This extension records:

1. **Federation** — which providers can serve an object (`federation:backends`).
2. **Harmonization provenance** — a 1:1 backtracking record: where the object came
   from, which rules/software were used, and every field-level change
   (`federation:provenance`, `federation:source_*`).

## Referencing it

Add the schema URL to an object's `stac_extensions`:

```json
"stac_extensions": [
  "https://raw.githubusercontent.com/suriyahgit/stac-federation-extension/main/v0.2.0/schema.json"
]
```

- Schema: [`v0.2.0/schema.json`](v0.2.0/schema.json)
- Schema `$id`: `https://raw.githubusercontent.com/suriyahgit/stac-federation-extension/main/v0.2.0/schema.json`
- Changelog: [`CHANGELOG.md`](CHANGELOG.md)

## Field reference

| Field | Location | Type | Description |
| --- | --- | --- | --- |
| `federation:backends` | Collection `summaries` · Item `properties` | `string[]` | EODAG provider names that can serve the object. Order not significant. |
| `federation:provenance` | Collection (top level) | `object` | 1:1 backtracking record (below). |
| `federation:source_<field>` | Collection `summaries` · Item `properties` | `array` | Original value(s) before vocabulary normalization. |

### `federation:provenance`

| Property | Type | Description |
| --- | --- | --- |
| `profile` | `string` | Harmonization profile id, e.g. `eodc-stac-v1`. |
| `profile_version` | `string` | Version of the harmonization rules. |
| `target_stac_version` | `string` | STAC version the object was migrated to. |
| `software` | `object` | The agent that harmonized it: `{ "name": ..., "version": ... }`. |
| `source` | `object` | Where it came from (see below). |
| `transformations` | `object[]` | Field-level operations (see below). |

`source`:

| Property | Type | Description |
| --- | --- | --- |
| `kind` | `enum` | `upstream-collection` (pass-through with a known source document URL) or `provider-product` (locally federated collection). |
| `collection_id` | `string` | Source collection id. |
| `href` | `uri` | Source collection URL (`upstream-collection` only). |
| `retrieved_at` | `date-time` | When the source metadata was fetched. |
| `sha256` | `string` | SHA-256 of the source metadata snapshot. |
| `providers` | `object[]` | For `provider-product`: `{ "name": ..., "url": ... }`. |

`transformations[]` — each entry is `{ target, operation, source?, value?, issue? }`:

| Property | Description |
| --- | --- |
| `target` | Path of the changed field, e.g. `summaries/constellation`, `stac_version`. |
| `operation` | Operation id (reference below). |
| `source` | Original value before harmonization. |
| `value` | New (canonical) value after harmonization. |
| `issue` | Upstream defect id (`UP-n`) this workaround addresses, when applicable. |

## Operation reference

| Operation | Meaning |
| --- | --- |
| `vocabulary:constellation` · `vocabulary:platform` · `vocabulary:instruments` · `vocabulary:processing:level` | Canonicalized to the profile vocabulary. |
| `license:deprecated` | Deprecated license value (`proprietary`/`various`) → `other`. |
| `migration:stac_version` | Stamped the target STAC version. |
| `bands:migrate` | `eo:bands`/`raster:bands` → common-metadata `bands`. |
| `schema:summaries_force_array` | Scalar `summaries` value → single-element array (STAC 1.1). |
| `schema:item_assets_min_properties` | Added fields so an `item_assets` entry has ≥ 2 properties. |
| `schema:stac_extensions_normalize` | Stripped a trailing `#` / deduped `stac_extensions`. |
| `extension:declare` | Declared an extension. |
| `extension:drop_unused` | Dropped a declared extension with no matching field (e.g. `UP-5`, `UP-7`, `UP-9`). |
| `datacube:fix_temporal_extent` | Repaired a datacube temporal `extent` shape (`UP-1`). |
| `datacube:drop_axis` | Dropped a disallowed `axis` on a temporal dimension. |

## Placement rules

- `federation:backends` and `federation:source_*` → Collection `summaries`, Item `properties`.
- `federation:provenance` → **Collection top level only**. It must **not** be in `summaries`,
  because a plain object is not a valid STAC 1.1 `summaries` value (array / JSON Schema / Range).
- Items carry **no** inline provenance object (avoids bloating search results); item-level
  backtracking uses `federation:backends` + asset `alternate.origin`.

## Examples

- [`examples/collection-example.json`](examples/collection-example.json)
- [`examples/item-example.json`](examples/item-example.json)

(Fragments, not complete STAC documents.)

## Validating

```bash
# with stac-check / stac-validator
stac-check https://raw.githubusercontent.com/suriyahgit/stac-federation-extension/main/v0.2.0/schema.json
```

## Versioning

- Path-versioned (`v0.2.0/`); the `$id` matches the schema URL. `v0.1.0/` is kept for immutability.
- Breaking changes get a new version directory. See [`CHANGELOG.md`](CHANGELOG.md).
- `main` is used in the raw URL for convenience; pin to a tag for immutable use.

## License

TBD — add a `LICENSE` for this repository.
