# ERD

Sơ đồ thực thể – quan hệ vẽ bằng draw.io. Mở bằng draw.io desktop hoặc extension VS Code "Draw.io Integration".

| File | Nội dung |
|---|---|
| `PRN232_Mooncraft.drawio` | Bản chính, 3 tab đồng bộ (2026-09-21): **Conceptual** — 19 entity, cardinality `1 / N / 0..1`; **Logical** — 19 bảng PascalCase, PK/FK, kiểu logic (`?` = nullable), crow's foot; **Physical** — 19 bảng `snake_case`, kiểu PostgreSQL, UQ/IX/CK, kèm ô ghi chú quy ước. Vị trí bảng ở tab Physical đặt theo lưới, kéo lại tuỳ ý |
| `.$PRN232_Mooncraft.drawio.bkp` | Backup tự sinh của draw.io — không sửa tay, có thể xoá |

## Quy ước

- Mỗi cấp một tab trong cùng file: `Conceptual Diagram` → `Logical Diagram` (PK/FK, kiểu dữ liệu) → `Physical` (tên bảng `snake_case`, index).
- Cardinality ghi ở hai đầu đường nối: `1`, `N`, `0..1`. Không dùng `0` đơn lẻ.
- Entity tùy chọn (chưa chắc làm) ghi hậu tố `(Optional)` ở cấp conceptual, bỏ hậu tố khi xuống logical.
- Tên entity `PascalCase` số ít; xuống physical đổi thành `snake_case` số nhiều (`GiftBoxItem` → `gift_box_items`).

## Phải khớp với

- [`../design/02-conceptual-model.md`](../design/02-conceptual-model.md) — danh sách entity/quan hệ dạng chữ; khi hai bên lệch nhau, sửa cả hai trong cùng commit.
- [`../design/04-physical-model.md`](../design/04-physical-model.md) — bản chữ của tab Physical (index, ON DELETE, enum).
- ADR ảnh hưởng tới sơ đồ: [0001](../adr/0001-role-bitmask.md) (không có bảng Role), [0002](../adr/0002-platform-store-as-vendor.md) (`Store.IsPlatformOwned`), [0003](../adr/0003-checkout-split-orders-single-payment.md) (`Payment 1─N Order`), [0004](../adr/0004-shipping-ghn.md) (`Shipment`, mã GHN trên `District`/`Ward`), [0005](../adr/0005-postgresql-int-pk-vnd.md) (kiểu dữ liệu Physical).
