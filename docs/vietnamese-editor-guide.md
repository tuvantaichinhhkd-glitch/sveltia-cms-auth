# Hướng dẫn đăng nhập CMS cho người biên tập nội dung

Tài liệu này dành cho người viết và sửa bài trên website **chigaitaichinh.com**, không cần biết kỹ thuật. Làm một lần khoảng 10 phút, những lần sau chỉ cần bấm một nút.

> **Vì sao phải dùng GitHub?** Nội dung website được lưu trên GitHub, một dịch vụ lưu trữ miễn phí. Vì vậy trang quản trị dùng **tài khoản GitHub** để biết bạn là ai — đăng nhập vẫn bằng **email và mật khẩu** như mọi trang web khác, chỉ là email và mật khẩu đó thuộc tài khoản GitHub của bạn.

---

## Lần đầu tiên: 4 bước

### Bước 1 — Tạo tài khoản GitHub (miễn phí)

1. Mở `https://github.com/signup`
2. Nhập **email** bạn hay dùng, đặt **mật khẩu**, chọn một **tên người dùng** (username, ví dụ `lan-nguyen`)
3. Giải câu đố xác minh, sau đó nhập **mã gồm 8 số** GitHub gửi về email
4. Nếu được hỏi chọn gói, chọn **Free** (miễn phí). Các câu hỏi khảo sát có thể bỏ qua

Bạn đã có tài khoản GitHub thì bỏ qua bước này.

### Bước 2 — Gửi tên người dùng cho người quản trị

Nhắn **tên người dùng GitHub** (username ở bước 1) cho người quản trị website. Không gửi mật khẩu — người quản trị không bao giờ cần mật khẩu của bạn.

### Bước 3 — Chấp nhận lời mời

1. Bạn sẽ nhận email từ GitHub với tiêu đề có chữ **invited you to collaborate**
2. Bấm **View invitation**, rồi bấm **Accept invitation**

Không thấy email? Xem thư mục Spam/Quảng cáo, hoặc mở `https://github.com/notifications`. Lời mời **hết hạn sau 7 ngày** — quá hạn thì nhờ người quản trị mời lại.

> Bỏ qua bước này thì vẫn đăng nhập được, nhưng **không lưu được bài**. Đây là lỗi hay gặp nhất.

### Bước 4 — Đăng nhập trang quản trị

1. Mở `https://chigaitaichinh.com/admin/`
2. Bấm **Đăng nhập bằng GitHub**
3. Một cửa sổ nhỏ hiện ra. Nhập **email và mật khẩu GitHub** của bạn
4. Nếu GitHub gửi **mã xác minh** về email, mở email lấy mã và nhập vào
5. Lần đầu tiên, GitHub hỏi có cho phép **Sveltia CMS Authenticator** không — bấm nút xanh **Authorize**
6. Cửa sổ nhỏ tự đóng, bạn vào thẳng trang soạn nội dung

---

## Những lần sau

Mở `https://chigaitaichinh.com/admin/` → bấm **Đăng nhập bằng GitHub** → vào luôn.

Trên **máy khác** hoặc **trình duyệt khác**: làm lại bước 4, tức là nhập email và mật khẩu GitHub một lần trên máy đó.

> Không bấm **Đăng nhập bằng mã truy cập**. Nút đó dành cho người quản trị khi có sự cố.

---

## Quên mật khẩu

Mở `https://github.com/password_reset`, nhập email, rồi làm theo email GitHub gửi về. Đổi xong là đăng nhập lại CMS bình thường.

## GitHub yêu cầu "xác thực hai bước" (2FA)

GitHub có thể yêu cầu bạn bật bước bảo vệ thêm này sau một thời gian dùng. Khi được hỏi:

1. Cài ứng dụng **Google Authenticator** hoặc **Microsoft Authenticator** trên điện thoại
2. Làm theo hướng dẫn trên màn hình GitHub để quét mã QR
3. GitHub đưa ra một danh sách **recovery codes** — **tải về và cất kỹ** (lưu vào Google Drive hoặc in ra). Mất điện thoại mà không có các mã này thì rất khó lấy lại tài khoản

Từ đó, mỗi lần đăng nhập trên máy mới, sau khi nhập mật khẩu bạn mở ứng dụng trên điện thoại lấy mã 6 số.

---

## Gặp trục trặc

| Hiện tượng | Cách xử lý |
| --- | --- |
| Bấm **Đăng nhập bằng GitHub** mà không có cửa sổ nào hiện ra | Trình duyệt đang chặn cửa sổ bật lên. Bấm biểu tượng chặn ở cuối thanh địa chỉ → chọn **Luôn cho phép** với `chigaitaichinh.com`, rồi bấm lại |
| Vào được nhưng **không lưu được bài** | Bạn chưa chấp nhận lời mời ở bước 3. Kiểm tra email hoặc `https://github.com/notifications` |
| Cửa sổ nhỏ hiện lên rồi tắt ngay, CMS báo lỗi màu đỏ | Chụp màn hình gửi người quản trị — đây là lỗi cấu hình, không phải do bạn |
| Không nhận được mã xác minh qua email | Xem thư mục Spam, đợi 1–2 phút rồi bấm gửi lại |

---

## Giữ an toàn

- Không đưa mật khẩu GitHub cho bất kỳ ai, kể cả người quản trị
- Mỗi người dùng một tài khoản riêng, không dùng chung
- Dùng máy công cộng thì đăng xuất GitHub khi làm xong: vào `https://github.com`, bấm ảnh đại diện góc phải → **Sign out**
