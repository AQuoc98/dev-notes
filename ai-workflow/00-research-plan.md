# 00 — Research plan

## 1. Objective

Thiết kế và kiểm chứng một workflow cá nhân có thể dùng nhất quán trên Codex, Claude Code và các agent tương thích khác; ưu tiên tính portable, kiểm chứng được, bảo mật và khả năng tích lũy kiến thức.

### Definition of done cho research phase

Bộ tài liệu đạt mốc đầu tiên khi có:

- một vocabulary chung và decision tree để chọn đúng primitive;
- một workflow end-to-end chạy được trên ít nhất Codex và Claude Code;
- một skill portable dựa trên `SKILL.md`;
- một MCP capability nhỏ với quyền tối thiểu;
- một guardrail deterministic và một quy trình human review;
- một prototype second brain có raw sources, wiki, schema, index và log;
- một eval suite nhỏ đủ để so sánh kết quả giữa host/model;
- tài liệu chỉ rõ phần nào là fact, phần nào là inference, phần nào chưa biết.

## 2. Scope

### In scope

- Agent loop, workflow orchestration và context engineering.
- Project guidance: `AGENTS.md`, `CLAUDE.md`, rules và shared contract.
- Agent Skills open standard: `SKILL.md`, progressive disclosure, references, scripts, assets.
- MCP: tools, resources, prompts, transport, auth, permissions và data boundary.
- Hooks: lifecycle automation, policy enforcement, logging và post-action verification.
- Custom subagents: role, context isolation, tool surface, model routing, stop conditions.
- Plugins/marketplaces: packaging, versioning, distribution, trust và portability.
- Memory/second brain: session memory, project memory, durable knowledge, agent memory.
- Tiny agent: minimal reference implementation hoặc sản phẩm được xác nhận sau.
- Evals, observability, cost/latency, security, prompt injection và human-in-the-loop.

### Out of scope ở vòng đầu

- Xây một autonomous agent hoạt động vô hạn.
- Chọn một vendor duy nhất làm source of truth.
- Tự động gửi email, thay đổi production hoặc thực hiện giao dịch.
- So sánh mọi model/provider bằng benchmark chung khi chưa có task set.
- Tối ưu vector database/RAG trước khi đo nhu cầu thật.

## 3. Research questions

### A — Vocabulary và primitives

1. “Agent” khác “workflow”, “tool”, “skill”, “subagent” và “plugin” ở đâu?
2. Khi nào một instruction phải trở thành `AGENTS.md`/`CLAUDE.md`, skill, hook hay code thường?
3. Đâu là ranh giới giữa model-controlled behavior và deterministic enforcement?

### B — Cross-platform portability

1. Phần tối thiểu nào có thể dùng nguyên trạng giữa Codex và Claude Code?
2. Cách map shared contract sang `AGENTS.md` và `CLAUDE.md` mà không tạo drift?
3. Skill nào dùng chung, metadata nào host-specific, và cách test compatibility?
4. MCP server nào nên là shared service, MCP nào nên local-only?
5. Plugin là packaging convenience hay là abstraction boundary cần cam kết lâu dài?

### C — Workflow design

1. Task nào nên là deterministic workflow, task nào mới đáng giao cho agent tự quyết định?
2. Làm sao chia task để subagent giảm context noise thay vì tăng chi phí?
3. Đâu là checkpoint bắt buộc trước khi agent tạo side effect?
4. Khi nào nên route model nhỏ/nhanh và khi nào cần model mạnh hơn?

### D — Knowledge và memory

1. Cách kết hợp raw sources, generated wiki, personal decisions và chat history?
2. Làm sao giữ provenance, phát hiện contradiction và tránh “AI-generated fact” bị xem là sự thật?
3. Khi nào index/markdown đủ tốt; khi nào cần local search, SQLite hoặc vector search?
4. Query nào nên trả lời rồi lưu lại thành artifact để lần sau không làm lại từ đầu?

### E — Quality, safety và operations

1. Làm sao đo task success ngoài việc agent tự nói “đã xong”?
2. Evals nào là code-based, model-based, human-based?
3. Làm sao giảm approval fatigue nhưng vẫn giữ quyền kiểm soát?
4. Làm sao test prompt injection từ repo, docs, web và MCP tool output?
5. Làm sao biết một skill/hook/MCP/plugin đang làm workflow tốt hơn hay chỉ làm context phình to?

## 4. Working taxonomy

| Thành phần | Vai trò chuẩn | Dấu hiệu nên dùng |
| --- | --- | --- |
| `AGENTS.md` / `CLAUDE.md` | Quy tắc luôn đúng, context nền | Agent lặp cùng một lỗi hoặc cần biết convention ở mọi task |
| Skill | Quy trình/kiến thức tái sử dụng, load on demand | Cùng một checklist/prompt/playbook xuất hiện nhiều lần |
| MCP | Kết nối tool/data bên ngoài | Cần đọc/ghi hệ thống ngoài workspace |
| Hook | Enforcement/automation theo event | Việc phải xảy ra đúng cách mỗi lần |
| Subagent | Worker có context riêng | Task chuyên biệt, noisy hoặc có thể chạy song song |
| Plugin | Gói để cài/chia sẻ nhiều capability | Cần reuse across repo/team hoặc phân phối |
| Memory/wiki | Context bền vững, có lịch sử | Kiến thức cần tích lũy và truy hồi qua session |
| Eval | Feedback loop có tiêu chí | Cần biết thay đổi có thật sự cải thiện không |

## 5. Roadmap

| Phase | Mục tiêu | Deliverables | Status / review gate |
| --- | --- | --- | --- |
| P0 | Chốt framing | Bộ file hiện tại, vocabulary, open questions | Đang review |
| P1 | Portable core | Shared contract, task brief, source/eval format | Chưa bắt đầu |
| P2 | Host adapters | Codex/Claude mapping, install/run checklist | Chưa bắt đầu |
| P3 | Memory layer | Second brain schema, ingest/query/lint workflow | Chưa bắt đầu |
| P4 | Tiny reference | Minimal agent loop hoặc tiny-agent integration | Chưa bắt đầu |
| P5 | Reliability | Hooks, permissions, evals, observability, red-team cases | Chưa bắt đầu |
| P6 | Packaging | Skill library, plugin packaging, versioning, rollout guide | Chưa bắt đầu |

### Gate để sang phase kế tiếp

Không chuyển phase chỉ vì tài liệu đã dài. Mỗi phase phải có artifact chạy/thử được, expected behavior, failure cases và một record của human review.

## 6. Research method

Mỗi claim mới nên được ghi theo mẫu:

1. Nguồn và ngày kiểm tra.
2. Claim ngắn, có thể kiểm chứng.
3. Phạm vi áp dụng: Codex, Claude, MCP standard hay general.
4. Evidence level: official/spec, primary engineering, experiment, community.
5. Caveat/version risk.
6. Quyết định hoặc thí nghiệm bị ảnh hưởng.

Ưu tiên nguồn chính thức và specification cho behavior của platform. Dùng community repos để tìm implementation và ý tưởng, không dùng chúng làm bằng chứng duy nhất cho security hoặc compatibility.

## 7. Decisions cần bạn review

- “Tini agent” bạn muốn nghiên cứu là sản phẩm/repo nào, hay chỉ là nguyên tắc bắt đầu bằng một agent thật nhỏ?
- Workflow ưu tiên đầu tiên là coding, research, học tập, quản lý knowledge hay công việc cá nhân?
- Source of truth của second brain sẽ là repo Markdown local-first, Obsidian vault, hay một hệ thống khác?
- Mức tự động hóa chấp nhận được: đề xuất, sửa file local, gọi API, hay side effect ngoài hệ thống?
- Có dữ liệu nào bắt buộc local-only hoặc không được gửi tới model/MCP không?
- Bạn muốn ưu tiên portability giữa host hay tối ưu sâu cho Codex trước?

