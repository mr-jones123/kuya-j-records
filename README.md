# Kuya J records

The records [Kuya J](https://github.com/mr-jones123/kuya-j) answers from, and the signed bundles phones download to
stay up to date. Kuya J is an offline assistant for Philippine government services by
[BetterGov.ph](https://bettergov.ph).

This repository is served by GitHub Pages at **https://mr-jones123.github.io/kuya-j-records**, which is the app's
`kuyaj.syncBaseUrl`.

## What's here

| Path | What |
|---|---|
| `records/*.json` | Every record, readable and diffable: one JSON array per source |
| `records/SOURCES.md` | The BetterGov.ph commit the records were harvested from, and counts |
| `api/v1/sync/kb` | The signed manifest: version, size, SHA-256, Ed25519 signature, bundle URL |
| `kb/bundle-<version>.json` | The exact bytes the manifest signs: all records in one file |

| Records | From | Count |
|---|---|---|
| `fees.json`, `holidays.json`, `hotlines.json`, `officials.json`, `requirements.json`, `services.json` | [Airgap](https://github.com/xmpuspus/airgap)'s government-services example | 27 |
| `bettergov-guides.json` | BetterGov.ph's 19 step-by-step guides (NBI, passport, PSA, TIN, PhilHealth, Pag-IBIG...), one record per section, text copied word for word | 158 |
| `bettergov-holidays.json` | Holidays for 2025-2027 from the proclamations, one record per holiday plus yearly lists | 69 |
| `bettergov-directory.json` | Department heads, executive offices, Congress, the Supreme Court, government corporations | 182 |
| `bettergov-hotlines.json` | Emergency and agency hotlines | 19 |
| `bettergov-visas.json` | Philippine visa types and their requirements | 23 |

Every record ends with its source and a date: **checked** when someone verified the source on that date (guides,
holidays, department heads), **listed** when it's only the date BetterGov.ph last updated that data.

## How phones use it

1. Every 6 hours on Wi-Fi, the app fetches `api/v1/sync/kb`.
2. If the version is newer than what it has, it downloads the bundle.
3. It checks the size, the SHA-256 and the **Ed25519 signature against a public key built into the app**. A
   bundle changed by anyone without the private key is rejected, and the phone keeps its current records.

The private key never leaves the publisher's computer, so hosting can move (to Cloudflare, or a BetterGov.ph
domain) without trusting the host. Details: [Record sync](https://github.com/mr-jones123/kuya-j/blob/main/docs/sync.md).

## Updating

From the [app repository](https://github.com/mr-jones123/kuya-j):

```bash
python3 tools/records/harvest.py --out ../kuya-j-records/records          # latest BetterGov.ph data
uv run --with cryptography tools/records/publish.py --records ../kuya-j-records/records \
    --site ../kuya-j-records --base-url https://mr-jones123.github.io/kuya-j-records
cd ../kuya-j-records && git add -A && git commit -m "Records <date>" && git push
```

Review the diff in `records/` before pushing: it's exactly what will change on every phone. The full workflow is in
[docs/records-pipeline.md](https://github.com/mr-jones123/kuya-j/blob/main/docs/records-pipeline.md).

## Licences and credit

- **BetterGov.ph** content (`bettergov-*.json`) is © BetterGov.ph contributors under
  [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/), adapted into records for Kuya J with
  BetterGov.ph's permission. Each record links its BetterGov.ph page (`metadata.via`) and pins the commit it came from.
- **Airgap** records are © 2026 Xavier Puspus under the MIT License; see `records/NOTICE-airgap.md`.
- The facts come from Philippine government websites, linked in each record. Government works are not subject to
  copyright in the Philippines (RA 8293, section 176).

Kuya J is not operated by any government agency. Check the agency's official page before acting on a fee, hotline
or office holder.
