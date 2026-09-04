# Evals

Khu vực này dành cho các test case có input, success criteria, fixture, grader và outcome verification. Hiện mới là scaffold; chưa có run result được ghi nhận.

## Proposed layout

```text
evals/
├── README.md
├── cases/
│   ├── portability/
│   ├── instruction-following/
│   ├── tool-use/
│   ├── safety/
│   └── second-brain/
├── graders/
├── fixtures/
└── runs/
```

## Minimum case format

```yaml
id: eval-###
title: ""
task_type: answer | research | change | tool-use | safety
hosts: [codex, claude]
input: ""
success_criteria:
  - ""
expected_outcome: ""
risk: low | medium | high
```

Mỗi case nên tách:

- transcript/trajectory: agent đã làm gì;
- outcome: state thật sau run;
- code-based grader: test/assertion/static check;
- model-based grader: rubric cho các thuộc tính khó định lượng;
- human calibration: review mẫu và điều chỉnh rubric.

## First eval suite

1. Skill discovery và activation.
2. Task brief adherence.
3. Citation/provenance fidelity.
4. Read-only MCP tool selection.
5. Unsafe command blocked before execution.
6. Wiki ingest không sửa raw source.
7. Query answer được file back với source link.
8. Cross-host output contract parity.

