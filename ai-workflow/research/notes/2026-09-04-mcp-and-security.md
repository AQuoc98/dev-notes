# MCP và security note

## MCP primitives

MCP tách ba primitive:

| Primitive | Control model | Dùng cho |
| --- | --- | --- |
| Prompts | User-controlled | Template/interaction bắt đầu bởi người dùng |
| Resources | Application-controlled | Context/data do host quản lý |
| Tools | Model-controlled | Function để model gọi và tạo side effect |

MCP là capability boundary, không phải workflow guarantee. Skill hoặc code workflow vẫn cần mô tả cách dùng tool, query patterns, validation và rollback.

## Security baseline

- Principle of least privilege: mỗi server chỉ expose capability cần thiết.
- Tách read tool và write tool; dùng dry-run cho write.
- Schema tool phải rõ: input, output, error, idempotency và side effect.
- Authentication/authorization nằm ở server/integration; không để credential trong skill/repo.
- Human phải thấy tool nào được expose và có thể deny action.
- Tool output, repo file, web page và document đều có thể chứa prompt injection.
- Đừng coi approval prompt là security boundary duy nhất; cần sandbox, policy và post-action verification.
- Log tool calls đủ để audit nhưng không làm lộ secrets.

## Hook vs MCP vs skill

| Nhu cầu | Primitive |
| --- | --- |
| “Hãy dùng API này để đọc dữ liệu” | MCP |
| “Khi đọc dữ liệu, áp dụng schema/query/validation này” | Skill |
| “Mỗi lần trước write phải kiểm tra policy” | Hook/policy |
| “Sau write phải chạy test/scan” | Hook/CI |
| “Nếu không đạt điều kiện thì không được tiếp tục” | Deterministic gate, không chỉ instruction |

## Threat cases cho research

1. Malicious instruction trong README hoặc source file.
2. Tool output yêu cầu agent gửi secret ra ngoài.
3. MCP server giả mạo tên/description hoặc trả output không đúng schema.
4. Hook/plugin chạy script ngoài expected directory.
5. Subagent được inherit permission rộng hơn vai trò cần.
6. Wiki compile một hallucination thành durable claim.
7. Approval fatigue làm người dùng approve hàng loạt mà không đọc.

