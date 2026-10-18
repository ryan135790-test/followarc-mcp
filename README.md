# FollowArc MCP connector

Connect Claude, ChatGPT or any MCP client to your [FollowArc](https://www.followarc.com) workspace. FollowArc is an AI sales assistant for LinkedIn and email outreach. Through this connector you can ask how your campaigns are doing, see which replies need an answer first, get drafted replies in your voice, review buying signals, and run a prospect search, without opening the app.

This is a hosted (remote) server. There is nothing to install or run. This repository holds the setup instructions and the tool list. You need a FollowArc account to use it.

## Server URL

```
https://www.followarc.com/api/mcp
```

Transport: HTTP (JSON-RPC over POST). Authentication: OAuth 2.1 with dynamic client registration and PKCE, or an API key sent as a bearer token.

## Connect

**Claude (custom connector).** In Claude, open Settings, then Connectors, add a custom connector, and paste the server URL above. Claude opens a FollowArc sign-in window and asks you to approve access.

**ChatGPT.** Add a connector in ChatGPT's settings and paste the server URL. Approve access in the FollowArc sign-in window.

**Other MCP clients with an API key.** In FollowArc, open Settings, then API keys, and create a key. Choose **read-only** (look things up and draft) or **write** (also take the actions listed below). Send it as a header:

```
Authorization: Bearer <your-key>
```

Example client configuration (see [examples/](examples)):

```json
{
  "mcpServers": {
    "followarc": {
      "url": "https://www.followarc.com/api/mcp",
      "headers": { "Authorization": "Bearer YOUR_API_KEY" }
    }
  }
}
```

**Clients that only speak stdio** can use a bridge such as `mcp-remote`. See [examples/stdio-bridge.json](examples/stdio-bridge.json).

## Things to ask

- "How are my campaigns doing over the last 30 days?"
- "Which replies should I answer first?"
- "Draft a reply to this message: 'Thanks for reaching out, can you send pricing?'" (drafts only, nothing is sent)
- "What buying signals showed up this month?"
- "Find VP of Sales contacts at B2B SaaS companies."

## Tools (20)

A read-only key can use the 15 read tools. The 5 write tools need a write-scoped key, and a read-only key is refused.

### Read

| Tool | Title | What it does |
| --- | --- | --- |
| `sophia.search_contacts` | Search Contacts | Search the workspace contacts table by free-text keywords (name, company, title). |
| `sophia.find_email` | Find Email | Find a work email for a person via a multi-provider lookup waterfall. |
| `sophia.get_campaign_stats` | Campaign Stats | Return per-campaign performance: contacts attached, sent, replied, accepted. |
| `sophia.get_recent_replies` | Recent Replies | Return the most recent 20 inbound LinkedIn/email replies with classified sentiment. |
| `sophia.get_activity` | Recent Activity | Return the most recent outreach activity (invites, messages, accepts and failures) for this workspace. |
| `sophia.get_analytics_overview` | Analytics Overview | Return the workspace's top-line funnel over a time window: invites sent, accepted, messages sent, replies received. |
| `sophia.get_team_analytics` | Team Analytics | Return per-rep and per-LinkedIn-account activity totals (invites, accepts, messages, replies) for this workspace over a window. |
| `sophia.list_saved_searches` | Saved Searches | List this workspace's saved LinkedIn/prospect search monitors, including their schedule and last-run status. |
| `sophia.list_signals` | Buying Signals | List the buying signals detected in this workspace in the last 30 days (new roles and promotions, engagement with your posts, interested replies), the 25 most recent, and which campaigns currently auto-enroll on signals. |
| `sophia.get_signal_triggers` | Signal Triggers for a Campaign | Show how a campaign is set to react to signals: whether it auto-enrolls, which signals, the minimum strength, how many people it has enrolled, and how many signals in the last 30 days it would have matched. |
| `sophia.draft_reply` | Draft Reply | Generate 3 reply-draft variants (warm / professional / quick) in Sophia's voice for an inbound message. |
| `sophia.get_inbox_priority_queue` | Inbox Priority Queue | Return the ranked queue of inbound replies/threads awaiting a response for this workspace (highest priority first). |
| `sophia.get_meeting_status` | Meeting Status | Return upcoming meetings, 30-day booked/no-show counts, and calendar-connection status for this workspace. |
| `sophia.score_contact_icp_fit` | Score ICP Fit | Score one or more contacts against a saved ICP profile's filters. |
| `sophia.get_crm_sync_status` | CRM Sync Status | Return the workspace's CRM sync health: synced/queued/failed counts over the last 24h and 7d, plus recent failures. |

### Write

| Tool | Title | What it does |
| --- | --- | --- |
| `sophia.add_to_best_friends` | Add to Best Friends | Add a LinkedIn profile to the best-friends drip list (workspace-wide; drip honors per-workspace pacing). |
| `sophia.send_invite` | Send LinkedIn Invite | Send a single LinkedIn connection invite. |
| `sophia.set_signal_triggers` | Set Signal Triggers | Turn signal-based enrollment on or off for a campaign. |
| `sophia.search_prospects` | Search Prospects | Run a REAL LinkedIn prospect search right now (not scheduled for later) and return actual results — same engine as the in-app LinkedIn Search, with the same safety bounds applied automatically: defaults to 2nd-degree connections and Link... |
| `sophia.build_campaign` | Build Campaign | Auto-build a full multi-step outreach sequence from a brief and save it as a DRAFT campaign — mirrors the in-app "Sophia builds it for you" flow. |

## Safety

- Every action that sends anything passes the same checks as the FollowArc app: daily and weekly limits, send windows, suppression lists and a one-click pause.
- Drafting a reply never sends it.
- An API key works only inside the workspace it was created in. A request that names another workspace is ignored. Signing in gives access to the workspaces you are a member of, and no others.
- Keys are rate limited, can be given an expiry date, and can be revoked at any time in Settings.

## Links

- Product: https://www.followarc.com
- Connector documentation: https://www.followarc.com/mcp
- Privacy policy: https://www.followarc.com/privacy
- Terms: https://www.followarc.com/terms
- Security: https://www.followarc.com/security
- Support: support@agentsophia.com

## License

The contents of this repository (documentation and example configuration) are released under the MIT License. The FollowArc service itself is a commercial product of TJR Universe LLC, doing business as FollowArc.
