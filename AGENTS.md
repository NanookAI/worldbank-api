# Agent Instructions

This repository contains an Agent Skill that teaches AI agents how to use the
World Bank Indicators API (https://api.worldbank.org/v2). The deliverable is
documentation, not application code.

## Layout

- `skills/worldbank-api/SKILL.md` — skill entry point (frontmatter, data-query workflow, top indicator codes, error handling, tips)
- `skills/worldbank-api/scripts/wb_data.py` — zero-dependency CLI covering the common case (indicator + countries → formatted series) plus client-side indicator search
- `skills/worldbank-api/references/endpoints.md` — full endpoint catalog: data queries, countries, indicators, topics, sources, regions, income levels, monthly/quarterly data, languages, pagination
- `skills/worldbank-api/references/indicators.md` — verified indicator codes grouped by topic

## Conventions

- **English only in all files.** No Chinese or other non-English text in any
  committed file, including examples and comments.
- **Verify before documenting.** Every endpoint, parameter, and indicator code
  must be checked against the live API
  (`curl "https://api.worldbank.org/v2/...?format=json"`) before it goes into
  the docs. Do not copy indicator codes from memory — the World Bank archives
  series without notice (e.g. all `EN.ATM.CO2E.*` CO2 codes and the entire
  Doing Business source are gone), and an archived code fails only on the
  *data* query (error id 175), not the metadata query.
- **Keep SKILL.md lean** (well under 500 lines). SKILL.md carries the workflow
  and the ~16 most-used codes; the exhaustive endpoint catalog and the larger
  code list belong in `references/`, with clear pointers from SKILL.md.
- **Explain the why.** When documenting a pitfall, state the reason (e.g.
  "prefer mrnev over mrv because most series lag 1–2 years") so agents can
  generalize instead of pattern-matching.
- **Free API is the only stance.** The Indicators API is keyless with no paid
  tier; never invent authentication steps.
- **The script stays dependency-free.** `wb_data.py` must run on a bare
  Python 3.8+ standard library (urllib, json, argparse) so it works in any
  agent sandbox without a pip install.

## Known live-API facts worth preserving

These were discovered by testing and are worth keeping accurate:

- The default response format is **XML**; `format=json` is required on every call.
- JSON responses are a 2-element array `[metadata, rows]`; `rows` is `null`
  when nothing matched.
- **Errors return HTTP 200** with `[{"message":[{"id","key","value"}]}]`.
  Error 120 = invalid code (also returned for economies the World Bank does
  not cover at all, e.g. Taiwan). Error 175 = indicator archived/deleted.
- The API intermittently returns HTML "Request Error" pages and HTTP 400s,
  especially under concurrent requests — always retry transient failures and
  keep concurrency low when bulk-checking codes.
- Country separators are **semicolons** (`US;JP`); `mrv`/`mrnev` override
  `date`; `gapfill=Y` only works together with `mrv`.
- Multi-indicator queries (`ind1;ind2`) require an explicit `source=` param.
- Large `per_page` values (20000+) are accepted, making pagination mostly
  unnecessary for data queries.
- `/v2/country?per_page=400` returns 295 entries; the 78 aggregates are the
  ones with `region.value == "Aggregates"`.
- There is **no server-side text search** for indicators; search client-side
  (source 2 catalog is one request at `per_page=2000`).
- Worldwide Governance Indicators (CC.EST etc.) need `source=3` on data queries.
- Localized paths (`/v2/zh/...`) translate labels only; `en`, `es`, `fr`,
  `ar`, `zh` are supported.

## Validation checklist before committing

1. `grep -rnP '[\x{4e00}-\x{9fff}]' . --exclude-dir=.git` returns nothing (no CJK text).
2. Any changed example URL or indicator code was re-run against the live API —
   for indicator codes, check the **data** endpoint
   (`/v2/country/US/indicator/CODE?format=json&mrnev=1`), not just metadata,
   to catch archived series.
3. `python3 skills/worldbank-api/scripts/wb_data.py gdp US --mrv 1` exits 0.
4. SKILL.md frontmatter still has valid `name` and `description` fields, and
   the description keeps its trigger keywords: GDP, population, inflation,
   unemployment, country data, economic indicators, World Bank.
