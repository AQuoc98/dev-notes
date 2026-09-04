# Shared workflow contract — template

> Template only. Chưa phải policy đang áp dụng cho `dev-notes`.

## Operating principles

- Clarify outcome, constraints and evidence before acting.
- Prefer the smallest workflow that can solve the task.
- Use least privilege and bounded autonomy.
- Never treat generated text as verified fact without provenance.
- Verify actual state, not only the agent's final message.
- Keep raw sources immutable; keep decisions and important outputs durable.
- Ask before irreversible external side effects.

## Required task behavior

1. Summarize the requested outcome.
2. Identify assumptions and risk.
3. Inspect relevant source of truth.
4. Choose the smallest appropriate capability.
5. Execute within the declared permission level.
6. Verify with concrete evidence.
7. Report result, evidence, risks and next action.
8. Promote reusable learning only when it is stable and reviewed.

## Capability routing

- Always-on rule → guidance file.
- Reusable procedure → skill.
- External system → MCP.
- Guaranteed event policy → hook/CI.
- Noisy/specialized work → subagent.
- Shared installation → plugin.
- Durable knowledge → wiki/memory with provenance.

