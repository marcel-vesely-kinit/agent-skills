---
name: fastapi-app-scaffolding
description: Use when starting, scaffolding, or restructuring a Python FastAPI backend that needs a database-backed foundation.
---

# FastAPI Application Scaffolding

Use this scaffold as a small, consistent starting point for Python FastAPI services. The default profile is **UV + FastAPI + asynchronous SQLAlchemy + PostgreSQL**. Keep HTTP handling, persistence models, and business logic in distinct places; add structure when the application needs it.

## Default project structure

```text
.
├── README.md
├── .env.example
├── .gitignore
├── .python-version          # 3.12 unless specified otherwise
├── docker-compose.postgres.yml
├── pyproject.toml
├── uv.lock
├── run.py
├── app/
│   ├── __init__.py
│   ├── main.py
│   ├── config.py
│   ├── models/
│   │   └── __init__.py
│   ├── routers/
│   │   └── __init__.py
│   └── services/
│       ├── __init__.py
│       ├── database.py
│       └── logging.py
└── tests/
    └── conftest.py
```

Create feature-specific modules under these folders as the application grows; do not populate the scaffold with empty example modules.

## Project setup

- Use UV to initialize and manage the project. Keep `pyproject.toml` as the dependency declaration and commit `uv.lock` for reproducible installs.
- Set Python 3.12 in `.python-version` unless the project or deployment environment specifies another version.
- Add packages with `uv add <package>` without a version in the command. For example:

  ```bash
  uv add fastapi uvicorn pydantic-settings 'sqlalchemy[asyncio]' asyncpg
  uv add --dev ruff ty pytest
  ```

  This lets UV resolve compatible releases, add version constraints to `pyproject.toml`, and record exact resolved versions in `uv.lock`. Specify a version constraint only when the project has a compatibility requirement.
- Include a root `README.md` with environment setup, local database startup, and app run instructions.
- `.env.example` documents settings using placeholders; keep real `.env` values out of version control. Configure `.gitignore` to exclude local environments, secrets, caches, and local database files while retaining `.env.example`.
- Use `docker-compose.postgres.yml` for a local PostgreSQL service. Keep its database name, user, password, host, and port aligned with application settings.

## Application boundaries

- `app/main.py` creates the FastAPI app, includes routers, configures required middleware, and owns lifespan startup and shutdown.
- `app/config.py` defines typed settings loaded from environment variables, including database connection settings.
- `app/models/` contains database entities.
- `app/routers/` contains HTTP routes and request/response handling. Keep business rules out of route handlers.
- `app/services/` contains business logic and infrastructure integrations. `database.py` owns the async engine, session factory, FastAPI session dependency, and engine disposal at shutdown. `logging.py` configures application logging.
- `run.py` provides a convenient local Uvicorn entry point for `app.main:app`.
- `tests/` is part of the initial structure. Put shared fixtures in `conftest.py`. When using `@pytest.mark.mock`, register the marker under `[tool.pytest.ini_options].markers` in `pyproject.toml`.

The scaffold does not include AI agents, model-provider clients, observability vendors, or a frontend. Add those only when the application requires them. Add separate schemas or other modules when they make a real boundary clearer.
