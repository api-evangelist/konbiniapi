---
name: konbiniapi-tiktok-video-transcript
description: >-
  Find what a TikTok creator actually said — walk from a handle to a video to its transcript with
  KonbiniAPI, and optionally get a header-free download URL for the video file. Use when asked what
  was said in a TikTok, to summarise a creator's recent videos, or to mine spoken content for
  keywords.
api: KonbiniAPI
base_url: https://api.konbiniapi.com
operations:
  - tiktokGetUser
  - tiktokGetUserVideos
  - tiktokGetVideo
  - tiktokGetVideoTranscript
  - tiktokGetVideoDownloadUrl
  - tiktokSearchVideos
generated: '2026-08-13'
method: generated
source: >-
  openapi/_original/konbiniapi-openapi.json (operationIds verified against the live spec) plus
  https://docs.konbiniapi.com/reference/api/tiktok
---

# TikTok video to transcript

## What this does

Turns "what is this creator talking about?" into text. Walks handle → video list → transcript, using
KonbiniAPI's transcript endpoint, which returns WebVTT.

## Steps

1. **Confirm the account exists.** `tiktokGetUser` — `GET /v1/tiktok/users/{username}`. Accepts the
   handle with or without a leading `@`. A `404` here means the account is private or gone; stop.

2. **List the videos.** `tiktokGetUserVideos` — `GET /v1/tiktok/users/{username}/videos`.
   - `count` maxes out at **35** per page. Asking for more does not get you more.
   - `order` takes `newest` (default), `popular` or `oldest`. Use `popular` when the task is "what is
     this creator known for", `newest` when it is "what are they saying now".
   - Page with `cursor`. Stop when `nextCursor` is `null`.

3. **Take `entityId` from each item.** That is the video ID for every follow-on call.

4. **Fetch the transcript.** `tiktokGetVideoTranscript` —
   `GET /v1/tiktok/videos/{videoId}/transcripts/{language}`.
   - Pass `original` as the language for the auto-generated transcript in whatever language the
     creator spoke.
   - Pass a BCP-47 code (`en`, `es`, `pt-BR`) for a machine translation.
   - Response is **WebVTT**, not JSON prose. Strip the cue timings before summarising.
   - A `404` means the video has no captions. Common. Not a failure — skip and move on.

5. **Optionally get video details.** `tiktokGetVideo` — `GET /v1/tiktok/videos/{videoId}` — adds
   engagement counts, hashtags (`tag`), the audio track and author info, if the task needs context
   around the words.

6. **Optionally download the file.** `tiktokGetVideoDownloadUrl` —
   `GET /v1/tiktok/videos/{videoId}/download` — returns a URL you can fetch with a plain GET: no
   cookies, no auth headers, no fingerprinting. **The link expires in a few hours.** Download
   immediately or re-request; never store the URL as a permanent reference.

## Alternative entry point

If you do not have a handle, start from `tiktokSearchVideos` —
`GET /v1/tiktok/search/videos` — which takes a query and supports sorting by relevance, likes or
date, and filtering by publish time. Then join at step 3.

## Rules

- **One credit per call, and the walk is multi-call.** A creator's 35 most recent videos plus
  transcripts is 1 + 1 + 35 = 37 credits. Check `X-Credits-Remaining` before fanning out.
- **A 404 on the transcript is charged.** Only `route_not_found`, 400 and 5xx are free. Do not
  brute-force transcripts across a whole catalog hoping some exist.
- **Never retry a 402.** `credits_exhausted` is terminal.
- **Retry 502 with backoff.** TikTok is upstream and does fail; those calls are refunded.
- **Transcripts are auto-generated.** They contain recognition errors. Quote them as "auto-generated
  captions", never as verbatim speech, when accuracy matters.

## If you are an agent with MCP

`tiktok_get_user`, `tiktok_get_user_videos`, `tiktok_get_video`, `tiktok_get_video_transcript` and
`tiktok_get_video_download_url` are all tools on `https://mcp.konbiniapi.com`. On
`tiktok_get_user_videos`, set `projection_preset: minimal` and `item_fields: ["entityId","content","published","viewCount"]`
— you only need the IDs to proceed, and the full video objects are large.
