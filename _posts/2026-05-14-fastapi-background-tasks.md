---
title: 'FastAPI Background Tasks vs Celery: When to Use Each'
date: 2026-05-14
permalink: /posts/2026/05/fastapi-background-tasks-vs-celery/
tags:
  - FastAPI
  - Python
  - Celery
  - backend
  - architecture
---

FastAPI ships with a built-in `BackgroundTasks` system. It's tempting to use it for everything async — but there's a clear line where you should switch to a proper task queue like Celery or Redis workers.

## FastAPI BackgroundTasks: What It Is

`BackgroundTasks` runs a function *in the same process* after the HTTP response is sent. It shares memory with the web server and runs in the same event loop.

```python
@app.post("/send-email")
async def send_email(background_tasks: BackgroundTasks, email: str):
    background_tasks.add_task(send_notification, email)
    return {"status": "queued"}
```

## When BackgroundTasks Is Fine

- Sending a welcome email after registration
- Logging an event to a database
- Invalidating a cache entry
- Sending a webhook notification

Anything that's **fast, stateless, and non-critical**. If it fails, the user doesn't need to know.

## When You Need a Real Queue

| Scenario | Use |
|---|---|
| Task takes > 5 seconds | Redis/Celery worker |
| Task uses lots of CPU/RAM | Separate worker process |
| Task needs retry logic | Celery with exponential backoff |
| User needs progress tracking | Redis + WebSocket |
| Task must survive server restart | Persistent queue |

For ViraClip, video rendering takes 30–300 seconds and uses all available CPU. Running that inside FastAPI would block every other request. It lives in a dedicated worker container.

## The Pattern I Use

1. HTTP request comes in → validate input → create job record in PostgreSQL
2. Push job ID to Redis queue
3. Return `202 Accepted` with job ID immediately
4. Separate worker container picks up job, processes it, updates Redis state
5. Frontend polls `/jobs/{id}/status` or subscribes to WebSocket

This keeps your API server snappy and your workers scalable independently.
