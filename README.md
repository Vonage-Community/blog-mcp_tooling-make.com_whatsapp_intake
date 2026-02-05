AI WhatsApp Intake Agent (Make + Vonage MCP)

This repo contains everything you need to quickly deploy the AI-powered WhatsApp intake assistant described in the blog post:

📘 Read the full walkthrough: Unclog Your Inbound Leads with WhatsApp Using Make + Vonage MCP

This README focuses on the quickstart path to get you up and running fast.

1. Prerequisites

You’ll need:

Make.com account

Active scenario with access to Webhooks, HTTP, OpenAI, and Data Store modules.

Vonage account

API key and secret.

A Vonage Application with Messages and Voice enabled.

A WhatsApp-enabled phone number (WABA).

OpenAI account (or another LLM provider)

API key and access to GPT-4 (or GPT-3.5).

Render account

To deploy the MCP Bridge service.

(Optional) GitHub account if you're contributing to the MCP tooling.

2. Clone or Copy This Repository

This repo includes:

make_scenario_blueprint.json – a sample Make scenario for WhatsApp intake.

process_inbound_message_schema.json – structure for the Make Data Store.

.env.example – reference for environment variables used by the Render MCP bridge.

3. Create a Vonage Application

Go to your Vonage Dashboard
.

Create a new application or use an existing one.

Enable Messages and Voice.

Set placeholder webhooks for now:

Inbound URL: https://placeholder.com/inbound

Status URL: https://placeholder.com/status

Link your WhatsApp Business Account (WABA).

Save your:

API_KEY, API_SECRET

APPLICATION_ID

Private key .key file (will be base64 encoded for the MCP bridge)

WhatsApp virtual number

4. Deploy the MCP Bridge to Render

The AI agent uses Vonage MCP tools via a simple /mcp endpoint hosted on Render.

Deploy Steps:

Fork or clone this MCP bridge repo
.

Click the "Deploy to Render" button in that repo.

Set the following environment variables in Render:

VONAGE_API_KEY, VONAGE_API_SECRET

VONAGE_APPLICATION_ID, VONAGE_PRIVATE_KEY64 (base64 of your .key)

VONAGE_VIRTUAL_NUMBER, VONAGE_WHATSAPP_NUMBER

MCP_AUTH_TOKEN (a long, random token)

Once deployed, note the MCP bridge URL:
https://your-app.onrender.com/mcp

5. Import and Configure the Make Scenario

In Make, click Scenarios → Create new.

Use the JSON from make_scenario_blueprint.json to import.

Set up your modules:

Replace webhook placeholders with your Make-generated URL.

Connect the MCP Client node to:

Your Render /mcp endpoint

Your MCP_AUTH_TOKEN via Bearer token auth

Set your OpenAI credentials and preferred model (e.g., gpt-4.0).

Create a Data Store using process_inbound_message_schema.json as the structure.

6. Final Setup

In Vonage Dashboard, update the Messages Inbound URL to point to your Make webhook URL.

Save and activate your scenario.

Test the full flow by sending a WhatsApp message to your Vonage number.

✅ You’re Done!

Your AI WhatsApp agent can now:

Respond to incoming WhatsApp messages using GPT-4.

Escalate urgent issues via SMS or voice calls.

Log structured data to a Make Data Store (acting as a lightweight CRM).

Optional: Extend the Agent

Add support for photo uploads (see blog challenge).

Include address, availability, or other custom fields in the AI prompt and schema.

Trigger Slack or email summaries for low-urgency issues.

Integrate with calendars, pricing tools, or external CRMs.

Resources

📘 Full Blog Post →

🛠 Vonage MCP Tooling Server (GitHub)

💬 Join the Community on Slack
