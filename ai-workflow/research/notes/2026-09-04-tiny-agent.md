# “Tini/tiny agent” note

## Ambiguity

User request ghi “tini agent”. Hiện chưa đủ context để biết đó là tên riêng hay “tiny agent” theo nghĩa agent tối giản. Vì vậy research giữ bốn nhánh:

### A — Tiny agent như nguyên tắc thiết kế

Một minimal agent loop gồm:

```text
input → model → tool choice → execute → observe → stop/continue → output
```

Chỉ một goal, một hoặc hai tools, bounded turns, explicit stop condition, structured output và log. Đây là ứng viên tốt nhất cho reference prototype vì giúp hiểu behavior trước khi thêm framework.

### B — `jellydn/tiny-coding-agent`

Một lightweight TypeScript coding agent community project, mô tả có multi-provider, MCP, memory, Agent Skills, plugins và lifecycle hooks. Cần kiểm tra version, license, install safety, actual implementation và maintenance trước khi dùng làm nền tảng.

### C — `tinyagent.page` / MCP UI

Một sản phẩm mô tả persistent shareable UI/data primitives cho agent: page, collection, items và human responses. Đây có thể là “human-in-the-loop surface” qua MCP, không nhất thiết là agent runtime.

### D — `alchemiststudiosDOTai/tinyAgent`

Một lightweight Python agent framework beta, phù hợp để nghiên cứu minimal abstractions và provider/tool loop. APIs có thể thay đổi; không xem là production foundation ở phase đầu.

## Decision pending

Khi user xác nhận “tini agent” là A/B/C/D hoặc một repo khác, chuyển nhánh tương ứng thành research track và thêm experiment riêng.

## Baseline prototype requirements

Bất kỳ candidate nào cũng phải pass:

- one task end-to-end;
- no secret in logs;
- tool allowlist;
- max turns/time/cost;
- explicit failure and retry policy;
- deterministic fixture and outcome grader;
- same task can be attempted with OpenAI và Anthropic provider nếu tool supports.
