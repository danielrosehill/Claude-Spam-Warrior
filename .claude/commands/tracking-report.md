Summarise accumulated tracking domain data from `data/tracking-domains.json`.

## Steps

1. **Read** `data/tracking-domains.json`. If the file doesn't exist or is empty, inform the user that no tracking domains have been recorded yet.

2. **Aggregate by domain and type**: Group all entries by domain. Show the breakdown by type (`tracking_pixel` vs `click_tracking`).

3. **Print a report** including:
   - Total unique tracking domains
   - Breakdown: how many are tracking pixel domains vs click-tracking redirect domains
   - Total instances detected across all emails
   - Top domains ranked by frequency
   - A list in DNS blocklist format (one domain per line) suitable for pasting into Pi-hole, AdGuard, or `/etc/hosts`

4. **Suggest next steps**: Mention that the user can export this list to their DNS filter or add it to a browser extension blocklist.
