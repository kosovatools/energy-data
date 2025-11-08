# Accredited Programmes

Accredited study programmes published by the Kosovo Accreditation Agency (Ministry of Education) are normalised into JSON for each available workbook:

- `programmes_2025_2026.json` — latest published revision (`Tabela-Programet-e-Akredituara-2025-2026_v5.xlsx`).
- `programmes_2023_2024.json` — programmes valid for academic year 2023-2024 (`AKA-KAA-Proggramet-e-Akredituara-2023-2024.xlsx`).

Each file has the same structure:

- `generated_at` (`string`): ISO-8601 UTC timestamp when the JSON export was produced.
- `period` (`string`): Academic period covered by the workbook (e.g., `2025-2026`).
- `version` (`string`, optional): Revision tag extracted from the filename (currently only for the `v5` workbook).
- `record_count` (`number`): Total number of programmes in `records`.
- `institution_count` (`number`): Distinct higher-education institutions present in the workbook.
- `source_url` (`string`): Original public workbook URL.
- `source_file` (`string`): Relative path to the Excel file inside `raw_data/me/`.
- `records` (`array<object>`): Programme entries ordered exactly as in the workbook.

`records` fields (all strings unless noted):

| Field | Description |
| --- | --- |
| `institution` | Higher education institution/faculty name as provided in Albanian (bilingual text is flattened by replacing newlines with spaces). |
| `program_number` (`number \| null`) | Ordinal number inside the source workbook; numbering restarts for each institution. |
| `programme_sq` | Programme name in Albanian. |
| `programme_en` | Programme name in English. |
| `campus` | Campus/city where the programme is delivered. |
| `level` | Study level abbreviation (BA, BSc, MA, MSc, DVM, PhD, etc.). |
| `ects` (`number \| null`) | Total European Credit Transfer and Accumulation System credits. |
| `quota` (`number \| null`) | Approved intake quota. |
| `accredited_until` (`string \| null`) | Accreditation expiry date in `YYYY-MM-DD` format. |

## Regeneration

1. Download the Excel files listed above into `raw_data/me/`.
2. Run `python scripts/generate_me_accreditation.py --raw-dir raw_data/me --output data/kaa`.

The script forward-fills institution names (because workbooks only show the name once per block), normalises whitespace, coerces numeric fields, and converts Excel dates (`I akredituar deri më`) to ISO strings.
