---
title: 'Safe Alembic Migrations in Production: A Practical Guide'
date: 2026-05-14
permalink: /posts/2026/05/alembic-migrations-production/
tags:
  - PostgreSQL
  - Alembic
  - Python
  - database
  - DevOps
---

Running database migrations in production is one of those tasks that's routine until it isn't. Here's the approach I use with Alembic to keep migrations safe and reversible.

## The Golden Rules

**1. Never edit a migration that has already been applied to production.**

If a migration is live, create a new one to fix it. Editing applied migrations breaks the revision chain and causes `alembic upgrade head` to fail or apply unexpected changes.

**2. Always test the downgrade.**

Write `downgrade()` properly, not just `pass`. You'll need it the day a deploy goes wrong:

```python
def downgrade():
    op.drop_column('users', 'new_column')  # explicit
    # NOT just: pass
```

**3. Check current revision before deploying.**

```bash
alembic current
alembic history --verbose
```

Always verify the database is at the expected revision before running `upgrade head` in CI/CD.

## The Pattern I Use in Docker

In ViraClip, migrations run as a one-shot step before the API starts:

```yaml
# docker-compose.yaml
backend:
  command: >
    sh -c "alembic upgrade head &&
           uvicorn src.main:app --host 0.0.0.0 --port 8000"
  depends_on:
    postgres:
      condition: service_healthy
```

This ensures the schema is always up to date when the API starts, and the `service_healthy` condition prevents migrations from running before PostgreSQL is ready.

## Dangerous Operations to Avoid

| Operation | Why dangerous | Safe alternative |
|---|---|---|
| `DROP COLUMN` immediately | Old code still references it | Deprecate first, drop in next deploy |
| Rename column in one step | Breaks existing queries | Add new column, migrate data, drop old |
| Add NOT NULL without default | Fails on existing rows | Add with default, then remove default |

## Zero-Downtime Strategy

For tables with millions of rows, use `op.execute()` with `ALTER TABLE ... ADD COLUMN` and a `DEFAULT` to avoid locking:

```python
def upgrade():
    op.execute(
        "ALTER TABLE videos ADD COLUMN processed BOOLEAN DEFAULT FALSE NOT NULL"
    )
```

This is faster than the ORM-generated equivalent and avoids table locks on PostgreSQL 11+.
