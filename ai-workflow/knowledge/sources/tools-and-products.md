# Tools and products

Các tool/product người dùng thường dùng hoặc dự định dùng. Đây là catalog theo vai trò trong workflow, không phải đánh giá chất lượng hay cam kết vendor.

## Catalog

| Tool/product | URL | Status | Workflow role | Candidate topics |
| --- | --- | --- | --- | --- |
| Artificial Analysis | [artificialanalysis.ai](https://artificialanalysis.ai/) | `following` | Model/provider benchmark, comparison và discovery | `models`, `benchmarks`, `latency`, `cost` |
| ChatGPT | [chatgpt.com](https://chatgpt.com/) | `following` | General reasoning, research, writing và agent surface | `agents`, `research`, `workflow` |
| Claude | [claude.ai/new](https://claude.ai/new) | `following` | Reasoning, writing, coding và long-context work | `claude-code`, `agents`, `context-engineering` |
| Gemini | [gemini.google.com/app](https://gemini.google.com/app) | `following` | Alternative model surface và multimodal/Google ecosystem exploration | `models`, `multimodal`, `research` |
| Grok | [grok.com](https://grok.com/) | `following` | Alternative model surface và current/social discovery | `models`, `social`, `ai-news` |
| Z.ai | [chat.z.ai](https://chat.z.ai/) | `following` | Alternative model/provider exploration | `models`, `coding-agents`, `cost` |
| skills.sh | [skills.sh](https://www.skills.sh/) | `following` | Discover/share Agent Skills và reusable workflows | `skills`, `SKILL.md`, `plugins` |

`following` bao gồm cả tool đang dùng và tool đã chọn để theo dõi/dùng trong tương lai; khi có quyết định cụ thể, ghi thêm `adoption: current | planned` trong research note.

## How each product fits the workflow

```text
Discover / compare models
  → Artificial Analysis

Reason / research / draft
  → ChatGPT, Claude, Gemini, Grok, Z.ai

Find reusable workflow knowledge
  → skills.sh

Promote verified findings
  → knowledge/ → research/notes/ → research/sources.md
```

## Product review fields

Khi đánh giá một tool/product, ghi thêm:

- use case cụ thể;
- model/provider và version nếu có;
- input/data classification;
- tool access và external side effects;
- output quality và evidence;
- latency/cost;
- privacy/retention;
- portability và export path;
- failure modes;
- ngày kiểm tra gần nhất.

## Tool selection rule

Không chọn tool chỉ vì model benchmark cao. Chọn theo task outcome, privacy, integration, controllability, cost và khả năng verify. Mọi claim về feature/version phải được ghi vào research note với nguồn chính thức hoặc thử nghiệm reproducible.
