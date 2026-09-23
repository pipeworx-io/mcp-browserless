# mcp-browserless

Browserless MCP — wraps the Browserless headless-Chromium REST API (browserless.io)

Part of [Pipeworx](https://pipeworx.io) — an MCP gateway connecting AI agents to 1663+ live data sources.

## Tools

| Tool | Description |
|------|-------------|
| `browserless_content` | Render a URL in headless Chromium and return the fully-executed HTML (after JavaScript runs) — use this when a plain fetch returns an empty shell or JS-rendered SPA. Example: browserless_content({ url: "https://example.com", _apiKey: "your-token" }) |
| `browserless_scrape` | Extract structured data from a rendered page using CSS selectors. Returns text, inner HTML, attributes and geometry for every matched element. Example: browserless_scrape({ url: "https://news.ycombinator.com", elements: [{ selector: ".titleline a" }], _apiKey: "your-token" }) |
| `browserless_screenshot` | Capture a screenshot of a rendered page. Returns the image byte-length and metadata only (the binary PNG is NOT inlined). Example: browserless_screenshot({ url: "https://example.com", fullPage: true, _apiKey: "your-token" }) |
| `browserless_smart_scrape` | AI-assisted extraction: tries a plain HTTP fetch first and falls back to a full headless browser, returning content in the requested formats (html, markdown, links, screenshot, pdf). Best when you just want clean page content and do not know the right selectors. Example: browserless_smart_scrape({ url: "https://example.com", formats: ["markdown", "links"], _apiKey: "your-token" }) |

## Quick Start

Add to your MCP client (Claude Desktop, Cursor, Windsurf, etc.):

```json
{
  "mcpServers": {
    "browserless": {
      "url": "https://gateway.pipeworx.io/browserless/mcp"
    }
  }
}
```

### What this endpoint actually serves

`tools/list` at `https://gateway.pipeworx.io/browserless/mcp` returns the tools in the table
above **plus the shared Pipeworx meta-tools** — `ask_pipeworx`,
`discover_tools`, `search_within`, `remember`/`recall` and the rest of the
gateway-wide set. So the tool count you see is larger than this table: a
single-pack endpoint currently lists roughly 30 shared tools alongside the
pack's own. The connection's `initialize` response states its exact scope, and
is the authoritative answer for a given day.

This is deliberate, not multiplexing by accident. The meta-tools are what let a
scoped connection answer a question this pack does not cover — via
`ask_pipeworx`, which routes across the whole catalog — without you adding a
second MCP server. There is currently no way to mount a pack endpoint without
them; if the extra schemas cost you more context than the routing is worth,
connect to the full gateway once rather than to several pack endpoints.

Or connect to the full Pipeworx gateway to get every pack's tools listed
directly, instead of just this one's:

```json
{
  "mcpServers": {
    "pipeworx": {
      "url": "https://gateway.pipeworx.io/mcp"
    }
  }
}
```

Both URLs reach the same gateway and the same 1663+ data sources. The
only difference is which pack's tools are listed **directly**; `ask_pipeworx`
reaches all of them from either one.

## No MCP client? Call it over HTTP

This pack takes your own API key (`_apiKey`) — we don't front one for it, so there's no curl here that would run without it. Inspect any tool: `GET https://gateway.pipeworx.io/v1/tools/browserless_content`. Find one: `POST https://gateway.pipeworx.io/v1/tools/search_packs` with `{"query":"..."}`.

## Standalone (no gateway account)

This package also runs as a local stdio MCP server — no Pipeworx account, no
gateway round-trip:

```json
{
  "mcpServers": {
    "browserless": {
      "command": "npx",
      "args": ["-y", "@pipeworx/mcp-browserless"]
    }
  }
}
```

Or run it directly to confirm it starts:

```bash
npx -y @pipeworx/mcp-browserless
```

It speaks MCP over stdin/stdout and answers `initialize`/`tools/list`/`tools/call`
for **only** this pack's tools — none of the shared meta-tools the gateway
connection above adds. Same source, same tools, no ask_pipeworx routing.

## Using with ask_pipeworx

Instead of calling tools directly, you can ask questions in plain English —
this works on the pack endpoint above as well as on the full gateway:

```
ask_pipeworx({ question: "your question about Browserless data" })
```

The gateway picks the right tool and fills the arguments automatically.

## More

- [Docs and guides](https://pipeworx.io/docs)
- [pipeworx.io](https://pipeworx.io)

## License

MIT
