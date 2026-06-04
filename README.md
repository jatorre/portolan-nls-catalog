# 🇫🇮 National Land Survey of Finland (Maanmittauslaitos) — Portolan catalog

A **git-backed Portolan** spatial-data catalog from **National Land Survey of Finland
(Maanmittauslaitos)**: git holds the catalog *definition* (STAC), the object store holds the
*data* — a static Apache Iceberg REST catalog + GeoParquet + a raquet raster, readable with no
server and no credentials.

**Catalog endpoint (Iceberg REST / STAC root):** `https://8et4c.upcloudobjects.com/carto-ogc-connect-helsinki/repo/portolan-nls-catalog`

## Datasets

| Dataset | What it is | Access |
|---|---|---|
| **Electricity transmission lines** (`sahkolinja`) | High-voltage overhead power lines (NLS Topographic Database). | `v3.power_lines` |
| **Lakes & water bodies** (`jarvi`) | Inland lakes and water bodies (NLS). | `v3.water` |
| **Protected nature areas** (`luonnonsuojelualue`) | Statutory nature-protection areas (NLS). | `v3.protected` |
| **Buildings** (`rakennus`) | NLS building footprints. | `v3.buildings` |
| **Elevation model — 2 m laser DEM** (`korkeusmalli_2m`) | NLS 2 m laser-scanned terrain elevation. | raquet raster (`read_raquet`) |

## Read it — no credentials, no server

**ATTACH (DuckDB / Snowflake):**
```sql
INSTALL iceberg; LOAD iceberg; INSTALL httpfs; LOAD httpfs;
ATTACH 'cat' (TYPE iceberg, ENDPOINT 'https://8et4c.upcloudobjects.com/carto-ogc-connect-helsinki/repo/portolan-nls-catalog', AUTHORIZATION_TYPE 'none');
SELECT * FROM cat.v3.power_lines LIMIT 10;
```

**Discover:** [`catalog.json`](catalog.json) (STAC root) → each `<id>/collection.json`. Vector
collections also ship a flattened GeoParquet snapshot on the bucket (`assets.<id>.href`); the DEM
is a raquet raster (`read_raquet`).

## How this repo is laid out (git-backed Portolan, model 3)

Git tracks the **definition** only:
- `catalog.json` — STAC `Catalog` (git-backed-catalog extension: `git:repository`, `git:ref`, vcs/issues/monitor links).
- `<id>/collection.json` — STAC `Collection` per dataset (stac-iceberg extension: `iceberg:catalog_uri`, `iceberg:table_id`, `iceberg:current_snapshot_id`).
- `versions.json` + `<id>/versions.json` — Portolan version manifests. `.portolan/` — CLI config.

Data bytes (parquet, raquet, Iceberg metadata + the `v1/` REST tree) live on the **bucket**, never in git (`.gitignore` blocks `*.parquet`).

## Contributing
Fix or extend the catalog with a **pull request** editing `catalog.json` or `<id>/collection.json`
(each carries a `git:edit_url`). Data bytes live on the bucket, never in git. See
[`AGENTS.md`](AGENTS.md) for the agent-facing guide and the opt-in usage-report channel.

## License
Data: **CC-BY-4.0** (© National Land Survey of Finland (Maanmittauslaitos)). See [`LICENSE`](LICENSE).

## Part of a federation
One child of the Portolan Helsinki *catalog of catalogs*.
