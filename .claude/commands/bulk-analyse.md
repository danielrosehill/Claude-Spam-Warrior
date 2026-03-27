Process all unanalysed `.eml` files in `inputs/emails/`.

## Steps

1. **List all `.eml` files** in `inputs/emails/` (not in `sanitised/`).

2. **Check for existing analyses** in `outputs/analyses/`. An email is "already analysed" if a corresponding `*-analysis.md` file exists (e.g., `email.eml` → `email-analysis.md`).

3. **For each unanalysed email**, run the same pipeline as `/analyse-email`:
   - Semantic Spam Analysis
   - Tracking Pixel Detection
   - Sender Block
   - Save report

4. **Print a summary table** when done:
   - Number of emails processed
   - Number already analysed (skipped)
   - Number of tracking pixels found across all emails
   - Number of new senders blocked
