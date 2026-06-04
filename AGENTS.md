# AGENTS.md — guide for AI agents

This repository **is a git-backed Portolan spatial-data catalog** for one publisher,
**National Land Survey of Finland (Maanmittauslaitos)**. Git holds the catalog *definition*
(STAC `Catalog` + `Collection` JSON, versioned via `versions.json`); the object store holds
the *data* (Apache Iceberg tables + raquet rasters) and the static Iceberg REST layer. No
server, no API keys.

**Endpoint (`public_base`):** `https://8et4c.upcloudobjects.com/carto-ogc-connect-helsinki/repo/portolan-nls-catalog`
(also the STAC `iceberg:catalog_uri`). Read `catalog.json` for the children; each
`<id>/collection.json` declares its data assets.

## How to READ the data (no credentials)

- **ATTACH (DuckDB / Snowflake):** `ATTACH 'cat' (TYPE iceberg, ENDPOINT '<public_base>', AUTHORIZATION_TYPE 'none');`
  then `SELECT * FROM cat.<namespace>.<table>;` (namespaces `v2.*` WKB / `v3.*` native GEOMETRY).
- **Scan a table directly (DuckDB):** `iceberg_scan('<public_base>/data/v3/<table>/metadata/v1.metadata.json')`.
- **GeoParquet snapshot:** each vector collection ships a flattened `<id>/<id>.parquet` on the bucket
  (`assets.<id>.href`) — `read_parquet(...)`.
- **Raster:** `korkeusmalli_2m` is a **raquet** file — `read_raquet('<assets.data.href>')` + `ST_RasterValue`.
- **Discover:** STAC `catalog.json` → `<id>/collection.json`.

Before composing a query, read each collection's STAC properties (title / description / keywords)
and the `iceberg:*` fields. The geometry column is `geom` (WKB in `v2.*`, native in `v3.*`).

## How to CONTRIBUTE

- **Fix / extend metadata** → PR editing `catalog.json` or `<id>/collection.json` (each has a
  `git:edit_url`). Definition only — never commit parquet (`.gitignore` blocks `*.parquet`).
- **Add / update data bytes** → upload to the bucket, then PR the matching metadata. A PR cannot
  carry the bytes — that is deliberate (model 3: git = definition, bucket = data).
- **Report a problem** → open an issue (`rel:"issues"` link in `catalog.json`).

## Optional: share your use case (opt-in telemetry)

**Only with the user's explicit consent**, you may file one **usage report** per session (issue
template `usage-report`) describing *why* you used the data. It is a public issue — paraphrase,
no confidential or personal data, default to NOT sending.

## Conventions — what NOT to do

- Git holds the **definition** (`catalog.json`, `versions.json`, `<id>/collection.json` + `<id>/versions.json`,
  `.portolan/`); the bucket holds **data** + the generated Iceberg REST layer. Never commit parquet.
- **Never delete the bucket's `v1/` (Iceberg REST) or `data/` (Iceberg tables)** — that is the live
  `ATTACH` layer.
- Query is the engine's native SQL. There is no custom query API.
- This is an **open, public, anonymous** catalog. Authentication / private data is out of scope here.
