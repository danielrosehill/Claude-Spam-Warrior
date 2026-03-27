[![Claude Code Projects Index](https://img.shields.io/badge/Claude%20Code-Projects%20Index-blue?style=flat-square&logo=github)](https://github.com/danielrosehill/Claude-Code-Repos-Index)

# Claude Spam Warrior

A **Claude Code workspace template** for analysing deceptive unsolicited email that evades conventional spam filters.

## The Problem

Modern spam increasingly uses AI to mimic personal correspondence. These emails — mostly scraped-list outreach from GitHub, personal websites, and public profiles — look like this:

- Someone references your "interest in X" or "impressive work" without engaging with anything specific you've done
- They want something from you (retweet, demo, intro call, backlink) while offering nothing in return
- Every link is routed through click-tracking domains, with a tracking pixel at the bottom
- The tone pretends to be personal but the email is clearly a mass campaign
- They pass every traditional spam filter: valid DKIM/SPF, real company domains, proper infrastructure

Traditional keyword and header-based filters miss them entirely. The signal is in the *intent*, not the headers. This template uses Claude Code's reasoning to ask the right question: **is this scraping spam or genuine outreach?**

## What It Does

For each email you feed it, Claude Spam Warrior runs three analyses:

| Stage | Output |
|-------|--------|
| **Semantic Spam Analysis** | Reasons about whether the email is scraped-list outreach or genuine correspondence, assigns a spam confidence score (0.0–1.0), and generates a reusable filter instruction |
| **Tracking Detection** | Extracts tracking pixels and click-tracking redirect domains, logs each with type (`tracking_pixel` or `click_tracking`) into a blocklist |
| **Sender Block** | Logs the sender to a local blocklist and (with MCP integration) pushes to a server-side email filter |
| **Processed Email Log** | Records every analysed email with sender details, full body text, spam score, and reasoning into a structured JSON data store |

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
  tracking-domains.json <- Tracking pixel and click-tracking domains (for DNS blocklists)
  sender-blocklist.json<- Blocked sender addresses
  processed-emails.json<- Log of all analysed emails with spam scores and full details
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

### Tracking Domain Blocklist

`data/tracking-domains.json` accumulates over time, with each entry typed as `tracking_pixel` or `click_tracking`. Use `/tracking-report` to generate a domain list suitable for:
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

---

For more Claude Code projects, visit [my index](https://github.com/danielrosehill/Claude-Code-Repos-Index).
