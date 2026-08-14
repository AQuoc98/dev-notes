# 16-Week Roadmap

Each week has one outcome that can be demonstrated. A week is complete when its
exit criterion is met, not when every resource has been consumed.

## Phase 1 — Core depth (Weeks 1–4)

| Week | Focus | Practical deliverable | Exit criterion |
| --- | --- | --- | --- |
| 1 | Event loop, execution contexts, closures, prototypes, async control flow | Event-loop prediction notes; typed debounce, throttle, and event emitter with tests | Explain ordering and edge cases without memorized slogans; implementations pass cancellation/context tests |
| 2 | TypeScript generics, narrowing, conditional/mapped types, discriminated unions | Type-safe async state model and polymorphic component API | No unsafe casts in the core API; explain inference and maintainability trade-offs |
| 3 | Browser rendering, network lifecycle, memory, GC, profiling, DOM events | Diagnose one CPU, memory, or rendering issue in a small demo and write a before/after report | Identify the bottleneck with tooling and support the fix with measurements |
| 4 | React render/commit, reconciliation, identity, state preservation, batching, effects | Build and test a component that exposes stale closure, key, and effect-lifecycle bugs, then fix them | Explain each bug from React's model and pass the first recalibration mock |

## Phase 2 — React and Next.js architecture (Weeks 5–8)

| Week | Focus | Practical deliverable | Exit criterion |
| --- | --- | --- | --- |
| 5 | State ownership, reducers, Context, external stores, server state, custom hooks | State decision record plus one capstone vertical slice | Justify state location and library choice by update frequency, ownership, and consistency needs |
| 6 | Profiling, memoization, virtualization, code splitting, Suspense, accessibility | Profile and optimize a deliberately slow screen; document rejected optimizations | Demonstrate an actual improvement without breaking accessibility or correctness |
| 7 | App Router, layouts, Server/Client Components, data flow, route handlers, Server Actions | Capstone shell with one read flow and one mutation across a clear server/client boundary | Explain serialized props, mutation lifecycle, error handling, and progressive enhancement |
| 8 | Static/dynamic rendering, revalidation, cache layers, streaming, middleware | Rendering/caching matrix for capstone routes plus a streaming loading experience | Predict freshness behavior and state how it will be verified; pass the second recalibration mock |

## Phase 3 — Senior system concerns (Weeks 9–12)

| Week | Focus | Practical deliverable | Exit criterion |
| --- | --- | --- | --- |
| 9 | Frontend system design method, design systems, monorepos, micro-frontends | 45-minute design for a multi-team commerce frontend and an ADR for modularity | Cover requirements, boundaries, data/state, delivery, failure modes, observability, and trade-offs |
| 10 | OAuth/OIDC concepts, PKCE, sessions/cookies, XSS, CSRF, CORS, CSP | Threat model and secure authentication flow for the capstone | Distinguish browser security controls from server authorization and propose layered mitigations |
| 11 | Core Web Vitals, image/font/script loading, CDN and HTTP caching, service workers | Performance budget and before/after lab report | Tie LCP, INP, and CLS findings to measured causes and user/business impact |
| 12 | Testing strategy: unit, component, integration, E2E, contracts, CI | Risk-based test matrix; implement critical component/integration/E2E paths | Explain why each test belongs at its level; pass a full mock loop and begin targeted applications |

## Phase 4 — Interview conversion (Weeks 13–16)

| Week | Focus | Practical deliverable | Exit criterion |
| --- | --- | --- | --- |
| 13 | Practical DS&A: arrays/maps, stacks/queues, trees/graphs, recursion, complexity | Eight timed problems including tree UI, autocomplete, interval, and cache-flavored tasks | Solve two medium practical problems in 45 minutes while narrating and testing |
| 14 | Senior behavioral signals and project communication | Eight STAR stories and a 10-minute capstone walkthrough | Stories include personal action, measurable result, reflection, and a credible leadership signal |
| 15 | Mixed mock interviews and targeted repair | Two full mock loops; error log grouped by root cause | No repeated critical mistake across consecutive mocks; weak areas have focused drills |
| 16 | Final simulation, applications, negotiation preparation | One final loop, resume/project evidence map, application pipeline, question bank | Meets readiness gates in the tracker and has active interview conversations |

## Topic boundaries

### Must be fluent

- JavaScript event loop, closures, object model, async failures, browser lifecycle.
- TypeScript modeling, generics, narrowing, and API design.
- React rendering, effects, state ownership, performance diagnosis, accessibility.
- Next.js server/client boundaries, rendering, mutation, caching, and failure states.
- Frontend system design, web performance, security, and testing strategy.
- Clear coding narration, trade-off analysis, and behavioral evidence.

### Learn to decision-making depth

- Micro-frontends, monorepo tooling, state libraries, service workers, advanced
  TypeScript machinery, and React internals.

For these topics, a senior candidate should recognize when they apply, compare
options, identify operational costs, and avoid overengineering. Reimplementing
framework internals is optional enrichment, not a core milestone.

