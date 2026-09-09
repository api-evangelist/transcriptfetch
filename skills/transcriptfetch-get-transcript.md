---
name: transcriptfetch-get-transcript
description: Fetch a timestamped transcript for a YouTube, TikTok, or Instagram video, a Spotify or Apple Podcasts episode, an RSS feed, or a direct media file, handling the async AI-transcription path.
api: TranscriptFetch REST API v2
operations: [fetchVideoTranscript, getTranscriptJob]
generated: '2026-09-09'
method: generated
source: openapi/transcriptfetch-api-v2-openapi.json, https://transcriptfetch.com/docs/endpoints
---

# Get a transcript

1. `POST https://transcriptfetch.com/api/v2/transcripts/video` (`fetchVideoTranscript`) with `Authorization: Bearer tf_live_...` and body `{"video": "<id-or-url>"}`. The `video` field accepts a bare 11-char YouTube ID, YouTube/TikTok/Instagram URLs, Spotify or Apple Podcasts episode URLs, a podcast RSS feed URL, or a direct media file URL (mp4/mp3/wav).
2. Send an `Idempotency-Key` header (any unique string ≤255 chars) so a retry replays the stored response instead of double-billing. Replays are kept 24 hours; reuse with a different body returns `409 idempotency_conflict`.
3. A `200` returns `{ ok, request_id, data, usage }`; `data.text` (or `data.segments[]` with `start`/`duration`/`text` when `timestamps` is true, the default) carries the transcript and `data.source` says `captions` or `audio`.
4. A `202` means audio transcription started (`JobAcceptedEnvelope` with `job_id` + `poll_url`): poll `GET /api/v2/transcripts/jobs/{jobId}` (`getTranscriptJob`) until `status` is `completed` or `failed`, or supply `callback_url` in step 1 to have the result POSTed to you (HMAC-signed via `X-TranscriptFetch-Signature` when configured). Polling is free.
5. On `422`, read `error.code` — the input is permanently unfetchable (`no_captions`, `private`, `drm_protected`, …). If `error.retry_with` is present, retry with that field (e.g. `{"mode": "audio"}`) to run AI transcription at 1 credit per started minute. On `429` wait `Retry-After` seconds; on `5xx`/`503` retry with backoff. Failures are never charged.
