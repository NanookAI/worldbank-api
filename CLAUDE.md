# CLAUDE.md

Follow the repository conventions in [AGENTS.md](AGENTS.md) — it is the single
source of truth for layout, style rules, and the pre-commit validation checklist.

Quick reminders for Claude Code sessions:

- All committed files are English-only; conversation language is unrestricted.
- Test every documented endpoint, parameter, and indicator code against the
  live API (`curl "https://api.worldbank.org/v2/...?format=json"`) before
  writing it down — indicator codes get archived without notice.
- The skill lives in `skills/worldbank-api/`; edit `SKILL.md` for workflow-level
  guidance, `references/` for the endpoint catalog and indicator code lists,
  and `scripts/wb_data.py` for the bundled CLI.
- Default stance is the free, keyless API — there is no paid tier to document.
