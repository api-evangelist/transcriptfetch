---
name: transcriptfetch-monitor-channel
description: Poll a YouTube channel, TikTok/Instagram profile, podcast show, or RSS feed for new uploads at zero cost, then transcribe only what is new.
api: TranscriptFetch REST API v2
operations: [fetchChannelVideos, fetchVideoTranscript]
generated: '2026-09-09'
method: generated
source: openapi/transcriptfetch-api-v2-openapi.json, https://transcriptfetch.com/docs/pagination
---

# Monitor a channel for new uploads

1. `POST https://transcriptfetch.com/api/v2/transcripts/channel` (`fetchChannelVideos`) with `{"channel": "@handle"}` — accepts a YouTube handle/channel ID/URL, TikTok or Instagram profile, Spotify or Apple Podcasts show, or an RSS feed. The first page (1 credit) returns `data.videos[]` newest-first plus `next_cursor`.
2. Store the newest `videoId` you processed. On every later poll, pass it as `since_video_id`: the page is trimmed to only videos newer than that watermark, and **an empty page is free** — so polling costs nothing until something new appears.
3. When the watermark is not found on the page (an upload burst, or the video was deleted), the page comes back untrimmed rather than dropping videos — de-duplicate against your own store.
4. Page deeper history by echoing `next_cursor` back as `cursor` (opaque token; `next_cursor: null` is the only terminator). Each non-empty page is 1 credit.
5. For each new video, fetch its transcript with `fetchVideoTranscript` (see the transcriptfetch-get-transcript skill), passing the listing row's `url` as-is.
