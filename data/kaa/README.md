# Kosovo Accreditation Agency Datasets

This directory contains derived datasets sourced from the Kosovo Accreditation Agency (under the Ministry of Education). The datasets are grouped by topic and generated via `scripts/generate_me_accreditation.py`, which reads the Excel exports downloaded into `raw_data/me`.

## Contents
- `index.json`: Machine-readable catalog of all generated datasets (category, output path, coverage period, record/institution counts, and source pointers).
- `accredited_programmes/`: Programme-level accreditation snapshots for academic years 2023-2024 and 2025-2026 (multiple workbook revisions).

`index.json` fields:
- `generated_at` (`string`): UTC timestamp when `generate_me_accreditation.py` last ran.
- `datasets` (`array<object>`): Summary of each derived dataset, containing:
  - `category` / `path`: Directory and relative file path of the dataset.
  - `period`, `record_count`, `institution_count`: High-level coverage metadata.
  - `source_url`, `source_file`: Where the Excel workbook originated and the local copy that was parsed.
  - `generated_at`: Timestamp shared with the detailed JSON export.
  - `version` (`string`, optional): Workbook revision tag when available.
  - Entries appear from the latest coverage period to the oldest so consumers can grab the freshest snapshot first.

## Regeneration

```bash
python scripts/generate_me_accreditation.py \
  --raw-dir raw_data/me \
  --output data/kaa
```

Before running the script, download the Excel files listed inside each subdirectory README into `raw_data/me/`.
