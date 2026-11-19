<!-- Generated from the SocQ API catalog and docs. Do not edit by hand. -->
# Facebook API

[![SocQ Facebook API](https://img.shields.io/badge/API-SocQ%20Facebook%20API-F64C31)](https://socq.ai/apis/facebook?utm_source=github&utm_medium=readme&utm_campaign=socq-facebook-api)
[![API documentation](https://img.shields.io/badge/Docs-docs.socq.ai-111827)](https://docs.socq.ai/api-manual/facebook/overview)
[![License: MIT](https://img.shields.io/badge/License-MIT-2563EB)](LICENSE)
[![Check examples](https://github.com/SocQAPI/facebook-api/actions/workflows/check.yml/badge.svg)](https://github.com/SocQAPI/facebook-api/actions/workflows/check.yml)

14 endpoints for public Facebook data through one SocQ API key, with runnable cURL, Node.js and Python examples and a valid request body for every endpoint.

[Browse Facebook APIs](https://socq.ai/apis/facebook?utm_source=github&utm_medium=readme&utm_campaign=socq-facebook-api) · [Get an API key](https://socq.ai/dashboard/api-key?utm_source=github&utm_medium=readme&utm_campaign=socq-facebook-api) · [Documentation](https://docs.socq.ai/api-manual/facebook/overview) · [All SocQ examples](https://github.com/SocQAPI/socq-examples)

## Endpoints

Prices are in SocQ credits; 1 credit = $0.005.

| Endpoint | publicId | What it returns | Price | Launched |
| --- | --- | --- | --- | --- |
| [Facebook Pages API](#facebook-pages-api) | `facebook/pages` | Collect public Facebook Page profile details, contact fields, visible follower and like counts, and page metadata from one or more Page URLs. | 2.4 credits ($0.012) per result | 2026-07-11 |
| [Facebook Posts API](#facebook-posts-api) | `facebook/posts` | Collect public Facebook posts, text, media references, timestamps, reactions, comments, shares, and author context from one or more public URLs. | 1 credit ($0.005) per result | 2026-07-11 |
| [Facebook Comments API](#facebook-comments-api) | `facebook/comments` | Collect public Facebook comments from one or more post URLs and return structured text, author details, timestamps, visible engagement, media, and source links. | 0.3 credits ($0.0015) per result | 2026-07-15 |
| [Facebook Video Transcript API](#facebook-video-transcript-api) | `facebook/video-transcript` | Extract available transcript text from public Facebook video post and Reel URLs, returning source links, format, and collection time as normalized records. | 0.7 credits ($0.0035) per result | 2026-07-23 |
| [Facebook Group Posts API](#facebook-group-posts-api) | `facebook/group-posts` | Collect public Facebook group posts from group URLs with selectable ordering, returning text, authors, timestamps, videos, and visible engagement as normalized records. | 1.2 credits ($0.006) per result | 2026-07-23 |
| [Facebook Events Search API](#facebook-events-search-api) | `facebook/events-search` | Search public Facebook events by keyword and return normalized identity, schedule, place, attendance, cover media, and event-state fields. | 0.6 credits ($0.003) per result | 2026-07-23 |
| [Facebook Ad Transcript API](#facebook-ad-transcript-api) | `facebook/ad-transcript` | Extract available spoken-word transcripts from Facebook Ad Library URLs, returning text, language, format, collection time, source links, and shortcode context. | 0.5 credits ($0.0025) per result | 2026-07-25 |
| [Facebook Comment Replies API](#facebook-comment-replies-api) | `facebook/comment-replies` | Retrieve public Facebook replies from a stable post URL and parent comment ID, returning text, author identity, engagement, timestamps, and parent context in normalized records. | 0.3 credits ($0.0015) per result | 2026-07-25 |
| [Facebook Company Reviews API](#facebook-company-reviews-api) | `facebook/company-reviews` | Collect public reviews from Facebook Page /reviews URLs and return normalized review text, reviewer links, ratings, likes, timestamps, and company context. | 0.5 credits ($0.0025) per result | 2026-07-24 |
| [Facebook Event Details API](#facebook-event-details-api) | `facebook/event-details` | Collect normalized event metadata, schedule, location, public media, and visible response metrics from known Facebook event URLs. | 0.5 credits ($0.0025) per result | 2026-07-25 |
| [Facebook Profile Events API](#facebook-profile-events-api) | `facebook/profile-events` | Collect public events associated with Facebook profile or Page URLs, returning names, descriptions, schedules, locations, media, visible metrics, and source context in normalized records. | 0.6 credits ($0.003) per result | 2026-07-25 |
| [Facebook Profile Photos API](#facebook-profile-photos-api) | `facebook/profile-photos` | Collect public Facebook profile and Page photos from one or more URLs, returning captions, author context, engagement, image metadata, and source links as normalized records. | 0.5 credits ($0.0025) per result | 2026-07-25 |
| [Facebook Profiles API](#facebook-profiles-api) | `facebook/profiles` | Collect public Facebook profile or Page details from known URLs, returning normalized identity, names, bio text, avatars, visible counters, and input context. | 1.2 credits ($0.006) per result | 2026-07-25 |
| [Facebook Reels API](#facebook-reels-api) | `facebook/reels` | Collect public Facebook Reels from profile or Page URLs, returning captions, author context, engagement, video metadata, and source links as normalized records. | 0.7 credits ($0.0035) per result | 2026-07-25 |

## Quick start

1. Create an API key at [socq.ai/dashboard/api-key](https://socq.ai/dashboard/api-key?utm_source=github&utm_medium=readme&utm_campaign=socq-facebook-api).
2. Export it. The examples read only `SOCQ_API_KEY`.

```bash
git clone https://github.com/SocQAPI/facebook-api.git
cd facebook-api
export SOCQ_API_KEY="your-api-key"
```

3. Run any endpoint by its publicId. Without a second argument the script sends `payloads/<slug>.json`; pass a JSON file or an inline JSON object to change the input.

```bash
# Node.js 20+
node examples/node/socq.mjs facebook/pages

# Python 3.8+ (standard library only)
python3 examples/python/socq.py facebook/pages payloads/pages.json

# cURL + jq
bash examples/curl/socq.sh facebook/pages '{"urls":["https://www.facebook.com/nasa"],"results_limit":20}'
```

Each script submits the task, polls it until it succeeds or fails, follows every result page, and writes the records to `output/<slug>-<task_id>.json`. Run `npm run check` to validate the payloads and scripts offline.

Never expose `SOCQ_API_KEY` in browser code, mobile apps, public repositories, screenshots, fixtures or logs.

## How the API works

Base URL `https://api.socq.ai`. Every request sends the API key as a bearer token:

```http
Authorization: Bearer <SOCQ_API_KEY>
Content-Type: application/json
```

All endpoints are asynchronous:

```text
POST /v1/<publicId>  ->  data.task_id  ->  GET /v1/tasks/{task_id}  ->  queued | running | succeeded | failed
```

1. **Submit** the JSON body to `POST https://api.socq.ai/v1/<publicId>`. The response returns `data.task_id` immediately; it means the task was created, not that data is ready.
2. **Poll** `GET https://api.socq.ai/v1/tasks/{task_id}` until `data.status` is `succeeded` or `failed` (a failed task carries `data.error_message`).
3. **Read results.** A succeeded task returns normalized records in `data.results.items`. Use `limit` (default 50, max 500) and pass `data.results.next_cursor` as `cursor` while `data.results.has_more` is true. `fields` selects up to 50 comma-separated fields or dot paths.
4. **Raw files (optional).** `GET https://api.socq.ai/v1/tasks/{task_id}/files` lists raw JSONL exports.

```bash
curl "https://api.socq.ai/v1/tasks/$TASK_ID?limit=100" \
  -H "Authorization: Bearer $SOCQ_API_KEY"

curl "https://api.socq.ai/v1/tasks/$TASK_ID?limit=100&cursor=$NEXT_CURSOR" \
  -H "Authorization: Bearer $SOCQ_API_KEY"
```

`callback_url` (where an endpoint accepts it) makes SocQ POST to your URL when the task reaches a terminal state; keep polling as a fallback.

**Billing.** A submit may reserve credits from `results_limit` or the number of submitted inputs; when the task finishes SocQ settles the charge from the actual result count and returns unused reserved credits. `402` means insufficient credits.

**Errors.** `400` invalid body or unsupported field, `401` missing or invalid key, `402` insufficient credits, `403` IP allowlist or task owned by another account, `404` endpoint or task not found, `422` input validation failed, `429` rate or credit-window limit (respect `Retry-After`), `500` submission failed. A client-side timeout does not cancel a task: resume polling the same `task_id` instead of submitting again.

Reference: [Authentication](https://docs.socq.ai/api-manual/agent-api/authentication) · [Task status and results](https://docs.socq.ai/api-manual/agent-api/status) · [Task files](https://docs.socq.ai/api-manual/agent-api/files) · [Callbacks](https://docs.socq.ai/api-manual/agent-api/callbacks) · [Errors](https://docs.socq.ai/api-manual/agent-api/errors)

## Endpoint reference

### Facebook Pages API

Collect public Facebook Page profile details, contact fields, visible follower and like counts, and page metadata from one or more Page URLs.

- **Request:** `POST https://api.socq.ai/v1/facebook/pages`
- **publicId:** `facebook/pages`
- **Price:** 2.4 credits ($0.012) per result
- **Launched:** 2026-07-11
- **Example body:** [`payloads/pages.json`](payloads/pages.json)

Collect public Facebook page/profile metadata, public URLs, profile fields, and related public metrics.

Provide public Facebook page/profile URLs in urls. Keyword search, username-only input, and individual post/reel URLs are not supported. Legacy query and usernames fields are ignored when valid urls are supplied.

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `callback_url` | string (uri) | No | - |
| `query` | string | No | Deprecated and ignored. Provide page/profile URLs in urls; this endpoint does not perform keyword search or resolve usernames. (deprecated) |
| `results_limit` | integer | No | (value 1 to 2000) |
| `urls` | array of string | Yes | Public Facebook page/profile URLs, such as https://www.facebook.com/nasa. Individual post/reel URLs are not supported. (items at least 1) |
| `usernames` | array of string | No | Deprecated and ignored. Provide page/profile URLs in urls; this endpoint does not perform keyword search or resolve usernames. (items at least 1; deprecated) |

```bash
curl -X POST "https://api.socq.ai/v1/facebook/pages" \
  -H "Authorization: Bearer $SOCQ_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
  "urls": [
    "https://www.facebook.com/nasa"
  ],
  "results_limit": 20
}'
```

Full docs: [https://docs.socq.ai/api-manual/facebook/pages](https://docs.socq.ai/api-manual/facebook/pages) · Landing page: [socq.ai/apis/facebook/pages](https://socq.ai/apis/facebook/pages?utm_source=github&utm_medium=readme&utm_campaign=socq-facebook-api)

### Facebook Posts API

Collect public Facebook posts, text, media references, timestamps, reactions, comments, shares, and author context from one or more public URLs.

- **Request:** `POST https://api.socq.ai/v1/facebook/posts`
- **publicId:** `facebook/posts`
- **Price:** 1 credit ($0.005) per result
- **Launched:** 2026-07-11
- **Example body:** [`payloads/posts.json`](payloads/posts.json)

Collect public Facebook post data, including post text, source URLs, media fields, and engagement metrics.

Provide public Facebook page/profile URLs in urls. Keyword search, username-only input, and individual post/reel URLs are not supported. Legacy query and usernames fields are ignored when valid urls are supplied. Use public Facebook Page URLs; personal-profile posts are not guaranteed to be available.

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `callback_url` | string (uri) | No | - |
| `query` | string | No | Deprecated and ignored. Provide page/profile URLs in urls; this endpoint does not perform keyword search or resolve usernames. (deprecated) |
| `results_limit` | integer | No | (value 1 to 2000) |
| `urls` | array of string | Yes | Public Facebook page/profile URLs, such as https://www.facebook.com/nasa. Individual post/reel URLs are not supported. (items at least 1) |
| `usernames` | array of string | No | Deprecated and ignored. Provide page/profile URLs in urls; this endpoint does not perform keyword search or resolve usernames. (items at least 1; deprecated) |

```bash
curl -X POST "https://api.socq.ai/v1/facebook/posts" \
  -H "Authorization: Bearer $SOCQ_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
  "urls": [
    "https://www.facebook.com/nasa"
  ],
  "results_limit": 20
}'
```

Full docs: [https://docs.socq.ai/api-manual/facebook/posts](https://docs.socq.ai/api-manual/facebook/posts) · Landing page: [socq.ai/apis/facebook/posts](https://socq.ai/apis/facebook/posts?utm_source=github&utm_medium=readme&utm_campaign=socq-facebook-api)

### Facebook Comments API

Collect public Facebook comments from one or more post URLs and return structured text, author details, timestamps, visible engagement, media, and source links.

- **Request:** `POST https://api.socq.ai/v1/facebook/comments`
- **publicId:** `facebook/comments`
- **Price:** 0.3 credits ($0.0015) per result
- **Launched:** 2026-07-15
- **Example body:** [`payloads/comments.json`](payloads/comments.json)

Collect public Facebook comment data for public post URLs, including comment text, author fields, engagement metrics, and raw JSONL output.

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `callback_url` | string (uri) | No | - |
| `urls` | array of string | Yes | Public profile or content URLs. (items at least 1) |

```bash
curl -X POST "https://api.socq.ai/v1/facebook/comments" \
  -H "Authorization: Bearer $SOCQ_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
  "urls": [
    "https://www.facebook.com/reel/1064194079626979/"
  ]
}'
```

Full docs: [https://docs.socq.ai/api-manual/facebook/comments](https://docs.socq.ai/api-manual/facebook/comments) · Landing page: [socq.ai/apis/facebook/comments](https://socq.ai/apis/facebook/comments?utm_source=github&utm_medium=readme&utm_campaign=socq-facebook-api)

### Facebook Video Transcript API

Extract available transcript text from public Facebook video post and Reel URLs, returning source links, format, and collection time as normalized records.

- **Request:** `POST https://api.socq.ai/v1/facebook/video-transcript`
- **publicId:** `facebook/video-transcript`
- **Price:** 0.7 credits ($0.0035) per result
- **Launched:** 2026-07-23
- **Example body:** [`payloads/video-transcript.json`](payloads/video-transcript.json)

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `urls` | array of string | Yes | Public profile or content URLs. (items at least 1) |

```bash
curl -X POST "https://api.socq.ai/v1/facebook/video-transcript" \
  -H "Authorization: Bearer $SOCQ_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
  "urls": [
    "https://www.facebook.com/reel/VIDEO_ID"
  ]
}'
```

Full docs: [https://docs.socq.ai/api-manual/facebook/video-transcript](https://docs.socq.ai/api-manual/facebook/video-transcript) · Landing page: [socq.ai/apis/facebook/video-transcript](https://socq.ai/apis/facebook/video-transcript?utm_source=github&utm_medium=readme&utm_campaign=socq-facebook-api)

### Facebook Group Posts API

Collect public Facebook group posts from group URLs with selectable ordering, returning text, authors, timestamps, videos, and visible engagement as normalized records.

- **Request:** `POST https://api.socq.ai/v1/facebook/group-posts`
- **publicId:** `facebook/group-posts`
- **Price:** 1.2 credits ($0.006) per result
- **Launched:** 2026-07-23
- **Example body:** [`payloads/group-posts.json`](payloads/group-posts.json)

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `results_limit` | integer | No | (value 3 to 2000) |
| `sort_by` | string | No | - |
| `urls` | array of string | Yes | Public profile or content URLs. (items at least 1) |

```bash
curl -X POST "https://api.socq.ai/v1/facebook/group-posts" \
  -H "Authorization: Bearer $SOCQ_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
  "urls": [
    "https://www.facebook.com/groups/GROUP_ID/"
  ],
  "results_limit": 30,
  "sort_by": "recent_activity"
}'
```

Full docs: [https://docs.socq.ai/api-manual/facebook/group-posts](https://docs.socq.ai/api-manual/facebook/group-posts) · Landing page: [socq.ai/apis/facebook/group-posts](https://socq.ai/apis/facebook/group-posts?utm_source=github&utm_medium=readme&utm_campaign=socq-facebook-api)

### Facebook Events Search API

Search public Facebook events by keyword and return normalized identity, schedule, place, attendance, cover media, and event-state fields.

- **Request:** `POST https://api.socq.ai/v1/facebook/events-search`
- **publicId:** `facebook/events-search`
- **Price:** 0.6 credits ($0.003) per result
- **Launched:** 2026-07-23
- **Example body:** [`payloads/events-search.json`](payloads/events-search.json)

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `query` | string | Yes | Search query. |
| `results_limit` | integer | No | (value 7 to 2000) |

```bash
curl -X POST "https://api.socq.ai/v1/facebook/events-search" \
  -H "Authorization: Bearer $SOCQ_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
  "query": "technology conference",
  "results_limit": 20
}'
```

Full docs: [https://docs.socq.ai/api-manual/facebook/events-search](https://docs.socq.ai/api-manual/facebook/events-search) · Landing page: [socq.ai/apis/facebook/events-search](https://socq.ai/apis/facebook/events-search?utm_source=github&utm_medium=readme&utm_campaign=socq-facebook-api)

### Facebook Ad Transcript API

Extract available spoken-word transcripts from Facebook Ad Library URLs, returning text, language, format, collection time, source links, and shortcode context.

- **Request:** `POST https://api.socq.ai/v1/facebook/ad-transcript`
- **publicId:** `facebook/ad-transcript`
- **Price:** 0.5 credits ($0.0025) per result
- **Launched:** 2026-07-25
- **Example body:** [`payloads/ad-transcript.json`](payloads/ad-transcript.json)

Collect transcripts for public Facebook ads. Results are normalized and billed by the actual saved record count.

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `callback_url` | string (uri) | No | - |
| `urls` | array of string | Yes | Public profile or content URLs. (items at least 1) |

```bash
curl -X POST "https://api.socq.ai/v1/facebook/ad-transcript" \
  -H "Authorization: Bearer $SOCQ_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
  "urls": [
    "https://www.facebook.com/ads/library?id=1545582153738441"
  ]
}'
```

Full docs: [https://docs.socq.ai/api-manual/facebook/ad-transcript](https://docs.socq.ai/api-manual/facebook/ad-transcript) · Landing page: [socq.ai/apis/facebook/ad-transcript](https://socq.ai/apis/facebook/ad-transcript?utm_source=github&utm_medium=readme&utm_campaign=socq-facebook-api)

### Facebook Comment Replies API

Retrieve public Facebook replies from a stable post URL and parent comment ID, returning text, author identity, engagement, timestamps, and parent context in normalized records.

- **Request:** `POST https://api.socq.ai/v1/facebook/comment-replies`
- **publicId:** `facebook/comment-replies`
- **Price:** 0.3 credits ($0.0015) per result
- **Launched:** 2026-07-25
- **Example body:** [`payloads/comment-replies.json`](payloads/comment-replies.json)

Collect public replies for a Facebook comment. Results are normalized and billed by the actual saved record count.

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `callback_url` | string (uri) | No | - |
| `comment_id` | string | One of* | A public comment identifier. |
| `expansion_token` | string | One of* | The token used to read replies for a Facebook comment. |
| `feedback_id` | string | One of* | The parent Facebook comment feedback identifier. |
| `results_limit` | integer | No | (value 20 to 2000; default `100`) |
| `url` | string | One of* | A public content or product URL. |

*Required rule: send at least one of these field sets: `comment_id` + `url`, `expansion_token` + `feedback_id`.

```bash
curl -X POST "https://api.socq.ai/v1/facebook/comment-replies" \
  -H "Authorization: Bearer $SOCQ_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
  "url": "https://www.facebook.com/NASA/posts/would-you-lie-in-bed-for-60-days-for-scientific-research11-volunteers-did-exactl/1271494461012594/",
  "comment_id": "Y29tbWVudDoxMjcxNDk0NDYxMDEyNTk0XzU4NzcxOTAzMTA0Mjk5MQ==",
  "results_limit": 20
}'
```

Full docs: [https://docs.socq.ai/api-manual/facebook/comment-replies](https://docs.socq.ai/api-manual/facebook/comment-replies) · Landing page: [socq.ai/apis/facebook/comment-replies](https://socq.ai/apis/facebook/comment-replies?utm_source=github&utm_medium=readme&utm_campaign=socq-facebook-api)

### Facebook Company Reviews API

Collect public reviews from Facebook Page /reviews URLs and return normalized review text, reviewer links, ratings, likes, timestamps, and company context.

- **Request:** `POST https://api.socq.ai/v1/facebook/company-reviews`
- **publicId:** `facebook/company-reviews`
- **Price:** 0.5 credits ($0.0025) per result
- **Launched:** 2026-07-24
- **Example body:** [`payloads/company-reviews.json`](payloads/company-reviews.json)

Collect public Facebook company reviews. Results are normalized and billed by the actual saved record count.

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `callback_url` | string (uri) | No | - |
| `results_limit` | integer | No | (value 20 to 2000; default `100`) |
| `urls` | array of string | Yes | Public profile or content URLs. (items at least 1) |

```bash
curl -X POST "https://api.socq.ai/v1/facebook/company-reviews" \
  -H "Authorization: Bearer $SOCQ_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
  "urls": [
    "https://www.facebook.com/PepsiCo/reviews/"
  ],
  "results_limit": 20
}'
```

Full docs: [https://docs.socq.ai/api-manual/facebook/company-reviews](https://docs.socq.ai/api-manual/facebook/company-reviews) · Landing page: [socq.ai/apis/facebook/company-reviews](https://socq.ai/apis/facebook/company-reviews?utm_source=github&utm_medium=readme&utm_campaign=socq-facebook-api)

### Facebook Event Details API

Collect normalized event metadata, schedule, location, public media, and visible response metrics from known Facebook event URLs.

- **Request:** `POST https://api.socq.ai/v1/facebook/event-details`
- **publicId:** `facebook/event-details`
- **Price:** 0.5 credits ($0.0025) per result
- **Launched:** 2026-07-25
- **Example body:** [`payloads/event-details.json`](payloads/event-details.json)

Collect detailed public Facebook event data. Results are normalized and billed by the actual saved record count.

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `callback_url` | string (uri) | No | - |
| `urls` | array of string | Yes | Public profile or content URLs. (items at least 1) |

```bash
curl -X POST "https://api.socq.ai/v1/facebook/event-details" \
  -H "Authorization: Bearer $SOCQ_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
  "urls": [
    "https://www.facebook.com/events/2255360061870188/"
  ]
}'
```

Full docs: [https://docs.socq.ai/api-manual/facebook/event-details](https://docs.socq.ai/api-manual/facebook/event-details) · Landing page: [socq.ai/apis/facebook/event-details](https://socq.ai/apis/facebook/event-details?utm_source=github&utm_medium=readme&utm_campaign=socq-facebook-api)

### Facebook Profile Events API

Collect public events associated with Facebook profile or Page URLs, returning names, descriptions, schedules, locations, media, visible metrics, and source context in normalized records.

- **Request:** `POST https://api.socq.ai/v1/facebook/profile-events`
- **publicId:** `facebook/profile-events`
- **Price:** 0.6 credits ($0.003) per result
- **Launched:** 2026-07-25
- **Example body:** [`payloads/profile-events.json`](payloads/profile-events.json)

Collect public events associated with Facebook profiles. Results are normalized and billed by the actual saved record count.

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `callback_url` | string (uri) | No | - |
| `results_limit` | integer | No | (value 20 to 2000; default `100`) |
| `urls` | array of string | Yes | Public profile or content URLs. (items at least 1) |

```bash
curl -X POST "https://api.socq.ai/v1/facebook/profile-events" \
  -H "Authorization: Bearer $SOCQ_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
  "urls": [
    "https://www.facebook.com/NASA"
  ],
  "results_limit": 20
}'
```

Full docs: [https://docs.socq.ai/api-manual/facebook/profile-events](https://docs.socq.ai/api-manual/facebook/profile-events) · Landing page: [socq.ai/apis/facebook/profile-events](https://socq.ai/apis/facebook/profile-events?utm_source=github&utm_medium=readme&utm_campaign=socq-facebook-api)

### Facebook Profile Photos API

Collect public Facebook profile and Page photos from one or more URLs, returning captions, author context, engagement, image metadata, and source links as normalized records.

- **Request:** `POST https://api.socq.ai/v1/facebook/profile-photos`
- **publicId:** `facebook/profile-photos`
- **Price:** 0.5 credits ($0.0025) per result
- **Launched:** 2026-07-25
- **Example body:** [`payloads/profile-photos.json`](payloads/profile-photos.json)

Collect photos from public Facebook profiles. Results are normalized and billed by the actual saved record count.

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `callback_url` | string (uri) | No | - |
| `results_limit` | integer | No | (value 20 to 2000; default `100`) |
| `urls` | array of string | Yes | Public profile or content URLs. (items at least 1) |

```bash
curl -X POST "https://api.socq.ai/v1/facebook/profile-photos" \
  -H "Authorization: Bearer $SOCQ_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
  "urls": [
    "https://www.facebook.com/NASA"
  ],
  "results_limit": 20
}'
```

Full docs: [https://docs.socq.ai/api-manual/facebook/profile-photos](https://docs.socq.ai/api-manual/facebook/profile-photos) · Landing page: [socq.ai/apis/facebook/profile-photos](https://socq.ai/apis/facebook/profile-photos?utm_source=github&utm_medium=readme&utm_campaign=socq-facebook-api)

### Facebook Profiles API

Collect public Facebook profile or Page details from known URLs, returning normalized identity, names, bio text, avatars, visible counters, and input context.

- **Request:** `POST https://api.socq.ai/v1/facebook/profiles`
- **publicId:** `facebook/profiles`
- **Price:** 1.2 credits ($0.006) per result
- **Launched:** 2026-07-25
- **Example body:** [`payloads/profiles.json`](payloads/profiles.json)

Collect public Facebook profile data. Results are normalized and billed by the actual saved record count.

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `callback_url` | string (uri) | No | - |
| `urls` | array of string | Yes | Public profile or content URLs. (items at least 1) |

```bash
curl -X POST "https://api.socq.ai/v1/facebook/profiles" \
  -H "Authorization: Bearer $SOCQ_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
  "urls": [
    "https://www.facebook.com/NASA"
  ]
}'
```

Full docs: [https://docs.socq.ai/api-manual/facebook/profiles](https://docs.socq.ai/api-manual/facebook/profiles) · Landing page: [socq.ai/apis/facebook/profiles](https://socq.ai/apis/facebook/profiles?utm_source=github&utm_medium=readme&utm_campaign=socq-facebook-api)

### Facebook Reels API

Collect public Facebook Reels from profile or Page URLs, returning captions, author context, engagement, video metadata, and source links as normalized records.

- **Request:** `POST https://api.socq.ai/v1/facebook/reels`
- **publicId:** `facebook/reels`
- **Price:** 0.7 credits ($0.0035) per result
- **Launched:** 2026-07-25
- **Example body:** [`payloads/reels.json`](payloads/reels.json)

Collect Reels from public Facebook profiles. Results are normalized and billed by the actual saved record count.

Provide Facebook page/profile URLs to collect their reels. A single /reel/... video URL is not a profile URL.

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `callback_url` | string (uri) | No | - |
| `results_limit` | integer | No | (value 20 to 2000; default `100`) |
| `urls` | array of string | Yes | Public Facebook page/profile URLs, such as https://www.facebook.com/nasa. Individual post/reel URLs are not supported. (items at least 1) |

```bash
curl -X POST "https://api.socq.ai/v1/facebook/reels" \
  -H "Authorization: Bearer $SOCQ_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
  "urls": [
    "https://www.facebook.com/nasa"
  ],
  "results_limit": 20
}'
```

Full docs: [https://docs.socq.ai/api-manual/facebook/reels](https://docs.socq.ai/api-manual/facebook/reels) · Landing page: [socq.ai/apis/facebook/reels](https://socq.ai/apis/facebook/reels?utm_source=github&utm_medium=readme&utm_campaign=socq-facebook-api)

## Repository contents

| Path | Purpose |
| --- | --- |
| [`examples/curl/socq.sh`](examples/curl/socq.sh) | Submit, poll and paginate with cURL and jq |
| [`examples/node/socq.mjs`](examples/node/socq.mjs) | Same workflow in Node.js 20+ (no dependencies) |
| [`examples/python/socq.py`](examples/python/socq.py) | Same workflow in Python 3.8+ (standard library) |
| [`payloads/`](payloads) | One documented request body per endpoint |
| [`endpoints.json`](endpoints.json) | Endpoint list, prices and request schemas used by the scripts and checks |
| [`fixtures/`](fixtures) | Synthetic submit and task response shapes |
| [`docs/production-notes.md`](docs/production-notes.md) | Retries, timeouts, pagination and data handling |
| [`scripts/check.mjs`](scripts/check.mjs) | Offline check: payloads, syntax and secrets |

## Responsible use and platform scope

- Use only publicly accessible Facebook data and the fields supported by each endpoint.
- Do not use the examples to access restricted content, login-only surfaces, authentication controls or non-public personal information.
- SocQ is not an official API of the represented platform and is not affiliated with or endorsed by it. Platform names and trademarks belong to their respective owners.
- Before production use, assess the laws, platform terms, privacy obligations and retention requirements that apply to your organization and use case.
- Collect only the fields needed for a defined purpose, restrict access, set retention periods, and support correction or deletion workflows where required.

This section describes the public-data boundary; it is not legal advice.

## License

[MIT](LICENSE)
