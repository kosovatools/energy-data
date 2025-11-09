# Energy Dataset

This folder contains the generated energy-flow snapshots that power the Kosovo Tools visualizations. The data is derived from the ENTSO-E cross-border flow API and is produced by the `src/fetch-entsoe.ts` script.

## File layout

- `index.json` – metadata pointing at every available monthly snapshot. `months` is kept in ascending chronological order and includes `{ id, periodStart, periodEnd, totals }`.
- `latest.json` – pointer to the most recent monthly snapshot (`snapshotId`, `periodStart`, `periodEnd`).
- `latest-daily.json` – the per-day imports/exports for the latest month (`days` holds `{ date, imports, exports, net }`).
- `monthly/<YYYY-MM>.json` – detailed snapshot for the month. Each file includes the period bounds and the per-neighbor breakdown with `{ code, country, importMWh, exportMWh, netMWh, hasData }` plus the aggregated `totals`.

All timestamps are UTC strings, all energy values are expressed in megawatt-hours (MWh), and arrays are pre-sorted so clients can consume them directly.

## Regenerating the dataset

1. Install dependencies: `pnpm install`.
2. Export your ENTSO-E API token: `export ENTSOE_API_KEY=...`.
3. Run `pnpm run generate:energy` (wrap with `env ENT...` if running once).

The generator writes into `data/energy` by default. Useful flags:

- `--month YYYY-MM` – regenerate a specific month instead of the previous month.
- `--backfill N` (or `--months N`) – fetch up to `N` months counting back from the base month (capped at 24).
- `--out <path>` – override the output directory if you need a different target.

Each run updates the monthly file, refreshes `index.json`, and rewrites the `latest*` pointers when a newer month is fetched.
