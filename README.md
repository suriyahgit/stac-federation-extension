# STAC Federation Extension (EODC)

A small, **third-party STAC extension** that formally defines the custom
`federation:*` fields used by the EODC federated STAC catalogue
([`catalogue.services.eodc.eu`](https://catalogue.services.eodc.eu)).

It documents two things:

1. **Federation** — which providers can serve an object (`federation:backends`).
2. **Harmonization provenance** — a 1:1 backtracking record of where a collection
   came from and what the harmonization layer changed (`federation:provenance`,
   `federation:source_*`).

The federation is **search-time only**: collections are **not merged**, so every
harmonized collection maps to exactly one source.

## Referencing it

Add the schema URL to an object's `stac_extensions`:

```json
"stac_extensions": [
  "https://raw.githubusercontent.com/suriyahgit/stac-federation-extension/main/v0.1.0/schema.json"
]
```

- Schema: [`v0.1.0/schema.json`](v0.1.0/schema.json)
- Schema `$id`: `https://raw.githubusercontent.com/suriyahgit/stac-federation-extension/main/v0.1.0/schema.json`

## Fields

| Field | Location | Type | Description |
| --- | --- | --- | --- |
| `federation:backends` | Collection `summaries` · Item `properties` | `string[]` | Names of the EODAG providers that can serve the object. |
| `federation:provenance` | Collection (top level) | `object` | 1:1 backtracking record (see below). |
| `federation:source_*` | Collection `summaries` · Item `properties` | `array` | Original value(s) before vocabulary normalization. |

### `federation:provenance`

```jsonc
{
  "profile": "eodc-stac-v1",
  "profile_version": "1",
  "target_stac_version": "1.1.0",
  "source": {
    "kind": "upstream-collection",        // or "provider-product"
    "collection_id": "GFM",
    "href": "https://stac.eodc.eu/api/v1/collections/GFM"
    // for "provider-product": "providers": [{ "name": "...", "url": "..." }]
  },
  "transformations": [
    { "target": "summaries/constellation", "operation": "vocabulary:constellation", "source": ["SENTINEL1"] },
    { "target": "stac_version",             "operation": "migration:stac_version",  "source": "1.0.0" }
  ]
}
```

- **`source.kind`** — `upstream-collection` (pass-through with a known source
  document URL) or `provider-product` (locally federated collection, identified by
  provider(s)).
- **`transformations`** — field-level operations applied by the harmonization
  layer (`target`, `operation`, `source`).

Note: `federation:provenance` is **top-level** on Collections. It must **not** be
placed in `summaries`, because a plain object is not a valid STAC 1.1 `summaries`
value (array / JSON Schema / Range).

## Examples

- [`examples/collection-example.json`](examples/collection-example.json)
- [`examples/item-example.json`](examples/item-example.json)

(Fragments, not complete STAC documents.)

## Versioning

- The path is versioned (`v0.1.0/`), and the `$id` matches the schema URL.
- Breaking changes get a new major version directory (`v0.2.0/`).
- `main` is used in the raw URL for convenience; pin to a tag for immutable use.

## License

TBD — add a `LICENSE` for this repository.
