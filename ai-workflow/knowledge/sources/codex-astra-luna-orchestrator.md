# Codex Astra–Luna Orchestrator

```yaml
source_type: repo
role: implementation
topics: [agents, coding-agents, orchestration, skills, subagents, models, cost]
status: following
trust_level: primary
last_checked: 2026-09-08
```

Repository: [donvito/codex-astra-luna-orchestrator](https://github.com/donvito/codex-astra-luna-orchestrator)

## Tóm tắt

Đây là một bộ cấu hình Codex theo mô hình **một agent gốc điều phối, nhiều subagent thực thi và một reviewer độc lập**. Repository không phải một ứng dụng chạy nền; installer chỉ sao chép cấu hình và hướng dẫn vào một project Codex đã tồn tại.

- **Codex Pro:** GPT-6 Astra làm root/orchestrator và reviewer; GPT-5.6 Luna chạy các vai trò thực thi.
- **Codex Plus:** GPT-5.6 Luna ở max reasoning làm root/orchestrator và thực thi; GPT-6 Astra vẫn làm reviewer độc lập.
- Các vai trò được pin model riêng trong `.codex/agents/*.toml`, vì vậy đổi model mặc định không tự động đổi model của các vai trò đã pin.

## Thành phần được cài

Installer hỏi từng thành phần và mặc định chọn cả ba:

| Thành phần | Vai trò |
| --- | --- |
| `.codex/` | Root config và năm profile: `explorer`, `worker`, `tester`, `reviewer`, `researcher` |
| `.agents/skills/astra-orchestrator/` | Skill hướng dẫn root agent phân rã, giao việc, tích hợp và verify |
| `AGENTS.md` | Project-level instruction: root giữ architecture/integration/final verification; subagent xử lý phần việc có boundary rõ |

Topology mặc định:

```text
root / orchestrator
├── explorer      Luna, read-only
├── worker        Luna, workspace-write
├── researcher   Luna, read-only
├── tester        Luna, workspace-write
└── reviewer      Astra, read-only, review độc lập
```

`worker` và `tester` có thể sửa workspace theo nhiệm vụ được giao; `explorer`, `researcher` và `reviewer` được cấu hình read-only. Root vẫn chịu trách nhiệm tích hợp và kiểm tra cuối.

## Cài đặt project-scoped — khuyến nghị

### Điều kiện

- Có Git.
- Dùng macOS/Linux với shell hoặc Windows với PowerShell.
- Có một target repository đã tồn tại; target phải khác thư mục clone của orchestrator.
- Tài khoản Codex/plan có thể sử dụng các model được cấu hình. Tên model và giới hạn sử dụng có thể thay đổi theo thời điểm.

### macOS/Linux

```sh
git clone https://github.com/donvito/codex-astra-luna-orchestrator.git
cd codex-astra-luna-orchestrator
./setup.sh
```

Khi installer hỏi:

1. Nhập đường dẫn tuyệt đối hoặc tương đối tới target repository, ví dụ `../my-project`.
2. Chọn plan:
   - `1` / `Pro`: Astra điều phối, Luna thực thi, Astra review.
   - `2` / `Plus`: Luna max điều phối, Luna thực thi, Astra review.
3. Với mỗi `.codex`, `.agents`, `AGENTS.md`, nhấn Enter để chọn mặc định `yes`, hoặc chọn `no` để bỏ qua.
4. Nếu file đích đã tồn tại, đọc danh sách overwrite. Mặc định của cập nhật file có sẵn là `no`.

### Windows

Trong Windows PowerShell:

```powershell
powershell -ExecutionPolicy Bypass -File .\setup.ps1
```

Với PowerShell 7:

```powershell
pwsh -File .\setup.ps1
```

Các câu hỏi và lựa chọn plan giống bản shell.

### Sau khi cài

Mở Codex từ target repository. Cấu hình `.codex` cấp project chỉ được nạp khi project được trust. Kiểm tra nhanh ba điểm:

```text
<target>/.codex/config.toml
<target>/.codex/agents/{explorer,worker,tester,reviewer,researcher}.toml
<target>/.agents/skills/astra-orchestrator/SKILL.md
```

Nếu project đã có `.codex`, `.agents` hoặc `AGENTS.md`, nên xem diff ngay sau setup để chắc rằng chỉ các file mong muốn bị thay đổi.

## Cài đặt global/personal — tùy chọn

Repository cũng mô tả cách dùng cấu hình cho mọi project của user:

- Copy các file agent vào `~/.codex/agents/`.
- Copy thư mục skill vào `~/.agents/skills/astra-orchestrator/`.
- Merge các setting từ `.codex/config.toml` (Pro) hoặc `.codex/config.plus.toml` (Plus) vào `~/.codex/config.toml`.

Không overwrite nguyên file global nếu file đó đã có MCP server, provider, permission hoặc setting khác. Cách này có scope rộng hơn project-scoped setup nên chỉ nên dùng sau khi đã hiểu rõ các boundary.

## Sử dụng

### Gọi skill

Codex có thể tự chọn skill khi task khớp mô tả. Có thể gọi rõ ràng bằng:

```text
$astra-orchestrator

Implement the new invoice export endpoint.
Have explorer map the existing invoice/export path first.
Use workers for bounded implementation, tester for verification,
and reviewer for an independent final review.
```

Nên dùng cho feature nhiều file, debugging qua nhiều component, repo-wide change, task có các nhánh song song hoặc cần review độc lập. Không cần dùng cho câu hỏi đơn giản hoặc thay đổi một file rất nhỏ.

### Luồng thực thi

1. Root xác định scope, architecture và cách chia việc.
2. `explorer` khảo sát code path, dependency, config và test hiện có.
3. `worker` thực hiện các phần việc bounded; mỗi worker nên có ownership file rõ ràng.
4. `tester` chạy kiểm tra tập trung hoặc bổ sung test khi được yêu cầu.
5. `reviewer` đọc diff ở chế độ read-only và tìm lỗi correctness, security, regression, concurrency, data-integrity hoặc thiếu test.
6. Root tích hợp, verify và trả kết quả cuối.

Không nên cho nhiều implementation agent cùng sửa một file nếu chưa có boundary và cách merge rõ ràng.

### Tinh chỉnh cấu hình

File chính là `<target>/.codex/config.toml`. Một số knob quan trọng trong bản Pro:

```toml
model = "gpt-6-astra"
model_reasoning_effort = "low"

[agents]
enabled = true
max_concurrent_threads_per_session = 4
default_subagent_model = "gpt-5.6-luna"
default_subagent_reasoning_effort = "medium"
```

Bản Plus dùng `model = "gpt-5.6-luna"` và `model_reasoning_effort = "max"` cho root; installer chuyển `config.plus.toml` thành `config.toml` khi chọn Plus. Các profile named agent vẫn giữ model pin riêng.

Gợi ý tuning từ repository:

- Muốn rẻ/nhanh hơn: Astra `medium`, Luna `low` hoặc `medium`, khoảng 3–4 thread đồng thời.
- Repo lớn: Astra `high`, Luna `medium`, chỉ tăng lên 6–8 thread khi các task thực sự độc lập.
- Muốn boundary chặt: giữ `explorer`/`researcher`/`reviewer` read-only và `worker`/`tester` workspace-write.

### Theo dõi token usage

Repository có script đọc rollout logs Codex trong `~/.codex/sessions`:

```sh
python scripts/token_usage.py --list --date YYYY-MM-DD
python scripts/token_usage.py --latest --date YYYY-MM-DD
```

Script này nên chạy từ thư mục clone của orchestrator và chỉ đọc log đã có; nó không thay đổi cấu hình hay giới hạn tài khoản.

## Gỡ cài đặt

Repository hiện không cung cấp uninstaller riêng. Setup có thể merge vào component đã tồn tại và có thể overwrite các path sau khi user xác nhận, nên cần phân biệt file do setup tạo với file đã có trước đó.

### Gỡ project-scoped an toàn

1. Đóng phiên Codex đang chạy trong target.
2. Kiểm tra trạng thái và diff của target:

   ```sh
   cd /path/to/target
   git status --short
   git diff -- .codex .agents AGENTS.md
   ```

3. Chỉ xóa hoặc chuyển sang thư mục backup các file thực sự thuộc bộ này:

   ```text
   .codex/agents/explorer.toml
   .codex/agents/worker.toml
   .codex/agents/tester.toml
   .codex/agents/reviewer.toml
   .codex/agents/researcher.toml
   .agents/skills/astra-orchestrator/SKILL.md
   ```

4. Với `.codex/config.toml` và `AGENTS.md`:
   - Nếu chúng là file mới do setup tạo, có thể xóa/chuyển backup sau khi xác nhận không có nội dung khác cần giữ.
   - Nếu chúng đã tồn tại trước setup hoặc đã được chỉnh sửa sau setup, không xóa cả file. Khôi phục bản trước từ Git/backup hoặc hoàn tác thủ công chỉ các đoạn do bộ này thêm.
5. Chỉ xóa các thư mục `.codex/agents/` hoặc `.agents/skills/astra-orchestrator/` nếu sau khi dọn chúng không còn file khác cần giữ.

Không xóa toàn bộ `.codex/`, `.agents/`, `~/.codex/` hoặc `~/.agents/` chỉ để gỡ bộ này; các thư mục đó có thể chứa cấu hình, skill hoặc agent khác.

### Gỡ global/personal

- Xóa các file role tương ứng trong `~/.codex/agents/` sau khi xác nhận chúng do bộ này thêm.
- Xóa `~/.agents/skills/astra-orchestrator/` nếu thư mục này không chứa nội dung khác.
- Hoàn tác thủ công các setting đã merge vào `~/.codex/config.toml`; không xóa cả file global.

Sau khi gỡ, mở lại Codex từ target và xác nhận skill/agent `astra-orchestrator` không còn được discover. Việc gỡ này không cần dọn daemon hay service riêng vì repository không cài một tiến trình chạy nền.

## Caveat và quyết định dùng trong workflow

- Đây là cấu hình vendor-specific cho Codex; phần skill và ý tưởng orchestration có thể tham khảo cho workflow portable, nhưng `.codex/*.toml`, model pin và cơ chế load không nên coi là portable mặc định.
- Root orchestration làm tăng token usage vì root giữ context trong suốt task và mỗi subagent có context riêng.
- Model, reasoning effort, schema config và rate-limit có thể thay đổi; trước khi áp dụng cho workflow lâu dài nên kiểm tra lại repository và tài liệu Codex.
- Với `ai-workflow`, nên xem repository này là **candidate implementation / adapter cho Codex**, không phải source of truth cho portable contract. Contract chung vẫn nên nằm ở `ai-workflow/portable/WORKFLOW-CONTRACT.md`.

## Nguồn chính

- [Repository README](https://github.com/donvito/codex-astra-luna-orchestrator)
- [Shell installer](https://raw.githubusercontent.com/donvito/codex-astra-luna-orchestrator/main/setup.sh)
- [PowerShell installer](https://raw.githubusercontent.com/donvito/codex-astra-luna-orchestrator/main/setup.ps1)
- [Project instructions](https://raw.githubusercontent.com/donvito/codex-astra-luna-orchestrator/main/AGENTS.md)
- [Orchestrator skill](https://raw.githubusercontent.com/donvito/codex-astra-luna-orchestrator/main/.agents/skills/astra-orchestrator/SKILL.md)
- [Pro config](https://raw.githubusercontent.com/donvito/codex-astra-luna-orchestrator/main/.codex/config.toml) và [Plus config](https://raw.githubusercontent.com/donvito/codex-astra-luna-orchestrator/main/.codex/config.plus.toml)

