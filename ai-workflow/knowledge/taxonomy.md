# Knowledge taxonomy

## 1. Source type

| Value | Meaning |
| --- | --- |
| `youtube` | Video/channel, thường phục vụ learning hoặc discovery |
| `news` | Tin tức, blog hoặc publication |
| `community` | Hacker News, Reddit, forum, discussion |
| `social` | X hoặc social post |
| `tool` | Công cụ phục vụ một tác vụ cụ thể |
| `product` | Nền tảng/app/model surface |
| `official-doc` | Documentation chính thức |
| `academic` | Paper, benchmark, research publication |
| `repo` | GitHub/GitLab implementation |

## 2. Source role

| Value | Dùng khi |
| --- | --- |
| `discovery` | Tìm ý tưởng, trend hoặc nguồn mới |
| `learning` | Học concept, tutorial hoặc workflow |
| `evidence` | Dùng để support một claim đã research |
| `benchmark` | So sánh model/tool bằng số liệu hoặc fixture |
| `implementation` | Tham khảo code/config có thể reproduce |
| `opinion` | Góc nhìn cá nhân; không dùng một mình làm evidence |

## 3. Trust level

- `official`: nguồn do vendor/standard owner công bố.
- `primary`: tác giả trực tiếp của research/product/implementation.
- `secondary`: phân tích/tổng hợp có dẫn nguồn.
- `community`: discussion, comment, social post hoặc repo chưa kiểm chứng.

Trust level không phải phán quyết “đúng/sai”; nó cho biết mức kiểm tra cần có trước khi promote thành evidence.

## 4. Status

- `following`: nguồn đang theo dõi định kỳ.
- `monitor`: có giá trị nhưng chưa cần đọc thường xuyên.
- `backlog`: muốn nghiên cứu sau.
- `archived`: không còn active hoặc đã thay thế.

## 5. Topic tags

```text
agents, agentic-ai, coding-agents, workflow, orchestration,
mcp, tools, skills, hooks, subagents, plugins,
memory, second-brain, context-engineering, rag, knowledge-graph,
evals, observability, security, prompt-injection, sandboxing,
models, benchmarks, multimodal, ai-news, automation, tiny-agent,
ai-engineering, ai-tools, llm-apps, latency, cost, research, early-signal
```

## 6. Search and maintenance rules

1. Một source chỉ xuất hiện một lần trong catalog chính; dùng tags để cross-reference.
2. URL phải là canonical URL, không giữ dấu phẩy hoặc tracking query không cần thiết.
3. Ghi `last_checked` khi xác nhận link hoặc cập nhật metadata.
4. Không hard-code những count hoặc trạng thái dễ stale nếu không cần.
5. Nếu source là social/community, tìm primary source trước khi biến thành claim.
6. Nếu tool/product thay đổi role, cập nhật entry và ghi lý do trong research note hoặc decision log.
