# MoonCraft — Conceptual Data Model

## Entity

### Nhóm 1 — Người dùng & phân quyền
| Entity | Ý nghĩa | Thuộc tính chính |
|---|---|---|
| User | Tài khoản đăng nhập, dùng chung cho mọi vai trò | email, passwordHash, fullName, phone, avatar, status, **roles (bitmask: Customer=1, Vendor=2, Admin=4)**, createdAt |
| Address | Địa chỉ giao hàng của khách | receiverName, phone, addressLine, ward, district, city, lat, lng, isDefault |

### Nhóm 2 — Cửa hàng & danh mục
| Entity | Ý nghĩa | Thuộc tính chính |
|---|---|---|
| Store | Cửa hàng của vendor; gian hàng của sàn cũng là một Store | name, slug, description, logo, coverImage, addressLine, city, lat, lng, phone, status (Pending/Approved/Suspended), isPlatformOwned, commissionRate, approvedAt |
| Category | Loại bánh, Admin quản lý, dùng chung toàn sàn | name, slug, parent (tùy chọn) |

### Nhóm 3 — Sản phẩm
| Entity | Ý nghĩa | Thuộc tính chính |
|---|---|---|
| Product | Bánh lẻ | name, slug, description, price, weight, stockQuantity, status, ingredients, shelfLifeDays |
| ProductImage | Ảnh của bánh | url, sortOrder, isPrimary |
| GiftBox | Hộp bánh (combo) do cửa hàng định nghĩa | name, description, price, boxType (Fixed/Custom), maxSlots, stockQuantity, status, image |
| GiftBoxItem | Bánh trong hộp + số lượng; với hộp Custom là danh sách bánh được phép chọn | quantity |

### Nhóm 4 — Giỏ hàng, đơn hàng, thanh toán
| Entity | Ý nghĩa | Thuộc tính chính |
|---|---|---|
| Cart | Giỏ hàng của khách, chứa hàng nhiều cửa hàng | updatedAt |
| CartItem | Dòng trong giỏ, trỏ tới Product hoặc GiftBox | itemType, quantity, unitPriceSnapshot |
| Order | Mỗi cửa hàng một Order, tách từ một lần checkout | orderCode, status (Pending/Confirmed/Preparing/Shipping/Delivered/Cancelled), subtotal, shippingFee, discount, total, note, placedAt |
| OrderItem | Dòng hàng trong đơn, snapshot tên/giá | itemType, itemName, unitPrice, quantity, lineTotal |
| Payment | Một giao dịch cho cả lần checkout (nhiều Order) | provider (VNPay/MoMo/COD), amount, status, transactionRef, paidAt |
| OrderStatusHistory | Lịch sử chuyển trạng thái | fromStatus, toStatus, changedAt, note |
| Shipment (tùy chọn) | Vận đơn GHN/GHTK | carrier, trackingCode, status, fee |

### Nhóm 5 — Khuyến mãi, đánh giá, đối soát
| Entity | Ý nghĩa | Thuộc tính chính |
|---|---|---|
| Voucher | Mã giảm giá thuộc Store hoặc toàn sàn (Store = null) | code, discountType (Percent/Fixed), value, minOrderValue, maxDiscount, startAt, endAt, usageLimit |
| Review | Đánh giá sau khi nhận hàng, gắn với OrderItem | rating (1–5), comment, images, createdAt |
| Payout (tùy chọn) | Kỳ đối soát trả tiền vendor sau khi trừ hoa hồng | periodStart, periodEnd, grossAmount, commissionAmount, netAmount, status |

## Quan hệ

```
User        ─── 1:N ──▶ Address
User        ─── 1:1 ──▶ Store             (vendor sở hữu một cửa hàng; Admin sở hữu Store isPlatformOwned)
User        ─── 1:1 ──▶ Cart
User        ─── 1:N ──▶ Order             (vai trò khách mua)
User        ─── 1:N ──▶ Review

Store       ─── 1:N ──▶ Product
Store       ─── 1:N ──▶ GiftBox
Store       ─── 1:N ──▶ Order
Store       ─── 1:N ──▶ Voucher           (nullable: voucher toàn sàn)
Store       ─── 1:N ──▶ Payout
Store       ─── 1:N ──▶ Review

Category    ─── 1:N ──▶ Product
Category    ─── 1:N ──▶ Category          (parent, tùy chọn)

Product     ─── 1:N ──▶ ProductImage
Product     ─── N:N ──▶ GiftBox           (qua GiftBoxItem, có quantity)
Product     ─── 1:N ──▶ Review

Cart        ─── 1:N ──▶ CartItem
CartItem    ─── N:1 ──▶ Product | GiftBox (một trong hai, theo itemType)

Order       ─── 1:N ──▶ OrderItem
Order       ─── N:1 ──▶ Address           (snapshot địa chỉ giao)
Order       ─── N:1 ──▶ Payment           (nhiều Order chung một Payment)
Order       ─── 0..1  ──▶ Voucher
Order       ─── 1:N ──▶ OrderStatusHistory
Order       ─── 1:0..1 ▶ Shipment
Order       ─── N:1 ──▶ Payout

OrderItem   ─── N:1 ──▶ Product | GiftBox (theo itemType)
OrderItem   ─── 1:0..1 ▶ Review
```

## Ràng buộc cần chú thích trên sơ đồ

0. Role dùng bitmask (`[Flags] enum UserRole { Customer=1, Vendor=2, Admin=4 }`) lưu trong `User.roles`; không có bảng Role/UserRole. Admin kiêm vendor = 6. Nếu sau này cần permission chi tiết mới tách bảng.

1. Checkout 1 → N Order → 1 Payment: tách giỏ theo Store thành nhiều Order, một Payment; cổng báo thành công thì cập nhật tất cả Order.
2. CartItem / OrderItem trỏ tới Product hoặc GiftBox (polymorphic). Khi xuống ERD: 2 FK nullable, hoặc gộp thành entity cha `SellableItem`.
3. Store.isPlatformOwned: gian hàng của sàn không có entity riêng; commissionRate = 0, Payout có thể bỏ qua.
