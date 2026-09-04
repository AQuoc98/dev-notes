# Platform note — Codex và Claude Code

## Scope

So sánh các extension primitives ở mức behavior và portability; không coi tên gọi giống nhau là bằng chứng compatibility.

## Codex — facts từ official docs

- Codex customization gồm project guidance (`AGENTS.md`), memories, skills, MCP và subagents.
- OpenAI Learn khuyến nghị một thứ tự phát triển: guidance trước; skill/plugin cho workflow tái sử dụng; MCP cho external systems; subagents khi đã sẵn sàng delegate noisy/specialized work.
- Skill là directory có `SKILL.md`, có thể kèm scripts/references/assets và optional `agents/openai.yaml`.
- Codex local skills có các scope repository/user/admin/system; project skills dùng `.agents/skills` theo docs hiện hành.
- Custom agents của Codex dùng TOML độc lập trong `.codex/agents/` hoặc `~/.codex/agents/`, với `name`, `description`, `developer_instructions` bắt buộc.
- Local memories là recall layer; rules bắt buộc nên nằm trong checked-in docs hoặc enforcement khác.
- Plugins có thể bundle skills, connectors/MCP và các capability khác tùy surface; plugin support khác nhau giữa desktop, CLI và IDE.

## Claude Code — facts từ official docs

- `CLAUDE.md` là persistent context; skills là reusable knowledge/workflows; MCP là external tools; subagents là isolated loops; hooks là lifecycle automation; plugins là packaging layer.
- Claude Code phân biệt standalone `.claude/` với plugin `.claude-plugin/plugin.json` cho distribution.
- Skills dùng `SKILL.md` và theo Agent Skills open standard; Claude có extensions như invocation control, subagent execution và dynamic context injection.
- Custom subagents là Markdown files trong `.claude/agents/` hoặc user scope; có thể cấu hình tools, model, skills, memory và isolation.
- Hook có thể là command, HTTP, MCP tool, prompt hoặc agent; hook dùng cho action phải xảy ra đều, còn skill cho reasoning/reference/workflow.

## Design inference

1. Shared source nên là skill body, task contract, wiki schema, evals và policy intent.
2. `AGENTS.md` và `CLAUDE.md` nên là thin adapters, không copy toàn bộ wiki hoặc playbook vào context mọi request.
3. Hooks, subagents và plugin manifests cần host-specific implementation/test.
4. Memory của từng sản phẩm không nên là canonical memory của workflow đa host; Git/Markdown có provenance phù hợp hơn cho lớp durable.

## Cần verify bằng experiment

- Discovery path thực tế ở các version đang dùng.
- Frontmatter fields được hai host hiểu giống nhau đến đâu.
- Hook input/output và block semantics tương ứng.
- MCP auth/transport và tool naming giữa hai host.
- Plugin support trên surface bạn thực sự sử dụng.

