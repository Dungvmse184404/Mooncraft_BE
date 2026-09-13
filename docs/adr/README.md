# ADR — Architecture Decision Records

Nhật ký **quyết định và lý do**. Mỗi quyết định một file, không sửa file cũ — đổi ý thì viết ADR mới và đánh dấu cái cũ `Superseded by`.

Tên file: `NNNN-ten-quyet-dinh.md` (số tăng dần toàn cục). Mở đầu mỗi file bắt buộc có:

```
# NNNN — <Tiêu đề>
- **Status:** Proposed | Accepted | Implemented | Superseded by NNNN
- **Date:** YYYY-MM-DD
- **Context:** tình huống / vấn đề
- **Decision:** quyết định
- **Consequences:** được gì, mất gì, việc phải làm kèm
```

| # | File | Status | Nội dung |
|---|---|---|---|
| 0001 | [0001-role-bitmask.md](./0001-role-bitmask.md) | Accepted | Role lưu bitmask trên `users.roles`, không có bảng Role |
| 0002 | [0002-platform-store-as-vendor.md](./0002-platform-store-as-vendor.md) | Accepted | Gian hàng của sàn là một Store `IsPlatformOwned`, không tách luồng |
| 0003 | [0003-checkout-split-orders-single-payment.md](./0003-checkout-split-orders-single-payment.md) | Proposed | Một checkout → N Order (mỗi cửa hàng một đơn) → 1 Payment |
