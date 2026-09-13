# API Documentation — MoonCraft

Mỗi controller một file, đánh số theo thứ tự nghiệp vụ. Nội dung phải phản ánh đúng code trong `WebAPI/Controllers` và DTO trong `Application`.

| #   | File                    | Controller             | Nội dung                                             |
| --- | ----------------------- | ---------------------- | ---------------------------------------------------- |
| 00  | `00-overview.md`        | —                      | Base URL, JWT, envelope response, mã lỗi, phân trang |
| 01  | `01-authentication.md`  | `AuthController`       | Đăng ký, đăng nhập, refresh, hồ sơ                   |
| 02  | `02-stores.md`          | `StoresController`     | Đăng ký / duyệt cửa hàng, tìm gần vị trí             |
| 03  | `03-products.md`        | `ProductsController`   | Bánh, biến thể, ảnh                                  |
| 04  | `04-giftboxes.md`       | `GiftBoxesController`  | Hộp bánh                                             |
| 05  | `05-categories.md`      | `CategoriesController` | Loại bánh                                            |
| 06  | `06-cart.md`            | `CartController`       | Giỏ hàng                                             |
| 07  | `07-orders.md`          | `OrdersController`     | Checkout, đơn hàng, trạng thái                       |
| 08  | `08-payments.md`        | `PaymentsController`   | Tạo thanh toán, IPN                                  |
| 09  | `09-vouchers.md`        | `VouchersController`   | Voucher                                              |
| 10  | `10-reviews.md`         | `ReviewsController`    | Đánh giá                                             |
| 99  | `99-appendix-models.md` | —                      | Enum, DTO dùng chung                                 |

Khung mỗi file:

```
# NN — <Tên>
## Bảng endpoint            (method · path · auth · mô tả một dòng)
## <METHOD> `/api/...`      (một mục mỗi endpoint: request, response, status code, rule)
```
