# AI Workflow Lab

Bộ tài liệu nghiên cứu và thiết kế workflow sử dụng agent + AI trên nhiều host, trước mắt là Codex và Claude Code.

> Trạng thái: `draft / P0 — framing`  
> Ngày khởi tạo: `2026-09-04`  
> Phạm vi: research planning, design principles, experiments và templates. Chưa phải cấu hình agent đang được kích hoạt.

## Giả thuyết làm việc

Workflow bền vững nên có một **portable core** bằng Markdown/Git và các adapter theo từng host:

`shared contract → AGENTS.md / CLAUDE.md → Agent Skills → MCP → hooks → subagents → plugins → evals + memory`

Mỗi lớp giải quyết một vấn đề khác nhau. Không bắt đầu bằng việc dựng một “siêu agent”; bắt đầu bằng một workflow nhỏ, có đầu vào/đầu ra kiểm chứng được, rồi chỉ thêm autonomy khi có bằng chứng nó làm tăng chất lượng hoặc giảm ma sát.

## Điều hướng

- [00 — Research plan](00-research-plan.md): mục tiêu, câu hỏi, phạm vi, roadmap và các quyết định cần review.
- [01 — Canonical workflow](01-canonical-workflow.md): workflow chuẩn đề xuất, routing rule và mức độ autonomy.
- [02 — Cross-platform matrix](02-cross-platform-matrix.md): phần nào portable, phần nào cần adapter Codex/Claude.
- [03 — Second brain and memory](03-second-brain-and-memory.md): kiến trúc Raw → Wiki → Schema theo hướng LLM Wiki.
- [04 — Experiment backlog](04-experiment-backlog.md): các thử nghiệm nhỏ, tiêu chí pass/fail và thứ tự ưu tiên.
- [05 — Review checklist](05-review-checklist.md): checklist để cùng review và chốt hướng.
- [Evals](evals/README.md): task cases, graders và outcome verification.

### Research notes

- [Sources](research/sources.md)
- [Codex và Claude Code](research/notes/2026-09-04-platforms.md)
- [Agent Skills](research/notes/2026-09-04-agent-skills.md)
- [MCP và security](research/notes/2026-09-04-mcp-and-security.md)
- [Agent architecture và evals](research/notes/2026-09-04-agent-architecture-and-evals.md)
- [LLM Wiki / second brain](research/notes/2026-09-04-llm-wiki.md)
- [“Tini/tiny agent”](research/notes/2026-09-04-tiny-agent.md)

### Templates

- [Source note](templates/source-note.md)
- [Experiment](templates/experiment.md)
- [Skill](templates/skill.md)
- [Subagent](templates/subagent.md)
- [MCP server](templates/mcp-server.md)
- [Hook review](templates/hook-review.md)

### Portable templates

- [Portable folder](portable/README.md)

`portable/` chỉ chứa bản mẫu để copy/adapt. Các file trong đó không tự động trở thành hướng dẫn cho repo này.

## Cách làm việc với bộ tài liệu

1. Review [00 — Research plan](00-research-plan.md) và các mục “Open questions”.
2. Chọn một use case thật trong [04 — Experiment backlog](04-experiment-backlog.md).
3. Chạy thử trên Codex và Claude Code bằng cùng một task brief.
4. Ghi kết quả, chi phí, lỗi và khác biệt hành vi vào research notes và [evals](evals/README.md).
5. Chỉ sau khi workflow lặp lại ổn định mới đóng gói thành skill, subagent hoặc plugin.

## Nguyên tắc evidence

Tài liệu phân biệt rõ:

- `fact`: có trong tài liệu chính thức hoặc specification.
- `observation`: kết quả thử nghiệm của chúng ta.
- `inference`: suy luận thiết kế từ facts/observations.
- `open`: chưa đủ bằng chứng, cần kiểm tra thêm.
