# 2. Định dạng proxy và xác thực

ipvora hỗ trợ ba giao thức: **HTTP**, **HTTPS** và **SOCKS5**. Phần này giải thích cách viết địa chỉ proxy và cách xác thực. Tất cả giá trị dưới đây là **placeholder**; định dạng thực tế hãy lấy từ trang quản lý tài khoản.

## Giao thức nào nên dùng?

| Giao thức | Đặc điểm | Khi nào dùng |
|---|---|---|
| HTTP | Proxy cho request HTTP; với trang HTTPS, client mở đường hầm bằng lệnh `CONNECT` nên nội dung vẫn được mã hóa đầu cuối | Phần lớn công cụ scraping, trình duyệt, thư viện HTTP |
| HTTPS | Kết nối tới proxy cũng được mã hóa | Khi muốn bảo vệ cả thông tin đăng nhập proxy trên đường truyền |
| SOCKS5 | Proxy ở tầng thấp hơn, chuyển tiếp TCP bất kỳ | Ứng dụng không phải HTTP, một số trình duyệt anti-detect, công cụ cần hỗ trợ giao thức tổng quát |

Nếu không chắc, hãy bắt đầu với HTTP: hầu hết thư viện và trình duyệt đều hỗ trợ sẵn.

## Định dạng URL proxy

Dạng URL phổ biến nhất mà các thư viện chấp nhận:

```
giao_thuc://USERNAME:PASSWORD@PROXY_HOST:PROXY_PORT
```

Ví dụ (placeholder):

```
http://USERNAME:PASSWORD@PROXY_HOST:PROXY_PORT
socks5://USERNAME:PASSWORD@PROXY_HOST:PROXY_PORT
socks5h://USERNAME:PASSWORD@PROXY_HOST:PROXY_PORT
```

`socks5h` nghĩa là việc phân giải tên miền (DNS) được thực hiện ở phía proxy thay vì trên máy bạn. Điều này giúp kết quả DNS khớp với vị trí của IP đầu ra — hữu ích khi kiểm tra website theo khu vực.

Một số công cụ (đặc biệt là trình duyệt anti-detect) yêu cầu dạng tách trường hoặc dạng chuỗi:

```
PROXY_HOST:PROXY_PORT:USERNAME:PASSWORD
```

Hãy đọc tài liệu của công cụ bạn dùng để biết thứ tự trường.

## Ký tự đặc biệt trong mật khẩu

Nếu username hoặc password chứa các ký tự như `@`, `:`, `/`, `#`, `%`, bạn phải **mã hóa URL** (URL-encode) trước khi đưa vào chuỗi proxy. Ví dụ `@` thành `%40`, `:` thành `%3A`.

```python
from urllib.parse import quote
user = quote("USERNAME", safe="")
pwd = quote("PASSWORD", safe="")
proxy = f"http://{user}:{pwd}@PROXY_HOST:PROXY_PORT"
```

## Xác thực bằng username/password

Đây là cách phổ biến nhất: client gửi thông tin đăng nhập kèm theo request tới proxy (với HTTP proxy là header `Proxy-Authorization`). Ưu điểm là dùng được từ mọi mạng, không phụ thuộc IP máy bạn.

Nếu trang quản lý của bạn cung cấp các hình thức xác thực khác (ví dụ whitelist IP), hãy làm theo hướng dẫn ở đó. Tài liệu này không giả định hình thức nào ngoài username/password.

## Chọn quốc gia và giữ phiên

Với proxy dân cư xoay, hai tùy chọn quan trọng là:

- **Quốc gia/khu vực của IP:** Việt Nam, Mỹ, Nhật Bản hoặc các thị trường châu Âu.
- **Thời gian giữ phiên:** từ 1 đến 120 phút.

Mỗi nhà cung cấp có cách cấu hình riêng (tham số trong username, cổng khác nhau, hoặc bộ tạo chuỗi proxy trên trang quản lý). Trong code ở phần 3, chúng tôi gói phần này vào một hàm `build_proxy()` để bạn chỉ cần sửa một chỗ khi đã biết định dạng thật.

Nguyên tắc chung khi dùng sticky session:

- Mỗi "phiên" công việc (một tài khoản, một luồng thanh toán thử nghiệm) nên dùng một mã phiên riêng.
- Chọn thời gian giữ phiên vừa đủ cho luồng công việc, không đặt dài hơn cần thiết.
- Khi phiên hết hạn, IP có thể thay đổi; code của bạn nên chịu được điều này (đăng nhập lại, thử lại).

## Proxy dân cư tĩnh

Với proxy tĩnh, mỗi IP bạn thuê là cố định trong suốt kỳ thuê, băng thông 5 Mbps mỗi IP và không giới hạn lưu lượng. Bạn chỉ cần dùng đúng host/port/thông tin đăng nhập được cấp cho IP đó. Nếu thuê nhiều IP, hãy lưu danh sách và gán mỗi IP cho một công việc hoặc một hồ sơ trình duyệt cố định.

## Kiểm tra nhanh từng giao thức

```bash
# HTTP
curl -x http://USERNAME:PASSWORD@PROXY_HOST:PROXY_PORT https://api.ipify.org
# SOCKS5 (DNS phân giải ở phía proxy)
curl -x socks5h://USERNAME:PASSWORD@PROXY_HOST:PROXY_PORT https://api.ipify.org
```
