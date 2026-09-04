# 01 — Canonical workflow

## 1. Mô hình đề xuất

Workflow mặc định là **human-led, agent-assisted, evidence-driven**:

```text
Human intent
  → Task brief
  → Classify / route
  → Plan
  → Execute with bounded tools
  → Verify against ground truth
  → Human review / approval
  → Commit artifact + update knowledge
  → Retrospective / improve system
```

Agent được tự quyết cách làm trong vùng được giao; con người vẫn sở hữu mục tiêu, trade-off, dữ liệu nhạy cảm, approval và tiêu chí “đã xong”.

## 2. Các lớp của workflow

| Layer | Câu hỏi | Artifact / control |
| --- | --- | --- |
| Intent | Ta muốn thay đổi điều gì? | Task brief, outcome, constraints |
| Persistent context | Agent luôn cần biết gì? | `AGENTS.md`, `CLAUDE.md`, shared contract |
| Task context | Chỉ task này cần gì? | Source list, relevant files, prior decisions |
| Knowledge | Thông tin nào cần tích lũy? | Wiki pages, decision records, index, log |
| Capability | Agent có thể làm gì? | Built-in tools, MCP, scripts |
| Orchestration | Chia/route công việc ra sao? | Single agent, sequential, parallel, subagents |
| Guardrails | Cái gì không được phép? | Permissions, sandbox, hooks, deny rules |
| Verification | Làm sao biết đúng? | Tests, assertions, source citations, state checks |
| Delivery | Kết quả đi đâu? | Diff, report, PR, wiki update, user approval |

## 3. Router: chọn đúng primitive

```text
Quy tắc phải đúng ở mọi task?
  └─ Có → AGENTS.md / CLAUDE.md hoặc hook nếu cần enforcement

Quy trình/kiến thức lặp lại?
  └─ Có → Skill

Cần hệ thống ngoài workspace?
  └─ Có → MCP; thêm skill nếu agent cần playbook để dùng MCP đúng

Việc phải chạy theo event, không cần model suy luận?
  └─ Có → Hook / script / CI

Task chuyên biệt, noisy hoặc song song được?
  └─ Có → Subagent

Cần cài/chia sẻ nhiều thành phần?
  └─ Có → Plugin / marketplace

Kiến thức cần sống qua nhiều session?
  └─ Có → Wiki/memory có provenance và lifecycle
```

Một task có thể dùng nhiều lớp. Ví dụ: `skill` mô tả quy trình review, `MCP` đọc issue/PR, `subagents` chạy các góc review độc lập, `hook` chặn secret, và `eval` kiểm tra kết quả.

## 4. Task lifecycle

### 4.1 Intake

Viết task brief trước khi mở rộng autonomy:

```markdown
## Outcome
Một câu mô tả kết quả observable.

## Context
Repo, nguồn, người dùng, trạng thái hiện tại.

## Constraints
Phạm vi file/data, thời hạn, privacy, không được làm gì.

## Evidence of success
Tests, state checks, citations, screenshots hoặc acceptance criteria.

## Permission level
Read-only | local edits | external writes | irreversible action.

## Delivery
Artifact cần trả về và nơi ghi lại.
```

### 4.2 Classify và route

Phân loại trước: `answer`, `research`, `change`, `automation`, `maintenance`. Gắn risk: `low`, `medium`, `high`. Không dùng cùng permission profile cho cả năm loại.

### 4.3 Plan

Plan phải nêu: assumption, files/systems sẽ chạm tới, tool cần dùng, checkpoints, stop conditions và cách verify. Nếu task chưa có success criteria thì chưa nên spawn nhiều agent.

### 4.4 Execute

- Mở tool surface nhỏ nhất đủ dùng.
- Đọc source of truth trước khi sửa generated view.
- Chia task theo ownership rõ ràng nếu dùng subagents.
- Ghi lại decision hoặc blocker ngay khi xuất hiện, không đợi cuối session.

### 4.5 Verify

Agent phải chứng minh state thực tế: test pass, diff, file tồn tại, API trả dữ liệu đúng, hoặc source trích dẫn được. “Tôi đã làm xong” không phải là bằng chứng.

### 4.6 Review và deliver

Human review tập trung vào intent, risk, scope creep và các claim quan trọng. Approval cho phép side effect cụ thể, không phải blanket approval cho mọi hành động sau đó.

### 4.7 Compound

Sau task, chỉ promote những gì có giá trị lâu dài:

- convention lặp lại → guidance;
- playbook ổn định → skill;
- check bắt buộc → hook/CI;
- kiến thức có provenance → wiki;
- failure lặp lại → eval/regression case;
- setup dùng ở nhiều repo → plugin.

## 5. Autonomy levels

| Level | Agent được làm | Human checkpoint |
| --- | --- | --- |
| L0 | Giải thích, đề xuất | Trước mọi hành động |
| L1 | Đọc/search/analyze | Trước thay đổi |
| L2 | Sửa local trong sandbox | Review diff và verify |
| L3 | Gọi external read APIs / tạo draft | Trước publish/send |
| L4 | Side effect ngoài hệ thống hoặc irreversible | Approval từng hành động hoặc policy rất rõ |

Mặc định khởi đầu ở L1/L2. Chỉ nâng level khi có eval, rollback và audit trail.

## 6. Output contract

Mọi workflow production-ish nên trả:

1. `Result`: đã tạo/thay đổi gì.
2. `Evidence`: test, state check, nguồn hoặc diff.
3. `Assumptions`: điều agent đã giả định.
4. `Risks`: điều chưa verify hoặc có thể sai.
5. `Next action`: việc cần người dùng quyết định.
6. `Memory update`: artifact nào được ghi vào wiki/log/eval.

## 7. Anti-patterns

- Đưa mọi rule vào một prompt dài và mong agent luôn nhớ.
- Dùng skill để “enforce” điều đáng ra phải là hook hoặc permission.
- Dùng MCP như một kho kiến thức không có schema, auth hoặc data boundary.
- Spawn nhiều subagents chỉ để tạo cảm giác mạnh hơn, không có aggregation rubric.
- Lưu generated summary làm source of truth mà bỏ raw source/provenance.
- Tự động hóa side effect trước khi có dry-run, approval và rollback.
- Đo chất lượng bằng độ dài output hoặc cảm giác “trông có vẻ hợp lý”.

