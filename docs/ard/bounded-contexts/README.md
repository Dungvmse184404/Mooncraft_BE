# Bounded Contexts

Mỗi phân hệ một file, cùng một khung mục để dễ tra chéo:

```
# <Context>
Phạm vi · Entity/bảng chính · Controller/endpoint chính
## Luồng: <tên luồng>        (một mục mỗi luồng quan trọng)
## Business rules & validation
## Edge case & lỗi thường gặp
## Liên quan                  (ADR, context khác)
```

| Context | Mã FR | Phạm vi dự kiến | File |
|---|---|---|---|
| Identity | IDT | Đăng ký, đăng nhập, JWT, role bitmask, hồ sơ | `identity.md` |
| Vendor | VEN | Đăng ký cửa hàng, vị trí, duyệt, gian hàng của sàn | `vendor.md` |
| Catalog | CAT | Bánh, biến thể, ảnh, danh mục, hộp bánh | `catalog.md` |
| Sales | SAL | Giỏ hàng nhiều cửa hàng, checkout tách đơn, trạng thái đơn | `sales.md` |
| Payment | PAY | VNPay/MoMo, IPN, COD, đối soát (payout) | `payment.md` |
| Shipping | SHP | Phí ship theo khoảng cách, GHN (tùy chọn) | `shipping.md` |
| Promotion | PRO | Voucher cửa hàng / toàn sàn | `promotion.md` |
| Review | REV | Đánh giá sản phẩm | `review.md` |
| Notification | NTF | Email OTP, xác nhận đơn, duyệt cửa hàng | `notification.md` |
