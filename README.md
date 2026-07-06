# World Bank API Skill

An [Agent Skill](https://docs.claude.com/en/docs/agents-and-tools/agent-skills) that
teaches AI agents (Claude Code, Claude.ai, and other skill-compatible agents) how to
use the free [World Bank Indicators API](https://datahelpdesk.worldbank.org/knowledgebase/articles/889392-about-the-indicators-api-documentation).

The API exposes ~30,000 time-series indicators — GDP, population, inflation,
unemployment, life expectancy, CO2 emissions, poverty, trade and more — for 217
economies and 78 aggregates, some series back to 1960. **No API key, no signup.**

## What the skill enables

Once loaded, an agent can reliably:

- Answer "GDP of X" / "compare Y across countries" in one step with the bundled
  `wb_data.py` script (zero dependencies, Python stdlib only)
- Query any indicator for any mix of countries, aggregates, or `all` economies
- Use the right time selectors (`date` ranges, `mrv`, `mrnev`, `gapfill`) and
  know why `mrnev` beats `mrv` for "the latest number"
- Find indicator codes by keyword despite the API having no server-side search
- Discover indicators via topics (21) and sources (~70), and fetch country
  metadata (region, income level, capital)
- Pull the few monthly/quarterly series (Global Economic Monitor, quarterly debt)
- Get localized labels (`en`, `es`, `fr`, `ar`, `zh`)
- Avoid real-world pitfalls (XML default without `format=json`, errors arriving
  as HTTP 200 message envelopes, archived indicator codes like the old CO2
  series, aggregates mixed into `country/all`, economies the World Bank doesn't
  cover, transient HTML error pages)

All request/response examples and every indicator code in the skill were
verified against the live API.

## Repository layout

```
skills/
└── worldbank-api/
    ├── SKILL.md                  # Entry point: workflow, top indicator codes, errors, tips
    ├── scripts/
    │   └── wb_data.py            # Zero-dependency CLI: indicator + countries → formatted series
    └── references/
        ├── endpoints.md          # Full endpoint catalog + all query parameters
        └── indicators.md         # Verified indicator codes grouped by topic
```

## Installation

### Claude Code (plugin marketplace — recommended)

This plugin is distributed through the [NanookAI/skills](https://github.com/NanookAI/skills)
marketplace. Inside Claude Code, run:

```
/plugin marketplace add NanookAI/skills
/plugin install worldbank-api@nanookai-skills
```

The skill is then available in every project, and `/plugin marketplace update`
picks up new versions.

### Claude Code (per-project)

Copy the skill folder into your project's `.claude/skills/` directory:

```bash
cp -r skills/worldbank-api /path/to/your-project/.claude/skills/
```

### Claude Code (personal, all projects)

```bash
cp -r skills/worldbank-api ~/.claude/skills/
```

### Claude.ai / other agents

Package the skill folder (respecting `.skillignore`) into a `.skill` bundle, or
upload the `skills/worldbank-api/` folder contents wherever your agent platform
accepts skills. The skill is self-contained; the bundled script needs only a
Python 3.8+ standard library (no packages to install), and everything else is
plain documentation.

## Try it

After installing, ask your agent things like:

- "Compare GDP per capita of Japan, Korea, and Germany over the last 20 years"
- "What's the current population of the world?"
- "Which countries have the highest inflation right now?"
- "Plot China's CO2 emissions per capita since 2000"
- "What share of people in India use the internet?"

## About the API

- Base URL: `https://api.worldbank.org/v2` (always add `format=json`)
- Free and keyless, no documented rate limit — but be gentle with concurrency;
  the server throttles bursts
- Official docs: [Indicators API Documentation](https://datahelpdesk.worldbank.org/knowledgebase/articles/889392-about-the-indicators-api-documentation)
- Data licensed under [CC BY 4.0](https://datacatalog.worldbank.org/public-licenses)
  (attribution: World Bank)

## License

The skill documentation in this repository is provided as-is. World Bank data
is subject to the World Bank's own
[terms of use](https://www.worldbank.org/en/about/legal/terms-of-use-for-datasets).
