---
title: "ViraClip"
excerpt: "AI video clipping engine — URL in, viral shorts out. Self-hostable."
collection: portfolio
date: 2025-01-01
header:
  teaser:
---

## ViraClip

AI-powered video processing platform that takes a YouTube/video URL and automatically generates viral short-form clips using Whisper transcription, LLM-based highlight detection, and ComfyUI visual enhancements.

### Key features

- Async job queue with **arq + Redis** — process multiple videos in parallel
- **Whisper** transcription with word-level timestamps for precise cuts
- **ComfyUI** integration for GPU-accelerated visual post-processing
- **FFmpeg** pipeline for encoding, subtitles, and format output
- REST API (FastAPI) + Next.js 15 dashboard
- Fully containerized with **Docker Compose** — one command to spin up
- GPU-ready (CUDA) with CPU fallback

### Stack

`Python` `FastAPI` `Next.js 15` `Whisper` `ComfyUI` `FFmpeg` `Redis` `arq` `PostgreSQL` `Docker`

### Links

- [GitHub Repository](https://github.com/sebsv123/ViraClip)
