# Subtask 04: Create a Debugging Workflow and Analytics QA Checklist

## Objective

Create a repeatable process that validates tracking from user interaction to GA4 and catches missing/duplicate events, invalid parameters, consent issues, and regressions before publication.

## Scope

- Data Layer, GTM Preview/Tag Assistant, browser network, and GA4 DebugView checks.
- Positive, negative, boundary, duplicate, and SPA test cases.
- Consent/privacy checks and cross-browser sanity testing.
- Pre-publish/post-publish checklists and evidence standards.
- No full automated analytics test suite.

## Work Items

1. Define a debugging workflow for each layer of the tracking pipeline.
2. Create a test-case template with expected/actual evidence.
3. Build checks for tags, triggers, variables, payloads, consent, and GA4.
4. Define defect severity and reporting rules.
5. Trial the checklist on at least one sample-flow event.
6. Refine the checklist based on defects and ambiguities found.

## Deliverables / Outputs

- Step-by-step debugging playbook.
- Pre-publish and post-publish analytics QA checklists.
- Test-case and evidence template.
- Defect severity guide and sample test report.

## Expected Result

A developer or QA engineer can independently validate tracking, provide consistent evidence, and prevent common analytics defects from reaching production.

## Acceptance Criteria

- [ ] Checks cover Data Layer → GTM → network request → GA4 DebugView.
- [ ] Tests cover missing/duplicate events, invalid types/values, SPA navigation, and consent state.
- [ ] Required evidence and expected results are stated for each check group.
- [ ] Pass/fail and defect severity rules are documented.
- [ ] The checklist has been trialed and its result is reproducible by a reviewer.

## Dependencies

- Test environment and browser developer tools.
- GTM Preview/Tag Assistant and GA4 DebugView access.
- Draft Data Layer specification, naming convention, and sample flow.

## Estimated Effort

**8 hours** — workflow 2h, checklist/template 3h, trial run 2h, refinement 1h.

## Instructions / Answer

See [04-debugging-analytics-qa-answer.md](./04-debugging-analytics-qa-answer.md).

