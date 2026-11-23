# Geospatial Inequality Analytics

Scores every Indonesian province on economic, infrastructure, health and education indicators, ranks them, tracks the
gap over time and shows it on an interactive map.

Built by a six-person team with trunk-based development, CI and a documented API.

## What it does

- **Composite score** per province and year from four weighted dimensions: economic (30%), infrastructure (25%),
  health (25%) and education (20%). Indicators are min-max normalised to 0–100 and "bad" indicators (unemployment,
  poverty, infant mortality) are inverted. The full method is in [docs/scoring-method.md](docs/scoring-method.md).
- **Choropleth map** of the scores (Leaflet), with a detail page per province and its history
- **Rankings**, top and bottom provinces, and a gap analysis between them
- **Year scoring** to recompute and compare years
- **Alerts** when a province crosses a threshold, with acknowledge and resolve
- **Admin** area with login and data import
- **Methodology** page that explains the score to non-technical readers

The bundled data is demonstration data. [docs/data-sources.md](docs/data-sources.md) lists the official sources (BPS
and others) to load instead.

## Architecture

```
data/raw ──► pipelines (pandas) ──► MongoDB ──► FastAPI ──► Next.js dashboard
                                      ▲                         │
                                      └── alerts, scoring tasks  └── Leaflet map, Recharts
```

| Path | Contents |
| --- | --- |
| `backend/` | FastAPI app: routers, services, repositories, pipelines and tasks over MongoDB (Motor) |
| `frontend/` | Next.js App Router: dashboard, regions, province, year scoring, alerts, admin, methodology |
| `data/` | Raw, processed and GeoJSON data |
| `docs/` | [Architecture](docs/architecture.md), [API reference](docs/API-REFERENCE.md), [data dictionary](docs/data-dictionary.md), [scoring method](docs/scoring-method.md) |

## Stack

Python 3.11, FastAPI, Motor (async MongoDB), pandas and NumPy on the backend. Next.js, React, TanStack Query,
React Leaflet, d3-scale, Recharts and Tailwind CSS on the frontend. Docker Compose for local runs, GitHub Actions for
CI.

## Run it locally

With Docker:

```sh
cp .env.example .env
cp backend/.env.example backend/.env
cp frontend/.env.example frontend/.env.local
docker compose up --build
curl http://localhost:8000/health     # API
open http://localhost:3000/dashboard  # dashboard
```

Without Docker:

```sh
cd backend && python -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt
uvicorn app.main:app --reload --port 8000

cd frontend && npm install && npm run dev
```

Branch and commit conventions are in [docs/branching.md](docs/branching.md).
