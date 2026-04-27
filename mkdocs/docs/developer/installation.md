# Installation Instructions

Steps for setting up a local development environment.

If you get stuck, ask in the [`#civictechjobs-dev` Slack channel](https://hackforla.slack.com/archives/C02509WHFQQ) or email <Civictechjobs@hackforla.org>. Pair-programming with an existing contributor is also an option.

## Required downloads

- **Git** — [Windows](https://git-scm.com/download/win) · [macOS](https://git-scm.com/download/mac) · [Linux](https://git-scm.com/download/linux)
- **Docker Desktop** (or Docker Engine on Linux) — [Windows](https://docs.docker.com/desktop/windows/install/) · [macOS](https://docs.docker.com/desktop/mac/install/) · [Linux](https://docs.docker.com/engine/install/)
- **Prettier — VS Code extension** — [marketplace](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode) (recommended for format-on-save)

For working outside Docker (rarely needed), also install:

- **Node.js 22 LTS** + **npm** (Next.js 15 minimum)
- **Python 3.12** + **Poetry**

<details>
<summary>Note for macOS</summary>
The macOS Git installer pulls in Homebrew, which can occupy several GB of disk space. If that's a problem, install <a href="https://www.datacamp.com/community/tutorials/homebrew-install-use">a minimal Homebrew via Xcode Command Line Tools</a>. Be aware Docker Desktop and the project's container images will also use a few GB — plan disk space accordingly.
</details>

## Repository setup

1. [Fork the repository](https://docs.github.com/en/get-started/quickstart/fork-a-repo#forking-a-repository) to your own GitHub account.
2. [Clone your fork locally](https://docs.github.com/en/get-started/quickstart/fork-a-repo#cloning-your-forked-repository).
3. [Add the upstream remote](https://docs.github.com/en/get-started/quickstart/fork-a-repo#configuring-git-to-sync-your-fork-with-the-original-repository) so you can pull updates from `hackforla/CivicTechJobs`.

Develop branches off `develop`, not `main`:

```sh
cd CivicTechJobs
git remote add upstream https://github.com/hackforla/CivicTechJobs.git
git fetch upstream develop
git checkout -b develop upstream/develop
```

See [git-branch-structure.md](git-branch-structure.md) for the branching model.

## Running the app locally

1. From the repo root, copy the dev env template:

    ```sh
    cp dev/dev.env.example dev/dev.env
    ```

2. Edit `dev/dev.env` and fill in the placeholders (database name, secret key, etc.). The lines marked `<...>` need real values; the rest can stay as defaults.

3. Install frontend dependencies (one time, used by the Next.js container build):

    ```sh
    cd frontend
    npm install
    cd ..
    ```

4. Start the full stack:

    ```sh
    docker compose up --watch
    ```

5. Open the app:
    - **Frontend** — http://localhost:3000
    - **Backend (Django admin + API)** — http://localhost:8000

`docker compose up --watch` enables hot reload — file edits in `frontend/` or `backend/` sync into the running containers automatically.

## dev.env values

| Variable | What to put |
|----------|-------------|
| `POSTGRES_DB` | Any name (`postgres` works) |
| `POSTGRES_USER` | Any username |
| `POSTGRES_PASSWORD` | Any password |
| `SECRET_KEY` | Random string of length 50; `python -c "import secrets; print(secrets.token_urlsafe(50))"` works |
| `SQL_DATABASE` | Same as `POSTGRES_DB` |
| `SQL_USER` | Same as `POSTGRES_USER` |
| `SQL_PASSWORD` | Same as `POSTGRES_PASSWORD` |

For Cognito and PeopleDepot variables (needed for auth + reference-data integration), see the relevant section in [deployment-infra.md](deployment-infra.md). **[Q4, Q23, Q24]** Local-dev contributors will hit unresolved questions about (a) how to authenticate against Django without a real Cognito flow, (b) how to call PeopleDepot without a dev account, and (c) how to bootstrap an admin / PM user. These need to resolve before the install flow is fully runnable end-to-end.

## Troubleshooting

**`The command 'docker' could not be found`** — start Docker Desktop (or `sudo systemctl start docker` on Linux).

**`unknown flag: --watch`** — your Docker Compose is older than 2.22.0, which introduced the watch feature. Upgrade Docker Desktop.

**`Cannot find module '<package>'`** — npm install failed inside the container. Run it manually: `docker compose run next npm install`.

**`code ERR_SOCKET_TIMEOUT` during `npm install`** — npm is overloading sockets. Edit `dev/next.dockerfile`, change `RUN npm install` to `RUN npm install --maxsockets=1`, rebuild, and revert the change once done.

**Port conflicts (3000 or 8000 already in use)** — kill the conflicting process, or override the port in `docker-compose.yml` / your local fork.

## Additional Resources

- [Git documentation](https://git-scm.com/doc)
- [Docker documentation](https://docs.docker.com/)
- [Quickstart Guide](quickstart-guide.md)
- [Backend Architecture](backend.md)
- [DevOps Architecture](devops.md)
