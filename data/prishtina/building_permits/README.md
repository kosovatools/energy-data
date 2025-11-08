# Prishtina Building Permits

Official building permit registries published by Komuna e Prishtinës (Drejtoria e Urbanizmit) for 2012‑2025. The Excel workbooks live in `raw_data/prishtina/building-permits-<year>.xlsx` and are parsed by `scripts/generate_prishtina_building_permits.py` into JSON exports under `data/prishtina/building_permits/`.

## Raw downloads

| Year | Workbook URL |
| --- | --- |
| 2025 | https://prishtinaonline.com/uploads/lista_e_lejeve_te_leshuara_per_vitin_2025%20%283%29.xlsx |
| 2024 | https://prishtinaonline.com/uploads/lista_e_lejeve_te_leshuara_per_vitin_2024%20%2810%29.xlsx |
| 2023 | https://prishtinaonline.com/uploads/lista_e_lejeve_te_leshuara_per_vitin_2023%20%281%29.xlsx |
| 2022 | https://prishtinaonline.com/uploads/lista_e_lejeve_te_leshuara_2022_23.03.2023.xlsx |
| 2021 | https://prishtinaonline.com/uploads/lista_e_lejeve_te_leshuara_per_vitin_2021_13.12.2021.xlsx |
| 2020 | https://prishtinaonline.com/uploads/lista_e_lejeve_te_leshuara_per_vitin_2020_final%20%281%29.xlsx |
| 2019 | https://prishtinaonline.com/uploads/lista_e_lejeve_te_leshuara_per_vitin_2019%20%2811%29.xlsx |
| 2018 | https://prishtinaonline.com/uploads/lista_e_lejeve_te_leshuara_per_vitin_2018%20%283%29.xlsx |
| 2017 | https://prishtinaonline.com/uploads/lista-e-lejeve-te-leshuara-per-vitin-2017%20%286%29.xlsx |
| 2016 | https://prishtinaonline.com/uploads/lista_e_lejeve_te_leshuara_per_vitin_2016%20%2816%29.xlsx |
| 2015 | https://prishtinaonline.com/uploads/lista_e_lejeve_te_leshuara_per_vitin_2015%20%288%29.xlsx |
| 2014 | https://prishtinaonline.com/uploads/lista_e_lejeve_te_leshuara_per_vitin_2014.xlsx |
| 2013 | https://prishtinaonline.com/uploads/lista_e_lejeve_te_leshuara_per_vitin_2013.xlsx |
| 2012 | https://prishtinaonline.com/uploads/lista_e_lejeve_te_leshuara_per_vitin_2012.xlsx |

## Outputs

### `permits_<year>.json`
One file per workbook (e.g., `permits_2024.json`) with:

- `year`, `generated_at`, `source_file`, `source_url`, `sheet_name`, `record_count`.
- `records`: permit rows in workbook order. Fields:
  - `permit_number` (`string`)
  - `application_date`, `issuance_date` (`string | null`, ISO `YYYY-MM-DD`)
  - `owner`, `investor`, `designer`, `neighbourhood` (`string | null`)
  - `total_floor_area_m2`, `density_fee_eur`, `administrative_fee_eur`, `total_fee_eur` (`number | null`)
  - `storeys`, `destination`, `document_reference` (`string | null`)
  - `document_url`, `situation_url` (`string | null`) — extracted from the Excel hyperlinks pointing to the PDF permit and the situational map.

### `index.json`

Summary metadata used by downstream tooling:

- `generated_at`: UTC timestamp shared with the per-year files.
- `years`: array describing each workbook with `year`, `records_file`, and `record_count`. The entries are ordered from the latest year to the oldest, and `records_file` is stored relative to this directory (e.g., `permits_2025.json`). Use the per-year files themselves for the sheet metadata (`sheet_name`, `source_url`, etc.).

## Regeneration

```bash
python scripts/generate_prishtina_building_permits.py \
  --raw-dir raw_data/prishtina \
  --output data/prishtina/building_permits
```

Drop new workbooks into `raw_data/prishtina/` (following the `building-permits-<year>.xlsx` naming pattern) before running the script.

## Structure notes

| Years | Row count range | Columns |
| --- | --- | --- |
| 2012‑2015 | 84‑159 rows | `#`, `Data e lëshimit të lejes`, owner, investor, designer, neighborhood, total floor area, total fee, floors, comment (used to infer destination), PDF link, situational plan. |
| 2016‑2017 | 101‑162 rows | Adds `Data e aplikimit të lejes`, detailed density fee (13.30€/6.70€), administrative fee, `Destinimi i objektit`. |
| 2018‑2025 | 58‑179 rows | Same columns as 2016‑2017 but density fee updated to dual tariff (4.30€/10.70€) and admin fee rounded to 6.50€/m². Column text occasionally truncates the trailing `m²`, but the content is consistent. All rows include a PDF URL and `Situacioni` (map). |

Additional observations:

- Header text is preceded by banner rows that must be skipped; the actual header is the first row containing `Data e lëshimit të lejes`.
- Permit numbering restarts every year (`#` column).
- The totals in the Excel sheet are already denominated in Euros; no conversion is performed by the parser.
- Hyperlinks are embedded inside Excel cells (display text such as “Leja dokumenti…” or “Situacioni”); the parser reads the hyperlink target to surface the actual URL.
- The workbooks embed Albanian-only text and UTF‑8 characters; all JSON exports stay in UTF‑8.
- Older (2012‑2015) sheets label `Destinimi` as a free-form “Koment”; the generator copies that value into `destination` when needed and omits a separate `comment` field from the JSON.
