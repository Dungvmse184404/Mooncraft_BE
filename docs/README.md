# MoonCraft — Tài liệu backend

Điểm vào duy nhất của mọi tài liệu. Bối cảnh dự án dành cho AI (giai đoạn, quyết định đã chốt, việc đang mở): [`../../AGENTS.md`](../../AGENTS.md) ở gốc repo.

Đọc theo nhu cầu:

| Bạn cần | Đọc |
|---|---|
| Hiểu dự án làm gì, ai dùng, tích hợp gì | [design/01-system-context.md](./design/01-system-context.md) |
| Entity và quan hệ nghiệp vụ | [design/02-conceptual-model.md](./design/02-conceptual-model.md) · ERD: [erd/](./erd/README.md) |
| Kiến trúc code, layer, pattern, quy ước | [ard/architecture-core/](./ard/architecture-core/) |
| Một phân hệ (giỏ hàng, đơn, thanh toán…) làm gì, luồng, rule | [ard/bounded-contexts/](./ard/bounded-contexts/) |
| Danh sách chức năng đã làm (FR-ID) | [ard/functional-requirements.md](./ard/functional-requirements.md) |
| Vì sao hồi đó quyết định như vậy | [adr/](./adr/) |
| Endpoint nhận gì, trả gì | [api-documents/](./api-documents/) |
| Quy tắc branch / commit / PR | [CONTRIBUTING.md](./CONTRIBUTING.md) |

## Ba loại tài liệu — đừng trộn

| Loại | Thư mục | Tính chất |
|---|---|---|
| **Thiết kế** (trước khi code) | `design/` | Viết ở giai đoạn đầu; khi code đã chạy, nội dung "hiện tại" chuyển sang `ard/`, file gốc giữ nguyên làm mốc |
| **Tham chiếu** (hiện tại là gì) | `ard/`, `api-documents/` | Sửa đè để luôn khớp code. Sai lệch với code = bug tài liệu |
| **Quyết định** (lịch sử) | `adr/` | Chỉ thêm file mới, không sửa file cũ. Đổi ý → ADR mới, đánh dấu cái cũ `Superseded` |

## Quy ước

- Định danh code, tên bảng, endpoint: tiếng Anh. Nội dung tài liệu: tiếng Việt.
- File đánh số `NN-ten-file.md` khi thứ tự đọc có ý nghĩa; không đánh số khi không.
- Mỗi thư mục có `README.md` làm mục lục của thư mục đó.
- Thay đổi schema / endpoint / business rule → cập nhật `ard/` và `api-documents/` **trong cùng PR**.
