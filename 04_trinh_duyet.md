# 4. Cấu hình trình duyệt và trình duyệt anti-detect

Phần này hướng dẫn chung cách dùng proxy trong trình duyệt. Tên menu có thể khác tùy phiên bản. Mọi giá trị đều là placeholder.

## 4.1 Firefox

Firefox có cài đặt proxy riêng, độc lập với hệ điều hành:

1. Mở **Cài đặt (Settings)** → kéo xuống **Cài đặt mạng (Network Settings)** → **Cài đặt...**
2. Chọn **Cấu hình proxy thủ công (Manual proxy configuration)**.
3. Với HTTP proxy: nhập `PROXY_HOST` và `PROXY_PORT` vào ô **HTTP Proxy**, đánh dấu **Cũng dùng proxy này cho HTTPS**.
4. Với SOCKS5: nhập vào ô **SOCKS Host**, chọn **SOCKS v5** và bật **Proxy DNS khi dùng SOCKS v5**.
5. Bấm **OK**, mở một trang bất kỳ; Firefox sẽ hỏi username/password của proxy.

## 4.2 Chrome / Edge

Chrome và Edge dùng cài đặt proxy của hệ điều hành. Để chỉ áp dụng proxy cho trình duyệt, bạn có thể:

- Khởi động với tham số dòng lệnh (dùng một hồ sơ riêng để không ảnh hưởng hồ sơ chính):

```bash
google-chrome --user-data-dir=/tmp/profile-proxy --proxy-server="http://PROXY_HOST:PROXY_PORT"
```

  Chrome sẽ hỏi username/password khi truy cập trang đầu tiên.

- Hoặc dùng một tiện ích quản lý proxy (proxy switcher) từ cửa hàng tiện ích chính thức, cho phép lưu nhiều cấu hình và chuyển nhanh.

## 4.3 Cài đặt proxy hệ điều hành

- **Windows:** Cài đặt → Mạng & Internet → Proxy → Thiết lập proxy thủ công.
- **macOS:** Cài đặt hệ thống → Mạng → chọn kết nối → Chi tiết → Proxy.

Lưu ý: proxy hệ điều hành áp dụng cho nhiều ứng dụng cùng lúc, có thể tiêu tốn dung lượng ngoài ý muốn với proxy tính phí theo GB. Nên ưu tiên cấu hình riêng cho từng trình duyệt hoặc ứng dụng.

## 4.4 Trình duyệt anti-detect / quản lý nhiều hồ sơ

Các trình duyệt quản lý nhiều hồ sơ (multi-profile) cho phép mỗi hồ sơ có cookie, bộ nhớ và cấu hình riêng. Quy trình gắn proxy thường giống nhau:

1. Tạo hồ sơ mới.
2. Trong phần **Proxy**, chọn loại: HTTP, HTTPS hoặc SOCKS5.
3. Nhập host, port, username, password — hoặc dán chuỗi dạng `PROXY_HOST:PROXY_PORT:USERNAME:PASSWORD` nếu công cụ hỗ trợ.
4. Bấm **Kiểm tra proxy (Check proxy)** để xác nhận IP và quốc gia.
5. Đặt múi giờ, ngôn ngữ và vị trí địa lý theo IP (nhiều công cụ có tùy chọn tự động theo IP).

Khuyến nghị chọn loại proxy:

| Mục đích của hồ sơ | Loại proxy |
|---|---|
| Hồ sơ dùng lâu dài cho một tài khoản bạn được phép quản lý | Proxy dân cư tĩnh — mỗi hồ sơ một IP cố định |
| Phiên làm việc ngắn, vài phút đến 2 giờ | Proxy xoay với sticky session (1–120 phút) |
| Xem nhanh nội dung theo khu vực, không đăng nhập | Proxy xoay |

Nguyên tắc nhất quán:

- Một hồ sơ ↔ một IP (hoặc một phiên sticky). Tránh đổi qua lại nhiều quốc gia trong cùng một hồ sơ.
- Múi giờ, ngôn ngữ trình duyệt nên khớp với vị trí IP.
- Tuân thủ điều khoản của nền tảng bạn truy cập (xem phần 5).

## 4.5 Kiểm tra sau khi cấu hình

Mở các trang sau trong trình duyệt đã gắn proxy:

- `https://api.ipify.org` — xem IP đầu ra.
- `https://ipinfo.io/json` — xem quốc gia, thành phố, nhà mạng của IP.

Nếu IP hiển thị vẫn là IP thật của bạn, kiểm tra lại xem cấu hình đã được lưu và trình duyệt đã khởi động lại chưa.
