---
name: nccn-guideline-downloader
description: "Download NCCN clinical practice guidelines and patient manuals in English or Chinese. Guides users through theme selection, language filter, cancer type filter, and PDF list selection. Triggers when user mentions NCCN, clinical guidelines, cancer guidelines, patient manuals, 指南下载, 癌症指南, 患者手册, or downloading medical guidelines from nccn.org."
---

# NCCN Guideline Downloader

Download NCCN (National Comprehensive Cancer Network) clinical guidelines and patient manuals through a guided conversational workflow.

## Setup

On first use, guide the user through configuration:

1. Install dependencies: `pip install -r scripts/requirements.txt`
2. Copy config template: `cp assets/config.json.template scripts/config.json`
3. Edit `scripts/config.json` with NCCN credentials (username/password or cookie)
4. If using cookie auth, place `extracted_cookies.txt` in `scripts/` directory

## Workflow

### 1. Identify Theme

Map user intent to one of 6 themes:

| # | Theme | URL Pattern |
|---|-------|-------------|
| 1 | Cancer Treatment (English) | `guidelines/category_1` |
| 2 | Supportive Care | `guidelines/category_3` |
| 3 | Patient Guidelines (English) | `patientresources/patient-resources/guidelines-for-patients` (English) |
| 4 | Clinical Guidelines (Chinese Translation) | `patientresources/patient-resources/guidelines-for-patients` (Chinese) |
| 5 | Patient Guidelines (Chinese Translation) | Translation page |
| 6 | Patient Guidelines (Chinese Version) | Translation page (direct) |

### 2. Language Filter

```
0. Chinese    1. English (default)    2. Japanese/Other    3. All
```

Default to English (1) unless user specifies Chinese.

### 3. Cancer Type Filter

65 cancer types with Chinese/English alias matching. See [references/cancer_types.md](references/cancer_types.md) for the full list.

Common shortcuts: `breast`/`乳腺`, `lung`/`肺`, `pancreatic`/`胰腺`, `colon`/`结肠`, `gastric`/`胃`, `prostate`/`前列腺`, `ovarian`/`卵巢`, `thyroid`/`甲状腺`.

Chinese keywords auto-expand to all English aliases (e.g., `胰腺` → `pancreatic adenocarcinoma`, `pancreatic`, `pancreas`).

Options: `0` = all, `L` = browse list from NCCN, `K` = manual keyword.

### 4. PDF List Selection

After parsing, show numbered list. User selects by number: `1,3,5-8` or `A`/Enter for all.

### 5. Confirm & Download

Show summary, confirm, then run:

```bash
python3 scripts/download_nccn.py
```

## Execution Modes

**Interactive (recommended):** Run the script directly and let the user interact with the menu.

```bash
python3 scripts/download_nccn.py
```

**Guided:** You orchestrate the conversation, collect choices, then run the script with the collected parameters.

## Key Behaviors

- Downloads go to `nccn_downloads/` subdirectory (auto-created)
- Existing valid PDFs are skipped (checks `%PDF` header + 100KB minimum)
- Failed downloads retry up to 3 times with exponential backoff
- Domain whitelist enforced: only `nccn.org` and subdomains
- Download stats saved to `nccn_downloads/logs/stats_*.json`
- 42 offline tests available: `python3 scripts/test_offline.py`

## Troubleshooting

- **Auth failure:** Check `config.json` credentials or refresh cookie
- **No PDFs found:** NCCN site structure may have changed; try `L` to refresh cancer list
- **Corrupted files:** Script validates `%PDF` header; check logs for file sizes
