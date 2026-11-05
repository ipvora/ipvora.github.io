# 3. Ví dụ code

Tất cả ví dụ đọc thông tin kết nối từ biến môi trường `PROXY_HOST`, `PROXY_PORT`, `PROXY_USER`, `PROXY_PASS` (xem phần 1). Đây là **placeholder** — hãy thay bằng giá trị thật từ trang quản lý tài khoản.

## 3.1 curl

```bash
# Kiểm tra IP đầu ra qua HTTP proxy
curl -x "http://$PROXY_USER:$PROXY_PASS@$PROXY_HOST:$PROXY_PORT" https://api.ipify.org

# Tách thông tin đăng nhập ra tham số riêng
curl -x "http://$PROXY_HOST:$PROXY_PORT" -U "$PROXY_USER:$PROXY_PASS" https://ipinfo.io/json

# SOCKS5, DNS phân giải phía proxy, có timeout
curl --max-time 30 -x "socks5h://$PROXY_USER:$PROXY_PASS@$PROXY_HOST:$PROXY_PORT" https://ipinfo.io/json

# Xem chi tiết quá trình kết nối khi gỡ lỗi
curl -v -x "http://$PROXY_USER:$PROXY_PASS@$PROXY_HOST:$PROXY_PORT" https://example.com -o /dev/null
```

## 3.2 Python với requests

Cài đặt: `pip install requests` (thêm `requests[socks]` nếu dùng SOCKS5).

```python
import os
import time
import requests

HOST = os.environ["PROXY_HOST"]
PORT = os.environ["PROXY_PORT"]
USER = os.environ["PROXY_USER"]
PASS = os.environ["PROXY_PASS"]

def build_proxy(scheme: str = "http") -> str:
    # Sửa hàm này theo định dạng thật (quốc gia, mã phiên...) trên trang quản lý
    return f"{scheme}://{USER}:{PASS}@{HOST}:{PORT}"

proxy = build_proxy()
proxies = {"http": proxy, "https": proxy}

def fetch(url: str, retries: int = 3) -> requests.Response:
    for attempt in range(1, retries + 1):
        try:
            r = requests.get(url, proxies=proxies, timeout=(10, 30))
            if r.status_code in (429, 502, 503):
                raise requests.HTTPError(f"status {r.status_code}")
            return r
        except requests.RequestException as e:
            if attempt == retries:
                raise
            time.sleep(2 ** attempt)  # backoff tăng dần

print(fetch("https://api.ipify.org").text)
```

### Giữ phiên với requests.Session

`requests.Session` giữ cookie và tái sử dụng kết nối. Kết hợp với sticky session ở phía proxy, bạn có thể đi qua một luồng nhiều bước với cùng IP:

```python
s = requests.Session()
s.proxies.update(proxies)
s.headers.update({"Accept-Language": "vi-VN,vi;q=0.9"})
home = s.get("https://example.com/", timeout=30)
page = s.get("https://example.com/san-pham", timeout=30)
```

## 3.3 Python với httpx (đồng bộ và bất đồng bộ)

Cài đặt: `pip install httpx` (thêm `httpx[socks]` cho SOCKS5).

```python
import asyncio, os
import httpx

proxy = f"http://{os.environ['PROXY_USER']}:{os.environ['PROXY_PASS']}@{os.environ['PROXY_HOST']}:{os.environ['PROXY_PORT']}"

# Đồng bộ
with httpx.Client(proxy=proxy, timeout=30) as client:
    print(client.get("https://api.ipify.org").text)

# Bất đồng bộ, giới hạn số request đồng thời
async def main(urls):
    sem = asyncio.Semaphore(5)
    async with httpx.AsyncClient(proxy=proxy, timeout=30) as client:
        async def one(u):
            async with sem:
                r = await client.get(u)
                return u, r.status_code
        return await asyncio.gather(*(one(u) for u in urls))

print(asyncio.run(main(["https://example.com"] * 3)))
```

Ghi chú: các phiên bản httpx cũ dùng tham số `proxies=` thay vì `proxy=`.

## 3.4 Node.js

Node.js không tự dùng biến môi trường proxy cho `fetch`. Cách đơn giản là dùng thư viện `undici` (HTTP) hoặc `socks-proxy-agent` (SOCKS5).

```bash
npm install undici socks-proxy-agent
```

```javascript
// HTTP proxy với undici
import { fetch, ProxyAgent } from "undici";

const { PROXY_USER, PROXY_PASS, PROXY_HOST, PROXY_PORT } = process.env;
const agent = new ProxyAgent(
  `http://${encodeURIComponent(PROXY_USER)}:${encodeURIComponent(PROXY_PASS)}@${PROXY_HOST}:${PROXY_PORT}`
);

const res = await fetch("https://api.ipify.org", { dispatcher: agent });
console.log(await res.text());
```

```javascript
// SOCKS5 với module https có sẵn
import https from "node:https";
import { SocksProxyAgent } from "socks-proxy-agent";

const { PROXY_USER, PROXY_PASS, PROXY_HOST, PROXY_PORT } = process.env;
const agent = new SocksProxyAgent(
  `socks5h://${PROXY_USER}:${PROXY_PASS}@${PROXY_HOST}:${PROXY_PORT}`
);

https.get("https://api.ipify.org", { agent }, (res) => {
  let body = "";
  res.on("data", (c) => (body += c));
  res.on("end", () => console.log(body));
});
```

## 3.5 Playwright (Python)

Cài đặt: `pip install playwright && playwright install chromium`.

```python
import os
from playwright.sync_api import sync_playwright

proxy = {
    "server": f"http://{os.environ['PROXY_HOST']}:{os.environ['PROXY_PORT']}",
    "username": os.environ["PROXY_USER"],
    "password": os.environ["PROXY_PASS"],
}

with sync_playwright() as p:
    browser = p.chromium.launch(proxy=proxy)
    context = browser.new_context(locale="vi-VN", timezone_id="Asia/Ho_Chi_Minh")
    page = context.new_page()
    page.goto("https://ipinfo.io/json", timeout=60_000)
    print(page.inner_text("body"))
    browser.close()
```

Mẹo:

- Đặt `locale` và `timezone_id` khớp với quốc gia của IP để trang hiển thị nhất quán.
- Mỗi `context` có thể dùng một proxy riêng (truyền `proxy=` vào `new_context`) nếu bạn cần nhiều IP trong cùng một trình duyệt.
- Chặn tải ảnh, font, video khi không cần để tiết kiệm dung lượng (proxy xoay tính phí theo GB):

```python
context.route("**/*", lambda route: route.abort()
              if route.request.resource_type in ("image", "media", "font")
              else route.continue_())
```

## 3.6 Playwright (Node.js)

```javascript
import { chromium } from "playwright";

const { PROXY_USER, PROXY_PASS, PROXY_HOST, PROXY_PORT } = process.env;
const browser = await chromium.launch({
  proxy: { server: `http://${PROXY_HOST}:${PROXY_PORT}`, username: PROXY_USER, password: PROXY_PASS },
});
const page = await browser.newPage();
await page.goto("https://api.ipify.org");
console.log(await page.textContent("body"));
await browser.close();
```

## 3.7 Thực hành tốt

- **Timeout rõ ràng:** proxy dân cư đi qua kết nối dân dụng nên độ trễ dao động; đặt timeout 20–60 giây và có retry.
- **Backoff:** khi gặp 429 hoặc lỗi mạng, chờ tăng dần trước khi thử lại.
- **Giới hạn đồng thời:** bắt đầu với số luồng nhỏ, tăng dần và theo dõi tỷ lệ lỗi.
- **Ghi log IP đầu ra** theo từng phiên để dễ gỡ lỗi.
- **Tiết kiệm dung lượng:** chỉ tải những gì cần (HTML/JSON), bật nén (`Accept-Encoding: gzip`), tránh tải lại trang không thay đổi.
