# KonbiniAPI (konbiniapi)

<!-- API-EVANGELIST-PROVENANCE:BEGIN -->
> ### About this repository
>
> **This is not our API.** This repository is an independent, third-party profile of a company's
> **publicly available** API surface, maintained by [API Evangelist](https://apievangelist.com).
> API Evangelist does not operate, host, resell, or support this company's APIs, and is not
> affiliated with or endorsed by the company unless stated on the profile.
>
> **Where the information came from.** Everything here is assembled from material a member of the
> public can reach with a browser and no credentials — the company's own website, developer portal
> and documentation, the specifications it publishes for public use (OpenAPI, AsyncAPI, JSON Schema,
> `apis.json`, `llms.txt` and similar), its public repositories, and its public status, pricing and
> changelog pages. **Nothing here is obtained by breaching a system, defeating an access control, or
> using credentials of any kind.**
>
> **The rating is an independent assessment.** The Kin Score and Agent Readiness rating are
> independently calculated scores of a company's *public* API artifacts, produced by API Evangelist
> against a published rubric. They are not certifications, endorsements, security assessments, or
> audits, and they score published artifacts — not the quality, safety, or security of the software.
>
> **Corrections, re-scores, and removal are free.** No partnership, contract, or purchase is
> required, and you do not need to justify the request.
>
> - **Something wrong?** Open an issue on this repository, or email
>   [info@apievangelist.com](mailto:info@apievangelist.com).
> - **Published something new?** Ask for a re-score and we will re-run the rating.
> - **Want the listing taken down?** Say so and we will honor it. The profile is reduced to your
>   company name, a factual description, and a link to your own site, and the company is recorded as
>   **unrated** — never scored zero for having asked.
>
> **Response times.** Acknowledgement within **one business day**; removal or restriction within
> **two business days**; corrections and re-scores within **five business days**.
>
> **On a security or compliance team?** Email
> [info@apievangelist.com](mailto:info@apievangelist.com) with *security* in the subject line and
> you will get a person, not a form. We will tell you exactly which public URLs this profile was
> built from so your team can see the same surface we did, and we will take the listing down on
> request while you work through it.
>
> Full detail: **[Where this data comes from](https://apievangelist.com/about/where-our-data-comes-from)**
<!-- API-EVANGELIST-PROVENANCE:END -->

KonbiniAPI is the social data layer for Instagram and TikTok, normalizing real-time public profile, post, video, comment, audio, location, and search data into a consistent ActivityStreams 2.0 (W3C) format. The service exposes one Bearer-authenticated REST API and an MCP (Model Context Protocol) interface so developers, AI agents, and automation platforms can fetch normalized social data without managing platform-specific JSON quirks, TLS fingerprinting, sessions, or pagination internals.

**APIs.json:** [https://raw.githubusercontent.com/api-evangelist/konbiniapi/refs/heads/main/apis.yml](https://raw.githubusercontent.com/api-evangelist/konbiniapi/refs/heads/main/apis.yml)

## Scope

- **Type:** Index

## Tags

- API
- Social Media
- Instagram
- TikTok
- ActivityStreams 2.0
- Scraping
- Data Extraction
- Public Data
- Influencer Marketing
- Social Listening
- Creator Tools
- MCP
- Model Context Protocol

## Timestamps

- **Created:** 2026-05-06
- **Modified:** 2026-05-19

## APIs

### KonbiniAPI Main Spec

Unified Instagram and TikTok REST API (30 endpoints) returning normalized ActivityStreams 2.0 JSON. Covers user profiles, posts, reels, videos, stories, highlights, comments, replies, transcripts, audios, hashtags/tags, collections, locations, live streams, and cross-platform search. Every authenticated response includes X-Credits-Remaining and X-Credits-Used headers, and failed requests are auto-refunded.

- **Human URL:** [https://docs.konbiniapi.com](https://docs.konbiniapi.com)
- **Base URL:** `https://api.konbiniapi.com/v1`

#### Tags

- Social Media
- Instagram
- TikTok
- ActivityStreams 2.0
- Unified
- Public Data

#### Properties

- [Documentation](https://docs.konbiniapi.com)
- [OpenAPI](https://docs.konbiniapi.com/openapi.json) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [OpenAPI](undefined) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Workspace](https://www.postman.com/konbiniapi)
- [Open A I Plugin Manifest](https://konbiniapi.com/.well-known/ai-plugin.json)
- [X-llms-txt](https://konbiniapi.com/llms.txt)
- [X-llms-full-txt](https://konbiniapi.com/llms-full.txt)
- [X-mcp](https://docs.konbiniapi.com/getting-started/mcp)
- [JSON Schema](undefined) — [JSON Schema](https://json-schema.org/specification)
- [JSON Structure](undefined)
- [JSON-LD](undefined) — [JSON-LD](https://www.w3.org/TR/json-ld11/)
- [Spectral Rules](undefined)
- [Vocabulary](undefined)
- [Plans](undefined)
- [Rate Limits](undefined)
- [Fin Ops](undefined)
- [Postman Collection](collections/konbiniapi.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/konbiniapi.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

## Common Properties

- [Authentication](https://docs.konbiniapi.com/getting-started/authentication)
- [Getting Started](https://docs.konbiniapi.com/getting-started/quickstart)
- [Documentation](https://docs.konbiniapi.com)
- [Sign Up](https://app.konbiniapi.com)
- [Pricing](https://konbiniapi.com/pricing)
- [Terms of Service](https://konbiniapi.com/terms)
- [Privacy Policy](https://konbiniapi.com/privacy)
- [Git Hub Org](https://github.com/konbiniapi)
- [Contact](mailto:hello@konbiniapi.com)

## Maintainers

**FN:** KonbiniAPI Team
**Email:** hello@konbiniapi.com
