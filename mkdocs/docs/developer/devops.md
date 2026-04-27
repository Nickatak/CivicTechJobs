# DevOps Architecture

**Stack:** Docker + Docker Compose, Daphne (ASGI for Django), Whitenoise (Django static files), Node.js (Next.js runtime), PostgreSQL 16.

DevOps files configure the three environments developers interact with — local dev, local stage approximation, and deployed stage. The deployed-stage AWS infrastructure itself is documented separately in [deployment-infra.md](deployment-infra.md).

## Project structure (DevOps-relevant files)

```
.
├── .dockerignore
├── .github/
│   └── workflows/             # CI/CD (see deployment-infra.md)
├── dev/
│   ├── dev.env.example        # Local-dev env-var template
│   ├── django.dockerfile      # Django dev container
│   ├── linter.dockerfile      # Pre-commit linter container
│   ├── linter.env.example     # Linter env-var template
│   └── next.dockerfile        # Next.js dev container
├── stage/
│   ├── django.dockerfile      # Django stage container
│   ├── next.dockerfile        # Next.js stage container
│   └── stage.env.example      # Local-stage env-var template
├── docker-compose.yml         # Local dev environment
└── docker-compose.stage.yml   # Local stage approximation
```

## Local development

Defined by `docker-compose.yml` + the `dev/` directory. Three services:

- **`pgdb`** — PostgreSQL 16, the dev database.
- **`django`** — Django app from `backend/`, built via `dev/django.dockerfile`. Runs migrations on start, then serves on `localhost:8000`.
- **`next`** — Next.js dev server from `frontend/`, built via `dev/next.dockerfile`. Serves on `localhost:3000` with hot reload.

Both `django` and `next` mount their source directories with Docker Compose's `develop.watch` so file changes sync into the containers without rebuilds.

Bring everything up:

```sh
docker compose up --watch
```

`dev.env` (copied from `dev/dev.env.example`) supplies environment variables to each service. The same env file is consumed by all three; service-specific variables are namespaced by prefix (`POSTGRES_*`, `SQL_*`, `NEXT_PUBLIC_*`, etc.).

## Local staging approximation

`docker-compose.stage.yml` runs a stage-shaped environment locally — production-like builds, no hot reload. Used to verify the production build before pushing. Mirrors the deployed-stage two-container shape (see [deployment-infra.md](deployment-infra.md)) so behavior is comparable.

Three services:

- **`pgdb`** — Postgres 16 with stage env vars.
- **`django`** — Django stage container, built from `stage/django.dockerfile` (Poetry install, `collectstatic`, Daphne on port 8000).
- **`next`** — Next.js stage container, built from `stage/next.dockerfile` (`npm run build`, Next.js production server on port 3000). Proxies `/api/*` and `/admin/*` to the `django` service. **[Q7, Q11]**

```sh
docker compose -f docker-compose.stage.yml up
```

`stage.env` (copied from `stage/stage.env.example`) supplies the env vars. **The local stage env is *not* the deployed stage env** — deployed values come from Terraform. See [deployment-infra.md](deployment-infra.md).

## Deployed staging

Lives at https://stage.civictechjobs.org/, built and deployed via [.github/workflows/deploy-stage.yml](https://github.com/hackforla/CivicTechJobs/blob/main/.github/workflows/deploy-stage.yml) on each push to `main`. Two ECR images, one ECS task, same shape as the local stage but with infrastructure values supplied by the Incubator Terraform module. Full details in [deployment-infra.md](deployment-infra.md).

## Linting

Run the pre-commit linters via the linter container:

```sh
docker compose run linter
```

Configuration is in `.pre-commit-config.yaml`. Frontend ESLint / Prettier (`npm run lint` / `npm run format`) and backend Python lint (`isort`/`black`/`flake8`) are invoked as part of pre-commit hooks; see [eslint-guide.md](eslint-guide.md) and [backend.md](backend.md) for stack-specific lint commands. **[Q9, Q10]**

## Useful Docker commands

```sh
docker compose down -v
```

Tear down containers and named volumes — the cleanest reset when the database is in a weird state.

```sh
docker compose run <service> <command>
```

Run a one-shot command in a service container without keeping it up. Common uses:

- `docker compose run django python manage.py makemigrations`
- `docker compose run django python manage.py migrate`
- `docker compose run django python manage.py createsuperuser`
- `docker compose run next npm install <package>`

```sh
docker exec -it <container> sh
```

Open a shell inside a running container for debugging.

```sh
docker compose build --progress=plain
```

Verbose build output — useful when a build step is failing opaquely.

## Resources

- [Docker Compose docs](https://docs.docker.com/compose/)
- [Daphne ASGI server](https://github.com/django/daphne)
- [Whitenoise](https://whitenoise.readthedocs.io/)
- [Next.js Docker docs](https://nextjs.org/docs/app/building-your-application/deploying#docker-image)
