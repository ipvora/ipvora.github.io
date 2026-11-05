# 5. Các trường hợp sử dụng

Dưới đây là bốn tình huống phổ biến, kèm gợi ý chọn loại proxy và cách làm. Mọi trường hợp đều cần tuân thủ pháp luật và điều khoản của website (xem mục 5.5).

## 5.1 Theo dõi giá thương mại điện tử

**Mục tiêu:** theo dõi giá, tình trạng còn hàng, khuyến mãi công khai của sản phẩm trên nhiều sàn hoặc nhiều thị trường (Việt Nam, Mỹ, Nhật Bản, châu Âu).

**Loại proxy:** proxy dân cư xoay. Mỗi trang sản phẩm là một request độc lập nên không cần giữ IP.

**Cách làm:**

1. Lập danh sách URL sản phẩm cần theo dõi và tần suất (ví dụ mỗi ngày một lần).
2. Chọn IP theo quốc gia của thị trường để thấy đúng giá và đơn vị tiền tệ địa phương.
3. Chỉ tải HTML hoặc API công khai, không tải ảnh, để tiết kiệm dung lượng.
4. Lưu kết quả kèm thời điểm và quốc gia của IP, so sánh theo thời gian.
5. Giới hạn tốc độ request, tôn trọng `robots.txt` và điều khoản của sàn.

```python
for url in product_urls:
    r = fetch(url)              # hàm fetch có retry ở phần 3
    price = parse_price(r.text) # hàm phân tích của riêng bạn
    save(url, price, country="VN")
    time.sleep(2)
```

## 5.2 Xác minh quảng cáo (ad verification)

**Mục tiêu:** kiểm tra quảng cáo của bạn hoặc của khách hàng có hiển thị đúng khu vực, đúng nội dung, đúng trang đích hay không.

**Loại proxy:** proxy xoay với sticky session. Một lượt kiểm tra thường gồm vài bước (mở trang, chờ quảng cáo tải, bấm vào để kiểm tra trang đích), nên cần giữ cùng IP trong vài phút.

**Cách làm:**

1. Dùng Playwright với `locale` và `timezone_id` khớp quốc gia của IP.
2. Mở trang có đặt quảng cáo, chụp màn hình khu vực quảng cáo.
3. Ghi lại chuỗi chuyển hướng tới trang đích để phát hiện chuyển hướng bất thường.
4. Lặp lại với nhiều khu vực để so sánh.

```python
page.goto(publisher_url)
page.wait_for_timeout(5000)
page.screenshot(path=f"ad_{country}.png", full_page=True)
```

## 5.3 Kiểm tra thứ hạng SEO

**Mục tiêu:** xem kết quả tìm kiếm cho các từ khóa mục tiêu như người dùng tại từng quốc gia nhìn thấy.

**Loại proxy:** proxy dân cư xoay, chọn IP theo quốc gia cần kiểm tra.

**Cách làm:**

1. Chuẩn bị danh sách từ khóa và quốc gia (ví dụ "proxy dân cư" tại Việt Nam).
2. Đặt ngôn ngữ (`Accept-Language`) và tham số ngôn ngữ/khu vực của công cụ tìm kiếm phù hợp.
3. Tần suất vừa phải: kiểm tra định kỳ (hàng ngày hoặc hàng tuần) thay vì liên tục.
4. Ưu tiên API chính thức của công cụ tìm kiếm nếu có và phù hợp nhu cầu; khi tự kiểm tra, tuân thủ điều khoản sử dụng của công cụ tìm kiếm.

## 5.4 Quản lý mạng xã hội

**Mục tiêu:** quản lý các tài khoản mạng xã hội mà bạn sở hữu hoặc được ủy quyền quản lý (ví dụ tài khoản thương hiệu cho từng thị trường), mỗi tài khoản có môi trường đăng nhập ổn định.

**Loại proxy:** proxy dân cư tĩnh. IP cố định trong kỳ thuê, không giới hạn lưu lượng, 5 Mbps mỗi IP — phù hợp cho hồ sơ trình duyệt dùng lâu dài.

**Cách làm:**

1. Mỗi tài khoản một hồ sơ trình duyệt và một IP tĩnh cố định.
2. Chọn quốc gia của IP trùng với thị trường của tài khoản.
3. Không đổi IP liên tục cho cùng một tài khoản.
4. Tuân thủ chính sách của nền tảng: không tạo tài khoản giả, không spam, không thao túng tương tác.

## 5.5 Sử dụng có trách nhiệm và hợp pháp

Proxy là công cụ kỹ thuật; trách nhiệm sử dụng thuộc về người dùng. Hãy luôn:

- **Chỉ thu thập dữ liệu công khai.** Không vượt qua đăng nhập, paywall hoặc biện pháp bảo vệ để truy cập dữ liệu không được phép.
- **Tôn trọng dữ liệu cá nhân.** Tuân thủ quy định bảo vệ dữ liệu cá nhân của Việt Nam (Nghị định 13/2023/NĐ-CP) và luật của nơi dữ liệu được thu thập, như GDPR ở châu Âu.
- **Tôn trọng điều khoản dịch vụ** và `robots.txt` của website; giới hạn tốc độ để không gây quá tải.
- **Chỉ quản lý tài khoản bạn được phép quản lý.**
- **Không dùng cho** gian lận, spam, tấn công, phát tán mã độc, vượt kiểm soát truy cập hay bất kỳ hoạt động trái pháp luật nào.

Nếu không chắc một hoạt động có hợp pháp hay không, hãy tham khảo ý kiến chuyên gia pháp lý trước khi thực hiện.
