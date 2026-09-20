# CLAUDE.md — Bối cảnh dự án MoonCraft

Đọc **đầu tiên** ở mỗi phiên. Quy tắc làm việc / naming nằm ở [`AGENTS.md`](AGENTS.md); file này chỉ nói dự án là gì, đang ở đâu, đã chốt gì, tìm chi tiết ở đâu — không lặp lại nội dung `docs/`, chỉ trỏ tới.

## 1. Dự án là gì

**MoonCraft** — sàn thương mại điện tử **đa người bán (multi-vendor)** chuyên **bánh trung thu**. Đồ án môn **PRN232** (ASP.NET Core Web API). Repo này là backend; frontend ở `../MoonCraft_FE`.

- **Người bán (Vendor)** đăng ký cửa hàng, khai **vị trí địa lý**, đăng **bánh lẻ** và **hộp bánh** (combo), xử lý đơn.
- **Khách hàng** tìm cửa hàng gần mình, mua bánh từ nhiều cửa hàng trong một giỏ, thanh toán một lần.
- **Sàn** cũng tự bán bánh — gian hàng "MoonCraft Official" là **một `Store` bình thường** với `IsPlatformOwned = true`, do Admin vận hành. `Product.StoreId` luôn NOT NULL; không dùng `StoreId NULL` để đánh dấu "bánh của sàn".

## 2. Trạng thái hiện tại (cập nhật khi đổi giai đoạn)

| Mốc | Trạng thái |
|---|---|
| System context (C4-L1) | ✅ `docs/design/01-system-context.md` |
| Conceptual model | ✅ `docs/design/02-conceptual-model.md` |
| ERD `docs/erd/PRN232_Mooncraft.drawio` | ✅ 3 tab Conceptual / Logical / Physical đồng bộ, 19 bảng (2026-09-21); bản chữ của Physical: `docs/design/04-physical-model.md` |
| Container diagram (C4-L2) | ⬜ |
| Tech stack | 🔄 .NET Web API + EF Core; **PostgreSQL** đang là giả định (ADR 0005 Proposed); FE framework chưa quyết |
| Code | ⬜ chưa có |

> Thấy code đã tồn tại mà bảng này vẫn ghi "chưa có" → bảng đã lỗi thời, cập nhật nó trước.

## 3. Quyết định đã chốt (ADR — không hỏi lại, không tự đổi)

| ADR | Quyết định |
|---|---|
| [0001](docs/adr/0001-role-bitmask.md) | Role là bitmask `[Flags] enum UserRole { Customer=1, Vendor=2, Admin=4 }` trên `users.roles`. **Không có bảng Role.** |
| [0002](docs/adr/0002-platform-store-as-vendor.md) | Gian hàng của sàn = một `Store` `IsPlatformOwned`, `CommissionRate = 0`. Không tách luồng. |
| [0003](docs/adr/0003-checkout-split-orders-single-payment.md) | *(Proposed — ERD Logical đã vẽ theo)* Một checkout → N `Order` (mỗi cửa hàng một đơn) → 1 `Payment`. |
| [0004](docs/adr/0004-shipping-ghn.md) | Vận chuyển qua **GHN**, một tài khoản của sàn; `District.GhnDistrictId`, `Ward.GhnWardCode`; `Shipment` 0..1 / Order. |
| [0005](docs/adr/0005-postgresql-int-pk-vnd.md) | *(Proposed)* PostgreSQL, PK `int identity`, tiền `numeric(12,0)`, enum `smallint`, `snake_case` tự động. |

Chốt thêm 2026-09-21, chưa có ADR riêng, ghi trong `02-conceptual-model.md` mục "Ràng buộc" / "Đối chiếu với ERD": **có `ProductItem`** (biến thể theo cỡ; giá/tồn kho ở đây; `CartItem`/`OrderItem`/`GiftBoxItem` trỏ `ProductItem`, `Review`/`ProductImage` trỏ `Product`); `Category 1─N Product` (phẳng, không cha–con); hộp bánh chỉ loại cố định (không Custom); audit `CreatedAt/UpdatedAt` khai chung qua `BaseEntity` ở Physical, không vẽ từng bảng; `CartItem`/`OrderItem` dùng 2 FK nullable + `ItemType`; `Review` gắn `OrderItem` (unique); **`User 1─N Store`** (một chủ nhiều điểm bán, `Store.OwnerId` FK thường; mọi endpoint vendor nhận `storeId` tường minh, role Vendor cấp khi có ≥1 store Approved).

Đổi một quyết định: ADR mới, đánh dấu cái cũ `Superseded` — không sửa file cũ.

## 4. Việc đang mở (cần người quyết, AI không tự chọn)

1. Xác nhận ADR 0005 (PostgreSQL / `int` / `numeric(12,0)`) → đổi Status thành Accepted, hoặc chọn SQL Server và sửa tab Physical + `04-physical-model.md`.
2. FE framework (`../MoonCraft_FE` còn trống).
3. Nguồn seed địa lý có mã GHN (API master-data GHN hay file tĩnh).

Đã chốt 2026-09-21 (không hỏi lại): bỏ `Cart`, `Payout`, `OrderStatusHistory`, `RefreshToken`, hộp Custom; giữ `Shipment`, `Voucher`, `Review`, `ProductImage`, `Province/District/Ward`.

## 5. Tìm gì ở đâu

Điểm vào: [`docs/README.md`](docs/README.md).

| Cần | Đọc |
|---|---|
| Ai dùng, tích hợp ngoài nào | `docs/design/01-system-context.md` |
| Entity, quan hệ, ràng buộc, mục còn mở của ERD | `docs/design/02-conceptual-model.md` + `docs/erd/README.md` |
| Vì sao quyết định như vậy | `docs/adr/` |
| Kiến trúc code *(khi có code)* | `docs/ard/architecture-core/` |
| Phân hệ X: luồng, rule, edge case *(khi có code)* | `docs/ard/bounded-contexts/` |
| Chức năng đã làm (FR-ID) | `docs/ard/functional-requirements.md` |
| Endpoint *(khi có code)* | `docs/api-documents/` |
| Branch / commit / PR | `docs/CONTRIBUTING.md` |
| Quy tắc làm việc, naming | `AGENTS.md` |

## 6. Sau mỗi lần chỉnh sửa — cập nhật docs theo đúng thứ vừa đổi

Không kết thúc một việc khi tài liệu liên quan còn lệch với thứ vừa sửa. Rà bảng này trước khi báo "xong":

| Vừa sửa gì | Phải cập nhật |
|---|---|
| ERD (`docs/erd/*.drawio`) | `docs/design/02-conceptual-model.md` (bảng entity + khối quan hệ + mục "Đối chiếu với ERD") và §2/§4 file này |
| `02-conceptual-model.md` | ERD cho khớp, và ngược lại |
| Chốt một mục ở §4 | ADR mới + dòng trong `docs/adr/README.md` + xoá khỏi §4 + ghi vào §3 |
| Đổi ý một ADR | ADR mới, đánh dấu cái cũ `Superseded by NNNN` ở cả file cũ và `adr/README.md` |
| Bắt đầu / kết thúc giai đoạn | Bảng §2 |
| Schema / migration | `docs/ard/architecture-core/03-data-and-integrations.md` + bounded-context liên quan + ERD tab Physical |
| Endpoint | `docs/api-documents/NN-*.md` + bảng endpoint ở bounded-context + FR trong `functional-requirements.md` |
| Business rule, luồng, edge case | `docs/ard/bounded-contexts/<context>.md` |
| Thêm file vào một thư mục docs | `README.md` của thư mục đó |
| Thêm tích hợp ngoài | `docs/design/01-system-context.md` + `03-data-and-integrations.md` |

Người dùng nói "tôi đã sửa ERD/tài liệu rồi" → **đọc lại file đó trước**, đối chiếu với `02-conceptual-model.md` / ADR, rồi mới tiếp tục; không dựa vào trí nhớ phiên trước.

## 7. Tham khảo

Dự án chị em `D:\Projects\SEP490\FengDeskAI` — cùng tác giả, cùng kiểu multi-vendor .NET, dùng làm mẫu **cách tổ chức docs và code**; **không** copy nghiệp vụ phong thủy sang. `AGENTS.md` ở đây cũng chép từ đó — phần nói về Supabase/Npgsql chỉ có hiệu lực khi §2 đã chốt PostgreSQL.
