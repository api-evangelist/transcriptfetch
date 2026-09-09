---
name: transcriptfetch-batch-transcripts
description: Transcribe up to 500 videos in one request and handle the three per-item outcomes without over-billing.
api: TranscriptFetch REST API v2
operations: [fetchTranscriptsBatch, getTranscriptJob]
generated: '2026-09-09'
method: generated
source: openapi/transcriptfetch-api-v2-openapi.json, https://transcriptfetch.com/docs/errors
---

# Batch transcripts

1. `POST https://transcriptfetch.com/api/v2/transcripts/batch` (`fetchTranscriptsBatch`) with `{"videos": [...]}` — up to 50 entries on Basic/Pro, 500 on Mega/Scale. Over the plan cap returns `400 batch_too_large` with the cap in `error.details`.
2. Send an `Idempotency-Key` header: batches are billed per successful item, and a replayed batch costs nothing extra.
3. A `200` does NOT mean every item succeeded. Inspect `data.results[]`: each entry carries exactly one `outcome` — `ok` (transcript present, 1 credit), `processing` (captionless entry escalated to AI audio transcription: free on this call, billed on delivery; carries `job_id` + `poll_url`), or `error` (free; the same error block as a request-level failure).
4. Collect `processing` entries by polling `GET /api/v2/transcripts/jobs/{jobId}` (`getTranscriptJob`) — polling is free — or re-send the same batch later: results are cached, so finished items return instantly and are charged once, not twice.
5. To avoid audio-transcription charges entirely, send `"mode": "captions"`; captionless entries then fail as `no_transcript`-style errors instead of escalating. On `402 insufficient_credits` nothing was charged — top up and retry.
