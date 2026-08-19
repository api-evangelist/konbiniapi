---
name: konbiniapi-reddit-community-listening
description: >-
  Monitor a subreddit or a Reddit-wide topic with KonbiniAPI — pull posts, drill into comment
  threads, and fetch up to 100 records per call with the batch endpoints. Use when asked what a
  community is saying about something, to track a subreddit, or to gather Reddit discussion at scale.
api: KonbiniAPI
base_url: https://api.konbiniapi.com
operations:
  - redditGetSubreddit
  - redditGetSubredditPosts
  - redditGetSubredditRules
  - redditSearchSubredditPosts
  - redditSearchPosts
  - redditGetPost
  - redditGetPostComments
  - redditGetCommentReplies
  - redditGetPostsBatch
  - redditGetCommentsBatch
  - redditGetSubredditsBatch
  - redditGetFeed
generated: '2026-08-13'
method: generated
source: >-
  openapi/_original/konbiniapi-openapi.json (operationIds verified against the live spec) plus
  https://docs.konbiniapi.com/reference/api/reddit
---

# Reddit community listening

## What this does

Reads a community and its conversation. Reddit is KonbiniAPI's deepest surface — 22 of the 67
operations — and the only one with batch endpoints, which changes how you should structure the work.

## Steps

1. **Establish the community.** `redditGetSubreddit` — `GET /v1/reddit/subreddits/{subreddit}` —
   returns title, description, icon, banner and subscriber count. Call
   `redditGetSubredditRules` — `GET /v1/reddit/subreddits/{subreddit}/rules` — if the task involves
   posting norms or moderation context; it returns structured rules.

2. **Choose the right read.**
   - Whole community, ranked: `redditGetSubredditPosts` —
     `GET /v1/reddit/subreddits/{subreddit}/posts`. Supports `hot`, `new`, `top`, `rising` and
     `controversial`, with a time window on `top` and `controversial`.
   - Topic inside one community: `redditSearchSubredditPosts` —
     `GET /v1/reddit/subreddits/{subreddit}/search`.
   - Topic across all of Reddit: `redditSearchPosts` — `GET /v1/reddit/search/posts`.
   - Site-wide feed: `redditGetFeed` — `GET /v1/reddit/feeds/{feed}` — `best`, `hot`, `new`, `top`,
     `rising`, `controversial`.

3. **Page with `cursor`.** Every list endpoint returns an `OrderedCollectionPage`. Follow
   `nextCursor` until it is `null`, or follow the pre-built `next` URL.

4. **Drill into a thread.** Take `entityId` from a post, then:
   - `redditGetPostComments` — `GET /v1/reddit/posts/{postId}/comments` — top-level comments.
   - `redditGetCommentReplies` — `GET /v1/reddit/posts/{postId}/comments/{commentId}/replies` —
     one level deeper. Repeat to walk a thread; the API does not return a whole tree in one call.
   - `redditGetPostDuplicates` — `GET /v1/reddit/posts/{postId}/duplicates` — crossposts and
     resubmissions, useful for measuring how far a story travelled.

5. **Use the batch endpoints when you already have IDs.** This is the part that matters at scale:
   - `redditGetPostsBatch` — `POST /v1/reddit/posts/batch`
   - `redditGetCommentsBatch` — `POST /v1/reddit/comments/batch`
   - `redditGetSubredditsBatch` — `POST /v1/reddit/subreddits/batch`

   Each takes an `entityIds` array of up to **100** IDs. One HTTP round trip instead of a hundred.

   ```bash
   curl -s https://api.konbiniapi.com/v1/reddit/posts/batch \
     -H "Authorization: Bearer $KONBINI_API_KEY" \
     -H "Content-Type: application/json" \
     -d '{"entityIds":["1tlh5aj","1t4mguu","1t6ddw2"]}'
   ```

## Rules

- **Batching saves round trips, not money.** Billing is **1 credit per ID submitted**. A 100-ID
  batch costs 100 credits, exactly the same as 100 individual calls. Use batch for latency and
  simplicity, never as a cost strategy.
- **Batch POSTs have no idempotency key.** A replayed batch call is billed again in full. Deduplicate
  your ID list before you send it, and do not blind-retry a batch that may have succeeded.
- **`hasReplies` before you walk.** `RedditComment` carries `hasReplies`; check it rather than
  calling `redditGetCommentReplies` speculatively — a fruitless call still costs a credit.
- **Never retry a 402.** Terminal.
- **Retry 502 and 503 with backoff.** Refunded and transient.
- **Watch the adult and locked flags.** Posts carry `isAdult`, `isSpoiler`, `isLocked`, `isPinned`
  and `isSponsored`. Filter on them before summarising a community to a human.

## If you are an agent with MCP

All twelve operations exist as tools: `reddit_get_subreddit`, `reddit_get_subreddit_posts`,
`reddit_search_posts`, `reddit_get_post_comments`, `reddit_get_comment_replies`,
`reddit_get_posts_batch`, and so on. For listening tasks set `projection_preset: content` to keep
text, tags and authorship while dropping media and vote internals — comment threads are the largest
payloads in this API.
