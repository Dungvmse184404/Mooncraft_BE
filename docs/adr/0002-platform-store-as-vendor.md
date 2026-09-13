# 0002 — Gian hàng của sàn là một Store bình thường

- **Status:** Accepted
- **Date:** 2026-09-13
- **Context:** Sàn muốn tự bán bánh bên cạnh các vendor. Có hai cách: tách luồng "sản phẩm của sàn" riêng, hoặc coi sàn là một vendor.
- **Decision:** Một bản ghi `Store` với `IsPlatformOwned = true`, `CommissionRate = 0`, owner là tài khoản Admin. Giỏ hàng, đơn hàng, voucher, review dùng chung toàn bộ logic; chỉ bỏ qua Payout cho store này.
- **Consequences:**
  - Được: không có nhánh `if (isPlatform)` rải trong code bán hàng; Admin dùng đúng màn hình vendor để quản lý gian hàng.
  - Mất: store của sàn được tạo sẵn ở trạng thái Approved khi seed, không qua luồng duyệt; báo cáo doanh thu sàn cần tách dòng `IsPlatformOwned`.
