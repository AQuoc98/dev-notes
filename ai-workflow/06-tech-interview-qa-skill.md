# 06 — Tech Interview Q&A skill guide

> Hướng dẫn sử dụng skill `$tech-interview-qa` trong repository này.

Skill là nguồn hướng dẫn chính cho việc xử lý và cập nhật tài liệu Q&A phỏng vấn kỹ thuật. Bạn chỉ cần gửi đầu vào ngắn gọn; không cần lặp lại quy trình đầy đủ trong mỗi prompt.

## Cách dùng nhanh

### Với URL bài viết hoặc video

```text
$tech-interview-qa
Tài liệu đích: typescript.md
URL: https://example.com/article
```

### Với câu hỏi tự sưu tầm

```text
$tech-interview-qa
Tài liệu đích: typescript.md

Câu hỏi:
1. What is event delegation?
2. When should I use a message queue?
```

Bạn có thể gửi thêm câu trả lời hoặc ghi chú thô nếu có:

```text
$tech-interview-qa
Tài liệu đích: interview.md

Câu hỏi:
1. What is event delegation?

Ghi chú sưu tầm:
- [Dán nội dung ở đây]
```

Nếu không ghi `Tài liệu đích`, skill dùng [`interview.md`](../interview.md). Target phải là một file Markdown cụ thể và tồn tại trong repository.

## Skill sẽ thực hiện gì?

Ở Giai đoạn 1, skill sẽ:

1. Đọc toàn bộ tài liệu đích.
2. Kiểm tra từng câu hỏi và nội dung liên quan đã tồn tại.
3. Đánh dấu từng mục là `Mới`, `Liên quan` hoặc `Trùng lặp`.
4. Viết lại câu hỏi và câu trả lời theo hướng dễ hiểu, chính xác, phù hợp phỏng vấn và nhất quán với tài liệu đích.
5. Thông báo section/câu hỏi liên quan và đề xuất nên thêm, mở rộng, gộp hoặc bỏ qua.
6. Trình bày proposal để bạn review.

Giai đoạn 1 không sửa file. Nếu chỉ gửi câu hỏi mà không có câu trả lời, skill có thể soạn câu trả lời và đánh dấu rõ phần bổ sung đó trong proposal.

## Phê duyệt và cập nhật

Sau khi review proposal, gửi:

```text
APPROVE
```

Hoặc approval có giới hạn:

```text
APPROVE. Chỉ cập nhật câu hỏi 1 và 3 trong typescript.md.
```

Skill chỉ cập nhật đúng tài liệu đích sau approval, giữ nguyên nội dung không liên quan, đọc lại file và kiểm tra diff.

Nếu muốn chỉnh proposal trước khi cập nhật, chỉ cần nói rõ yêu cầu, ví dụ:

```text
Bỏ câu hỏi về logging, gộp câu hỏi caching với nội dung hiện có. Chưa cập nhật file.
```
