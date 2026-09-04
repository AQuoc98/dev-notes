# Agent Skills note

## Core facts

Agent Skills là format mở để đóng gói domain knowledge và workflow tái sử dụng. Minimum shape:

```text
skill-name/
├── SKILL.md
├── scripts/       # optional
├── references/    # optional
└── assets/        # optional
```

`SKILL.md` có YAML frontmatter với `name` và `description` bắt buộc. Standard khuyến nghị:

- `name` lowercase, số và hyphen; trùng tên directory;
- `description` nói cả skill làm gì và khi nào dùng;
- body nên có steps, examples, edge cases;
- file dài nên tách references;
- relative paths được resolve từ skill root;
- skill nên được validate trước khi share.

## Progressive disclosure

1. Discovery: model thấy name/description.
2. Activation: model load full `SKILL.md` khi task phù hợp.
3. Resources: scripts/references/assets chỉ load khi cần.

Thiết kế skill vì vậy phải tối ưu description và giữ body vừa đủ. Cài nhiều skill nhưng nhồi full nội dung vào system prompt sẽ phá lợi ích của standard.

## Portability rules đề xuất

- Viết core skill không phụ thuộc vendor.
- Không giả định agent có tool cụ thể; mô tả capability và fallback.
- Dùng path tương đối cho resources.
- Ghi rõ prerequisites và side effects.
- Có “When not to use”.
- Nếu cần host-specific behavior, tách `adapters/codex.md` và `adapters/claude.md` thay vì làm body chung mơ hồ.
- Skill có side effect nên cần explicit invocation hoặc permission gate phù hợp.

## Skill quality rubric

| Dimension | Câu hỏi |
| --- | --- |
| Trigger | Description có đủ cụ thể, ít false positive không? |
| Scope | Skill có một nhiệm vụ chính không? |
| Procedure | Steps có thứ tự và stop conditions không? |
| Evidence | Có cách verify outcome không? |
| Safety | Có data boundary và side-effect warning không? |
| Context | Full body có nhỏ, rõ, progressive không? |
| Portability | Chạy được trên hơn một host không? |
| Maintenance | Có version/owner/test fixture không? |

