# Career Coaching

CareerVillage career coaching for Claude Code and Cowork. This plugin bundles two
things that work together:

- **`coach-mcp`** — a remote MCP server exposing CareerVillage's structured
  coaching **playbooks** (`GetCareerCoachingTopics`,
  `GetCareerCoachingGuidance`) plus scoped, source-cited career **data tools**
  (O\*NET, CareerOneStop, College Scorecard, BLS, job search, and more).
- **`career-coaching` skill** — the prompt that drives Claude to select the right
  playbook, retrieve its guidance, and run a grounded multi-turn coaching
  conversation using the `coach-mcp` data tools.

Together they let Claude deliver career guidance aligned with CareerVillage's
proven coaching frameworks, with explicit source attribution.

## Install

```shell
/plugin install career-coaching@claude-plugins-official
```

Then ask a career question — for example *"Help me improve my resume"* or
*"I don't know what career to pursue."*

## Components

| Component | Path | Purpose |
|---|---|---|
| Skill | `skills/career-coaching/SKILL.md` | Auto-triggers on any career/education topic; orchestrates playbook selection and the coaching conversation |
| MCP server | `coach-mcp` (in `.claude-plugin/plugin.json`) | Provides the playbook and data-source tools the skill relies on |

Both are auto-discovered — `skills/` from the plugin root, and the MCP server
from the manifest.

## How it works

1. A career/education message activates the `career-coaching` skill.
2. The skill calls `GetCareerCoachingTopics`, picks the best playbook (or
   `chat_with_coach` when ambiguous), then `GetCareerCoachingGuidance`.
3. It follows the retrieved guidance, pulling in `coach-mcp` data tools as needed
   and citing every source inline.

## MCP server

The plugin connects to CareerVillage's hosted server at
`https://mcp.aicareercoach.org/mcp` over streamable HTTP. It is declared inline
in [`.claude-plugin/plugin.json`](.claude-plugin/plugin.json) — there is no
separate `.mcp.json`, since a plugin should declare its servers in one place.

The URL is hardcoded rather than a `${settings.*}` variable because the managed
marketplace connector flow validates the literal URL and rejects a `${...}`
placeholder.

No API keys or credentials are required from the user; the server holds its own
provider credentials. It reads no user data beyond the questions asked in the
conversation.

### Run against a local server

The server is the
[`coach-deconstructed-mcp`](https://github.com/CareerVillage/coach-deconstructed-mcp)
FastMCP app. To develop against a local instance, clone this repo, point the
`url` in `.claude-plugin/plugin.json` at `http://127.0.0.1:8080/mcp`, and load
the plugin from disk:

```bash
claude --plugin-dir /path/to/coach-plugin-claude
```

Inside the session, run `/mcp` and confirm **`coach-mcp`** shows as
**connected**.

## Development

Validate the manifest and components before pushing:

```bash
claude plugin validate . --strict
```

## License

[Apache License 2.0](LICENSE). Copyright 2026 CareerVillage.

## About CareerVillage

[CareerVillage](https://careervillage.org) is a nonprofit that has helped
millions of learners navigate their career paths.
[AI Career Coach](https://aicareercoach.org) brings that guidance to
conversational AI.
