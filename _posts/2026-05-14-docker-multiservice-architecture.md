---
title: 'Docker Multi-Service Architecture: Lessons from Running 6 Containers in Production'
date: 2026-05-14
permalink: /posts/2026/05/docker-multiservice-architecture/
tags:
  - Docker
  - DevOps
  - architecture
  - Redis
  - PostgreSQL
---

Running a production system with 6+ Docker containers taught me more about resource management and failure modes than any tutorial ever could. Here's what I learned building ViraClip's infrastructure.

## The Setup

ViraClip runs as a Docker Compose stack with these services:

```yaml
services:
  backend:    # FastAPI API server
  worker:     # FFmpeg processing workers
  comfyui:    # AI video generation
  redis:      # Job queue + progress tracking
  postgres:   # Primary database
  rust-agent: # Self-healing watchdog
```

## Lesson 1: Resource Limits Are Not Optional

Without `mem_limit` and `cpus` constraints, ComfyUI will happily consume all available RAM when generating video, killing every other container. Always set explicit limits:

```yaml
comfyui:
  mem_limit: 8g
  cpus: '4'
```

## Lesson 2: Health Checks Save Your Sanity

Docker's `depends_on` only waits for a container to *start*, not to be *ready*. A PostgreSQL container is "running" 3 seconds before it accepts connections. Add proper health checks:

```yaml
postgres:
  healthcheck:
    test: ["CMD-SHELL", "pg_isready -U $POSTGRES_USER"]
    interval: 5s
    timeout: 5s
    retries: 10
```

## Lesson 3: The Rust Watchdog Pattern

Instead of relying solely on Docker's restart policies, I built a Rust agent that:

1. Polls health endpoints every 30s
2. Runs diagnostic commands on failure
3. Attempts a graceful restart before forcing one
4. Sends a webhook notification with full context

This gives much richer failure information than a bare `restart: always`.

## Lesson 4: Redis as the Source of Truth for Job State

When a worker crashes mid-job, you need to know exactly where it failed. Storing granular progress in Redis (not just "running/done") lets you resume or retry intelligently:

```python
redis.hset(f"job:{job_id}", mapping={
    "status": "processing",
    "step": "ffmpeg_encode",
    "progress": 67,
    "started_at": timestamp
})
```

## Lesson 5: Shared Volumes Need Clear Ownership

When multiple containers write to the same volume (e.g., the `output/` directory), file permission conflicts are inevitable. Use a single writer pattern — one container writes, others read via API.

---

Building a robust multi-service system is mostly about designing for failure gracefully. Each of these lessons came from a real production outage — the best kind of teacher.
