# Design — tài liệu thiết kế trước khi code

Thứ tự đọc có ý nghĩa (từ ngoài vào trong):

| File | Cấp | Trả lời |
|---|---|---|
| [01-system-context.md](./01-system-context.md) | C4-L1 | Ai dùng hệ thống, hệ thống nói chuyện với dịch vụ ngoài nào |
| [02-conceptual-model.md](./02-conceptual-model.md) | Conceptual | Có những entity nào, quan hệ ra sao (chưa PK/FK) |
| `03-container.md` | C4-L2 | FE / API / DB / adapter ngoài — *chưa viết* |
| `04-logical-model.md` | Logical/ERD | PK, FK, kiểu dữ liệu — hoặc trỏ sang `../erd/` |

Sau khi code chạy, phần "hiện tại là gì" được chép sang `../ard/`; file ở đây giữ nguyên làm mốc so sánh.
