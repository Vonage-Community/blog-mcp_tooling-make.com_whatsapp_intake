# AI WhatsApp Intake Agent (Make + Vonage MCP)

Companion quickstart repo for the tutorial:

**Unclog Your Inbound Leads with WhatsApp Using Make + Vonage MCP**

This AI WhatsApp intake agent (Make.com + OpenAI + Vonage MCP tools) helps a plumbing business:

- reply to inbound WhatsApp messages
- triage urgency
- escalate emergencies via SMS or outbound voice calls
- log clean structured records into a Make Data Store (lightweight CRM)

This README focuses on the quickstart path so you can get the demo running in a few minutes.

## What you’ll set up (high level)

- A Vonage Application with Messages + Voice enabled and a WhatsApp number
- A hosted MCP Bridge on Render that exposes `https://YOUR-APP.onrender.com/mcp`
- A Make scenario that receives inbound WhatsApp webhooks from Vonage, runs an AI Agent, and logs results to a Data Store

## Prerequisites

- [Make.com account](https://www.make.com/en/register)
- [Vonage API account](https://dashboard.vonage.com/sign-up)
  - A WhatsApp Business Account (WABA) / WhatsApp-enabled number
- [OpenAI account](https://platform.openai.com/docs/overview) (or another LLM provider)
- [Render account](https://dashboard.render.com/register)

## Quickstart (non-developer friendly)

### Step 1) Create a Vonage Application (Messages + Voice)

In the Vonage Dashboard:

1. Create a new Vonage Application (or reuse an existing one).
2. Enable:
   - Messages
   - Voice
3. For Messages webhooks, set placeholders for now:
   - Inbound URL: `https://placeholder.com/inbound`
   - Status URL: `https://placeholder.com/status`
4. Link your WhatsApp Business Account (WABA) to the application.

Keep these values handy:

- `VONAGE_API_KEY`
- `VONAGE_API_SECRET`
- `VONAGE_APPLICATION_ID`
- Your application private key (downloaded `.key` file)
- Your Vonage virtual number and WhatsApp number (E.164 format)

### Step 2) Deploy the Vonage MCP Bridge to Render

Make needs a public HTTPS endpoint to talk to an MCP server. The bridge hosts a single endpoint:

- `https://YOUR-RENDER-APP.onrender.com/mcp`

Deploy the bridge using the Render blueprint in the bridge repo (from the tutorial).

During deployment set these environment variables in Render:

- `VONAGE_API_KEY`
- `VONAGE_API_SECRET`
- `VONAGE_APPLICATION_ID`
- `VONAGE_PRIVATE_KEY64` (your `.key` file base64-encoded)
- `VONAGE_VIRTUAL_NUMBER`
- `VONAGE_WHATSAPP_NUMBER`
- `MCP_AUTH_TOKEN` (a long random token; you’ll paste this into Make later)

When Render finishes deploying, copy your MCP URL:

- `https://YOUR-RENDER-APP.onrender.com/mcp`

Note: Render free instances can sleep when idle. If Make says it can’t reach your MCP server, restart/redeploy the service in Render or upgrade the plan.

### Step 3) Create the Make Data Store (CRM)

In Make:

1. Go to Data Stores.
2. Click Add data store.
3. Name it: `processed_inbound_messages`
4. Create a new Data structure from JSON.

Paste this JSON to generate the schema (also available in this repo as `process_inbound_message_schema.json`):

```json
{
  "customer_number": "16462364506",
  "last_message_at": "2026-01-30T11:25:00Z",
  "issue_type": "slow drain",
  "urgency": "low",
  "summary_for_plumber": "Bathroom sink draining slowly; no flooding; shut-off accessible.",
  "price_min": 80,
  "price_max": 150,
  "status": "pending-customer"
}
```

Save the data structure.

### Step 4) Create the Make scenario

Recommended (easiest): use the public shared Make scenario:

- https://us2.make.com/public/shared-scenario/it5YTv5H65X/vonage-tooling-mcp-integration

Open the link and choose the option to create your own copy in Make.

Fallback (advanced): import `make_scenario_blueprint.json` from this repo into Make.

### Step 5) Connect the inbound webhook (Make → Vonage)

In Make:

1. Open the scenario.
2. Click the first module (Custom Webhook).
3. Create a webhook (example name: `Incoming Vonage Message`).
4. Copy the generated webhook URL.

In Vonage:

1. Open your Vonage Application settings.
2. In the Messages capability, set Inbound URL to the Make webhook URL.
3. Save.

At this point, inbound WhatsApp messages should reach Make.

### Step 6) Connect the AI Agent to MCP + OpenAI

In the scenario:

1. Open the AI Agent module.
2. Set/confirm your OpenAI connection (API key) and model.
3. In the MCP section:
   - MCP URL: `https://YOUR-RENDER-APP.onrender.com/mcp`
   - Auth: Bearer token
   - Token: your `MCP_AUTH_TOKEN`

### Step 7) Turn it on and test

1. Save the scenario.
2. Turn the scenario ON.
3. Send a WhatsApp message to your Vonage WhatsApp number.

You should see:

- a scenario run in Make
- an AI response sent back to WhatsApp
- a record written to the Make Data Store

## Troubleshooting

- Make says it can’t connect to MCP
  - Your Render service may be sleeping. Restart/redeploy it in Render and try again.
  - Confirm the MCP URL ends with `/mcp`.
  - Confirm Make uses Bearer auth and the token matches `MCP_AUTH_TOKEN`.

- No Make executions when you message WhatsApp
  - Confirm the Vonage Messages Inbound URL matches your Make webhook URL.
  - Confirm the Vonage Application is linked to the correct WhatsApp number/WABA.

- Data Store write fails
  - Confirm the data store exists and is selected in the Data Store module.
  - Regenerate the data structure from the provided JSON and reselect it.

## Repo contents

- `make_scenario_blueprint.json` (backup import path)
- `process_inbound_message_schema.json` (Data Store schema starter)

## Resources

- Tutorial: [(Unclog Your Inbound Leads with WhatsApp Using Make + Vonage MCP)](https://developer.vonage.com/en/blog)
- 9Vonage MCP Tooling Serve)[https://developer.vonage.com/en/blog/introducing-the-vonage-mcp-tooling-server]
- (Vonage Community Slack)[https://developer.vonage.com/en/community/slack]