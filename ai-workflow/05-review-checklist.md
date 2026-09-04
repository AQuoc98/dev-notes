# 05 — Review checklist

## 1. Câu hỏi chiến lược

- [ ] Mục tiêu chính là tăng output coding, research, learning hay knowledge continuity?
- [ ] Workflow đầu tiên có một outcome observable và một người dùng cụ thể chưa?
- [ ] Ta ưu tiên portability hay tối ưu sâu cho một host trong 4–6 tuần đầu?
- [ ] “Đúng cách” được định nghĩa bằng metric nào: chất lượng, tốc độ, chi phí, privacy, hay khả năng kiểm chứng?

## 2. Câu hỏi về scope và autonomy

- [ ] Agent được phép đọc/sửa/gọi API/gửi message tới đâu?
- [ ] Side effect nào luôn cần human approval?
- [ ] Có dry-run, rollback và audit log không?
- [ ] Có data local-only hoặc sensitive data không được đưa vào MCP/web không?

## 3. Câu hỏi về primitive

- [ ] Rule này có phải luôn đúng không? Nếu có, để ở guidance.
- [ ] Đây là procedure lặp lại không? Nếu có, viết skill.
- [ ] Cần hệ thống ngoài workspace không? Nếu có, thiết kế MCP.
- [ ] Việc phải chạy theo event và không cần reasoning không? Nếu có, dùng hook/CI.
- [ ] Task có noisy/parallel/chuyên biệt không? Nếu có, cân nhắc subagent.
- [ ] Cần chia sẻ/reuse qua repo/team không? Nếu có, đóng gói plugin.

## 4. Checklist cho artifact

- [ ] Có owner và intended scope.
- [ ] Có input/output contract.
- [ ] Có examples và edge cases.
- [ ] Có negative scope: khi nào không dùng.
- [ ] Có version/date và dependency list.
- [ ] Có security review tương xứng với quyền.
- [ ] Có test case và failure mode.
- [ ] Có đường rollback hoặc disable.
- [ ] Có link về source/decision liên quan.

## 5. Checklist cho second brain

- [ ] Raw source immutable/append-only.
- [ ] Wiki generated được đánh dấu rõ.
- [ ] Mỗi claim quan trọng có provenance.
- [ ] Contradiction không bị âm thầm overwrite.
- [ ] Index và log có thể kiểm tra bằng tool đơn giản.
- [ ] Có cơ chế file-back cho answer/decision hữu ích.
- [ ] Có quy định expiry/stale review.
- [ ] Có separation giữa personal judgment và AI synthesis.

## 6. Review gate đề xuất

### Gate A — Sau framing

Chốt use case, “tiny agent”, data boundary, source of truth và 3 experiments đầu tiên.

### Gate B — Sau portable prototype

Chốt skill format, task brief, output contract và mức adapter chấp nhận được.

### Gate C — Trước external action

Chốt permissions, hooks, approval, rollback, audit và injection tests.

### Gate D — Trước packaging

Chốt versioning, supported hosts, install instructions, trust model và maintenance owner.

