# 1. Bắt đầu

Phần này mô tả các bước chung để bắt đầu dùng proxy dân cư. Giao diện và tên menu cụ thể có thể khác; khi có khác biệt, hãy làm theo những gì hiển thị trên website ipvora.

> Lưu ý: website ipvora hiện chỉ có phiên bản tiếng Việt.

## Bước 1: Xác định nhu cầu

Trước khi chọn gói, hãy trả lời ba câu hỏi:

1. **Bạn cần IP thay đổi hay cố định?**
   - Thay đổi liên tục hoặc giữ trong tối đa 120 phút → proxy dân cư xoay.
   - Cố định nhiều ngày/tuần → proxy dân cư tĩnh.
2. **Bạn cần IP ở đâu?** ipvora có IP tại Việt Nam, Mỹ, Nhật Bản và các thị trường châu Âu.
3. **Bạn dùng bao nhiêu dung lượng?** Proxy xoay tính theo GB. Một trang web đầy đủ (HTML, ảnh, script) có thể nặng vài MB; nếu chỉ tải HTML hoặc API JSON, mỗi request nhẹ hơn nhiều. Hãy ước lượng bằng một đợt chạy thử nhỏ.

## Bước 2: Chọn gói

**Proxy dân cư xoay** có 10 gói từ 1 GB đến 5 TB:

- Gói khởi đầu 1 GB giá $2.09, thời hạn 30 ngày — phù hợp để thử nghiệm.
- Giá mỗi GB giảm dần theo dung lượng, đến $1.39/GB ở gói 5 TB.
- Với các gói từ 100 GB, dung lượng chưa dùng không hết hạn.

**Proxy dân cư tĩnh** thuê theo IP mỗi tháng, từ $3.18/IP/tháng (IP Hà Lan: $4.88/IP/tháng). Mỗi IP có băng thông 5 Mbps và không giới hạn lưu lượng trong thời hạn thuê.

Trang chủ không nêu thông tin về gói miễn phí hay dùng thử. Hãy xem trang giá tại <https://www.ipvora.com> để biết các gói hiện hành.

## Bước 3: Tạo tài khoản và thanh toán

Quy trình chung ở hầu hết dịch vụ proxy:

1. Đăng ký tài khoản trên website.
2. Chọn sản phẩm (xoay hoặc tĩnh) và gói phù hợp.
3. Thanh toán theo các phương thức website cung cấp.

## Bước 4: Lấy thông tin kết nối

Sau khi kích hoạt gói, bạn cần các thông tin sau để kết nối (tên gọi có thể khác tùy giao diện):

| Thông tin | Ý nghĩa | Ví dụ placeholder |
|---|---|---|
| Host | Địa chỉ máy chủ proxy | `PROXY_HOST` |
| Port | Cổng kết nối | `PROXY_PORT` |
| Username | Tên đăng nhập proxy | `USERNAME` |
| Password | Mật khẩu proxy | `PASSWORD` |
| Giao thức | HTTP, HTTPS hoặc SOCKS5 | `http` / `socks5` |

Cách chọn quốc gia và cách bật sticky session (ví dụ qua tham số trong username, cổng riêng hay tùy chọn trên trang quản lý) **khác nhau giữa các nhà cung cấp**. Hãy lấy định dạng chính xác từ trang quản lý tài khoản của bạn, đừng đoán.

## Bước 5: Kiểm tra kết nối

Chạy lệnh sau (thay placeholder bằng thông tin thật):

```bash
curl -x http://USERNAME:PASSWORD@PROXY_HOST:PROXY_PORT https://api.ipify.org
```

Nếu lệnh trả về một địa chỉ IP khác với IP mạng của bạn, proxy đã hoạt động. Để xem quốc gia của IP đầu ra, bạn có thể gọi một dịch vụ tra cứu vị trí IP công khai, ví dụ `https://ipinfo.io/json`.

## Bước 6: Lưu thông tin an toàn

- Không hard-code username/password vào mã nguồn hoặc commit lên Git.
- Dùng biến môi trường hoặc file `.env` (và thêm `.env` vào `.gitignore`).
- Nếu nghi ngờ lộ mật khẩu proxy, hãy đổi mật khẩu ngay nếu trang quản lý cho phép.

```bash
export PROXY_HOST="PROXY_HOST"
export PROXY_PORT="PROXY_PORT"
export PROXY_USER="USERNAME"
export PROXY_PASS="PASSWORD"
```

Các ví dụ ở phần 3 đều đọc thông tin từ các biến môi trường này.
