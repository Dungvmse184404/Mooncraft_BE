# 0004 — Vận chuyển qua GHN, một tài khoản của sàn

- **Status:** Accepted
- **Date:** 2026-09-21
- **Context:** Cần tính phí ship và tạo vận đơn thật cho đơn hàng đa cửa hàng. Hai lựa chọn: cửa hàng tự giao + phí cố định theo khoảng cách, hoặc tích hợp một đơn vị vận chuyển.
- **Decision:** Tích hợp **GHN** (Giao Hàng Nhanh) — API tính phí + tạo đơn + webhook trạng thái. Dùng **một** tài khoản/ShopId GHN của sàn; điểm lấy hàng truyền qua `from_*` theo địa chỉ của từng `Store` (không đăng ký mỗi store một GHN shop). Vì GHN định danh theo quận/phường của họ, `District.GhnDistrictId` và `Ward.GhnWardCode` được thêm vào bảng địa lý và đồng bộ một lần từ master-data GHN. `Shipment` 0..1 mỗi `Order`, `TrackingCode` = `order_code` GHN. `Address` không cần lat/lng.
- **Consequences:**
  - Được: phí ship thật theo tuyến, theo dõi trạng thái qua webhook, mỗi Order (= mỗi store) một vận đơn khớp với cách tách đơn ở ADR 0003.
  - Mất: phụ thuộc sandbox GHN; phải seed bảng địa lý có mã GHN; store của sàn cũng phải có địa chỉ phường thật.
  - Kèm: `Shipment.Carrier` giữ giá trị `SelfDelivery` làm đường thoát khi GHN lỗi hoặc demo offline.
