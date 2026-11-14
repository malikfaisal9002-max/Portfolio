# OneDrive Bulk PNG Cleanup Automation

**Creator:** Mohammad Faisal  
**Stack:** n8n, Microsoft Graph API, Microsoft OneDrive, OAuth 2.0

## Problem
A large number of unwanted generated PNG files filled OneDrive storage. Manually locating and removing them would have taken hours.

## Solution
A five-node n8n workflow lists items from a chosen OneDrive folder, splits the results, filters PNG files by their creation date, and sends Microsoft Graph API deletion requests for matching items.

### Workflow
1. **Start** — manual trigger.
2. **List all items in target folder** — Microsoft Graph API request with next-page pagination.
3. **Split items** — split the returned `value` array into individual files.
4. **Keep PNGs created 5 Nov 2026** — require a file, not already deleted, a case-insensitive `.png` extension, and a creation timestamp in the configured window.
5. **Delete PNG** — send `DELETE /me/drive/items/{id}` with batches of 10, 500 ms apart, and retry on failure.

## Outcome
Used in a real OneDrive cleanup to remove thousands of unwanted images and save hours of repetitive manual work, with no additional software cost.

## Import and configure
1. Download `workflow.json` and import it into n8n.
2. Create/select your own **Microsoft OneDrive OAuth2** credential in both HTTP Request nodes. No credentials are included in this public export.
3. Set the n8n environment variable `ONEDRIVE_FOLDER_PATH` to the URL-encoded folder path relative to your OneDrive root (for example `MyFolder/Exports`), or replace the placeholder in the listing URL with your folder path. Some n8n hosting plans restrict environment variable access in expressions.
4. Review the filter's **UTC** date boundaries. The original window `2026-11-04T19:00:00Z` through `2026-11-05T19:00:00Z` corresponds to 5 November in Pakistan (UTC+5).
5. **Test safely:** disable the Delete PNG node, execute the workflow, and inspect the filtered file IDs/names. Only enable deletion after confirming the matches.
6. Run manually and inspect the execution results.

## Safety
**Destructive operation:** The Delete node sends real Microsoft Graph DELETE requests. Microsoft OneDrive typically moves deleted items to the recycle bin, but recovery is not guaranteed in all circumstances. Use a test folder first. The public workflow contains no OAuth credential IDs, tokens, account details, or personal OneDrive path. No dry-run toggle or deletion audit log is built into this five-node example.

## Notes
The published workflow is a sanitized, configurable adaptation of a successful one-off workflow. It has **not** been independently executed after sanitization. The screenshot and exact execution statistics are not included here.

## Portfolio summary
Built an n8n workflow integrating Microsoft Graph API to identify and remove unwanted OneDrive PNG files using file metadata and date filters, saving hours of manual cleanup.
