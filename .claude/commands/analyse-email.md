Run the full Spam Warrior analysis pipeline on the newest `.eml` file in `inputs/emails/`.

## Steps

1. **Find the target email**: Identify the most recently modified `.eml` file in `inputs/emails/` (not in the `sanitised/` subdirectory). If no `.eml` files exist, inform the user and stop.

2. **Parse the email**: Read the `.eml` file. Extract:
   - From address and display name
   - Subject line
   - Date
   - Reply-To (if different from From)
   - Full HTML and plain-text body
   - All headers

3. **Semantic Spam Analysis (SSA)**: Reason about whether this is scraped-list outreach or genuine correspondence. The core question: does this person actually care about the recipient, or are they carpet-bombing a scraped list?

   These emails typically pass every traditional spam filter — valid DKIM/SPF, real company domains, proper infrastructure. The signal is in the *intent*, not the headers.

   Reasoning signals (use judgement, not a checklist):
   - **Scraped personalisation**: vague references to "your interest in X" or "impressive work" that could apply to anyone, vs. specific engagement with something you actually did
   - **Asymmetric ask**: wants something (retweet, intro, demo, backlink) while offering nothing genuine in return
   - **Templated structure**: reads like a mail-merge with a name swapped in
   - **Tracking infrastructure**: links routed through click-tracking domains, tracking pixels — signs of a mass campaign
   - **Artificial urgency**: deadlines pressuring a quick reply with no real reason
   - **Commercial intent disguised as friendliness**: pretends to be peer-to-peer but wants free promotion, a sales call, or a backlink

   Don't over-weight technical signals like missing unsubscribe headers or domain age — those are orthogonal. Focus on reasoning about intent.

   Write a report with:
   - Your reasoning about whether this is scraping spam or genuine outreach
   - Specific evidence quoted from the email
   - A spam confidence score: 0.0 (definitely genuine) to 1.0 (definitely scraping spam)
   - A reusable instruction snippet for an AI email-filter agent

4. **Tracking Detection**: Scan the HTML body for two types of tracking infrastructure:

   **Tracking pixels**: `<img>` tags with 1x1 dimensions, hidden/zero-size styling, or loaded from URLs containing unique tracking IDs/encoded identifiers.

   **Click-tracking redirects**: hyperlinks routed through redirect domains (e.g., `qmailroute.net`, `mailtrack.io`, `emailroute.net`) rather than linking directly to the destination.

   For each tracker found, append an entry to `data/tracking-domains.json` (create as array if needed). Each entry:
   - `domain`: the tracking domain (e.g., `skyfall-ai.qmailroute.net`)
   - `type`: either `tracking_pixel` or `click_tracking`
   - `url`: the full URL (for reference)
   - `detected_in`: the `.eml` filename
   - `date`: ISO 8601 date

5. **Sender Block**: Add the sender to `data/sender-blocklist.json` (create as array if needed). Each entry: `email`, `domain`, `display_name`, `reason`, `date`. If an MCP tool is available for email filtering, also push the block there.

6. **Log to processed emails**: Append an entry to `data/processed-emails.json` (create as array if needed). Each entry:
   - `filename`: the `.eml` filename
   - `date_analysed`: ISO 8601 timestamp of when the analysis was run
   - `date_sent`: the Date header from the email
   - `from_email`: sender email address
   - `from_name`: sender display name
   - `subject`: email subject line
   - `body_plain`: the plain-text body of the email (full text)
   - `spam_confidence`: the 0.0–1.0 score from the SSA
   - `spam_reasoning`: one-sentence summary of why the score was assigned
   - `why_added`: the agent's rationale for flagging this email — what type of scraping spam it is and what gave it away
   - `flags`: an object of boolean flags for common scraping-spam patterns:
     - `scraped_personalisation`: vague references to recipient's work/interests that could apply to anyone
     - `asymmetric_ask`: wants something (retweet, demo, backlink) while offering nothing
     - `tracking_links`: links routed through click-tracking redirect domains
     - `tracking_pixels`: hidden tracking pixel(s) in the HTML
     - `artificial_urgency`: deadline pressure with no real reason
     - `templated_structure`: reads like a mail-merge with a name swapped in
     - `commercial_disguised_as_personal`: sales/promo intent masked as friendly outreach
   - `trackers_found`: object with counts: `{ "tracking_pixels": N, "click_tracking": N }`
   - `analysis_file`: path to the output analysis report

7. **Save the report**: Write the full analysis to `outputs/analyses/` as a markdown file named after the input file (e.g., `email.eml` → `email-analysis.md`).

8. **Summary**: Print a brief summary of findings to the user.
