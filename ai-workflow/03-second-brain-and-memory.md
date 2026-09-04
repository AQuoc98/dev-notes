# 03 — Second brain and memory

## 1. Direction

Nghiên cứu theo pattern **LLM-maintained wiki** của Andrej Karpathy, nhưng giữ nguyên tắc kiểm soát provenance:

```text
Raw sources (immutable)
        ↓ ingest / discuss / extract
Wiki (generated, interlinked, revisable)
        ↓ query / synthesis / lint
New answers, decisions, questions → file back into wiki
```

Schema của hệ thống là “operating manual” cho agent: cấu trúc thư mục, naming, citation, cách ingest, cách query, cách lint và điều gì không được coi là fact.

## 2. Ba lớp chính

### Raw sources

- Article, paper, transcript, screenshot, dataset, chat export.
- Immutable hoặc append-only; không để agent sửa nguồn gốc.
- Có metadata: URL/path, captured date, author, source type, trust, license/privacy.

### Wiki

- Summary, concept, entity, comparison, decision, synthesis.
- Agent được tạo/cập nhật, nhưng phải link về raw source.
- Chứa frontmatter để truy vấn và lint.
- Generated text không tự động trở thành authoritative fact.

### Schema

- Quy tắc cập nhật wiki.
- Page types và required fields.
- Quy tắc contradiction, superseded claim, confidence và citation.
- Workflow `ingest`, `query`, `file-back`, `lint`.
- Quyền của agent: được sửa wiki, không được sửa raw; được đề xuất source mới, không tự publish claim quan trọng.

## 3. Operational loop

### Ingest

1. Capture source và lưu immutable.
2. Agent đọc source, xác định topic/entity/concept liên quan.
3. So với wiki/index hiện có trước khi tạo page mới.
4. Tạo hoặc cập nhật page; link tới source.
5. Ghi contradiction/uncertainty thay vì âm thầm hợp nhất.
6. Cập nhật index và append log.
7. Human skim summary ở các domain quan trọng.

### Query

1. Đọc index hoặc router index trước.
2. Chọn page liên quan, rồi đọc sâu đúng phần cần.
3. Trả lời với citation và mức confidence.
4. Nếu câu trả lời có insight/decision mới, hỏi hoặc đề xuất file back.

### Lint

Kiểm tra định kỳ:

- broken links, orphan pages, duplicate concepts;
- claims thiếu source hoặc source đã stale;
- contradiction giữa pages;
- entity/concept được nhắc nhưng chưa có page;
- index/log không đồng bộ;
- generated page lẫn với personal judgment;
- copied state: ngày, count, SHA, path bị hard-code sai.

## 4. Memory taxonomy

| Memory | Scope | Nơi nên lưu | Chính sách |
| --- | --- | --- | --- |
| Session context | Một cuộc trao đổi | Thread/transcript | Không xem là durable fact |
| Project guidance | Quy tắc luôn áp dụng | `AGENTS.md`/`CLAUDE.md` | Ngắn, review như code |
| Workflow knowledge | Quy trình tái sử dụng | `SKILL.md` + refs | Version, test, owner |
| Durable knowledge | Kiến thức có provenance | Raw/Wiki/Git | Có source, date, confidence |
| Agent memory | Pattern agent học được | Scoped memory dir | Không thay thế rules/source |
| Operational log | Điều đã xảy ra | Append-only log/telemetry | Có thể audit và replay |

## 5. Provenance contract

Mỗi claim trong wiki nên phân biệt:

```yaml
claim_type: source | synthesis | inference | decision | generated
confidence: high | medium | low
sources:
  - id: source-2026-09-04-001
captured_at: 2026-09-04
review_status: unreviewed | human-reviewed | superseded
```

Nguyên tắc: câu trả lời tốt có thể được truy ngược về page, page về source, và source về artifact bất biến. Khi không truy ngược được, agent phải nói rõ đó là inference hoặc không biết.

## 6. Indexing strategy

### Vòng đầu

- `index.md` theo category/page summary.
- `log.md` append-only.
- Search bằng file tools hoặc local grep/BM25 khi vault còn nhỏ.

### Khi scale

- Chia root index thành domain indexes.
- Thêm SQLite/BM25/hybrid search nếu việc đọc index không còn đủ.
- Chỉ thêm vector/graph khi có query class mà lexical/index-based search không giải quyết tốt.
- Benchmark bằng query set thực, không dựa vào cảm giác “RAG hiện đại hơn”.

## 7. Risks và guardrails

| Risk | Guardrail |
| --- | --- |
| Hallucination bị compile thành fact | Citation bắt buộc, confidence, human review cho claim quan trọng |
| Wiki drift so với nguồn | `captured_at`, `reviewed_at`, superseded links, lint |
| AI content lấn át personal thinking | Tách `source`, `generated`, `personal`, `decision` |
| Agent duplicate page | Index-first, canonical page rule, duplicate lint |
| Privacy leak qua MCP/web | Data classification, local-only zone, explicit consent |
| Context quá lớn | Progressive disclosure, indexes, subagents, compaction |
| Generated view trở thành source of truth | Raw immutable + Git history + provenance |

## 8. Initial folder proposal

```text
knowledge/
├── raw/
│   └── assets/
├── wiki/
│   ├── index.md
│   ├── log.md
│   ├── concepts/
│   ├── entities/
│   ├── sources/
│   ├── decisions/
│   └── syntheses/
└── schema.md
```

Đây là proposal để thử nghiệm, chưa tự động áp dụng vào `dev-notes`.

