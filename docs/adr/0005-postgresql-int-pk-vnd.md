# 0005 — PostgreSQL, PK int identity, tiền numeric(12,0), enum smallint

- **Status:** Proposed *(giả định khi sinh tab Physical — xác nhận hoặc đổi trước migration đầu tiên)*
- **Date:** 2026-09-21
- **Context:** Tab Physical cần kiểu dữ liệu cụ thể. `AGENTS.md` (chép từ FengDeskAI) đã quy định `UseNpgsql().UseSnakeCaseNamingConventions()`.
- **Decision:** PostgreSQL (Supabase hoặc local). PK `id int GENERATED ALWAYS AS IDENTITY`. Tiền `numeric(12,0)` — VND không có phần lẻ. Enum C# lưu `smallint`. Bảng/cột `snake_case` sinh tự động bởi `EFCore.NamingConventions`, không đặt tên tay trong Fluent API. Mọi bảng có `created_at`, `updated_at` qua `BaseEntity`; không soft-delete.
- **Consequences:**
  - Nếu đổi sang SQL Server: `identity` giữ nguyên ý nghĩa, `numeric` → `decimal`, `timestamptz` → `datetime2`, `double precision` → `float`, bỏ `UseSnakeCaseNamingConventions`. Tab Physical và `04-physical-model.md` phải sửa theo.
  - `int` đủ cho quy mô đồ án; đổi sang `Guid` sau khi có dữ liệu rất tốn công — chốt trước migration đầu tiên.
