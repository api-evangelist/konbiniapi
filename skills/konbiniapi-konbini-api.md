---
name: Konbini Api
description: Use when fetching social media data from TikTok, Instagram, Reddit, X, or LinkedIn via REST API or MCP. Agents should reach for this skill when building integrations with social platforms, automating data collection, connecting AI agents to social data, or setting up workflow automation with social media sources.
metadata:
    mintlify-proj: konbiniapi
    version: "1.0"
---

# KonbiniAPI Skill

## Product summary

KonbiniAPI is a unified REST API and MCP server for fetching data from TikTok, Instagram, Reddit, X, and LinkedIn. All responses use the W3C ActivityStreams 2.0 standard, so the same code works across all platforms. Use the REST API (`https://api.konbiniapi.com`) with Bearer token authentication, or connect the hosted MCP server (`https://mcp.konbiniapi.com`) to AI agents like Claude, ChatGPT, Cursor, or automation tools like Zapier and n8n. Every request costs 1 credit; failed requests (400, 5xx, 502) are refunded. Get your API key at [app.konbiniapi.com](https://app.konbiniapi.com).

## When to use

Reach for this skill when:
- Building integrations that fetch user profiles, posts, videos, comments, or search results from social platforms
- Connecting AI agents (Claude, ChatGPT, Cursor) to social media data without writing integration code
- Setting up workflow automation (Zapier, n8n, Make, Pipedream) that pulls social data on demand
- Normalizing social data across multiple platforms into a consistent schema
- Fetching transcripts, download URLs, or engagement metrics from TikTok, Instagram, Reddit, X, or LinkedIn
- Chaining API calls to drill down from users → posts → comments or similar hierarchies

## Quick reference

### REST API essentials

| Task | Command |
|------|---------|
| Get user profile | `GET /v1/{platform}/users/{username}` |
| Get user posts/videos | `GET /v1/{platform}/users/{username}/videos` |
| Get post details | `GET /v1/{platform}/videos/{videoId}` |
| Get comments | `GET /v1/{platform}/videos/{videoId}/comments` |
| Search users | `GET /v1/{platform}/search/users?q=query` |
| Search posts | `GET /v1/{platform}/search/posts?q=query` |

### Authentication

```bash
curl https://api.konbiniapi.com/v1/tiktok/users/example \
  -H "Authorization: Bearer knbn_your_api_key"
```

API key format: `knbn_` prefix. Get it at [app.konbiniapi.com](https://app.konbiniapi.com).

### Response headers

| Header | Meaning |
|--------|---------|
| `X-Credits-Remaining` | Credits left in account |
| `X-Credits-Used` | Credits charged (1 or 0) |

### Pagination

Use cursor-based pagination for list endpoints:

```bash
# First page
curl "https://api.konbiniapi.com/v1/tiktok/users/example/videos?count=30"

# Next page (use nextCursor from response)
curl "https://api.konbiniapi.com/v1/tiktok/users/example/videos?cursor=abc123&count=30"
```

Response includes `nextCursor` (null when done), `itemCount`, and `orderedItems`.

### Supported platforms

| Platform | Users | Posts/Videos | Comments | Search | Special |
|----------|-------|--------------|----------|--------|---------|
| TikTok | ✓ | ✓ | ✓ | ✓ | Transcripts, audios, collections, live streams |
| Instagram | ✓ | ✓ | ✓ | ✓ | Reels, highlights, locations |
| Reddit | ✓ | ✓ | ✓ | ✓ | Subreddits, feeds, sticky posts |
| X | ✓ | ✓ | — | — | Communities, highlights |
| LinkedIn | ✓ | ✓ | — | — | Companies, articles, video transcripts |

### MCP tools

All REST endpoints map to typed MCP tools. Tool names follow the pattern: `{platform}_{operation}` (e.g., `tiktok_get_user`, `instagram_get_user_posts`, `reddit_get_subreddit`).

MCP projection presets (keep payloads small):
- `minimal` (default): Identity + paging fields
- `identity`: Names, bios, verification, follower counts
- `engagement`: Views, likes, comments, shares, reposts
- `content`: Text, media, tags, duration, authorship
- `full`: Complete response

## Decision guidance

### When to use REST API vs MCP

| Scenario | Use REST API | Use MCP |
|----------|-------------|---------|
| Building a backend service or script | ✓ | — |
| Integrating with Claude, ChatGPT, or Cursor | — | ✓ |
| Setting up Zapier, n8n, Make, or Pipedream automation | — | ✓ |
| Need fine-grained error handling and retry logic | ✓ | — |
| Want to avoid writing integration code | — | ✓ |
| Calling from any language or framework | ✓ | — |

### When to use projection presets

| Goal | Preset | Add fields with |
|------|--------|-----------------|
| Minimal token usage, identity lookups | `minimal` | `data_fields` |
| Engagement metrics and comparisons | `engagement` | `item_fields` |
| Content analysis, text extraction | `content` | `item_fields` |
| Full payload access (rare) | `full` | — |

## Workflow

### Fetching data via REST API

1. **Get your API key** at [app.konbiniapi.com](https://app.konbiniapi.com) (format: `knbn_...`)
2. **Identify the endpoint** you need from the [API reference](/reference/api/overview) — platform, resource type (user, post, comment), and operation (get, search, list)
3. **Make the request** with Bearer token in the `Authorization` header
4. **Extract `entityId`** from the response — use this to chain related requests (e.g., get a video's `entityId`, then fetch its comments)
5. **Handle pagination** for list endpoints: check `nextCursor` in the response; if not null, make another request with `cursor=nextCursor`
6. **Check credit headers** (`X-Credits-Remaining`, `X-Credits-Used`) to track usage
7. **Handle errors** by checking the `errors` array in the response body; 400, 5xx, and 502 errors are refunded

### Connecting MCP to an AI agent

1. **Choose your client** (Claude, ChatGPT, Cursor, Zapier, n8n, etc.)
2. **Add the remote MCP server**: `https://mcp.konbiniapi.com`
3. **Authorize** via OAuth (browser login) or API key (if supported by your client)
4. **Call tools directly** in your agent or workflow — tool names are `{platform}_{operation}` (e.g., `tiktok_get_user_videos`)
5. **Use projections** to keep payloads small: set `projection_preset` to `minimal` or `engagement` and add specific fields with `data_fields` or `item_fields`
6. **Monitor credits** in the response metadata (`creditsUsed`, `creditsRemaining`)

## Common gotchas

- **Using `id` or `url` as a parameter**: Always use `entityId` from the response when chaining requests. The `id` field is a canonical URL, not a parameter value.
- **Forgetting Bearer token prefix**: API keys start with `knbn_`, and the header must be `Authorization: Bearer knbn_...` (not just the key alone).
- **Assuming credits roll over**: Unused credits do not carry to the next billing cycle. Plan usage within your monthly allocation.
- **Not handling pagination**: List endpoints return only one page at a time. Always check `nextCursor` and loop until it's null.
- **Ignoring error refunds**: 400, 5xx, and 502 errors don't charge credits, but 404 (not found) does. Validate parameters before making requests.
- **Mixing response formats**: All endpoints return ActivityStreams 2.0. Don't expect platform-specific JSON shapes — the schema is consistent across TikTok, Instagram, Reddit, X, and LinkedIn.
- **Exposing API keys in client code**: Keep keys server-side. Never commit them to version control or expose them in frontend code.
- **Assuming all fields are present**: Some fields are platform-specific (e.g., `isVerified` may not exist on all platforms). Check for null or missing fields before using them.
- **Rate limiting confusion**: There are no per-second or per-minute rate limits. The only limit is your credit balance. When credits hit zero, requests return 402 `credits_exhausted`.

## Verification checklist

Before submitting work with KonbiniAPI:

- [ ] API key is stored securely (server-side, not in client code or version control)
- [ ] All requests include `Authorization: Bearer knbn_...` header
- [ ] Pagination is handled correctly: loop until `nextCursor` is null
- [ ] `entityId` is used for chaining requests, not `id` or `url`
- [ ] Error responses are checked for `errors` array and handled appropriately
- [ ] Credit headers (`X-Credits-Remaining`, `X-Credits-Used`) are logged or monitored
- [ ] MCP tools use appropriate projection presets to minimize token usage
- [ ] Platform-specific fields are checked for null before use
- [ ] Failed requests (400, 5xx, 502) are expected to be refunded and don't count against quota
- [ ] Response structure matches ActivityStreams 2.0 (check `@context`, `type`, `orderedItems` for lists)

## Resources

- **Full page navigation**: [https://docs.konbiniapi.com/llms.txt](https://docs.konbiniapi.com/llms.txt)
- **API Reference**: [https://docs.konbiniapi.com/reference/api/overview](https://docs.konbiniapi.com/reference/api/overview)
- **MCP Overview**: [https://docs.konbiniapi.com/reference/mcp/overview](https://docs.konbiniapi.com/reference/mcp/overview)
- **Getting Started**: [https://docs.konbiniapi.com/getting-started/quickstart](https://docs.konbiniapi.com/getting-started/quickstart)

---

> For additional documentation and navigation, see: https://docs.konbiniapi.com/llms.txt