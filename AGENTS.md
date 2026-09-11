# Codex project instructions

For complex coding tasks, use the `astra-orchestrator` skill when its trigger conditions match.

The root agent owns architecture, decomposition, integration, and final verification.
Prefer specialized subagents for bounded exploration, implementation, testing, review, and technical research.

Do not delegate trivial work merely for parallelism.
Do not let multiple implementation agents edit the same files without explicit ownership boundaries.
User instructions always take precedence over this orchestration policy.

## Tech Interview Q&A skill

Use the repository skill at
[`.agents/skills/tech-interview-qa/SKILL.md`](.agents/skills/tech-interview-qa/SKILL.md)
for technical interview Q&A updates based on an article/YouTube URL or
user-provided questions.

- Load the skill when the task matches, or when the user invokes `$tech-interview-qa`.
- Use one concrete Markdown target from `Tài liệu đích`/`TARGET_FILE`; default to [`interview.md`](interview.md).
- Keep Phase 1 read-only: read the entire target, classify `Mới`/`Liên quan`/`Trùng lặp`, and present a proposal.
- Modify only the target file after clear approval such as `APPROVE`; if the target changes, repeat the duplication check.
