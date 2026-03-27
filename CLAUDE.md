# Claude Spam Warrior

A Claude Code workspace template for analysing deceptive unsolicited email — the kind that slips past conventional spam filters.

## Purpose

This template provides a structured environment for using Claude Code to dissect spam/outreach emails that evade traditional filtering. These emails typically:

- Use AI-generated pseudo-personalisation (e.g., referencing your website or job title with generic phrasing)
- Omit unsubscribe links or `List-Unsubscribe` headers
- Disguise commercial intent as personal correspondence
- Embed tracking pixels for open-rate surveillance

## Workflow

1. Drop a raw `.eml` file into `inputs/emails/`
2. Run `/analyse-email` to trigger the full analysis pipeline
3. Review the output in `outputs/analyses/`

## Directory Structure

```
inputs/
  emails/           ← Drop raw .eml files here
    sanitised/      ← Redacted/sample emails for sharing
data/
  tracking-pixels.json   ← Accumulated tracking pixel domains
  sender-blocklist.json  ← Blocked sender addresses
outputs/
  analyses/         ← Per-email analysis reports
```

## Analysis Pipeline

Each email goes through three stages:

### 1. Semantic Spam Analysis (SSA)

Produce a short report identifying deception indicators and generate a reusable instruction snippet for an AI email-filter agent. The instruction should be specific enough to catch similar emails without false-positiving on legitimate correspondence.

Indicators to look for:
- Generic flattery disguised as personalisation ("I came across your impressive work...")
- Templated structure with mail-merge variables
- Missing or fake sender identity signals
- Urgency/scarcity language without substance
- No unsubscribe mechanism (link or `List-Unsubscribe` header)
- Sender domain age, reputation, or mismatch with claimed identity

### 2. Tracking Pixel Detection

Scan the HTML body for tracking pixels — typically 1x1 transparent images or zero-dimension elements loaded from external URLs. Record each pixel's full URL and domain in `data/tracking-pixels.json`. This data feeds a DNS blocklist over time.

### 3. Sender Block

Add the sender's address to `data/sender-blocklist.json`. If an MCP tool is available for server-side email filtering, use it to push the block upstream.

## Slash Commands

| Command | Description |
|---------|-------------|
| `/analyse-email` | Run the full pipeline on the newest `.eml` in `inputs/emails/` |
| `/bulk-analyse` | Process all unanalysed `.eml` files in `inputs/emails/` |
| `/tracking-report` | Summarise accumulated tracking pixel domains |

## Customisation

- **MCP integration**: If you have an MCP server for your email provider, update the "Sender Block" step to call it directly.
- **DNS blocklist export**: The tracking pixel data in `data/tracking-pixels.json` can be exported to Pi-hole, AdGuard, or similar DNS filter formats.
- **Filter agent training**: The SSA instruction snippets in each analysis can be aggregated into a system prompt for an autonomous email-triage agent.
