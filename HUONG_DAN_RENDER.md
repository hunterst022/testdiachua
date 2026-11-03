# Đưa Chánh Điện Tịnh Tâm lên Render

## 1. Đẩy mã lên GitHub
Giải nén, tạo repo GitHub (nên để **Private**) rồi push toàn bộ thư mục này lên.

## 2. Tạo dịch vụ trên Render
**Cách nhanh:** Render → New → **Blueprint** → chọn repo → nhập `CONTROL_PASSWORD` (mật khẩu bảng điều khiển, tự đặt, dài và khó đoán) → Apply.

**Cách thủ công:** New → **Web Service** → chọn repo, rồi điền:

| Mục | Giá trị |
|---|---|
| Runtime | Node |
| Build Command | `cd live && npm ci --omit=dev` |
| Start Command | `cd live && node server.mjs` |
| Health Check Path | `/healthz` |
| Region | Singapore |
| Instance type | **Free** |

Environment variables:

| Tên | Bắt buộc | Ý nghĩa |
|---|---|---|
| `NO_AUTH` | Có (nếu không dùng mật khẩu) | `1` = bảng điều khiển không cần mật khẩu |
| `CONTROL_PASSWORD` | Không | Chỉ dùng khi muốn có mật khẩu; đặt thì bỏ `NO_AUTH` |
| `TZ` | Nên | `Asia/Ho_Chi_Minh` để lịch phát và mục tiêu ngày theo giờ Việt Nam |
| `TIKTOK_USERNAME` | Không | Tự kết nối live `@username` mỗi khi server khởi động |
| `EULER_API_KEY` | Không | Khóa của Euler Stream, dùng khi TikTok chặn IP máy chủ |
| `DATA_DIR` | Không | Thư mục của Persistent Disk (ví dụ `/var/data`) để giữ số nén hương khi server khởi động lại |

## 3. Dùng
Giả sử địa chỉ Render là `https://ten-cua-ban.onrender.com`:

- **Bảng điều khiển:** `https://ten-cua-ban.onrender.com/live/control.html` (hỏi mật khẩu một lần mỗi tab)
- **Overlay 2.5D:** `https://ten-cua-ban.onrender.com/live/overlay.html`
- **Overlay 3D:** `https://ten-cua-ban.onrender.com/live/overlay3d.html`

OBS: *Browser source* → dán link overlay, kích thước 1080×1920 → chuột phải → *Interact* → **Bật âm thanh**.
Trong bảng điều khiển, nhập `@username` rồi bấm **Kết nối** (hoặc đặt `TIKTOK_USERNAME`).

## Dùng gói Free
- Gói Free **tự ngủ sau 15 phút không có truy cập**. Server đã có sẵn cơ chế tự gọi chính nó mỗi 10 phút để không ngủ khi đang chạy.
- Nó **không tự thức dậy được** sau khi đã ngủ. Trước khi live, mở `https://<địa-chỉ>/live/control.html` để đánh thức (mất khoảng 30 đến 60 giây).
- Muốn chắc hơn, tạo thêm một monitor miễn phí trên UptimeRobot hoặc cron-job.org gọi `https://<địa-chỉ>/healthz` mỗi 5 phút.
- Đặt `TIKTOK_USERNAME` để server tự kết nối lại live nếu nó bị khởi động lại.
- Gói Free có khoảng 750 giờ chạy mỗi tháng, đủ cho một dịch vụ chạy cả tháng. Mỗi lần khởi động lại, số nén hương trong ngày về 0.
- Tắt cơ chế tự gọi bằng biến `KEEP_ALIVE=0`.

## Lưu ý
- Overlay là trang công khai (cần vậy để OBS tải được); chỉ bảng điều khiển có mật khẩu.
- Server tự kết nối lại sau 20 giây nếu mất kết nối với phòng live.
- TikTok có thể hạn chế IP của máy chủ đám mây. Nếu bảng điều khiển báo lỗi kết nối: thử đặt `EULER_API_KEY`, hoặc chạy bản này trên máy cá nhân (`cd live && npm start`).
- `tiktok-live-connector` là thư viện cộng đồng, có thể ngừng chạy khi TikTok đổi hệ thống.
- Không có Persistent Disk thì `data.json` mất mỗi lần deploy/khởi động lại; điều này chỉ làm số nén hương trong ngày về 0.
