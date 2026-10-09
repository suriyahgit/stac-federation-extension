# STAC Federation Extension (EODC)

A small STAC extension that describes **federation** and **metadata-harmonization
provenance** for collections published by the EODC STAC catalogue
([`catalogue.services.eodc.eu`](https://catalogue.services.eodc.eu)).

It answers two questions for a consumer:

1. **Which providers** can serve this collection (`federation:backends`).
2. **Where the collection came from and what was harmonized** (`federation:provenance`).

The federation is search-time only: collections are **not merged**, so each
harmonized collection maps to exactly one source.

## Referencing it

Add the schema URL to an object's `stac_extensions`:

```json
"stac_extensions": [
  "https://raw.githubusercontent.com/suriyahgit/stac-federation-extension/main/v0.3.0/schema.json"
]
```

- Schema: [`v0.3.0/schema.json`](v0.3.0/schema.json)
- Changelog: [`CHANGELOG.md`](CHANGELOG.md)

## Fields

| Field | Location | Description |
| --- | --- | --- |
| `federation:backends` | Collection `summaries`, Item `properties` | Providers that can serve the object. |
| `federation:provenance` | Collection (top level) | Source + harmonization result (below). |

### `federation:provenance`

```jsonc
{
  "source": {
    "collection_id": "GFM",
    "href": "https://stac.eodc.eu/api/v1/collections/GFM",
    "providers": ["eodc"]
  },
  "harmonized": {
    "profile": "eodc-stac-v1",
    "profile_version": "1",
    "stac_version": "1.1.0",
    "upstream_stac_version": "1.0.0"
  },
  "changes": [
    "Migrated to STAC 1.1.0",
    "Normalized license",
    "Repaired extension metadata"
  ]
}
```

| Property | Meaning |
| --- | --- |
| `source.collection_id` | The source collection id. |
| `source.href` | URL of the source collection (present for pass-throughs). |
| `source.providers` | Providers that serve the source. |
| `harmonized.profile` / `profile_version` | The EODC harmonization profile applied. |
| `harmonized.stac_version` | STAC version of the harmonized collection. |
| `harmonized.upstream_stac_version` | STAC version of the source, when it differed. |
| `changes` | Plain-language summary of what the harmonization changed. |

`federation:provenance` is placed at the **collection top level**; it is not a
valid STAC `summaries` value and must not be used there.

## Examples

- [`examples/collection-example.json`](examples/collection-example.json)
- [`examples/item-example.json`](examples/item-example.json)

## Versioning

Path-versioned (`v0.3.0/`); the `$id` matches the schema URL. Previous versions
are kept for immutability. See [`CHANGELOG.md`](CHANGELOG.md).

## License

Apache-2.0 (see [`LICENSE`](LICENSE)).
