# Subagent template

```yaml
name: focused-worker
scope: codex | claude | portable-spec
role: ""
model_policy: fast | balanced | strong | host-default
permission: read-only | workspace-write | external-read | external-write
max_turns: 0
```

## Mission

Một job hẹp, có output rõ; không ôm toàn bộ mục tiêu của parent.

## Context it receives

- Files/sources cần đọc.
- Shared contract/skill cần preload.
- What it must not assume.

## Allowed tools

## Forbidden actions

## Work method

## Stop conditions

## Handoff/output

- Summary.
- Evidence with paths/links/IDs.
- Findings ranked by confidence/impact.
- Unresolved questions.

