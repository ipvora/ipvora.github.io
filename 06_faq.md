# 6. Câu hỏi thường gặp và xử lý sự cố

## Câu hỏi thường gặp

**ipvora có những loại proxy nào?**
Hai loại: proxy dân cư xoay (tính phí theo GB) và proxy dân cư tĩnh (thuê theo IP mỗi tháng).

**IP có ở những quốc gia nào?**
Việt Nam, Mỹ, Nhật Bản và các thị trường châu Âu.

**Hỗ trợ giao thức nào?**
HTTP, HTTPS và SOCKS5, cho cả hai loại proxy.

**Giá bao nhiêu?**
Proxy xoay có 10 gói từ 1 GB đến 5 TB: gói 1 GB giá $2.09 (thời hạn 30 ngày), gói 5 TB tương đương $1.39/GB (tổng $6,950.00). Proxy tĩnh từ $3.18/IP/tháng; IP Hà Lan $4.88/IP/tháng. Giá niêm yết bằng USD và có thể thay đổi — hãy kiểm tra website.

**Dung lượng proxy xoay có hết hạn không?**
Gói 1 GB có thời hạn 30 ngày. Với các gói từ 100 GB, dung lượng còn lại không hết hạn.

**Sticky session giữ IP được bao lâu?**
Từ 1 đến 120 phút.

**Proxy tĩnh có giới hạn lưu lượng không?**
Không giới hạn lưu lượng trong thời hạn thuê; băng thông 5 Mbps mỗi IP.

**Có dùng thử miễn phí không?**
Trang chủ không có thông tin về gói miễn phí hoặc dùng thử. Gói 1 GB là lựa chọn nhỏ nhất để thử nghiệm.

**Nên chọn xoay hay tĩnh?**
Cần nhiều IP khác nhau hoặc giữ IP tối đa 120 phút → xoay. Cần một IP cố định nhiều ngày → tĩnh. Xem bảng ở phần README.

## Xử lý sự cố

| Triệu chứng | Nguyên nhân thường gặp | Cách xử lý |
|---|---|---|
| `407 Proxy Authentication Required` | Sai username/password, hoặc ký tự đặc biệt chưa được mã hóa URL | Kiểm tra lại thông tin đăng nhập; URL-encode mật khẩu (phần 2) |
| `Connection refused` / không kết nối được | Sai host/port, sai giao thức (dùng `http://` cho cổng SOCKS5 hoặc ngược lại), tường lửa chặn cổng | Kiểm tra lại host/port/giao thức; thử từ mạng khác |
| Timeout | Độ trễ của kết nối dân dụng, website đích chậm, timeout đặt quá ngắn | Tăng timeout lên 30–60 giây, thêm retry với backoff |
| IP đầu ra không đúng quốc gia | Định dạng chọn quốc gia sai, hoặc DNS phân giải tại máy bạn | Kiểm tra định dạng trên trang quản lý; dùng `socks5h://` với SOCKS5 |
| IP thay đổi giữa luồng nhiều bước | Đang dùng chế độ xoay, hoặc phiên sticky đã hết hạn | Bật sticky session, chọn thời gian giữ phiên đủ dài (tối đa 120 phút) |
| `429 Too Many Requests` | Gửi request quá nhanh | Giảm tốc độ, giảm số luồng đồng thời, thêm khoảng nghỉ |
| `403 Forbidden` / trang xác minh | Website hạn chế truy cập tự động | Giảm tần suất, dùng header hợp lý, kiểm tra điều khoản của website; không cố vượt qua biện pháp bảo vệ |
| Lỗi chứng chỉ SSL | Đồng hồ hệ thống sai, phần mềm chặn SSL trên máy, thư viện cũ | Đồng bộ giờ hệ thống, cập nhật thư viện; không tắt kiểm tra SSL trong môi trường thật |
| Dung lượng tiêu hao nhanh | Tải ảnh/video/font, request lặp lại, proxy đặt ở cấp hệ điều hành | Chặn tài nguyên không cần, bật nén, cấu hình proxy riêng cho ứng dụng |

## Danh sách kiểm tra khi gỡ lỗi

1. Chạy `curl -v -x ... https://api.ipify.org` để xác nhận proxy hoạt động độc lập với code của bạn.
2. Kiểm tra giao thức trong URL proxy khớp với loại cổng.
3. In ra (đã che mật khẩu) chuỗi proxy thực tế mà code đang dùng.
4. Kiểm tra IP và quốc gia đầu ra bằng `https://ipinfo.io/json`.
5. Thử với một URL đơn giản (ví dụ `https://example.com`) để loại trừ vấn đề ở website đích.
6. Ghi log mã trạng thái HTTP và thời gian phản hồi cho từng request.

## Thông tin thêm

Để xem các gói hiện hành và bắt đầu sử dụng, truy cập <https://www.ipvora.com>.

---

*Tài liệu do đội ngũ ipvora biên soạn — SHIHUA TECHNOLOGY CO., LIMITED (Hồng Kông). © 2026 ipvora.*
