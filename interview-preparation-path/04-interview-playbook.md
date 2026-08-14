# Interview Playbook

## Live coding

Use the same loop every time:

1. Restate the problem and clarify inputs, outputs, constraints, and examples.
2. Describe a simple correct approach before optimizing.
3. Name the data structure and expected time/space complexity.
4. Implement in small executable steps while narrating intent.
5. Test normal, boundary, invalid, and failure cases.
6. Refactor only after correctness; summarize trade-offs.

High-yield exercises include debounce/throttle, event emitter, promise pool,
tree traversal and filtering, autocomplete, nested comments/file explorer,
normalized state updates, accessible modal/tabs, and data fetching with race and
error handling.

## Frontend system design

Drive a 45-minute conversation through this sequence:

1. **Requirements:** users, core journeys, scale, devices, accessibility, SEO,
   freshness, security, and constraints.
2. **Contracts:** data entities, APIs/events, error semantics, and ownership.
3. **Architecture:** rendering mode, component/module boundaries, state and data
   flow, routing, and delivery.
4. **Quality attributes:** performance, reliability, security, accessibility,
   testing, observability, and internationalization.
5. **Evolution:** team boundaries, rollout/migration, experimentation, and cost.
6. **Trade-offs:** state assumptions explicitly and identify what measurements
   could reverse a decision.

Practice prompts: analytics dashboard, commerce catalog, collaborative editor,
news feed, video homepage, component library, and a multi-team admin platform.

## Technical deep dive

Structure answers as: definition → mechanism → practical consequence → trade-off
→ example from experience. If unsure, state the boundary of certainty and
describe how it would be verified.

For every major topic, prepare:

- a two-minute explanation;
- a concrete debugging or delivery example;
- one common misconception;
- one case where the technique should not be used.

## Behavioral story bank

Prepare eight stories that can be adapted across prompts:

1. Led an ambiguous project.
2. Made or reversed an architectural decision.
3. Improved performance or reliability with measurements.
4. Disagreed with a teammate or stakeholder constructively.
5. Mentored someone or raised team capability.
6. Handled a production incident or serious defect.
7. Improved a process, test strategy, or delivery workflow.
8. Failed, learned, and changed subsequent behavior.

Use **Situation (10%) → Task (10%) → Action (60%) → Result/Reflection (20%)**.
Separate personal contribution from the team's work and quantify the result when
possible. Senior signals include managing ambiguity, making trade-offs, aligning
people, reducing future risk, and multiplying others—not merely completing a
difficult ticket.

## Mock cadence

- Weeks 1–4: one recorded technical explanation each week; one live mock at W4.
- Weeks 5–8: one system-design drill every two weeks; one live mock at W8.
- Weeks 9–12: one coding and one design/behavioral session weekly; full loop W12.
- Weeks 13–16: at least two timed interview sessions weekly and full loops in
  Weeks 15 and 16.

After each mock, log only observable behavior: what happened, likely root cause,
the smallest corrective drill, and the date it will be retested.

