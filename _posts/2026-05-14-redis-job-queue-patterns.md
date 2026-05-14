---
title: 'Redis as a Job Queue: Patterns I Use in Production'
date: 2026-05-14
permalink: /posts/2026/05/redis-job-queue-patterns/
tags:
  - Redis
  - Python
  - architecture
  - FastAPI
  - backend
---

After running Redis as a job queue in ViraClip for months, I've settled on a set of patterns that make async video processing reliable and observable.

## Why Redis Over a Dedicated Queue

Tools like Celery + RabbitMQ are powerful but heavy. For a single-product SaaS with predictable load, Redis gives you 90% of what you need with far less operational overhead. The key is being disciplined about your data structures.

## Pattern 1: Hash-Based Job State

Don't store job state as a JSON blob in a string key. Use a Hash so you can update individual fields atomically:

```python
# Set initial state
redis.hset(f"job:{job_id}", mapping={
    "status": "queued",
    "created_at": time.time(),
    "user_id": user_id,
    "type": "video_render"
})

# Update just one field mid-processing
redis.hset(f"job:{job_id}", "status", "processing")
redis.hset(f"job:{job_id}", "progress", 42)
```

## Pattern 2: Sorted Sets for Priority Queues

A plain `LPUSH/RPOP` list queue doesn't support priority. Sorted sets do:

```python
# Enqueue with priority score (lower = higher priority)
redis.zadd("job_queue", {job_id: priority_score})

# Dequeue the highest priority job
job_id = redis.zpopmin("job_queue", count=1)
```

## Pattern 3: Pub/Sub for Real-Time Progress

For live progress bars in the UI, I publish updates to a channel and the frontend subscribes via WebSocket:

```python
# Worker publishes progress
redis.publish(f"job:progress:{job_id}", json.dumps({
    "progress": 67,
    "step": "encoding",
    "eta_seconds": 12
}))
```

## Pattern 4: TTL on Everything

Always set a TTL on job keys. Completed jobs accumulate fast and will eventually fill your Redis memory:

```python
# After job completes, keep state for 24h for UI polling
redis.expire(f"job:{job_id}", 86400)
```

## Pattern 5: Dead Letter Queue

Jobs that fail repeatedly go to a dead letter list so they don't block the main queue:

```python
if job_attempts >= MAX_RETRIES:
    redis.lpush("job_queue:dead", job_id)
    redis.hset(f"job:{job_id}", "status", "failed")
```

These five patterns together give you a production-grade async system with zero external dependencies beyond Redis itself.
