# KonbiniAPI (konbiniapi)

KonbiniAPI is the social data layer for Instagram and TikTok, normalizing real-time public profile, post, video, comment, audio, location, and search data into a consistent **ActivityStreams 2.0** (W3C) format. The service exposes one Bearer-authenticated REST API and a Model Context Protocol (MCP) interface, so developers, AI agents, and automation platforms can fetch normalized social data without managing platform-specific JSON quirks, TLS fingerprinting, sessions, or pagination internals.

- **Source:** https://konbiniapi.com/
- **Docs:** https://docs.konbiniapi.com
- **Sign up:** https://app.konbiniapi.com
- **Inbox issue:** [#901](https://github.com/api-evangelist/inbox/issues/901)
- **Repo type:** `company`

## API Surface

The KonbiniAPI v1 REST API exposes 30 GET endpoints split across two platforms:

| Platform | Endpoints | Highlights |
|---|---|---|
| Instagram | 10 | profiles, posts, reels, tagged posts, story highlights, post comments, location feeds, search |
| TikTok | 20 | profiles, videos, stories, collections, followers/following, likes, reposts, live streams, video details, comments + replies, transcripts (WebVTT), audio details, hashtag/challenge feeds, search |

All responses are normalized to ActivityStreams 2.0 with a `@context` of `https://www.w3.org/ns/activitystreams#` plus `https://konbiniapi.com/ns/social#` for KonbiniAPI-specific fields.

## Authentication

- **Scheme:** HTTP Bearer
- **Header:** `Authorization: Bearer knbn_your_api_key`
- **Key prefix:** `knbn_`
- Keys are obtained at https://app.konbiniapi.com — one rotatable key per account; credits are tied to the account.

## Metering & Rate Limits

KonbiniAPI uses a **prepaid-credit** model rather than throughput-based rate limits.

- 1 credit per successful request.
- Failed requests (400 / 5xx / upstream errors) are auto-refunded (`X-Credits-Used: 0`).
- Every authenticated response includes `X-Credits-Remaining` and `X-Credits-Used` headers.
- HTTP 402 is returned when credits are exhausted; no automatic overages.

## Pricing (USD / month)

| Plan  | Price | Credits  |
|-------|-------|----------|
| Plus  | $49   | 25,000   |
| Pro   | $99   | 55,000   |
| Max   | $199  | 160,000  |
| Ultra | $499  | 450,000  |
| Custom| —     | Negotiated |

See [`plans/konbiniapi-plans-pricing.yml`](./plans/konbiniapi-plans-pricing.yml).

## Repository Artifacts

| Artifact | Path |
|---|---|
| apis.yml | [`apis.yml`](./apis.yml) |
| OpenAPI 3.1 spec | [`openapi/konbiniapi-openapi.yml`](./openapi/konbiniapi-openapi.yml) |
| Operation examples (30) | [`examples/`](./examples/) |
| JSON Schemas (26 entities) | [`json-schema/`](./json-schema/) |
| JSON Structure docs (26 entities) | [`json-structure/`](./json-structure/) |
| JSON-LD context | [`json-ld/konbiniapi-context.jsonld`](./json-ld/konbiniapi-context.jsonld) |
| Spectral ruleset | [`rules/konbiniapi-rules.yml`](./rules/konbiniapi-rules.yml) |
| Naftiko capabilities | [`capabilities/`](./capabilities/) |
| Vocabulary | [`vocabulary/konbiniapi-vocabulary.yml`](./vocabulary/konbiniapi-vocabulary.yml) |
| Plans / pricing | [`plans/konbiniapi-plans-pricing.yml`](./plans/konbiniapi-plans-pricing.yml) |
| Rate-limit policy | [`rate-limits/konbiniapi-rate-limits.yml`](./rate-limits/konbiniapi-rate-limits.yml) |
| FinOps mapping | [`finops/konbiniapi-finops.yml`](./finops/konbiniapi-finops.yml) |

## Capability Workflows

| Workflow | Description |
|---|---|
| [`influencer-vetting`](./capabilities/influencer-vetting.yaml) | Cross-platform creator vetting (profile + recent feed, IG and TikTok). |
| [`social-listening`](./capabilities/social-listening.yaml) | Brand/topic search and hashtag-expansion across both platforms. |
| [`content-research`](./capabilities/content-research.yaml) | TikTok deep-dive: video details, comments, reply threads, transcript. |

## Provider Notes

- **GitHub org:** https://github.com/konbiniapi — no public repositories at capture time (2026-05-06).
- **MCP integration:** Compatible with Claude, ChatGPT, Cursor, n8n, Zapier, Make, Pipedream, Replit, Codex, and others.
- **Data scope:** Public data only. Use must comply with the [Terms of Service](https://konbiniapi.com/terms); reselling raw responses or building competing social-data APIs is prohibited.
- **Coming soon:** YouTube and X/Twitter (per provider roadmap).
