---
title: "[API] Monitors: get every new video from a channel or profile"
url: "https://transcriptfetch.com/docs/monitors"
date: "2026-09-26"
feed_url: "https://transcriptfetch.com/changelog/rss.xml"
---
POST /api/v2/monitors watches a YouTube channel or a TikTok or Instagram profile and reports each new video as an event. The event is sent to your webhook, signed with HMAC-SHA256, and is always readable from the events endpoint. Add transcripts: true and each event carries the new videos' transcripts.
