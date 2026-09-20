# MoonCraft — Physical Data Model (PostgreSQL)

> Sinh từ tab **Physical Diagram** trong `../erd/PRN232_Mooncraft.drawio` (2026-09-21). Khi sửa một bên, sửa bên kia trong cùng commit.

## Quy ước

- Bảng `snake_case` số nhiều, cột `snake_case`, FK `{bảng_số_ít}_id` (riêng `stores.owner_id → users.id`).
- PK `id int GENERATED ALWAYS AS IDENTITY`.
- **Mọi bảng** có `created_at timestamptz NOT NULL DEFAULT now()`, `updated_at timestamptz NOT NULL` — khai qua `BaseEntity`, không liệt kê lại bên dưới. Không dùng soft-delete.
- Tiền: `numeric(12,0)` (VND, không lẻ). Enum lưu `smallint`; giá trị ở mục cuối.
- Với EF Core: `UseNpgsql(...).UseSnakeCaseNamingConventions()`; entity C# `PascalCase` số ít (`ProductItem` → `product_items`).

## Bảng

### `users` ← `User`

| Khoá | Cột |
|---|---|
| PK | `id : int identity` |
|  | `email : varchar(256) NOT NULL` |
|  | `password_hash : varchar(256) NOT NULL` |
|  | `full_name : varchar(100) NOT NULL` |
|  | `phone : varchar(20) NOT NULL` |
|  | `status : smallint NOT NULL` |
|  | `roles : int NOT NULL` |
| UQ | `(email)` |

### `provinces` ← `Province`

| Khoá | Cột |
|---|---|
| PK | `id : int identity` |
|  | `name : varchar(100) NOT NULL` |
|  | `code : varchar(20) NOT NULL` |
| UQ | `(code)` |

### `districts` ← `District`

| Khoá | Cột |
|---|---|
| PK | `id : int identity` |
| FK | `province_id : int NOT NULL` |
|  | `name : varchar(100) NOT NULL` |
|  | `code : varchar(20) NOT NULL` |
|  | `ghn_district_id : int NULL` |
| UQ | `(code)` |
| IX | `(province_id)` |

### `wards` ← `Ward`

| Khoá | Cột |
|---|---|
| PK | `id : int identity` |
| FK | `district_id : int NOT NULL` |
|  | `name : varchar(100) NOT NULL` |
|  | `code : varchar(20) NOT NULL` |
|  | `ghn_ward_code : varchar(20) NULL` |
| UQ | `(code)` |
| IX | `(district_id)` |

### `addresses` ← `Address`

| Khoá | Cột |
|---|---|
| PK | `id : int identity` |
| FK | `user_id : int NOT NULL` |
| FK | `ward_id : int NOT NULL` |
|  | `receiver_name : varchar(100) NOT NULL` |
|  | `phone : varchar(20) NOT NULL` |
|  | `address_line : varchar(255) NOT NULL` |
|  | `is_default : boolean NOT NULL DEFAULT false` |
| IX | `(user_id)` |

### `stores` ← `Store`

| Khoá | Cột |
|---|---|
| PK | `id : int identity` |
| FK | `owner_id : int NOT NULL → users` |
| FK | `ward_id : int NOT NULL` |
|  | `name : varchar(150) NOT NULL` |
|  | `description : text NULL` |
|  | `logo_url : varchar(500) NULL` |
|  | `address_line : varchar(255) NOT NULL` |
|  | `lat : double precision NULL` |
|  | `lng : double precision NULL` |
|  | `phone : varchar(20) NOT NULL` |
|  | `status : smallint NOT NULL` |
|  | `is_platform_owned : boolean NOT NULL DEFAULT false` |
| IX | `(owner_id)` |
| IX | `(status, lat, lng)` |

### `categories` ← `Category`

| Khoá | Cột |
|---|---|
| PK | `id : int identity` |
|  | `name : varchar(100) NOT NULL` |
| UQ | `(name)` |

### `products` ← `Product`

| Khoá | Cột |
|---|---|
| PK | `id : int identity` |
| FK | `store_id : int NOT NULL` |
| FK | `category_id : int NOT NULL` |
|  | `name : varchar(150) NOT NULL` |
|  | `description : text NULL` |
|  | `status : smallint NOT NULL` |
| IX | `(store_id)` |
| IX | `(category_id)` |

### `product_items` ← `ProductItem`

| Khoá | Cột |
|---|---|
| PK | `id : int identity` |
| FK | `product_id : int NOT NULL` |
|  | `name : varchar(100) NOT NULL` |
|  | `weight : int NOT NULL` |
|  | `price : numeric(12,0) NOT NULL` |
|  | `stock_quantity : int NOT NULL DEFAULT 0` |
| UQ | `(product_id, weight)` |
| CK | `price >= 0 AND stock_quantity >= 0` |

### `product_images` ← `ProductImage`

| Khoá | Cột |
|---|---|
| PK | `id : int identity` |
| FK | `product_id : int NOT NULL` |
|  | `url : varchar(500) NOT NULL` |
|  | `sort_order : int NOT NULL DEFAULT 0` |
| UQ | `(product_id, sort_order)` |

### `gift_boxes` ← `GiftBox`

| Khoá | Cột |
|---|---|
| PK | `id : int identity` |
| FK | `store_id : int NOT NULL` |
|  | `name : varchar(150) NOT NULL` |
|  | `description : text NULL` |
|  | `price : numeric(12,0) NOT NULL` |
|  | `stock_quantity : int NOT NULL DEFAULT 0` |
|  | `status : smallint NOT NULL` |
|  | `image_url : varchar(500) NULL` |
| IX | `(store_id)` |

### `gift_box_items` ← `GiftBoxItem`

| Khoá | Cột |
|---|---|
| PK | `id : int identity` |
| FK | `gift_box_id : int NOT NULL` |
| FK | `product_item_id : int NOT NULL` |
|  | `quantity : int NOT NULL` |
| UQ | `(gift_box_id, product_item_id)` |

### `cart_items` ← `CartItem`

| Khoá | Cột |
|---|---|
| PK | `id : int identity` |
| FK | `user_id : int NOT NULL` |
| FK | `product_item_id : int NULL` |
| FK | `gift_box_id : int NULL` |
|  | `item_type : smallint NOT NULL` |
|  | `quantity : int NOT NULL` |
| UQ | `(user_id, product_item_id)` |
| UQ | `(user_id, gift_box_id)` |
| CK | `đúng một trong product_item_id / gift_box_id` |

### `payments` ← `Payment`

| Khoá | Cột |
|---|---|
| PK | `id : int identity` |
|  | `provider : smallint NOT NULL` |
|  | `amount : numeric(12,0) NOT NULL` |
|  | `status : smallint NOT NULL` |
|  | `transaction_ref : varchar(100) NULL` |
|  | `paid_at : timestamptz NULL` |
| UQ | `(transaction_ref)` |

### `vouchers` ← `Voucher`

| Khoá | Cột |
|---|---|
| PK | `id : int identity` |
| FK | `store_id : int NULL` |
|  | `code : varchar(30) NOT NULL` |
|  | `discount_type : smallint NOT NULL` |
|  | `value : numeric(12,0) NOT NULL` |
|  | `start_at : timestamptz NOT NULL` |
|  | `end_at : timestamptz NOT NULL` |
| UQ | `(code)` |

### `orders` ← `Order`

| Khoá | Cột |
|---|---|
| PK | `id : int identity` |
| FK | `user_id : int NOT NULL` |
| FK | `store_id : int NOT NULL` |
| FK | `address_id : int NOT NULL` |
| FK | `payment_id : int NOT NULL` |
| FK | `voucher_id : int NULL` |
|  | `status : smallint NOT NULL` |
|  | `shipping_fee : numeric(12,0) NOT NULL` |
|  | `discount : numeric(12,0) NOT NULL DEFAULT 0` |
|  | `total : numeric(12,0) NOT NULL` |
|  | `note : text NULL` |
| IX | `(user_id)` |
| IX | `(store_id, status)` |
| IX | `(payment_id)` |

### `order_items` ← `OrderItem`

| Khoá | Cột |
|---|---|
| PK | `id : int identity` |
| FK | `order_id : int NOT NULL` |
| FK | `product_item_id : int NULL` |
| FK | `gift_box_id : int NULL` |
|  | `item_type : smallint NOT NULL` |
|  | `item_name : varchar(200) NOT NULL` |
|  | `unit_price : numeric(12,0) NOT NULL` |
|  | `quantity : int NOT NULL` |
| IX | `(order_id)` |
| CK | `đúng một trong product_item_id / gift_box_id` |

### `shipments` ← `Shipment`

| Khoá | Cột |
|---|---|
| PK | `id : int identity` |
| FK | `order_id : int NOT NULL` |
|  | `carrier : smallint NOT NULL` |
|  | `tracking_code : varchar(50) NULL` |
|  | `status : smallint NOT NULL` |
|  | `expected_delivery_at : timestamptz NULL` |
| UQ | `(order_id)` |
| UQ | `(tracking_code)` |

### `reviews` ← `Review`

| Khoá | Cột |
|---|---|
| PK | `id : int identity` |
| FK | `user_id : int NOT NULL` |
| FK | `product_id : int NOT NULL` |
| FK | `order_item_id : int NOT NULL` |
|  | `rating : smallint NOT NULL` |
|  | `comment : text NULL` |
| UQ | `(order_item_id)` |
| IX | `(product_id)` |
| CK | `rating BETWEEN 1 AND 5` |

## Khoá ngoại & ON DELETE

| Bảng con | → Bảng cha | NULL? | ON DELETE | Lý do |
|---|---|---|---|---|
| `districts` | `provinces` | không | RESTRICT | giữ lịch sử / dữ liệu tham chiếu |
| `wards` | `districts` | không | RESTRICT | giữ lịch sử / dữ liệu tham chiếu |
| `addresses` | `users` | không | CASCADE | con vô nghĩa khi cha mất |
| `addresses` | `wards` | không | RESTRICT | giữ lịch sử / dữ liệu tham chiếu |
| `stores` | `users` | không | RESTRICT | giữ lịch sử / dữ liệu tham chiếu |
| `stores` | `wards` | không | RESTRICT | giữ lịch sử / dữ liệu tham chiếu |
| `products` | `stores` | không | RESTRICT | giữ lịch sử / dữ liệu tham chiếu |
| `products` | `categories` | không | RESTRICT | giữ lịch sử / dữ liệu tham chiếu |
| `product_items` | `products` | không | CASCADE | con vô nghĩa khi cha mất |
| `product_images` | `products` | không | CASCADE | con vô nghĩa khi cha mất |
| `gift_boxes` | `stores` | không | RESTRICT | giữ lịch sử / dữ liệu tham chiếu |
| `gift_box_items` | `gift_boxes` | không | CASCADE | con vô nghĩa khi cha mất |
| `gift_box_items` | `product_items` | không | RESTRICT | giữ lịch sử / dữ liệu tham chiếu |
| `cart_items` | `users` | không | CASCADE | con vô nghĩa khi cha mất |
| `cart_items` | `product_items` | có | CASCADE | con vô nghĩa khi cha mất |
| `cart_items` | `gift_boxes` | có | CASCADE | con vô nghĩa khi cha mất |
| `vouchers` | `stores` | có | CASCADE | con vô nghĩa khi cha mất |
| `orders` | `users` | không | RESTRICT | giữ lịch sử / dữ liệu tham chiếu |
| `orders` | `stores` | không | RESTRICT | giữ lịch sử / dữ liệu tham chiếu |
| `orders` | `addresses` | không | RESTRICT | giữ lịch sử / dữ liệu tham chiếu |
| `orders` | `payments` | không | RESTRICT | giữ lịch sử / dữ liệu tham chiếu |
| `orders` | `vouchers` | có | SET NULL | đơn vẫn hợp lệ khi voucher bị xoá |
| `order_items` | `orders` | không | CASCADE | con vô nghĩa khi cha mất |
| `order_items` | `product_items` | có | RESTRICT | giữ lịch sử / dữ liệu tham chiếu |
| `order_items` | `gift_boxes` | có | RESTRICT | giữ lịch sử / dữ liệu tham chiếu |
| `shipments` | `orders` | không | CASCADE | con vô nghĩa khi cha mất |
| `reviews` | `users` | không | CASCADE | con vô nghĩa khi cha mất |
| `reviews` | `products` | không | CASCADE | con vô nghĩa khi cha mất |
| `reviews` | `order_items` | không | CASCADE | con vô nghĩa khi cha mất |

## Giá trị enum

| Cột | Giá trị |
|---|---|
| `users.status` | Active = 1, Banned = 2 |
| `users.roles` | bitmask: Customer = 1, Vendor = 2, Admin = 4 |
| `stores.status` | Pending = 1, Approved = 2, Suspended = 3 |
| `products.status / gift_boxes.status` | Draft = 1, Active = 2, Hidden = 3 |
| `cart_items.item_type / order_items.item_type` | Product = 1, GiftBox = 2 |
| `orders.status` | Pending = 1, Confirmed = 2, Preparing = 3, Shipping = 4, Delivered = 5, Cancelled = 6 |
| `payments.provider` | COD = 1, VNPay = 2, MoMo = 3 |
| `payments.status` | Pending = 1, Paid = 2, Failed = 3, Refunded = 4 |
| `vouchers.discount_type` | Percent = 1, Fixed = 2 |
| `shipments.carrier` | GHN = 1, SelfDelivery = 2 |
| `shipments.status` | Created = 1, PickedUp = 2, Delivering = 3, Delivered = 4, Returned = 5, Cancelled = 6 |

## Ràng buộc không biểu diễn được bằng DDL (kiểm ở service)

1. `gift_box_items.product_item_id` phải thuộc `products.store_id` = `gift_boxes.store_id`.
2. Checkout: nhóm `cart_items` theo store → mỗi store một `orders`, tất cả cùng một `payments`; `payments.amount` = Σ `orders.total`.
3. Trừ kho khi đơn Confirmed: dòng Product trừ `product_items.stock_quantity`; dòng GiftBox trừ `gift_boxes.stock_quantity` **và** từng `product_items` trong `gift_box_items` × số hộp.
4. `orders.total` = Σ(`order_items.unit_price × quantity`) + `shipping_fee` − `discount`.
5. `reviews` chỉ tạo được khi `order_items.order_id` có `status = Delivered` và `orders.user_id` = người review.
6. Store `is_platform_owned` được seed sẵn với `status = Approved`, `owner_id` = admin.
7. GHN: phí và vận đơn tính từ `stores.ward_id` → `wards.ghn_ward_code` / `districts.ghn_district_id` (điểm lấy) tới `addresses.ward_id` (điểm giao); `shipments.tracking_code` = `order_code` GHN trả về; dùng **một** tài khoản GHN của sàn, gửi `from_*` theo địa chỉ store.
