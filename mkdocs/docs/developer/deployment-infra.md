# Deployment & Infrastructure

CivicTechJobs is deployed on Hack for LA's Incubator AWS account (035866691871, `us-west-2`), managed by the DevOps Community of Practice. The shared infrastructure (ECS Fargate cluster, RDS, Route53) is provisioned via Terraform in the [hackforla/incubator](https://github.com/hackforla/incubator/tree/main/terraform/projects/civic-tech-jobs) repository.

![CTJ Infrastructure Diagram](../assets/ctj-infra-diagram.png)

## Topology

CTJ runs as two containers in a shared ECS task on the `incubator-prod` Fargate cluster:

- **Next.js container** — serves the frontend (App Router pages, server components, server actions). Owns the request boundary; the browser only talks to this container.
- **Django container** — serves the CTJ API (`/api/*`) and the Django admin (`/admin/*`). The Next.js container forwards API and admin traffic to it over the task's internal network.

Both containers share the task's network namespace so the Next.js → Django call hops localhost.

External dependencies referenced at request time:

- **AWS Cognito** — JWT-based auth (the same user pool that backs PeopleDepot).
- **PeopleDepot API** — reference data (user identity, practice areas, roles, project metadata). See [backend.md](backend.md) for the integration shape.

## AWS resources

| Resource | Where |
|----------|-------|
| ECS Fargate cluster | `incubator-prod` (shared with other Incubator projects) |
| ECS service + task definition | CTJ-owned, defined in Incubator Terraform |
| ECR repositories | One image per container (`civic-tech-jobs-frontend`, `civic-tech-jobs-backend`) |
| RDS PostgreSQL 16 | `incubator-prod-database` (shared instance, CTJ-owned database) |
| Route53 zone | `civictechjobs.org` |
| IAM role | `incubator-cicd-civic-tech-jobs` (assumed via OIDC by the deploy workflow) |

## Deployment workflow

The deploy workflow at [.github/workflows/deploy-stage.yml](https://github.com/hackforla/CivicTechJobs/blob/main/.github/workflows/deploy-stage.yml) runs on every push to `main` (excluding `mkdocs/**` and `dev/**` paths):

1. Assumes the `incubator-cicd-civic-tech-jobs` IAM role via GitHub OIDC — no static AWS credentials live in this repo.
2. Logs into Amazon ECR.
3. Builds both container images (frontend and backend) and pushes them to their respective ECR repositories with the `stage` tag.
4. Forces a redeployment of the ECS service so the new task spec pulls both fresh images.

There is no separate production workflow yet — `stage` is the only deployed environment.

## Environment variables

Stage environment variables live in Terraform alongside the rest of the Incubator-managed configuration: see `environment-stage.tf` in [hackforla/incubator/terraform/projects/civic-tech-jobs](https://github.com/hackforla/incubator/tree/main/terraform/projects/civic-tech-jobs).

Adding or editing an environment variable for the deployed app means changing the Terraform module — not editing anything in this repo. The local-dev equivalents in [dev/dev.env.example](https://github.com/hackforla/CivicTechJobs/blob/main/dev/dev.env.example) are kept loosely in sync but are not the source of truth for stage.

Variables of note for the Next.js + Django split:

- **Cognito** — pool ID, region, public-key URL (consumed by both containers).
- **PeopleDepot API** — base URL, auth credentials (consumed by both containers).
- **Postgres** — `SQL_HOST`, `SQL_DATABASE`, `SQL_USER`, `SQL_PASSWORD`, `SQL_PORT` (Django container only).
- **Next.js runtime** — `NEXT_PUBLIC_*` for browser-exposed values (e.g., Cognito client ID), private values for server actions.

## DNS

The stage environment is reachable at https://stage.civictechjobs.org/. The zone (`civictechjobs.org`) and records are managed in Route53 via Terraform in the Incubator repository.

## Local equivalents

The local stage approximation lives in [docker-compose.stage.yml](https://github.com/hackforla/CivicTechJobs/blob/main/docker-compose.stage.yml) and the per-container Dockerfiles under `stage/`. See the [DevOps Architecture](devops.md) doc for the local stage build process.

## Additional Resources

- [Incubator Terraform module for CTJ](https://github.com/hackforla/incubator/tree/main/terraform/projects/civic-tech-jobs)
- [AWS ECS Fargate documentation](https://docs.aws.amazon.com/AmazonECS/latest/userguide/what-is-fargate.html)
