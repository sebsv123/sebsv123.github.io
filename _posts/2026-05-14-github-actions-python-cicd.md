---
title: 'GitHub Actions CI/CD for Python Projects: My Minimal Setup'
date: 2026-05-14
permalink: /posts/2026/05/github-actions-python-cicd/
tags:
  - GitHub Actions
  - CI/CD
  - Python
  - DevOps
  - Docker
---

After setting up CI/CD for ViraClip and agente_seguros_ai, I've distilled my GitHub Actions setup to the smallest config that still gives me real value.

## What I Actually Need from CI

For a solo developer on a SaaS product, CI doesn't need to be complex. My requirements:

1. Run tests on every push to `main` and on PRs
2. Build and push Docker image on merge to `main`
3. Notify me if anything breaks

That's it. No staging environments, no approval gates — just fast feedback.

## The Workflow

```yaml
# .github/workflows/ci.yml
name: CI

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    services:
      postgres:
        image: postgres:15
        env:
          POSTGRES_PASSWORD: testpass
          POSTGRES_DB: testdb
        options: >-
          --health-cmd pg_isready
          --health-interval 5s
          --health-timeout 5s
          --health-retries 10
      redis:
        image: redis:7
        options: >-
          --health-cmd "redis-cli ping"
          --health-interval 5s

    steps:
      - uses: actions/checkout@v4

      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: '3.11'
          cache: 'pip'

      - name: Install dependencies
        run: pip install -r requirements.txt

      - name: Run Alembic migrations
        run: alembic upgrade head
        env:
          DATABASE_URL: postgresql://postgres:testpass@localhost/testdb

      - name: Run tests
        run: pytest tests/ -v --tb=short
        env:
          DATABASE_URL: postgresql://postgres:testpass@localhost/testdb
          REDIS_URL: redis://localhost:6379
```

## Key Decisions

**Use `services:` for dependencies** — GitHub Actions can spin up Postgres and Redis as sidecar containers. No mocking, no SQLite workarounds — your tests run against the real thing.

**Cache pip dependencies** — `cache: 'pip'` in `setup-python` cuts install time from ~45s to ~5s on repeat runs.

**Run migrations in CI** — This catches broken migrations before they hit production. If `alembic upgrade head` fails, the build fails.

## Docker Build on Merge

```yaml
  build:
    needs: test
    if: github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Build and push Docker image
        uses: docker/build-push-action@v5
        with:
          push: true
          tags: ghcr.io/${{ github.repository }}:latest
```

This only runs after tests pass and only on `main` — safe and simple.
