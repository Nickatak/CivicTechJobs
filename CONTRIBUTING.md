# Contributing to CivicTechJobs

CivicTechJobs is a [Hack for LA](https://www.hackforla.org/) project. Before contributing, please read the Hack for LA [code of conduct](https://github.com/hackforla/codeofconduct).

The maintainer team reviews issues and pull requests at our regular checkpoint meetings — that's the cadence to expect for triage and review. You don't need to ping; open work gets picked up on that schedule.

This guide covers the mechanics. For the *why* behind issue style and review criteria, see the [development culture guide](docs/developer/development-culture.md).

## Setting up your environment

Follow the [installation instructions](docs/developer/installation.md) before starting work. The dev environment runs via `docker compose`.

## Filing issues

Open an issue from the [new issue screen](https://github.com/hackforla/CivicTechJobs/issues/new/choose) using the appropriate template (the [Blank Issue](https://github.com/hackforla/CivicTechJobs/issues/new?assignees=&labels=&template=blank-issue.md&title=) form is fine for most things).

A good issue describes the situation, the desired outcome, and enough context that someone else could pick it up cold. Add at least one **size**, **role**, and **feature** label — leave labeling open if nothing fits and a maintainer will fill it in during triage.

New issues land on the [Hack for LA project board](https://github.com/orgs/hackforla/projects/37) and get prioritized there.

## Submitting a pull request

1. Find an issue in the **Prioritized Backlog** column on the [project board](https://github.com/orgs/hackforla/projects/37). The **size** label is the rough time commitment; the **role** label says where the work lives.
2. Comment on the issue to claim it; assign yourself.
3. Fork the repo, then branch off `develop` (not `main`). See [git-branch-structure.md](docs/developer/git-branch-structure.md).
4. Make your changes.
5. Open a PR against `develop` from your fork, following the [PR template](https://github.com/hackforla/CivicTechJobs/blob/main/.github/pull_request_template.md).
6. Confirm the automated checks pass (bottom of the PR view).

Frontend styling uses **CSS Modules** — co-locate a `Component.module.css` next to each component's `.tsx` file. No Tailwind, no styled-components, no CSS-in-JS runtime. See the [design system guide](docs/developer/design-system.md) for the full conventions.

If an issue takes much longer than its size suggested, post an update on the issue with an honest read on whether you can finish — releasing it back to the backlog is fine.

First time contributing to open source? This [video series](https://app.egghead.io/playlists/how-to-contribute-to-an-open-source-project-on-github) is a useful starter.

## Additional resources

[Resources for developers](docs/resources.md#for-developers).
