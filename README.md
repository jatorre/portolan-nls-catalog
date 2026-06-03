# 🇫🇮 National Land Survey of Finland (Maanmittauslaitos) — Portolan catalog

A Portolan spatial-data catalog from **National Land Survey of Finland (Maanmittauslaitos)**: git-sourced metadata, published to object storage as a **static Apache Iceberg REST catalog** + STAC + OGC API - Records + direct download — readable with no server.

**Catalog endpoint (Iceberg REST):** `https://8et4c.upcloudobjects.com/carto-ogc-connect-helsinki/repo/portolan-nls-catalog`

## Datasets

| Dataset | What it is | Access |
|---|---|---|
| **Lakes & water bodies** (`jarvi`) | Inland lakes and water bodies (NLS). | `v2.water` · `v3.water` |
| **Elevation model — 2 m laser DEM** (`korkeusmalli_2m`) | NLS 2 m laser-scanned terrain elevation. | raquet raster (`read_raquet`) |
| **Protected nature areas** (`luonnonsuojelualue`) | Statutory nature-protection areas (NLS). | `v2.protected` · `v3.protected` |
| **Buildings** (`rakennus`) | NLS building footprints. | `v2.buildings` · `v3.buildings` |
| **Electricity transmission lines** (`sahkolinja`) | High-voltage overhead power lines (NLS Topographic Database). | `v2.power_lines` · `v3.power_lines` |

## Read it — no credentials, no server

**ATTACH (DuckDB / Snowflake):**
```sql
INSTALL iceberg; LOAD iceberg; INSTALL httpfs; LOAD httpfs;
ATTACH 'cat' (TYPE iceberg, ENDPOINT 'https://8et4c.upcloudobjects.com/carto-ogc-connect-helsinki/repo/portolan-nls-catalog', AUTHORIZATION_TYPE 'none');
SELECT * FROM cat.v2.water LIMIT 10;
```

**Discover:** [`catalog.json`](catalog.json) (STAC) · [`records/catalog.json`](records/catalog.json) (OGC API - Records) · [`index.html`](https://8et4c.upcloudobjects.com/carto-ogc-connect-helsinki/repo/portolan-nls-catalog/index.html) (human view). Direct GeoParquet download links are in the explorer.

## Contributing
Fix or extend the catalog with a **pull request** (edit `portolan.config.json` / `datasets/<id>.json` / the Iceberg metadata, then run `tools/generate_stac.py` + `tools/validate.py`); a merge republishes to the bucket. Data bytes live on the bucket, never in git. See [`AGENTS.md`](AGENTS.md) for the agent-facing guide and the opt-in usage-report channel.

## License
Data: **CC-BY-4.0** (© National Land Survey of Finland (Maanmittauslaitos)). Tooling: Apache-2.0. See [`LICENSE`](LICENSE).

## Part of a federation
One child of the Portolan Helsinki *catalog of catalogs*: `https://8et4c.upcloudobjects.com/carto-ogc-connect-helsinki/catalog/stac.json`

