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
  tracking-domains.json  ← Accumulated tracking pixel and click-tracking domains
  sender-blocklist.json  ← Blocked sender addresses
  processed-emails.json  ← Log of all analysed emails with spam confidence scores
outputs/
  analyses/         ← Per-email analysis reports
```

## Analysis Pipeline

Each email goes through three stages:

### 1. Semantic Spam Analysis (SSA)

Produce a short report that reasons about whether the email is **scraped-list outreach or genuine correspondence**. The core question: does this person actually care about the recipient, or are they carpet-bombing a list they scraped?

These emails typically pass every traditional spam filter — valid DKIM/SPF, real company domains, proper infrastructure. The signal is in the *intent*, not the headers.

Reasoning signals (not a checklist — use judgement):
- **Scraped personalisation**: vague references to your "interest in X" or "impressive work" that could apply to anyone on the list, vs. specific engagement with something you actually did
- **Asymmetric ask**: wants something from you (retweet, intro, demo, backlink) while offering nothing of genuine value in return
- **Templated structure**: the email reads like a mail-merge with a name swapped in, even if well-written
- **Tracking infrastructure**: all links routed through click-tracking domains (e.g., `qmailroute.net`, `mailtrack.io`), tracking pixels — signs of a mass campaign, not a personal email
- **Artificial urgency**: deadlines that pressure a quick reply ("by Friday", "spots filling up") with no real reason
- **Commercial intent disguised as friendliness**: the tone pretends to be peer-to-peer but the sender wants free promotion, a sales call, or a backlink

Important: many of these emails come from real people at real companies with legitimate DNS. Don't weight technical signals like missing unsubscribe headers or domain age — those are orthogonal to this threat.

### 2. Tracking Detection

Scan the HTML body for two types of tracking infrastructure:
- **Tracking pixels**: 1x1 transparent images or zero-dimension elements loaded from external URLs
- **Click-tracking redirects**: hyperlinks routed through redirect domains (e.g., `qmailroute.net`, `mailtrack.io`) rather than linking directly to the destination

Record each tracker's domain and type (`tracking_pixel` or `click_tracking`) in `data/tracking-domains.json`. This data feeds a DNS blocklist over time.

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
- **DNS blocklist export**: The tracking domain data in `data/tracking-domains.json` can be exported to Pi-hole, AdGuard, or similar DNS filter formats.
- **Filter agent training**: The SSA instruction snippets in each analysis can be aggregated into a system prompt for an autonomous email-triage agent.
