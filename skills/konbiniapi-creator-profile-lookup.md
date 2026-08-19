---
name: konbiniapi-creator-profile-lookup
description: >-
  Look up the same creator across TikTok, Instagram, X, Reddit and LinkedIn with KonbiniAPI and
  compare their audience and engagement using one response shape. Use when asked to profile a
  creator, compare reach across platforms, or assemble a cross-platform audience snapshot.
api: KonbiniAPI
base_url: https://api.konbiniapi.com
operations:
  - instagramGetUser
  - tiktokGetUser
  - xGetUser
  - redditGetUser
  - linkedinGetUser
generated: '2026-08-13'
method: generated
source: >-
  openapi/_original/konbiniapi-openapi.json (operationIds verified against the live spec) plus
  https://docs.konbiniapi.com/getting-started/response-format
---

# Cross-platform creator profile lookup

## What this does

Fetches one creator's public profile on up to five platforms and returns a single comparable
snapshot. Because every KonbiniAPI response is ActivityStreams 2.0, the five profiles come back with
the same field names — you compare `followerCount` to `followerCount`, not `fans` to `followers` to
`connections`.

## Before you start

- Get an API key at https://app.konbiniapi.com. Keys carry the `knbn_` prefix.
- Send it as `Authorization: Bearer knbn_...` on every request.
- Each call costs 1 credit. A 404 (creator not on that platform) **is** charged.

## Steps

1. **Resolve the handle per platform.** Usernames are not portable. Ask for, or carry, the handle
   for each platform separately — do not assume the TikTok handle is the Instagram handle.

2. **Call each profile endpoint.** All five are single GETs, and they are independent, so run them
   concurrently — KonbiniAPI publishes no rate limit and no concurrency cap.

   | Platform | Operation | Path |
   |---|---|---|
   | TikTok | `tiktokGetUser` | `GET /v1/tiktok/users/{username}` |
   | Instagram | `instagramGetUser` | `GET /v1/instagram/users/{username}` |
   | X | `xGetUser` | `GET /v1/x/users/{username}` |
   | Reddit | `redditGetUser` | `GET /v1/reddit/users/{username}` |
   | LinkedIn | `linkedinGetUser` | `GET /v1/linkedin/users/{username}` |

   ```bash
   curl -s https://api.konbiniapi.com/v1/tiktok/users/khaby.lame \
     -H "Authorization: Bearer $KONBINI_API_KEY"
   ```

3. **Read the common fields.** Every profile is a `Person` with `preferredUsername`, `name`,
   `summary` (bio), `icon`, `url`, `entityId` and, where the platform exposes them, `followerCount`
   and `followingCount`. Missing fields mean the platform does not publish them, not that the call
   failed.

4. **Keep `entityId`.** It is the platform-native ID and the input to every follow-on call —
   `tiktokGetUserVideos`, `instagramGetUserPosts`, `xGetUserPosts`, `redditGetUserPosts`,
   `linkedinGetUserPosts` all take the handle, but list items you fetch later are addressed by
   `entityId`.

5. **Handle absences honestly.** A `404` with code `not_found` means the account is not public on
   that platform. Report it as "not found on X", never as zero followers.

## Rules

- **Do not retry a 402.** `credits_exhausted` is terminal for the run. A human must upgrade the plan
  or enable extra usage. Retrying burns time and cannot succeed.
- **Retry 502 and 503 with backoff.** `platform_error` and `service_unavailable` are upstream and
  transient, and they are refunded (`X-Credits-Used: 0`).
- **Budget before you fan out.** Read `X-Credits-Remaining` from the first response. Five platforms
  is five credits per creator; a hundred creators is five hundred.
- **LinkedIn returns less.** Fields the member has restricted are simply omitted. Do not infer that
  a missing headline means an empty profile.

## Errors you will actually hit

| Status | Code | What to do |
|---|---|---|
| 401 | `missing_api_key` | The `Authorization` header is absent or malformed. |
| 402 | `credits_exhausted` | Stop. Terminal. |
| 404 | `not_found` | Account is not public on that platform. Charged. |
| 502 | `platform_error` | Upstream platform failed. Retry with backoff. Refunded. |

## If you are an agent with MCP

The same five calls exist as MCP tools on `https://mcp.konbiniapi.com`: `tiktok_get_user`,
`instagram_get_user`, `x_get_user`, `reddit_get_user`, `linkedin_get_user`. Pass
`projection_preset: identity` to get the profile fields only and cut the token cost. Cost is
identical to REST.
