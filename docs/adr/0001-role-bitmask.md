# 0001 — Role lưu dạng bitmask, không có bảng Role

- **Status:** Accepted
- **Date:** 2026-09-13
- **Context:** Hệ thống chỉ có 3 vai trò cố định (Customer, Vendor, Admin), không cần Admin tạo role mới lúc runtime, không cần permission chi tiết. Một user có thể giữ nhiều vai trò cùng lúc (Admin kiêm Vendor của gian hàng sàn).
- **Decision:** `[Flags] enum UserRole { Customer = 1, Vendor = 2, Admin = 4 }`, lưu `int` ở cột `users.roles`. Không có bảng `roles` / `user_roles`. Khi phát JWT, tách bitmask thành từng claim `role` riêng để `[Authorize(Roles = "Vendor")]` hoạt động.
- **Consequences:**
  - Được: bỏ 2 bảng + 1 join; kiểm tra role là phép AND.
  - Mất: DB không ràng buộc giá trị hợp lệ (validate ở application); không index theo role; thêm role = sửa enum + deploy.
  - Nếu sau này cần permission chi tiết (nhân viên cửa hàng chỉ được xử lý đơn) → ADR mới, tách bảng.
