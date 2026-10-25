<p align="center">
  <img src="dial-logo.svg" alt="Dial" width="260">
</p>

<p align="center"><b>Phone numbers for AI agents.</b><br>
Give your agent its own real phone number, read the verification codes texted to it, and let it place AI voice calls. One remote MCP server, no install.</p>

<p align="center">
  <a href="https://alohalabshq.com/dial/">Website</a> ·
  <a href="https://alohalabshq.com/dial/#pricing">Pricing</a> ·
  <a href="mailto:dial@alohalabshq.com">dial@alohalabshq.com</a>
</p>

---

## What it does

- **A number per agent.** US and Canadian numbers in the area code you ask for.
- **Verification codes, handled.** When a site texts a code, Dial reads it, works out who sent it (Google, Stripe, GitHub and so on) and hands it straight back to the waiting agent.
- **AI voice calls.** The agent can have a voice assistant phone a business for its human (book a table, make an appointment, ask a question) and get back a summary and transcript.
- **Self-serve.** An agent can sign itself up, confirm its email and get a number without a human touching a dashboard.

## Quick start (MCP)

**Server URL:** `https://aloha-dial-api.onrender.com/mcp` (Streamable HTTP)

You can connect two ways:

1. **No key yet (the agent signs itself up).** Add the server with no headers. The agent calls `sign_up` with an email inbox it can read, saves the `api_key` it returns (shown once), opens the confirmation link, then passes `api_key` to the other tools.
2. **Already have a key.** Sign up at [alohalabshq.com/dial](https://alohalabshq.com/dial/) and add the key as a header: `Authorization: Bearer YOUR_API_KEY`.

### Claude Code

```bash
# keyless: the agent signs itself up
claude mcp add --transport http dial https://aloha-dial-api.onrender.com/mcp

# with a key
claude mcp add --transport http dial https://aloha-dial-api.onrender.com/mcp \
  --header "Authorization: Bearer YOUR_API_KEY"
```

### Claude Desktop, Claude.ai

Settings → Connectors → **Add custom connector** → paste `https://aloha-dial-api.onrender.com/mcp`.

### Cursor, Windsurf, VS Code and other MCP clients

```json
{
  "mcpServers": {
    "dial": {
      "url": "https://aloha-dial-api.onrender.com/mcp",
      "headers": { "Authorization": "Bearer YOUR_API_KEY" }
    }
  }
}
```

Leave out `headers` to run keyless and let the agent call `sign_up`.

### Clients that only speak stdio

```json
{
  "mcpServers": {
    "dial": {
      "command": "npx",
      "args": ["-y", "mcp-remote", "https://aloha-dial-api.onrender.com/mcp",
               "--header", "Authorization: Bearer YOUR_API_KEY"]
    }
  }
}
```

## Tools

| Tool | What it does |
| --- | --- |
| `sign_up` | Create an account with an email the agent can read. Returns the `api_key` once. |
| `resend_confirmation_email` | Send a new confirmation link. |
| `get_phone_number` | Get this agent's number (reuses an existing one unless `force_new`). Optional `area_code`. |
| `list_phone_numbers` | List the account's numbers. |
| `wait_for_verification_code` | Wait for a code texted to a number and return it. Each code is returned once. |
| `read_text_messages` | Read recent texts on a number. |
| `release_phone_number` | Give up a number. |
| `make_phone_call` | Have an AI voice assistant call a business or person for your human, within limits you set. |
| `list_voices` | Voices available for calls. |
| `get_call_result` | Status, outcome, summary and transcript of a call. |
| `account_usage` | Plan, limits, free credit, billing status. |
| `start_subscription` | Stripe Checkout link for Starter, Pro or Scale. |
| `manage_billing` | Stripe portal link to change plan, card or cancel. |

### Typical flow

```
sign_up(email)                      -> api_key        (open the emailed link)
get_phone_number(api_key, "310")    -> +1 310 ...     (type it into the site's form)
wait_for_verification_code(api_key, number_id, service: "github")  -> "774210"
```

## REST

Everything the MCP server does is also a plain HTTP API at `https://aloha-dial-api.onrender.com/v1`.

```bash
curl -X POST https://aloha-dial-api.onrender.com/v1/numbers \
  -H "authorization: Bearer YOUR_API_KEY" \
  -H "content-type: application/json" \
  -d '{"area_code":"310"}'
```

## Pricing

| Plan | Price | Numbers | Call minutes | Texts |
| --- | --- | --- | --- | --- |
| Free | $5 credit, no card | 1 | about 33 (from the credit) | inbound codes included |
| Starter | $15/mo | 1 | 100 | 400 |
| Pro | $49/mo | 5 | 250 | 1,000 |
| Scale | $179/mo | 20 | 1,000 | 5,000 |

Overage on paid plans: $0.15 per call minute, $2/mo per extra number, $0.015 per text. Full details at [alohalabshq.com/dial#pricing](https://alohalabshq.com/dial/#pricing).

## Fair use

Use Dial only with services whose terms allow it. Voice calls always introduce themselves as an AI assistant calling on someone's behalf. Abuse limits apply (signups per IP, codes and calls per day).

## About

Dial is built by [Aloha Labs](https://alohalabshq.com), an AI and crypto product lab in Los Angeles. This repository holds documentation and brand assets only; the server is hosted. Questions or problems: [dial@alohalabshq.com](mailto:dial@alohalabshq.com) or open an issue.
