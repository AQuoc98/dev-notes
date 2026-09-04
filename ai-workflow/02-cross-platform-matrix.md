# 02 — Cross-platform matrix

## 1. Kết luận sơ bộ

Portable nhất là **nội dung và contract**, không phải mọi file config. Nên giữ một nguồn dùng chung cho task format, skill body, schema, eval cases và security policy; sau đó có adapter mỏng cho Codex và Claude Code.

## 2. Capability mapping

| Capability | Portable core | Codex adapter | Claude Code adapter | Portability risk |
| --- | --- | --- | --- | --- |
| Always-on guidance | `WORKFLOW-CONTRACT.md`, conventions, boundaries | `AGENTS.md` | `CLAUDE.md` | Hai cơ chế load/inheritance khác nhau |
| Reusable workflow | `skills/<name>/SKILL.md` | `.agents/skills/` + optional `agents/openai.yaml` | `.claude/skills/<name>/SKILL.md` | Frontmatter extension và invocation khác nhau |
| External tools/data | MCP server contract | Codex MCP config | Claude MCP config | Auth, transport, lifecycle và naming |
| Deterministic enforcement | Script + policy tests | Codex hooks/config | Claude hooks/settings/plugin | Event names, input/output và block semantics |
| Specialized worker | Role spec + output contract | `.codex/agents/*.toml` | `.claude/agents/*.md` | File format, model fields, inheritance |
| Bundle/distribution | Component manifest intent | `.codex-plugin/plugin.json` | `.claude-plugin/plugin.json` | Marketplace/schema/supported components |
| Persistent knowledge | Markdown/Git + provenance | local memories optional | agent memory optional | Product memories không portable |
| Evaluation | Task cases + graders + expected outcomes | local/CI harness | local/CI harness | Host observability khác nhau |

## 3. Portable contract

Các nội dung sau nên là source of truth chung:

- `WORKFLOW-CONTRACT.md`: principles, task brief, output contract, risk levels.
- `skills/`: Agent Skills standard, mỗi skill có `SKILL.md` và resources tương đối.
- `knowledge/`: raw sources, generated wiki, schema, index, log.
- `evals/`: test cases, fixtures, graders, expected outcomes.
- `security/`: data classification, allowed actions, threat cases.

Host-specific chỉ nên chứa:

- entrypoint/load path;
- plugin manifest và marketplace metadata;
- model/permission/sandbox defaults;
- hook wiring;
- MCP credentials/transport không commit secret;
- subagent file format;
- command aliases.

## 4. Proposed repository topology

Đây là topology mục tiêu để nghiên cứu, chưa được bật trong repo hiện tại:

```text
ai-workflow-runtime/
├── shared/
│   ├── WORKFLOW-CONTRACT.md
│   ├── task-brief.md
│   ├── skills/
│   ├── knowledge/
│   │   ├── raw/
│   │   ├── wiki/
│   │   ├── schema.md
│   │   ├── index.md
│   │   └── log.md
│   ├── evals/
│   └── security/
├── codex/
│   ├── AGENTS.md
│   ├── agents/
│   ├── hooks/
│   └── plugin/
└── claude/
    ├── CLAUDE.md
    ├── agents/
    ├── hooks/
    └── plugin/
```

Không symlink mù giữa các host. Mỗi adapter cần có test xác nhận nó vẫn trỏ tới đúng shared artifact.

## 5. Rules cho compatibility

1. Viết skill theo Agent Skills open standard trước; chỉ thêm host-specific metadata khi cần.
2. Không đưa credentials, absolute local paths hoặc vendor-specific tool names vào shared skill body nếu có thể tránh.
3. Mọi host-specific instruction phải chỉ rõ: `codex-only`, `claude-only` hoặc `portable`.
4. Dùng cùng một task brief và expected outcome khi benchmark hai host.
5. Ghi version/date của behavior vì platform docs và event schemas thay đổi.
6. Nếu hai host khác semantics, ưu tiên một shared intent + hai implementation adapters thay vì ép abstraction giả.
7. Product memory chỉ là recall layer; rule bắt buộc phải nằm trong checked-in docs hoặc enforcement code.

## 6. Compatibility test matrix

| Test | Codex | Claude Code | Pass condition |
| --- | --- | --- | --- |
| Skill discovery | ☐ | ☐ | Name/description hiển thị đúng |
| Skill activation | ☐ | ☐ | Đúng workflow, không load thừa resources |
| Relative resource path | ☐ | ☐ | Script/reference chạy từ skill root |
| MCP read-only tool | ☐ | ☐ | Schema, auth, output và error giống nhau |
| Guardrail block | ☐ | ☐ | Unsafe action bị chặn trước side effect |
| Subagent handoff | ☐ | ☐ | Summary đủ evidence, không tràn main context |
| Wiki ingest | ☐ | ☐ | Raw immutable, wiki/index/log cập nhật đúng |
| Eval outcome | ☐ | ☐ | Chấm cùng rubric trên cùng fixture |
