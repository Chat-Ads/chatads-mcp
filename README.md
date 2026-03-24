[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

# ChatAds MCP Server

Turn AI conversations into affiliate revenue — no code changes required.

ChatAds gives AI assistants a tool that scans their responses for product mentions and returns monetized affiliate links from Amazon. Your AI mentions headphones, ChatAds returns a tracked link to buy them.

This is a **hosted MCP server**. No local install, no dependencies — just a JSON config block and an API key.

[Get a free API key](https://app.getchatads.com) · [Docs](https://docs.getchatads.com/sdks/mcp-server) · [API Reference](https://docs.getchatads.com/api-reference/messages)

---

## Quick Start

1. [Sign up](https://app.getchatads.com) for a free account (no credit card required)
2. Copy your API key from the dashboard (starts with `cak_`)
3. Add the config below to your MCP client
4. Restart the client

### Claude Code (CLI)

Add to `~/.claude.json`:

```json
{
  "mcpServers": {
    "chatads": {
      "url": "https://api.getchatads.com/mcp/mcp",
      "transport": "http",
      "headers": {
        "x-api-key": "cak_your_api_key"
      }
    }
  }
}
```

### Claude Desktop

Edit your config file:
- **macOS:** `~/Library/Application Support/Claude/claude_desktop_config.json`
- **Windows:** `%APPDATA%\Claude\claude_desktop_config.json`
- **Linux:** `~/.config/Claude/claude_desktop_config.json`

```json
{
  "mcpServers": {
    "chatads": {
      "url": "https://api.getchatads.com/mcp/mcp",
      "transport": "http",
      "headers": {
        "x-api-key": "cak_your_api_key"
      }
    }
  }
}
```

### Cursor

Go to **Settings → Features → MCP Servers → Add Server**, then use:

- **Name:** `chatads`
- **URL:** `https://api.getchatads.com/mcp/mcp`
- **Transport:** `http`
- **Headers:** `x-api-key: cak_your_api_key`

### Other MCP Clients

Any client that supports [MCP Streamable HTTP](https://modelcontextprotocol.io/specification/2025-03-26/basic/transports#streamable-http) works — including Windsurf, Zed, and VS Code. Point it at:

```
POST https://api.getchatads.com/mcp/mcp
```

With the header `x-api-key: cak_your_api_key`.

---

## How It Works

```
User asks AI about headphones
        ↓
AI generates response mentioning, say, Sony WH-1000XM5
        ↓
ChatAds MCP tool scans the response
        ↓
Returns affiliate link for the Sony WH-1000XM5
        ↓
AI includes the monetized link in its answer
```

The server exposes one MCP tool — `chatads_message_send` — that your AI assistant calls automatically when it detects a product-related conversation.

---

## Tool Reference

### `chatads_message_send`

Send an AI assistant's response to ChatAds and get affiliate product links back.

| Parameter | Required | Type | Description |
|-----------|----------|------|-------------|
| `message` | Yes | string | The AI response text or image URL to scan for product mentions (max 10,000 chars for text, min 10 words) |
| `input_type` | No | string | `text` (default) or `image_url` |
| `extraction_mode` | No | string | `none`, `fast`, or `standard` (default: `standard`). Forced to `none` for `image_url` |
| `resolution_mode` | No | string | `none` or `standard` (default: `standard`) |
| `image_title_filter` | No | string | Filter image resolution results by title (max 200 chars, `image_url` only) |
| `ip` | No | string | End-user IP for geo-targeting |
| `country` | No | string | ISO 3166-1 alpha-2 country code |

### Example Response

```json
{
  "data": {
    "status": "filled",
    "offers": [
      {
        "link_text": "Sony WH-1000XM5",
        "url": "https://www.amazon.com/dp/B09XS7JWHH/?tag=...",
        "product": {
          "title": "Sony WH-1000XM5 Premium Noise Canceling Headphones",
          "price": 278.00,
          "image": "https://m.media-amazon.com/images/I/..."
        }
      }
    ],
    "requested": 1,
    "returned": 1,
    "billed": true
  },
  "error": null,
  "meta": {
    "request_id": "abc-123",
    "timestamp": "2025-01-15T12:00:00Z",
    "version": "1.0.0"
  }
}
```

---

## Troubleshooting

### "Missing API key" (401)

Make sure the `x-api-key` header is in your config:

```json
"headers": {
  "x-api-key": "cak_your_api_key"
}
```

### Tool not appearing in your client

1. Verify the URL is exactly `https://api.getchatads.com/mcp/mcp`
2. Check that transport is set to `"http"` (not `"stdio"`)
3. Restart your MCP client completely
4. Ask your AI assistant: *"What tools do you have access to?"*

### "Client must accept both application/json and text/event-stream"

This happens with raw HTTP calls. Add the header:

```
Accept: application/json, text/event-stream
```

MCP clients handle this automatically — you only need this for direct API usage.

---

## About ChatAds

ChatAds is affiliate monetization infrastructure for AI applications. Send us an AI response, get back tracked affiliate links. As of 2026, our focus is on Amazon affiliate offers, with more partners onboarding soon.

**Built for AI-native teams:**
- Chatbots and AI assistants that recommend products
- Recipe apps, shopping copilots, review sites
- Any conversational agent where users ask about things to buy

**Response times:** We average 200ms, with p50 at 75ms. Non-monetizeable messages get returned < 25ms.

**Free to start.** No credit card required. Pay-as-you-go when you're ready to scale.

[Get started](https://app.getchatads.com) · [Website](https://www.getchatads.com) · [Pricing](https://www.getchatads.com/pricing) · [Full docs](https://docs.getchatads.com)

### Also Available As

- [TypeScript SDK](https://docs.getchatads.com/sdks/typescript) — `npm install @chat-ads/chatads-sdk`
- [Python SDK](https://docs.getchatads.com/sdks/python) — `pip install chatads-sdk`
- [REST API](https://docs.getchatads.com/api-reference/messages) — Direct HTTP
- [n8n Node](https://docs.getchatads.com/sdks/n8n) — Visual workflow automation
- [Zapier](https://docs.getchatads.com/sdks/zapier) — No-code integration

---

## Contributing

Found an issue or have a suggestion? [Open an issue](https://github.com/chatads/chatads-mcp-server/issues) or submit a pull request.

## License

MIT — see [LICENSE](LICENSE).
