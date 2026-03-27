Summarise accumulated tracking pixel data from `data/tracking-pixels.json`.

## Steps

1. **Read** `data/tracking-pixels.json`. If the file doesn't exist or is empty, inform the user that no tracking pixels have been recorded yet.

2. **Aggregate by domain**: Group all tracked pixels by their domain and count occurrences.

3. **Print a report** including:
   - Total unique tracking domains
   - Total pixel instances detected
   - Top domains ranked by frequency
   - A list in DNS blocklist format (one domain per line) suitable for pasting into Pi-hole, AdGuard, or `/etc/hosts`

4. **Suggest next steps**: Mention that the user can export this list to their DNS filter or add it to a browser extension blocklist.
