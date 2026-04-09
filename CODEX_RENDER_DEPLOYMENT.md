# Render Deployment Notes

## Purpose

This file explains how to deploy the full app to Render.

Unlike GitHub Pages, this Render deployment runs the Dockerized backend and frontend together, so routes like `/ai-settings` work.

## Deployment Target

- Repo: `https://github.com/stevenszeles/hedge-fund-tracker`
- Render config file: `render.yaml`
- Service type: Render web service
- Runtime: Docker

## What `render.yaml` Does

The root `render.yaml` defines a single Docker web service:

- service name: `stevenszeles-hedge-fund-tracker`
- branch: `master`
- plan: `free`
- health check: `/health`
- required env var: `DOCKER_ENV=1`

This uses the repo's existing `Dockerfile`, which:

1. builds the React frontend
2. installs Python dependencies
3. serves the app through FastAPI

## Important Limitation

This Blueprint is set to `plan: free` to avoid assuming paid billing.

That means the Render filesystem will be ephemeral unless you later change the service to a paid plan and add a persistent disk in the Render dashboard.

On the free plan:

- the app will run publicly
- `/ai-settings` will work
- optional API keys can be configured in the Render dashboard
- local file changes made by the running app are not guaranteed to persist across redeploys/restarts

## How To Create The Render Service

1. Open the Render dashboard.
2. Click `New` -> `Blueprint`.
3. Connect the repo `stevenszeles/hedge-fund-tracker` if it is not already connected.
4. Select branch `master`.
5. Render should automatically detect the root `render.yaml`.
6. Review the service and click `Deploy Blueprint`.

Official Render docs used for this setup:

- Blueprints: `https://render.com/docs/infrastructure-as-code`
- Blueprint spec: `https://render.com/docs/blueprint-spec`
- Docker services: `https://render.com/docs/docker`
- Web services: `https://render.com/docs/web-services`
- Health checks: `https://render.com/docs/health-checks`

## Optional Environment Variables

You can add these later in the Render dashboard if you want AI and enhanced ticker resolution:

- `FINNHUB_API_KEY`
- `GITHUB_TOKEN`
- `GOOGLE_API_KEY`
- `GROQ_API_KEY`
- `HF_TOKEN`
- `OPENROUTER_API_KEY`

The app's entrypoint will generate `.env` from these environment variables at runtime.

## Verification

After Render deploys the service:

1. Open the Render service URL.
2. Confirm `/health` returns status `healthy`.
3. Confirm `/ai-settings` loads in the browser.

## If Persistence Is Needed Later

If you want updates/reports/cache to survive restarts:

1. Upgrade the service to a paid plan in Render.
2. Add a persistent disk.
3. Decide which application paths should be moved under the mounted disk.

That persistence work is not included in the current free-plan Blueprint.
