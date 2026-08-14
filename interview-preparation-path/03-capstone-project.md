# Capstone Project

Build one interview-sized, production-shaped application rather than several
tutorial clones. A good default is a **collaborative issue or project tracker**:
it has public and authenticated routes, read-heavy and mutation-heavy flows,
permissions, search/filtering, optimistic interactions, and useful performance
trade-offs.

## Required scope

- Next.js App Router with deliberate Server and Client Component boundaries.
- Typed domain model and validation at trust boundaries.
- Authentication and at least two authorization roles.
- List/detail/create/update flows with loading, empty, error, and recovery states.
- Search, filtering, pagination or infinite loading, and URL-addressable state.
- Accessible keyboard interaction and responsive layouts.
- One optimistic mutation with rollback/error behavior.
- Unit/component tests plus two critical E2E paths.
- Error reporting, structured logs, and a small performance budget.
- A deployable README, architecture diagram, and two ADRs.

## Milestones

| Week | Milestone |
| --- | --- |
| 1–2 | Problem statement, user journeys, domain types, and repository skeleton |
| 3–4 | UI states, baseline accessibility, tests, and profiling setup |
| 5–6 | State architecture, primary vertical slice, and performance pass |
| 7–8 | App Router data/mutation flows, caching matrix, streaming/error boundaries |
| 9–10 | Architecture ADRs, authentication/authorization, and threat model |
| 11–12 | Web Vitals pass, test strategy, E2E paths, and CI checks |
| 13–14 | Polish, architecture diagram, demo script, and project walkthrough |
| 15–16 | Fix mock-interview feedback; freeze features and stabilize the demo |

## Evidence package

The project is useful only if the candidate can explain it. Keep these artifacts:

- one-page architecture overview;
- rendering and caching decision table per route;
- state ownership map;
- threat model with trust boundaries;
- performance report with baseline, change, and result;
- test strategy tied to product risk;
- ADRs that include context, options, decision, and consequences;
- a 3-minute product demo and a 10-minute engineering walkthrough.

## Scope control

Prefer a finished vertical slice over feature count. Use external services where
backend implementation is not the learning goal. Do not spend core roadmap time
on branding, elaborate animation, or infrastructure that cannot be discussed in
a frontend interview.

