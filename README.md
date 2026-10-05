# StoreRadar MCP server for Shopify store data

<!-- mcp-name: io.storeradar/shopify-store-database -->

A hosted Model Context Protocol (MCP) server that searches a database of 2.7 million live Shopify stores. Your AI assistant can find, count and look up Shopify stores by installed apps, theme, tech stack, country and catalog size.

It works with Claude, ChatGPT, Claude Code, Cursor and any other MCP client that speaks streamable HTTP. It is read only. It is free to connect, with anonymized results on the free plan.

```
https://www.storeradar.io/mcp
```

StoreRadar is independent and is not affiliated with Shopify.

## Contents

- [Which Shopify MCP server do you need?](#which-shopify-mcp-server-do-you-need)
- [Quick start](#quick-start)
- [Example prompts](#example-prompts)
- [Tools](#tools)
- [Free and paid access](#free-and-paid-access)
- [How to read the results](#how-to-read-the-results)
- [FAQ](#faq)
- [Links](#links)

## Which Shopify MCP server do you need?

"Shopify store data" has three meanings. Each meaning needs a different server.

| Your goal | Server | What it reads |
| --- | --- | --- |
| Build a shopping assistant for one store | [Shopify Storefront Catalog MCP](https://shopify.dev/docs/agents/catalog/storefront-catalog) | The public catalog of one store |
| Search products across Shopify merchants | [Shopify Global Catalog MCP](https://shopify.dev/docs/agents/catalog/global-catalog) | Products from many merchants, for shopping agents |
| Manage your own store | An Admin API MCP server | The orders, customers and inventory of your store, with its authorization |
| Find and filter Shopify stores across the market | StoreRadar MCP (this server) | A database of 2.7 million live Shopify stores: apps, theme, tech stack, country, catalog size, contacts |

Use StoreRadar for prospecting, market sizing and competitor research. Use the other servers to read products, prices or orders.

## Quick start

| Field | Value |
| --- | --- |
| URL | `https://www.storeradar.io/mcp` |
| Transport | Streamable HTTP |
| Authentication | OAuth, or a bearer token in the `Authorization` header |

1. Create a free StoreRadar account at [storeradar.io/sign-in](https://www.storeradar.io/sign-in). You do not need a password or a card.
2. Add the server to your client. The steps for each client are below.
3. Ask your assistant to run the `health` tool. The tool answers `ok` and tells you if your results are anonymized.

### Claude.ai and Claude Desktop

1. Open Settings, then Connectors.
2. Select Add custom connector.
3. Paste `https://www.storeradar.io/mcp` as the server URL.
4. When the consent screen opens, sign in to StoreRadar.

The free Claude plan allows one custom connector.

### ChatGPT

Custom connectors need a paid ChatGPT plan and Developer mode.

1. Open Settings, then Apps and connectors, then Advanced.
2. Turn on Developer mode.
3. In Apps and connectors, select Create.
4. Paste `https://www.storeradar.io/mcp` as the MCP server URL.
5. When ChatGPT asks, sign in to StoreRadar.

### Claude Code

Get a token from [your API tokens page](https://www.storeradar.io/api_tokens). Then run this command with your token:

```bash
claude mcp add --transport http storeradar https://www.storeradar.io/mcp \
  --header "Authorization: Bearer YOUR_TOKEN"
```

### Cursor

Add this block to `~/.cursor/mcp.json` for all projects, or to `.cursor/mcp.json` for one project:

```json
{
  "mcpServers": {
    "storeradar": {
      "url": "https://www.storeradar.io/mcp",
      "headers": {
        "Authorization": "Bearer YOUR_TOKEN"
      }
    }
  }
}
```

### Other MCP clients

Point the client at `https://www.storeradar.io/mcp`. Give it an OAuth sign-in or a bearer token from your API tokens page.

The server publishes its OAuth metadata at `https://www.storeradar.io/.well-known/oauth-protected-resource`. A request without a token gets HTTP 401 with a `WWW-Authenticate` header that points to this metadata. The full tool list is at `https://www.storeradar.io/.well-known/mcp/server-card.json`.

## Example prompts

- "How many Shopify stores run both Klaviyo and Judge.me?"
- "Find Shopify stores with more than 1,000 products that run Recharge."
- "Which theme and apps does shop.example.com use?"
- "Which email marketing apps can you filter Shopify stores by?"
- "Run the StoreRadar health tool."

## Tools

All tools are read only. They cannot change your account or start a purchase.

| Tool | What it does | Free limit | Annual limit |
| --- | --- | --- | --- |
| `search_stores` | Returns a page of up to 20 stores that match your filters | 20 per minute, 1,000 per day | 5 times the free limit |
| `count_stores` | Counts the stores that match your filters, without rows | 30 per minute, 2,000 per day | 5 times the free limit |
| `get_store` | Returns the record of one store by domain | 30 per minute, 500 per day | 5 times the free limit |
| `list_filter_options` | Lists the valid values for the filters: industries, countries, languages, technologies, Shopify apps, TLDs | 60 per minute, 3,000 per day | 5 times the free limit |
| `health` | Tests the connection and tells you if your results are anonymized | Not metered | Not metered |

A store record contains these fields:

- Theme and theme version
- Shopify apps and other detected technologies
- Catalog size and blog size
- Country and language
- Contact emails, phone numbers and social profiles
- The date that StoreRadar first saw the store, and the date of the last live confirmation

On the annual plan, the record also contains:

- Estimated Google search traffic for live .com and .fr stores
- Customer-review totals (beta)

Filters cover Shopify apps, technologies, tracking pixels, theme, country, language, industry, top-level domain, catalog size, blog size, email and phone presence, social platforms, live status and scores. Get the valid filter values from `list_filter_options` before you search.

## Free and paid access

| | Free account | StoreRadar Annual ($399 per year) |
| --- | --- | --- |
| All 5 tools | Yes | Yes |
| Store domains, emails and phone numbers | Anonymized | Shown |
| Segments of fewer than 25 stores | Count only, no rows | All rows |
| Free-text domain search | No | Yes |
| Verified-email filter | No | Yes |
| Rate limits | Free limits | 5 times the free limits |
| Full database export as a file | No | Yes, any day of the year |

An anonymized response contains an upgrade link. Your assistant can tell you which question needed the paid plan.

Plans and prices: [storeradar.io/pricing](https://www.storeradar.io/pricing).

## How to read the results

These rules prevent wrong conclusions:

- A count of "fewer than 25" with an empty `stores` array means that the segment is too small to show. It does not mean "no matches".
- A count of "10000+" means more than 10,000 stores. This cap applies to all plans.
- A count of "unknown" means that the count timed out. It is not zero.
- `search_stores` stops at offset 500. If `next_cursor` is absent, you can be at this limit and not at the end of the results.
- Search traffic is an estimate of Google organic traffic. It is not visits or sales.
- `requires_annual` means that the value is locked for your plan. It does not mean zero.

## FAQ

### Is this the official Shopify MCP server?

No. StoreRadar is independent and is not affiliated with Shopify. Shopify publishes its own MCP servers for store catalogs and for development. This server searches a database of Shopify stores across the whole market.

### Can it read the products, prices or orders of a store?

No. It reads store-level records: apps, theme, tech stack, country, catalog size and contacts. For products and prices, use the Shopify Storefront Catalog MCP. For orders of your own store, use an Admin API server.

### Where does the data come from?

StoreRadar crawls public Shopify storefronts and records what each storefront shows. Each record carries the date of the first detection and the date of the last live confirmation.

### Is it free?

Yes, it is free to connect. A free account gets all 5 tools with anonymized domains and contacts. StoreRadar Annual shows real domains and contacts.

### What data do you keep about my calls?

StoreRadar records which account made each call, to apply rate limits. StoreRadar does not send your prompts to other parties. You can revoke a connected assistant from your API tokens page.

### Can I export a list as a CSV file?

Not with the MCP tools. The [StoreRadar REST API](https://www.storeradar.io/api/docs) exports a filtered segment or the full database as CSV. It uses an API token from your account.

## Links

- Product page: [storeradar.io/shopify-mcp](https://www.storeradar.io/shopify-mcp)
- Agent guide: [storeradar.io/agents.md](https://www.storeradar.io/agents.md)
- REST API: [storeradar.io/api/docs](https://www.storeradar.io/api/docs)
- Official MCP registry entry: `io.storeradar/shopify-store-database`

This repository contains documentation only. The server runs at `https://www.storeradar.io/mcp`.

## License

The text and examples in this repository are under the MIT license. See [LICENSE](LICENSE).
