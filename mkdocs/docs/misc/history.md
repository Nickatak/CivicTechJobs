# History

## How HfLA volunteer onboarding evolved

In the pre-COVID era, Hack for LA practiced in-person onboarding nights. Each volunteer felt valued and had agency in finding the right team, but the process didn't scale.

From 2020 to mid-2021, HfLA shifted to listing open roles on its main site. Volunteers either contacted teams directly (which dumped onboarding work onto each project lead, leading to inconsistent processes and burnout) or attended a weekly Zoom onboarding (where domain experts often weren't present to claim new volunteers in real time).

From mid-2021 onward, HfLA moved to a Communities of Practice (CoP) model: new volunteers attend onboarding, then join a CoP, and projects post opportunities to the CoP boards. This puts qualified candidates in front of project leads, lets CoP leads coach unsure candidates, and surfaces signals about whether projects are ready to take on volunteers.

CivicTechJobs was greenlit as a project to give that opportunity-matching its own dedicated interface — a job-board page where volunteers can find positions matching their skills and interests, while still flowing through CoP review.

## Technical lineage

CTJ has had three technical leads, each serving roughly 8 months:

- **Ava Li** (Oct 2021 – June 2023, ~20 months) — built the project from scratch: initial React frontend, first Django backend, Docker setup, AWS ECS deployment, GitHub Actions CI/CD, and the original component library. Moved on to a professional role mid-2023.
- **Jimmy Juarez** (Jan 2024 – Sept 2024, ~8 months) — first senior-level engineer on the project. Decided to scrap and rebuild the original Django backend, producing the current data model (UUID PKs, SkillMatrix concept, permission classes, PeopleDepot integration hooks). The `ava-main-v1` branch preserves the pre-reset code.
- **Terence Lo** (Aug 2024 – April 2025, ~8 months) — focused on dev environment work (Vite HMR, Docker compose improvements) and PR review. Began the Jest → Vitest migration.

Between leads there have been several-month gaps; the project has been largely inactive since August 2025.

## The rewrite proposal era

In April 2026 a volunteer surfaced an engineering assessment proposing to keep the (well-designed) Django backend, replace the Vite frontend with a Next.js 15 application, and integrate CTJ as a thin client of HfLA's actively-maintained PeopleDepot system for reference data. The current state of the docs (this directory) reflects the proposed shape under that direction.
