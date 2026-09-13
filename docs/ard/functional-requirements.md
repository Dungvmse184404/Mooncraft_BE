# Functional Requirements — MoonCraft

Tổng hợp chức năng **đã triển khai**, tổ chức theo bounded context (khớp 1-1 với `bounded-contexts/`).
Tài liệu sống: thêm dòng khi có chức năng mới, không tạo lại từ đầu.

## Quy ước ID

`FR-<CTX>-<NN>` — `<CTX>` theo bảng ở `bounded-contexts/README.md`; `<NN>` tăng dần trong context, không tái sử dụng số đã gỡ (đánh dấu `(đã gỡ)`).

## Actor

| Actor | Ghi chú |
|---|---|
| Anonymous | Chưa đăng nhập |
| Customer | `UserRole.Customer = 1`, mặc định khi đăng ký |
| Vendor | `UserRole.Vendor = 2`, cấp khi cửa hàng được duyệt |
| Admin | `UserRole.Admin = 4`; đồng thời là Vendor của gian hàng `IsPlatformOwned` |

## Identity — `identity.md`

| ID | Chức năng | Actor | Ghi chú |
|---|---|---|---|
| FR-IDT-01 | *(chưa có)* | | |
