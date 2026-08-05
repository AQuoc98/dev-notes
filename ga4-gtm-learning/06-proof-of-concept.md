# Subtask 06: Build a Proof of Concept

## Objective

Demonstrate that the proposed standards can be implemented end to end for one representative journey and provide a reference implementation for future tracking work.

## Scope

- One audit-prioritized flow containing 2–4 related events.
- Data Layer implementation or a sufficiently realistic mock.
- Required GTM variables, triggers, GA4 event tags, and configuration.
- Non-production testing using the QA checklist.
- No production rollout or full-container refactor.

## Work Items

1. Select the use case and document the POC hypothesis/success measures.
2. Finalize its tracking plan and Data Layer contract.
3. Create a dedicated GTM workspace and configure variables, triggers, and tags according to the guidelines.
4. Implement or coordinate the Data Layer changes in the test environment.
5. Test applicable positive, negative, duplicate, SPA, and consent cases.
6. Verify payloads in network requests and events in GA4 DebugView.
7. Capture evidence, limitations, lessons learned, and rollout recommendations.

## Deliverables / Outputs

- POC implementation in a non-production workspace/environment.
- POC tracking plan and configuration inventory.
- Test report with screenshot/log evidence.
- Before/after comparison where a previous flow exists.
- POC summary covering outcome, limitations, lessons, and rollout recommendation.

## Expected Result

A clean, traceable tracking reference passes QA and proves that the Data Layer contract, variable guidelines, and naming convention work together.

## Acceptance Criteria

- [ ] Use case, success criteria, and boundaries are agreed before implementation.
- [ ] Events fire only on the intended interactions, with no missing or duplicate events in test cases.
- [ ] Event, parameter, and variable names comply with the standards.
- [ ] Required parameters have correct values/types in the Data Layer, network request, and DebugView.
- [ ] No PII is sent; behavior under relevant consent states is tested and recorded.
- [ ] The QA checklist is complete with evidence and reviewer sign-off.
- [ ] The recommendation is explicit: adopt, revise, or reject, with next steps.

## Dependencies

- Stable outputs from the four standards/guideline subtasks.
- Audit findings that provide the use case and baseline.
- GTM workspace, GA4 test property/data stream, and test environment.
- Developer support for Data Layer work where needed and reviewer availability.

## Estimated Effort

**13 hours** — planning 2h, implementation 5h, testing/evidence 4h, summary/review 2h.

## Instructions / Answer

See [06-proof-of-concept-answer.md](./06-proof-of-concept-answer.md).
