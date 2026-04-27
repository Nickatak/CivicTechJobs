# Development Culture

The CivicTechJobs developer workflow has three core activities:

- Make issues
- Resolve issues
- Review code

This guide covers expectations and conventions for each. For mechanics (forking, branching, opening PRs), see [CONTRIBUTING.md](https://github.com/hackforla/CivicTechJobs/blob/main/CONTRIBUTING.md).

## Make issues

Project work starts with an issue. A good issue describes the situation, the desired outcome, and any context another developer would need to pick it up cold.

When writing an issue:

- Open with a 1–2 sentence overview that includes *why* the change is needed.
- Use plain language; avoid jargon in the overview.
- Action items should be concrete — step-by-step instructions or a list of acceptance criteria.
- Put longer context, references, or design links in an "Instructions / Resources" section below the action items.
- If the issue depends on another issue, link it explicitly. If the dependency doesn't exist yet, file it.

Once the issue is filed, add labels. At minimum, every issue should have one label each from the **size**, **role**, and **feature / p-feature** series. Don't invent new labels — if existing options don't fit, leave the labeling open and a team member will add what's needed during triage.

Issues are tracked on the [Hack for LA project board](https://github.com/orgs/hackforla/projects/37). New issues land in "New Issue Approval" and move through prioritization columns as the team triages them. A maintainer or PM may comment with questions or revisions before approval — read those carefully and update the issue or reply.

## Resolve issues

When picking work from the "Prioritized Backlog" column, the **size** and **role** labels are the main signals: size is roughly the time commitment (small ≈ a week, large ≈ two to three weeks), role is which expertise the issue is aimed at. New contributors are encouraged to start with smaller issues to calibrate.

Mechanics:

1. Comment on the issue to claim it; assign it to yourself.
2. Branch off `develop` (see [git-branch-structure.md](git-branch-structure.md)).
3. Open a PR back into `develop` when the work is ready.

If an issue is taking significantly longer than its size suggests, post an update on the issue with an ETA and an honest read on whether you can finish. It's better to release an issue back to the backlog than to block on a stalled task.

**Don't ping for review until 72 hours after opening a PR.** The team sweeps PRs on its own cadence; pinging earlier creates noise. If you're done with one task and waiting for review, pick up another issue or review someone else's PR.

### Frontend vs backend issues

Most work falls into two broad categories. Under the Next.js + Django stack, the boundary is fuzzier than it used to be (server components and server actions live on the frontend side but talk directly to the database via the API), so the labels are a hint rather than a hard divide.

**Frontend** — work in `frontend/` (Next.js app, components, server actions). Usually paired with a Figma reference. Visual or interaction-heavy.

**Backend** — work in `backend/` (Django models, API endpoints, permissions, PeopleDepot integration, matching). Usually involves data flow, schema, or auth concerns and benefits from upfront discussion in the issue.

## Review code

PRs are reviewed against three criteria:

- **Correctness.** Does the change resolve the stated issue? Are there extraneous changes?
- **Soundness.** Does it break existing functionality? Are responsive / accessibility behaviors preserved?
- **Maintainability.** Is the code reasonable to read and extend? Are abstractions appropriate?

A PR can merge with one approving review. For larger or higher-risk changes, request additional reviewers or raise the PR in a developer meeting. **Avoid merging without review** — even small fixes benefit from a second pair of eyes, and accidental direct merges to `develop` or `main` are painful to undo.

See the [GitHub documentation on reviewing pull requests](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/reviewing-changes-in-pull-requests/about-pull-request-reviews) for mechanics.
