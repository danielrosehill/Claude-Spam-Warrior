# Claude Spam Warrior

A **Claude Code workspace template** for analysing deceptive unsolicited email that evades conventional spam filters.

## The Problem

Modern spam increasingly uses AI to mimic personal correspondence. These emails:
- Reference your name, company, or website to fake familiarity
- Omit unsubscribe links and `List-Unsubscribe` headers
- Embed tracking pixels to monitor open rates
- Disguise commercial pitches as genuine outreach

Traditional keyword and header-based filters miss them. This template uses Claude Code's analytical capabilities to dissect these emails systematically.

## What It Does

For each email you feed it, Claude Spam Warrior runs three analyses:

| Stage | Output |
|-------|--------|
| **Semantic Spam Analysis** | A report identifying deception indicators with quoted evidence, plus a reusable instruction for training an AI email-filter agent |
| **Tracking Pixel Detection** | Extracts hidden tracking pixel URLs and domains, accumulating them into a blocklist |
| **Sender Block** | Logs the sender to a local blocklist and (with MCP integration) pushes to a server-side email filter |

## Quick Start

### Use as a GitHub Template

1. Click **"Use this template"** on GitHub to create your own repo
2. Clone your new repo locally
3. Open it in Claude Code: `cd your-repo && claude`
4. *(Optional)* Run `/setup-mcp` to connect your email provider and server-side filter
5. Drop a `.eml` file into `inputs/emails/`
6. Run `/analyse-email`

### Directory Layout

```
inputs/
  emails/              <- Drop raw .eml files here
    sanitised/         <- Redacted samples safe to share/commit
data/
  tracking-pixels.json <- Accumulated tracking pixel domains
  sender-blocklist.json<- Blocked sender addresses
outputs/
  analyses/            <- Per-email markdown analysis reports
.claude/
  commands/            <- Slash commands
  agents/              <- Subagent definitions
```

## Slash Commands

| Command | Description |
|---------|-------------|
| `/analyse-email` | Analyse the newest `.eml` in `inputs/emails/` |
| `/bulk-analyse` | Process all unanalysed emails in batch |
| `/tracking-report` | Summarise tracking pixel domains and export a DNS blocklist |
| `/setup-mcp` | Interactive setup for email provider and filter MCP connections |

## MCP Integration

This template is designed to work with MCP (Model Context Protocol) servers for two purposes:

1. **Email access**: Connect an MCP server for your email provider (e.g., Gmail, Fastmail, Google Workspace) to read emails directly instead of exporting `.eml` files manually.
2. **Server-side filtering**: The sender-block step can push blocks to a server-side email filter via MCP. **This feature requires an MCP integration** — without one, sender blocks are recorded locally in `data/sender-blocklist.json` only.

Run `/setup-mcp` to configure your `.mcp.json` with the servers you want to use. If you already have MCP servers configured at the user level (`~/.claude/.mcp.json`), those will also be available automatically.

## Outputs

### Analysis Reports

Each analysis is saved to `outputs/analyses/<filename>-analysis.md` and includes:
- Sender details and header assessment
- Deception indicators with quoted evidence and severity ratings
- Tracking pixels found (if any)
- A confidence rating (low/medium/high)
- A filter instruction snippet for downstream AI agents

### Tracking Pixel Blocklist

`data/tracking-pixels.json` accumulates over time. Use `/tracking-report` to generate a domain list suitable for:
- [Pi-hole](https://pi-hole.net/) blocklists
- [AdGuard Home](https://adguard.com/adguard-home/overview.html) filters
- Browser extension blocklists
- `/etc/hosts` entries

## Extending

**Aggregated filter training**: The filter instruction snippets from each analysis can be collected into a system prompt for an autonomous email-triage agent — building a custom spam classifier from real examples.

## Requirements

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) CLI
- Raw `.eml` files (or an email MCP server for direct access)

## License

MIT
