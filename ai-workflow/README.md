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

- [Knowledge hub](knowledge/README.md)
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

## Cách sử dụng bộ tài liệu

Bộ tài liệu này được dùng như một lab có vòng lặp: nghiên cứu → thử nghiệm → đánh giá → chuẩn hoá → triển khai. Không cần đọc hết một lần; mỗi task chỉ cần mở đúng lớp tài liệu liên quan.

### Lộ trình bắt đầu

1. Đọc [00 — Research plan](00-research-plan.md) để hiểu mục tiêu, phạm vi và các câu hỏi còn mở.
2. Đọc [01 — Canonical workflow](01-canonical-workflow.md) để chọn cách agent nên xử lý task.
3. Đọc [02 — Cross-platform matrix](02-cross-platform-matrix.md) khi cần chạy cùng workflow trên Codex và Claude Code.
4. Chọn một bài nhỏ trong [04 — Experiment backlog](04-experiment-backlog.md), ưu tiên `E-001` hoặc `E-002`.
5. Sau mỗi lần thử, ghi kết quả vào research note và cập nhật [evals](evals/README.md).

### Chọn đúng nơi để ghi thông tin

| Loại thông tin | Nơi lưu | Khi nào dùng |
| --- | --- | --- |
| URL, video, bài viết, tool mới | [Knowledge inbox](knowledge/inbox.md) | Vừa phát hiện, chưa phân loại hoặc kiểm chứng |
| Nguồn đã phân loại | [Knowledge hub](knowledge/README.md) và `knowledge/sources/` | Muốn tìm lại hoặc theo dõi lâu dài |
| Phân tích một chủ đề | `research/notes/YYYY-MM-DD-*.md` | Đã đọc và rút ra claim, observation hoặc open question |
| Danh mục nguồn có thể dùng làm evidence | [Research sources](research/sources.md) | Nguồn đã được chọn cho research chính thức |
| Thử nghiệm có fixture và tiêu chí pass/fail | [Experiment backlog](04-experiment-backlog.md) và `templates/experiment.md` | Muốn so sánh, đo lường hoặc reproduce |
| Quy tắc dùng agent | `portable/WORKFLOW-CONTRACT.md` | Quy tắc chung cho mọi host |
| Hướng dẫn lặp lại | `templates/skill.md` | Quy trình đủ ổn định để đóng gói thành skill |
| Công việc chuyên biệt hoặc song song | `templates/subagent.md` | Cần tách context, vai trò hoặc worker |
| Quyền truy cập hệ thống ngoài | `templates/mcp-server.md` | Cần thiết kế MCP và permission boundary |
| Policy chạy trước/sau một event | `templates/hook-review.md` | Cần guardrail, validation hoặc automation |

### Vòng lặp research hằng ngày

```text
Discover → Capture → Triage → Verify → Distill → Experiment → Promote
```

- **Discover:** tìm từ [search keywords](knowledge/search-keywords.md), YouTube, news, community hoặc tool catalog.
- **Capture:** đưa URL và lý do muốn đọc vào [inbox](knowledge/inbox.md), không để link rải trong nhiều file.
- **Triage:** chuẩn hoá URL, gắn `source_type`, `role`, `trust_level`, `status` và topic tags theo [taxonomy](knowledge/taxonomy.md).
- **Verify:** kiểm tra claim quan trọng bằng documentation chính thức, paper, repo hoặc một thử nghiệm tái lập được.
- **Distill:** viết note ngắn có claim, evidence, observation, inference và open questions.
- **Experiment:** biến ý tưởng thành task thật, fixture cố định, rubric và stop/rollback rule.
- **Promote:** chỉ đưa kết quả đã lặp lại ổn định vào workflow contract, skill, hook, subagent, MCP hoặc plugin.

### Cách đưa workflow sang Codex và Claude Code

1. Chỉnh [shared workflow contract](portable/WORKFLOW-CONTRACT.md) cho phù hợp với nguyên tắc cá nhân/project.
2. Dùng [AGENTS.md adapter](portable/AGENTS.md) cho Codex và [CLAUDE.md adapter](portable/CLAUDE.md) cho Claude Code.
3. Giữ phần cốt lõi ở dạng Markdown/Git; chỉ để adapter chứa khác biệt của từng host.
4. Chạy cùng task brief, input, permission boundary và acceptance criteria trên hai host.
5. So sánh output, tool calls, lỗi, latency, cost và mức cần sửa của con người; ghi vào cross-platform matrix hoặc eval case.

Không copy toàn bộ knowledge hub vào prompt. Hãy trỏ agent tới đúng nguồn cần dùng, giữ provenance và chỉ nạp context vừa đủ cho task.

### Thứ tự đóng gói capability

Khi một cách làm có giá trị, phát triển theo thứ tự nhỏ đến lớn:

```text
contract → host adapter → skill → MCP → hook → subagent → plugin → eval + memory
```

- Dùng **contract/adapter** cho nguyên tắc luôn đúng.
- Dùng **skill** cho quy trình có thể lặp lại.
- Dùng **MCP** khi cần dữ liệu hoặc hệ thống bên ngoài.
- Dùng **hook** cho guardrail hoặc event policy cần thực thi nhất quán.
- Dùng **subagent** khi cần context/role riêng hoặc xử lý song song.
- Dùng **plugin** khi cần đóng gói và chia sẻ nhiều capability.
- Luôn thêm **eval** trước khi tăng autonomy; chỉ lưu vào **memory/wiki** những gì có provenance và còn hữu ích.

### Quy tắc ghi và review

- Mỗi note chỉ nên trả lời một câu hỏi chính.
- Phân biệt `fact`, `observation`, `inference` và `open`; không biến suy đoán thành fact.
- Mọi benchmark, feature/version, security claim hoặc chi phí phải có ngày kiểm tra và nguồn.
- Một experiment phải có input/fixture, expected outcome, grader, metric và quyết định `keep / revise / reject / defer`.
- Nếu có side effect ngoài phạm vi, credential exposure hoặc prompt injection chưa kiểm soát, dừng thử nghiệm, ghi incident và sửa boundary trước khi chạy lại.

### Nhịp duy trì đề xuất

- **Mỗi phiên:** xử lý các link mới trong inbox và ghi lại decision quan trọng.
- **Mỗi tuần:** review nguồn đang follow, cập nhật backlog và chọn một experiment nhỏ.
- **Sau mỗi experiment:** cập nhật kết quả, matrix và contract nếu có thay đổi đã được chứng minh.
- **Mỗi tháng:** kiểm tra link stale, archive nguồn không còn giá trị và promote các pattern ổn định.

## Nguyên tắc evidence

Tài liệu phân biệt rõ:

- `fact`: có trong tài liệu chính thức hoặc specification.
- `observation`: kết quả thử nghiệm của chúng ta.
- `inference`: suy luận thiết kế từ facts/observations.
- `open`: chưa đủ bằng chứng, cần kiểm tra thêm.
