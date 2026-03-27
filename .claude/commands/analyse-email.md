Run the full Spam Warrior analysis pipeline on the newest `.eml` file in `inputs/emails/`.

## Steps

1. **Find the target email**: Identify the most recently modified `.eml` file in `inputs/emails/` (not in the `sanitised/` subdirectory). If no `.eml` files exist, inform the user and stop.

2. **Parse the email**: Read the `.eml` file. Extract:
   - From address and display name
   - Subject line
   - Date
   - Reply-To (if different from From)
   - Full HTML and plain-text body
   - All headers (especially `List-Unsubscribe`, `X-Mailer`, `Received` chain)

3. **Semantic Spam Analysis (SSA)**: Analyse the email for deception indicators:
   - AI-generated pseudo-personalisation
   - Templated/mail-merge structure
   - Missing unsubscribe mechanism
   - Urgency or scarcity language
   - Sender identity red flags (domain mismatch, no DKIM, etc.)
   - Generic flattery posing as familiarity

   Write a report with:
   - A list of specific indicators found (with quoted evidence)
   - A confidence assessment (low/medium/high) that this is deceptive spam
   - A reusable instruction snippet for an AI email-filter agent

4. **Tracking Pixel Detection**: Scan the HTML body for:
   - `<img>` tags with 1x1 dimensions or hidden/zero-size styling
   - External image URLs from known tracking domains
   - Any `<img>` loaded from a URL containing tracking parameters (e.g., unique IDs, encoded email addresses)

   If found, append each to `data/tracking-pixels.json` (create the file as an array if it doesn't exist). Each entry should have: `url`, `domain`, `detected_in` (filename), `date`.

5. **Sender Block**: Add the sender to `data/sender-blocklist.json` (create as array if needed). Each entry: `email`, `domain`, `display_name`, `reason`, `date`. If an MCP tool is available for email filtering, also push the block there.

6. **Save the report**: Write the full analysis to `outputs/analyses/` as a markdown file named after the input file (e.g., `email.eml` → `email-analysis.md`).

7. **Summary**: Print a brief summary of findings to the user.
