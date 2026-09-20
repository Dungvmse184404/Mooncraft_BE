# MoonCraft — Conceptual Data Model

## Entity

> Rà soát 2026-09-21 theo phạm vi lab: bỏ mọi thuộc tính không phục vụ trực tiếp một chức năng phải demo. Cột audit `createdAt / updatedAt` **không liệt kê** — khai một lần ở Physical qua `BaseEntity` cho mọi bảng.

### Nhóm 1 — Người dùng & địa lý
| Entity | Ý nghĩa | Thuộc tính chính |
|---|---|---|
| User | Tài khoản đăng nhập, dùng chung cho mọi vai trò | email, passwordHash, fullName, phone, status, **roles (bitmask: Customer=1, Vendor=2, Admin=4)** |
| Address | Địa chỉ giao hàng của khách (phí ship GHN tính theo phường, không cần toạ độ) | receiverName, phone, addressLine, isDefault |
| Province / District / Ward | Đơn vị hành chính (seed sẵn), Address và Store trỏ tới Ward; District/Ward mang mã GHN để gọi API phí & tạo vận đơn | name, code, ghnDistrictId / ghnWardCode |

### Nhóm 2 — Cửa hàng & danh mục
| Entity | Ý nghĩa | Thuộc tính chính |
|---|---|---|
| Store | Cửa hàng của vendor; gian hàng của sàn cũng là một Store | name, description, logoUrl, addressLine, lat, lng, phone, status (Pending/Approved/Suspended), isPlatformOwned |
| Category | Loại bánh, Admin quản lý, dùng chung toàn sàn, phẳng (không cha–con) | name |

### Nhóm 3 — Sản phẩm
| Entity | Ý nghĩa | Thuộc tính chính |
|---|---|---|
| Product | Bài đăng bánh (để duyệt, tìm kiếm, đánh giá) — **không có giá/tồn kho** | name, description, status |
| ProductItem | Biến thể của một bánh theo cỡ — nơi định giá và trừ kho | name (nhãn: "150g"), weight, price, stockQuantity |
| ProductImage | Ảnh của bánh, ảnh đầu (sortOrder = 0) là ảnh đại diện | url, sortOrder |
| GiftBox | Hộp bánh cố định do cửa hàng định nghĩa (**không có hộp tự chọn**) | name, description, price, stockQuantity, status, imageUrl |
| GiftBoxItem | ProductItem nào trong hộp, bao nhiêu cái | quantity |

### Nhóm 4 — Giỏ hàng, đơn hàng, thanh toán
| Entity | Ý nghĩa | Thuộc tính chính |
|---|---|---|
| CartItem | Dòng trong giỏ của một User (không có entity Cart riêng), trỏ tới ProductItem hoặc GiftBox; giá lấy hiện hành | itemType, quantity |
| Order | Mỗi cửa hàng một Order, tách từ một lần checkout; Id dùng làm mã đơn | status (Pending/Confirmed/Preparing/Shipping/Delivered/Cancelled), shippingFee, discount, total, note |
| OrderItem | Dòng hàng trong đơn, snapshot tên/giá tại thời điểm mua | itemType, itemName, unitPrice, quantity |
| Payment | Một giao dịch cho cả lần checkout (nhiều Order) | provider (VNPay/MoMo/COD), amount, status, transactionRef, paidAt |
| Shipment | Vận đơn GHN (0..1 mỗi Order) | carrier (GHN/SelfDelivery), trackingCode (order_code GHN), status, expectedDeliveryAt |

### Nhóm 5 — Khuyến mãi, đánh giá
| Entity | Ý nghĩa | Thuộc tính chính |
|---|---|---|
| Voucher | Mã giảm giá thuộc Store hoặc toàn sàn (Store = null) | code, discountType (Percent/Fixed), value, startAt, endAt |
| Review | Đánh giá sau khi nhận hàng, gắn với OrderItem | rating (1–5), comment |

## Quan hệ

```
User        ─── 1:N ──▶ Address
Province    ─── 1:N ──▶ District
District    ─── 1:N ──▶ Ward
Ward        ─── 1:N ──▶ Address
Ward        ─── 1:N ──▶ Store
User        ─── 1:N ──▶ Store             (một chủ nhiều điểm bán; Admin sở hữu Store isPlatformOwned. Chốt 2026-09-21)
User        ─── 1:N ──▶ CartItem
User        ─── 1:N ──▶ Order             (vai trò khách mua)
User        ─── 1:N ──▶ Review

Store       ─── 1:N ──▶ Product
Store       ─── 1:N ──▶ GiftBox
Store       ─── 1:N ──▶ Order
Store       ─── 1:N ──▶ Voucher           (nullable: voucher toàn sàn)
Store       ─── 1:N ──▶ Review

Category    ─── 1:N ──▶ Product

Product     ─── 1:N ──▶ ProductImage
Product     ─── 1:N ──▶ ProductItem       (≥1 item)
Product     ─── 1:N ──▶ Review            (đánh giá bài đăng, không đánh giá từng cỡ)

ProductItem ─── 1:N ──▶ GiftBoxItem
GiftBox     ─── 1:N ──▶ GiftBoxItem       (ruột hộp: biến thể nào, bao nhiêu cái; cùng Store với GiftBox)
ProductItem ─── 1:N ──▶ CartItem
ProductItem ─── 1:N ──▶ OrderItem

CartItem    ─── N:1 ──▶ ProductItem | GiftBox (một trong hai, theo itemType)

Order       ─── 1:N ──▶ OrderItem
Order       ─── N:1 ──▶ Address           (snapshot địa chỉ giao)
Order       ─── N:1 ──▶ Payment           (nhiều Order chung một Payment)
Order       ─── 0..1  ──▶ Voucher
Order       ─── 1:0..1 ▶ Shipment

OrderItem   ─── N:1 ──▶ ProductItem | GiftBox (theo itemType)
OrderItem   ─── 1:0..1 ▶ Review
```

## Ràng buộc cần chú thích trên sơ đồ

0. Role dùng bitmask (`[Flags] enum UserRole { Customer=1, Vendor=2, Admin=4 }`) lưu trong `User.roles`; không có bảng Role/UserRole. Admin kiêm vendor = 6. Nếu sau này cần permission chi tiết mới tách bảng.

1. Checkout 1 → N Order → 1 Payment: tách giỏ theo Store thành nhiều Order, một Payment; cổng báo thành công thì cập nhật tất cả Order.
2. CartItem / OrderItem trỏ tới ProductItem hoặc GiftBox (polymorphic). **Đã chọn ở Logical:** 2 FK nullable (`ProductItemId?`, `GiftBoxId?`) + `ItemType`; ràng buộc đúng một trong hai khác null.
3. Store.isPlatformOwned: gian hàng của sàn không có entity riêng; không có hoa hồng / đối soát (bỏ Payout). **Product luôn có StoreId (NOT NULL)** — bánh của sàn thuộc store của sàn, không dùng `StoreId NULL` để đánh dấu.
4. **Có `ProductItem`** (biến thể theo cỡ, chốt 2026-09-21): `Product` là bài đăng (duyệt / tìm / review), `ProductItem` là cỡ (giá / tồn kho / bỏ vào giỏ). Mọi thứ bán được hoặc nằm trong hộp trỏ `ProductItem`. Ràng buộc: `UNIQUE(productId, weight)`, mỗi Product ≥1 item; item hiển thị mặc định = item rẻ nhất. Trừ kho: dòng Product trừ `ProductItem.stock`; dòng GiftBox trừ `GiftBox.stock` **và** từng `ProductItem` trong `GiftBoxItem` × số hộp.
5. `Review` gắn với `OrderItem` (0..1, unique) để chỉ người đã mua mới đánh giá; `StoreId`, `ProductId` trên Review là denormalize có chủ đích để query rating nhanh.

## Đối chiếu với ERD (2026-09-21, sau rà soát)

Ba tab **Conceptual / Logical / Physical** đã đồng bộ: 19 entity, 29 quan hệ. Đã bỏ hẳn: `Cart` (gộp vào `CartItem.userId`), `Payout`, `OrderStatusHistory`, `RefreshToken`, `CartItemDetail` / `OrderItemDetail`, `Address.lat/lng`. Giữ: `Shipment` (tích hợp GHN), `Voucher`, `Review`, `ProductImage`, `Province/District/Ward` (+ mã GHN).

Chi tiết kiểu dữ liệu, index, ON DELETE, enum: [`04-physical-model.md`](./04-physical-model.md).

Giả định đang dùng ở Physical, ghi ở ADR 0005 (Proposed) — xác nhận hoặc đổi: **PostgreSQL** (theo `AGENTS.md`), PK `int identity`, tiền `numeric(12,0)`, enum `smallint`.
