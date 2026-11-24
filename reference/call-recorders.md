# Connecting call transcripts

Any call recorder works. What matters is how transcripts reach the agent server. **Check the tool's current docs and the owner's plan before building**: API access often depends on the plan.

## Order of preference
1. **Webhook + API:** the tool notifies the agent server when a transcript is ready; the agent fetches it. Best: near real time.
2. **API only:** the agent server checks for new transcripts on a schedule (for example every 30 minutes).
3. **Fallback, no API:** a daily import. Either the tool emails transcripts to a dedicated address and the agent reads that mailbox, or transcripts are saved to a Google Drive folder the agent reads (needs the `drive.readonly` scope on that folder's owner account).
4. **None for now:** skip. Agents work without transcripts; add later.

## Tool notes (verify each against current docs)
| Tool | What to look for |
|---|---|
| Granola | Personal API key in the Granola app; a webhook for "note ready" pointing to `https://<agent>.onrender.com/webhooks/granola`, with a signing secret. API access has been on business plans. Notes only appear once summary and transcript exist. |
| Fireflies | API key from Fireflies settings; GraphQL API; webhook when a transcript is ready. |
| Fathom | Check for a public API and webhooks on their plan. |
| Otter | Check whether their plan includes API access; otherwise use the fallback. |
| Zoom | Cloud recording with transcripts on paid plans; a Zoom app (server-to-server OAuth) can fetch recordings and transcripts, and Zoom can send webhooks when a transcript is ready. |
| Google Meet | On Workspace plans with transcripts, Meet saves transcripts as Google Docs in the organiser's Drive. Read them via the Drive API (fallback 3). |
| Other | Ask for the tool's name, check its docs for API or webhooks, else fallback 3. |

## Rules
- Verify webhook signatures; store event ids so retries run once.
- Store a per-source status and last sync in `source_status`, shown in Settings.
- Match people by email address from the calendar invite, never by first name in the transcript.
- Quotes taken from a transcript are checked word for word against it before they're shown.
