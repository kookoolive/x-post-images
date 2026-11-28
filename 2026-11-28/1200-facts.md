# 2026-11-28 12:00 facts — Google Workspace MCP/CLI GA + Agent REST cap

## Sources (fetched 2026-11-28 ~10:10 Asia/Shanghai)
- Workspace Updates blog (2026-11-24): https://workspaceupdates.googleblog.com/2026/11/mcp-servers-cli-standardized-usage-tiers-now-available.html
- Product blog, Yulie Kwon Kim, VP Product - Workspace (2026-11-24): https://workspace.google.com/blog/product-announcements/built-for-trust-and-control-new-ai-agent-protections-and-developer-tools-in-google-workspace
- Quota / tiers docs: https://developers.google.com/workspace/guides/quota-usage-tiers
- Configure MCP servers: https://developers.google.com/workspace/guides/configure-mcp-servers
- Workspace AI Tools Addendum (last modified 2026-11-24): https://workspace.google.com/terms/ai-tools/
- Gmail MCP tools: create_draft https://developers.google.com/workspace/gmail/api/reference/mcp/tools_list/create_draft ; send_message https://developers.google.com/workspace/gmail/api/reference/mcp/tools_list/send_message
- Official launch post: https://x.com/ChanduThota/status/2125275496355348799 (2026-11-24 17:08 UTC; thread 1/3)
  Retrieved ~10:10+08: likes 2332, bookmarks 2008, quotes 156, retweets 281, impressions ~470707
- Do not treat secondary writeups (e.g. "Gmail MCP cannot send") as official. Official tool list includes send_message; sending uses form elicitation on /mcp/v1, and is unavailable on /mcp/v1self.

## What launched 2026-11-24
- Workspace MCP servers and the official Workspace CLI are generally available.
- MCP covers Gmail, Drive, Docs, Sheets, Slides, Calendar, Chat, and Tasks (Keep coming soon).
- MCP: Google-hosted front door for cloud/web agents. CLI: local/desktop/terminal agents (terminals, VMs, container sandboxes), with packaged skills, dynamic command discovery, scoped sign-in.
- Official framing: traditional REST APIs were built for deterministic, human-paced app calls; MCP/CLI are purpose-built for dynamic agent workflows.
- Token/context: MCP tools return synthesized summaries within Workspace's boundary rather than downloading raw data line by line via REST.
- Built-in: user confirmations, DLP, access controls, audit logging.
- Available to all Google Workspace customers and developers.
- Rollout: Rapid and Scheduled Release domains, rolling out to all in 2 weeks for MCP GA, CLI GA, admin HITL/audit controls, usage dashboard, and Scaled tier selection.
- Chandu Thota launch thread also mentions Google Cloud Agent Gateway alongside MCP and CLI. This daily does not expand Agent Gateway (docs not fetched).

## The two dates (do not conflate)
- February 1, 2027:
  - Workspace AI Tools Addendum takes effect.
  - MCP requirement enforcement: AI agents or similar access tools accessing Google Workspace data must use Google Workspace MCP servers or the Google Workspace CLI.
  - Updates blog: agents must use MCP or CLI rather than calling Workspace REST APIs directly. Technical enforcement begins February 1, 2027.
  - Quota page, exact: "Starting February 1, 2027, AI agents or similar access tools that call Google Workspace REST APIs directly (instead of MCP) are limited to Standard Tier quotas, regardless of the tier selected. To obtain higher quotas for agentic use, switch to MCP and enroll in the Scaled Tier (MCP server quotas aren't eligible for increases under the internal-use exception)."
- April 1, 2027:
  - Scaled tier billing begins for the Gmail, Google Calendar, and Google Drive APIs, and the Workspace CLI and MCP servers.
  - REST API usage is billed after exceeding daily no-charge allowances.
  - Scaled Tier Gmail, Calendar, and Drive MCP usage has no daily no-charge allowance and is billed from the first request.
  - If no action is taken before April 1, 2027, projects that still have higher usage limits and have not switched to Scaled or been marked internal move to Standard tier limits, at no additional cost.
  - Two-tier model starts with Gmail, Drive, and Calendar; additional data access mechanisms will be updated similarly in the coming months.

## Tiers and the 5,000 number
- Standard tier: no cost, published quotas; no Google Cloud Billing account required for Standard MCP.
- As of the Nov 24 rollout, across GA Workspace MCP servers the Standard Tier includes up to 5,000 requests per day, per project, per product.
- Some MCP tools count as multiple requests because they make multiple API calls.
- Official: over 99% of active developers currently operate within the Standard tier / less than 1% are expected to need to move beyond it.
- Scaled: higher quotas; must select for the whole Cloud project, not per API. Enrolling removes the free Standard Tier MCP allowance for that project.
- Internal-use exception can raise per-project REST API quotas without Scaled; it does not apply to MCP server quotas. Only for projects used by users in the same Workspace organization that don't share API/MCP access directly with third-party services.
- Do not claim a specific "20,000 emails a minute" REST figure unless independently verified from the quota tables (not used in this post).

## Identity, confirmations, coming soon
- External AI agents must use dedicated OAuth client IDs. In Cloud console, select "Use this client for an AI-powered agent" (is_agentic). Traditional non-agent features should use a separate OAuth client.
- Least-privilege scopes; time-limited approvals coming soon; usage quotas (billing begins April 1, 2027).
- Official blog: people review and confirm consequential actions, like sending external emails, changing Drive sharing permissions, or making bulk edits. External agents obtain user confirmations that adhere to IT-defined Workspace policies.
- Chandu 2/3 (visible text ends mid-sentence): drafts actions are autonomous; actions like sending email, Drive sharing changes, or bulk… (ending not fully returned; do not invent the last words). Pair with the official blog list above.
- Gmail MCP is GA and includes create_draft and send_message. send_message can send a new message or an existing draft. Do not write "Gmail MCP cannot send."
- Coming soon (do not describe as already shipped): users view AI agent activity and revoke access; AI access restrictions to block external agents from specific Gmail messages and Drive files/folders; time-limited approvals.

## Addendum prohibitions (effective February 1, 2027 with the MCP requirement)
- GWS Access Tool that is or is driven by an AI Agent, AI Harness, or an AI model/service employing an AI model must use a Google MCP. May not obfuscate or circumvent classification.
- No using user data or Google Technical Information for model training, fine-tuning, evaluation dataset building, or distillation.
- Google Technical Information (including vector embeddings, memory indexes, user profiles, organizational graphs, and findings) may not be stored more than 30 days, and only to fulfill active, user-authorized tasks.
- Must delete user data and Google Technical Information after disconnecting or revoking access.
- No scraping or systematic probing beyond what a user-authorized task requires.
- No using that data to develop a product similar to a GWS Service.

## Angle
- The news is not "Google has MCP now." It is that agents and ordinary apps are now different kinds of clients.
- After February 1, 2027, an agent still hitting Gmail/Drive REST is capped at Standard Tier quotas even if the project is on Scaled. Paying for Scaled does not lift a direct-REST agent path.
- February 1 is the door; April 1 is the bill. Do not merge them.
- Split OAuth clients and move the agent path first; do not write that changing models is the migration.
