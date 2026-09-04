# Agent architecture và evals note

## Workflow vs agent

Theo Anthropic, workflow có control flow được định trước; agent để model tự điều hướng process và tool usage. Hướng dẫn xây agent hiệu quả khuyến nghị bắt đầu bằng giải pháp đơn giản nhất, chỉ tăng complexity khi value biện minh cho latency/cost/risk.

## Patterns cần nghiên cứu

- Prompt chaining: các bước tuần tự, có gate giữa bước.
- Routing: phân loại input rồi chuyển tới specialist.
- Parallelization: nhiều worker cùng xử lý, aggregate/vote.
- Orchestrator-workers: orchestrator chia task động cho worker.
- Evaluator-optimizer: một agent tạo output, agent/logic khác đánh giá và refine.
- Single agent với tools: khi task cần feedback từ environment và recovery.

## Design inference

Personal workflow nên tăng complexity theo đường:

```text
single prompt
→ structured task brief
→ skill
→ skill + tool/MCP
→ bounded subagent
→ parallel/orchestrated workers
→ long-running automation
```

Mỗi bước phải có evidence rằng bước trước đã không đủ.

## Evals vocabulary

- Task: input + success criteria.
- Trial: một lần chạy task.
- Grader: code/model/human logic chấm một khía cạnh.
- Transcript/trajectory: toàn bộ tool calls, outputs và interaction.
- Outcome: state thực tế trong environment.
- Eval harness: chạy task, record, grade, aggregate.

## Eval policy đề xuất

- Chấm outcome, không chỉ final text.
- Kết hợp code-based checks, model-based rubric và human calibration.
- Chạy nhiều trials vì model output có variance.
- Track regression khi đổi model, prompt, skill, MCP hoặc permission.
- Theo dõi quality cùng latency, token, cost và error rate.
- Sau mỗi incident, thêm fixture vào regression suite.

