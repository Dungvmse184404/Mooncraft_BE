# 0003 — Một checkout tách thành N Order, một Payment

- **Status:** Proposed
- **Date:** 2026-09-13
- **Context:** Giỏ hàng chứa bánh của nhiều cửa hàng. Mỗi cửa hàng cần xử lý và giao đơn của mình độc lập, nhưng khách chỉ muốn thanh toán một lần.
- **Decision:** Khi checkout, nhóm `CartItem` theo `Store` → tạo một `Order` mỗi store (`Store 1─N Order`), tất cả trỏ về một `Payment` (`Payment 1─N Order`). Cổng thanh toán báo thành công → cập nhật mọi Order thuộc Payment đó cùng lúc, trong một transaction.
- **Consequences:**
  - Được: vendor chỉ thấy đơn của mình; khách quét mã một lần; phí ship tính theo từng store.
  - Mất: huỷ một đơn sau khi đã trả tiền → phải hoàn tiền một phần; `Payment.amount` = tổng các Order, phải kiểm tra khớp khi nhận IPN.
  - Việc kèm: ERD hiện vẽ `Order 1─N Payment` (ngược chiều) — sửa lại khi ADR này được chấp nhận.
