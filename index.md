# Hướng dẫn sử dụng proxy dân cư ipvora

> **Công khai:** Tài liệu này do đội ngũ ipvora biên soạn. Các ví dụ code là code chung, dùng placeholder cho host/port/username/password và chạy được với bất kỳ proxy nào hỗ trợ HTTP/HTTPS/SOCKS5.

ipvora (<https://www.ipvora.com>) là dịch vụ proxy dân cư dành cho khách hàng Việt Nam, ra mắt tháng 10/2026, do SHIHUA TECHNOLOGY CO., LIMITED (Hồng Kông) vận hành. Dịch vụ gồm hai sản phẩm: **proxy dân cư xoay** (rotating) và **proxy dân cư tĩnh** (static), với IP tại Việt Nam, Mỹ, Nhật Bản và các thị trường châu Âu. Cả hai đều hỗ trợ HTTP, HTTPS và SOCKS5.

Bộ tài liệu này giúp bạn hiểu proxy dân cư là gì, chọn đúng loại proxy, cấu hình trong code và trình duyệt, áp dụng cho các tình huống thực tế và xử lý lỗi thường gặp.

## Proxy dân cư là gì?

Khi bạn truy cập một website, máy chủ của website nhìn thấy địa chỉ IP của bạn. Proxy là một máy trung gian: request của bạn đi tới proxy, proxy chuyển tiếp tới website, và website nhìn thấy IP của proxy thay vì IP của bạn.

Có ba nhóm proxy phổ biến:

- **Datacenter proxy:** IP thuộc trung tâm dữ liệu hoặc nhà cung cấp cloud. Nhanh và rẻ, nhưng nhiều website nhận ra đây là IP máy chủ, nên nội dung hiển thị hoặc cách xử lý có thể khác với người dùng thông thường.
- **Proxy dân cư (residential):** IP do nhà mạng cấp cho kết nối dân dụng. Lưu lượng đi qua các IP này giống với người dùng bình thường ở khu vực đó, nên phù hợp để xem nội dung theo vùng địa lý.
- **Proxy dân cư tĩnh (static residential / ISP):** IP dân dụng nhưng cố định trong suốt thời gian thuê. Phù hợp khi bạn cần một địa chỉ ổn định nhiều ngày hoặc nhiều tuần.

## Rotating hay sticky session?

Với proxy dân cư xoay, bạn có hai cách sử dụng chính:

- **Rotating (xoay IP):** mỗi request hoặc mỗi kết nối có thể đi ra từ một IP khác. Thích hợp khi bạn truy cập nhiều trang công khai độc lập nhau, ví dụ danh sách sản phẩm hoặc trang giá.
- **Sticky session (giữ phiên):** giữ cùng một IP trong một khoảng thời gian. Với ipvora, phiên có thể giữ từ **1 đến 120 phút**. Thích hợp cho các luồng nhiều bước như đăng nhập, giỏ hàng, form nhiều trang.

Nếu bạn cần cùng một IP trong nhiều ngày, hãy chọn **proxy dân cư tĩnh**.

| Tình huống | Nên dùng |
|---|---|
| Thu thập nhiều trang công khai độc lập | Rotating |
| Luồng nhiều bước cần giữ cùng IP vài phút đến 2 giờ | Sticky session (1–120 phút) |
| Cần IP cố định nhiều ngày/tuần, whitelist IP với đối tác | Proxy dân cư tĩnh |

## Tóm tắt hai sản phẩm

| | Proxy dân cư xoay | Proxy dân cư tĩnh |
|---|---|---|
| Cách tính phí | Theo dung lượng (GB) | Theo IP mỗi tháng |
| Gói | 10 gói từ 1 GB đến 5 TB | Thuê theo tháng |
| Giá tham khảo | 1 GB: $2.09 (thời hạn 30 ngày); gói 5 TB: $1.39/GB (tổng $6,950.00) | Từ $3.18/IP/tháng (Hà Lan: $4.88/IP/tháng) |
| Thời hạn dung lượng | Gói từ 100 GB: dung lượng còn lại không hết hạn | — |
| Băng thông / lưu lượng | — | 5 Mbps mỗi IP, không giới hạn lưu lượng trong kỳ thuê |
| Giữ phiên | 1–120 phút | IP cố định trong kỳ thuê |
| Giao thức | HTTP, HTTPS, SOCKS5 | HTTP, HTTPS, SOCKS5 |

Giá niêm yết bằng USD. Giá có thể thay đổi, hãy luôn kiểm tra trang giá trên website trước khi mua.

## Khi nào nên dùng proxy dân cư?

- Thu thập dữ liệu công khai (giá, danh mục sản phẩm) ở nhiều thị trường.
- Nghiên cứu thị trường: xem website, kết quả tìm kiếm, quảng cáo hiển thị thế nào với người dùng ở Việt Nam, Mỹ, Nhật Bản hoặc châu Âu.
- Quản lý các tài khoản mà bạn được phép quản lý, cần IP ổn định.
- Kiểm tra website theo khu vực (geo testing): nội dung, ngôn ngữ, giá, chuyển hướng theo quốc gia.

## Cấu trúc tài liệu

1. `01_bat_dau.md` — Bắt đầu
2. `02_dinh_dang_xac_thuc.md` — Định dạng proxy và xác thực
3. `03_vi_du_code.md` — Ví dụ code: curl, Python, Node.js, Playwright
4. `04_trinh_duyet.md` — Cấu hình trình duyệt và trình duyệt anti-detect
5. `05_truong_hop_su_dung.md` — Các trường hợp sử dụng và lưu ý pháp lý
6. `06_faq.md` — Câu hỏi thường gặp và xử lý sự cố
