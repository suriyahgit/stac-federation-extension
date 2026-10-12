# STAC Federation Extension (EODC)

A small STAC extension that describes **federation** and **metadata-harmonization
provenance** for collections published by the EODC STAC catalogue
([`catalogue.services.eodc.eu`](https://catalogue.services.eodc.eu)).

It answers two questions for a consumer:

1. **Which providers** can serve this collection (`federation:backends`).
2. **What the catalogue harmonized** in this collection, and which rules it used
   (`federation:provenance`).

The federation is search-time only: collections are **not merged**, so each
harmonized collection maps to exactly one source.

## Referencing it

Add the schema URL to an object's `stac_extensions`:

```json
"stac_extensions": [
  "https://raw.githubusercontent.com/suriyahgit/stac-federation-extension/main/v0.4.0/schema.json"
]
```

- Schema: [`v0.4.0/schema.json`](v0.4.0/schema.json)
- Changelog: [`CHANGELOG.md`](CHANGELOG.md)

## Fields

| Field | Location | Description |
| --- | --- | --- |
| `federation:backends` | Collection `summaries`, Item `properties` | Providers that can serve the object. |
| `federation:provenance` | Collection (top level) | The harmonization profile applied and what changed (below). |

### `federation:provenance`

Minimal by design. Everything else is already carried by the collection: its own
`id`, its providers (`summaries["federation:backends"]`) and its `stac_version`.
The **origin** of an upstream pass-through is a standard STAC link, not a field:

```jsonc
"links": [{ "rel": "via", "href": "https://stac.eodc.eu/api/v1/collections/GFM" }],
"federation:provenance": {
  "profile": "eodc-stac-v1@1",
  "changes": [
    "Migrated to STAC 1.1.0",
    "Updated license to a current value",
    "Repaired extension metadata"
  ]
}
```

| Property | Meaning |
| --- | --- |
| `profile` | The harmonization profile and rule version applied, as `name@version`. |
| `changes` | Plain-language summary of what the harmonization changed. |

A local product with no upstream collection to point at has no `via` link and
keeps only `profile` + `changes`. When the harmonization changed nothing, no
`federation:provenance` object is emitted at all (the no-op rule).

`federation:provenance` is placed at the **collection top level**; it is not a
valid STAC `summaries` value and must not be used there.

## Examples

- [`examples/collection-example.json`](examples/collection-example.json)
- [`examples/item-example.json`](examples/item-example.json)

## Versioning

Path-versioned (`v0.4.0/`); the `$id` matches the schema URL. Previous versions
are kept for immutability. See [`CHANGELOG.md`](CHANGELOG.md).

## License

Apache-2.0 (see [`LICENSE`](LICENSE)).
