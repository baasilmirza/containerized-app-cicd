# Project 01 — Containerized Application CI/CD

A Python/FastAPI REST API, packaged with a multi-stage Docker build, validated by GitHub Actions (lint → test → Trivy scan → build), and published to GHCR with semantic versioning.

## Stack

- FastAPI + uvicorn
- pytest + httpx
- ruff (lint)
- Docker (multi-stage, non-root runtime)
- GitHub Actions CI
- Trivy (image scan)
- GHCR (registry)

## Endpoints

- `GET /` — service + version
- `GET /health` — health check
- `GET /items/{item_id}` — sample resource

## Local run

```bash
python -m venv .venv
.venv\Scripts\activate          # Windows
pip install -r requirements.txt -r requirements-dev.txt
uvicorn app.main:app --reload
```

## Tests

```bash
pytest
```

## Build image

```bash
docker build -t portfolio-api .
docker run -p 8000:8000 portfolio-api
```

Multi-stage runtime image is ~258MB vs ~1.7GB for a naive single-stage build (~85% smaller). Runs as non-root `appuser`.

## Security

`Trivy scan` in CI fails on fixable HIGH/CRITICAL findings (`ignore-unfixed`). Base-image OS packages with no upstream fix yet are non-blocking and tracked for resolution.

## CI

On push/PR: lint → test → build → Trivy scan (fails HIGH/CRITICAL).
On `v*` tag: pushes semver-tagged image to GHCR.

## Release

```bash
git tag v0.1.0
git push origin v0.1.0
```

See `INSTRUCTIONS.md` for the full step-by-step guide.
