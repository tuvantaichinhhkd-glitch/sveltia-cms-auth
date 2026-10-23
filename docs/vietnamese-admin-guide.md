# Hướng dẫn đăng nhập & vận hành CMS (tiếng Việt)

Tài liệu này dành cho người quản trị nội dung website **chigaitaichinh.com**. Nó giải thích cách đăng nhập vào Sveltia CMS, cách xử lý khi đổi máy, cách thêm người cùng sửa nội dung, và bảng tra mã lỗi khi đăng nhập thất bại.

Phần kỹ thuật bằng tiếng Anh nằm ở [README.md](../README.md).

---

## 1. Cấu hình hiện tại

| Hạng mục | Giá trị |
| --- | --- |
| Địa chỉ CMS | `https://chigaitaichinh.com/admin/` |
| Worker xác thực (OAuth) | `https://sveltia-cms-auth.tuvantaichinhhkd.workers.dev` |
| `base_url` trong `admin/config.yml` | chính là địa chỉ Worker ở trên |
| Biến `ALLOWED_DOMAINS` trên Cloudflare | `chigaitaichinh.com, www.chigaitaichinh.com` |
| Biến `GITHUB_CLIENT_ID` | Client ID của GitHub OAuth App |
| Biến `GITHUB_CLIENT_SECRET` | Client Secret của GitHub OAuth App — **phải bấm Encrypt** để lưu dạng mã hoá |
| Nơi chỉnh các biến này | Cloudflare dashboard → Workers → `sveltia-cms-auth` → **Settings** → **Variables and Secrets** |

> **Lưu ý:** sau khi thêm hoặc sửa bất kỳ biến nào ở Cloudflare, phải **Deploy** lại Worker. Chỉ bấm Save là chưa có hiệu lực.

---

## 2. Đăng nhập bằng cách nào?

**CMS không có tài khoản email + mật khẩu riêng.** Toàn bộ quyền đăng nhập dựa trên tài khoản GitHub, vì nội dung website được lưu trong repository GitHub.

Có hai cách:

### Cách 1 — Sign in with GitHub (đang dùng, khuyến nghị)

1. Mở `https://chigaitaichinh.com/admin/`
2. Bấm **Sign in with GitHub**
3. Một cửa sổ pop-up mở ra:
   - Nếu chưa đăng nhập GitHub trên máy đó → GitHub hỏi email/mật khẩu GitHub (và mã 2FA nếu có bật)
   - Nếu đăng nhập lần đầu → GitHub hiện trang **Authorize Sveltia CMS Authenticator**, bấm **Authorize**
4. Pop-up tự đóng, CMS vào thẳng giao diện soạn nội dung

Lần sau trên cùng máy, cùng trình duyệt, bước 3 thường bị bỏ qua — bấm là vào luôn.

### Cách 2 — Personal Access Token (dự phòng, khi Worker có sự cố)

Dùng khi Cloudflare Worker gặp lỗi mà vẫn cần sửa nội dung gấp:

1. Vào `https://github.com/settings/tokens` → **Generate new token (classic)**
2. Chọn quyền (scope) `repo`
3. Copy token (chỉ hiện một lần)
4. Ở trang đăng nhập CMS, chọn phương thức token và dán vào

Token là chuỗi bí mật tương đương mật khẩu — không gửi qua chat, không lưu vào file trong repo.

---

## 3. Đổi máy hoặc dùng máy khác thì sao?

Không cần làm gì thêm ở Cloudflare hay GitHub. Tài khoản GitHub không bị khoá theo thiết bị.

Trên máy mới:

1. Mở `https://chigaitaichinh.com/admin/`
2. Bấm **Sign in with GitHub**
3. Nhập **email + mật khẩu GitHub** (đây là tài khoản GitHub, không phải "mật khẩu CMS")
4. Nếu có bật 2FA, nhập mã xác thực
5. Bấm **Authorize** nếu GitHub hỏi lại

Vào được như bình thường. Lý do máy hiện tại "vào thẳng" là vì trình duyệt đã nhớ phiên đăng nhập GitHub, không phải vì máy đó được cấp quyền riêng.

**Nên chuẩn bị trước khi đổi máy:**

- Biết chắc email và mật khẩu GitHub đang dùng
- Nếu đã bật 2FA: mang theo điện thoại có app xác thực, hoặc lưu **recovery codes** ở nơi an toàn (`https://github.com/settings/auth/recovery-codes`)
- Trên máy lạ, máy dùng chung: đăng xuất GitHub sau khi làm xong

---

## 4. Thêm người khác cùng sửa nội dung

**Không bao giờ chia sẻ mật khẩu GitHub của mình.** Thay vào đó, mời họ bằng tài khoản GitHub riêng của họ:

1. Người đó tự tạo tài khoản GitHub (miễn phí)
2. Vào repository chứa website trên GitHub → **Settings** → **Collaborators** → **Add people**
3. Nhập username hoặc email GitHub của họ, chọn quyền **Write**
4. Họ nhận email mời, bấm chấp nhận
5. Từ đó họ tự vào `https://chigaitaichinh.com/admin/` và bấm **Sign in with GitHub** bằng tài khoản của chính họ

Ưu điểm: mỗi thay đổi nội dung được ghi nhận đúng tên người làm, và khi họ không còn phụ trách thì chỉ cần xoá khỏi danh sách Collaborators — không phải đổi mật khẩu của ai.

---

## 5. Bảng tra lỗi khi đăng nhập thất bại

### Cách đọc mã lỗi

Pop-up đăng nhập có thể tắt rất nhanh khiến không đọc được thông báo. Cách xem chắc chắn, không cần công cụ lập trình:

1. Mở trực tiếp đường dẫn này trên một tab mới:

   ```
   https://sveltia-cms-auth.tuvantaichinhhkd.workers.dev/auth?provider=github&site_id=chigaitaichinh.com
   ```

2. Nếu chạy đúng, trang sẽ nhảy sang màn hình đăng nhập/authorize của GitHub
3. Nếu lỗi, trang sẽ trắng hoặc hiện thông báo. Bấm **Ctrl + U** để xem mã nguồn, tìm chữ `errorCode` — giá trị bên cạnh là mã lỗi

### Bảng tra

| Mã lỗi | Nghĩa là gì | Cách xử lý |
| --- | --- | --- |
| `MISCONFIGURED_CLIENT` | Worker chưa có Client ID / Client Secret của GitHub | Vào Cloudflare → `sveltia-cms-auth` → Settings → Variables and Secrets, thêm `GITHUB_CLIENT_ID` và `GITHUB_CLIENT_SECRET` (bấm Encrypt cho secret), rồi **Deploy** lại |
| `UNSUPPORTED_DOMAIN` | Tên miền của website không khớp danh sách `ALLOWED_DOMAINS` | Sửa `ALLOWED_DOMAINS` thành đúng `chigaitaichinh.com, www.chigaitaichinh.com` — **chỉ tên miền trần**, không có `https://`, không có dấu `/` ở cuối. Xem mục 6 bên dưới |
| `UNSUPPORTED_BACKEND` | Khai báo `backend.name` trong `admin/config.yml` sai | Phải là `github` (hoặc `gitlab`), không viết hoa, không viết tắt khác |
| `AUTH_CODE_REQUEST_FAILED` | GitHub trả về mà không kèm mã xác thực | Thường do bấm **Cancel** ở màn hình Authorize. Nếu lần nào cũng lỗi: kiểm tra Authorization callback URL của GitHub OAuth App phải đúng `https://sveltia-cms-auth.tuvantaichinhhkd.workers.dev/callback` |
| `CSRF_DETECTED` | Cookie bảo vệ phiên đăng nhập bị mất giữa đường | Trình duyệt đang chặn cookie, hoặc đang ở chế độ ẩn danh / chặn theo dõi nghiêm ngặt, hoặc mở dở flow ở cửa sổ này rồi hoàn tất ở cửa sổ khác. Cho phép cookie với tên miền Worker và làm lại từ đầu trong **một** cửa sổ |
| `TOKEN_REQUEST_FAILED` | GitHub từ chối đổi mã xác thực thành token | Gần như luôn là do Client Secret sai, bị copy thiếu, hoặc đã bị tạo lại. Vào GitHub OAuth App tạo secret mới, cập nhật `GITHUB_CLIENT_SECRET` trên Cloudflare, Deploy lại |
| `MALFORMED_RESPONSE` | GitHub (hoặc thứ gì đứng trước nó) trả về dữ liệu không đọc được | Kiểm tra biến `GITHUB_HOSTNAME` nếu có đặt. Xem thêm log ở Cloudflare → Worker → tab **Logs** |

### Không hiện mã lỗi nào, chỉ "pop-up hiện lên rồi biến mất"

Theo thứ tự:

1. Mở lại và đọc mã lỗi theo cách ở trên — đa số trường hợp vẫn là một trong các mã trong bảng
2. Kiểm tra trình duyệt có chặn pop-up không (biểu tượng ở thanh địa chỉ)
3. Thử trình duyệt khác, hoặc tắt tiện ích chặn quảng cáo
4. Kiểm tra đang mở CMS bằng `https://`, không phải `http://` — token chỉ được giao cho trang chạy HTTPS

---

## 6. Lỗi hay gặp nhất: `ALLOWED_DOMAINS` ghi sai định dạng

`ALLOWED_DOMAINS` chỉ nhận **tên miền trần**. Ghi kèm `https://` hay dấu `/` ở cuối sẽ làm mọi lần đăng nhập đều lỗi `UNSUPPORTED_DOMAIN`.

| Đúng                                         | Sai                           |
| -------------------------------------------- | ----------------------------- |
| `chigaitaichinh.com, www.chigaitaichinh.com` | `https://chigaitaichinh.com/` |
| `www.example.com`                            | `www.example.com/`            |
| `*.example.com`                              | `https://*.example.com:443`   |

Ghi hoa hay thường không quan trọng — `Chigaitaichinh.com` và `chigaitaichinh.com` được coi là một.

---

## 7. Những việc tuyệt đối không làm

- Không chia sẻ mật khẩu GitHub cho bất kỳ ai — muốn thêm người thì mời làm Collaborator (mục 4)
- Không lưu `GITHUB_CLIENT_SECRET` dạng chữ thường (plain text) trên Cloudflare — luôn bấm **Encrypt**
- Không dán Client Secret hay Personal Access Token vào file trong repository, vào chat, hay vào email
- Không để trống `ALLOWED_DOMAINS` — để trống thì website khác cũng dùng được Worker này và có thể lấy được token
- Không xoá GitHub OAuth App đang dùng; nếu nghi secret bị lộ thì tạo secret mới rồi cập nhật lên Cloudflare

---

## 8. Checklist khi có sự cố

| Bước | Kiểm tra | Ở đâu |
| --- | --- | --- |
| 1 | Đọc `errorCode` | Mở Worker URL trực tiếp + Ctrl + U |
| 2 | `GITHUB_CLIENT_ID`, `GITHUB_CLIENT_SECRET` đã có và đã Deploy | Cloudflare → Worker → Settings → Variables and Secrets |
| 3 | `ALLOWED_DOMAINS` là tên miền trần, đúng cả bản có và không có `www` | cùng chỗ trên |
| 4 | Callback URL khớp | GitHub → Settings → Developer settings → OAuth Apps |
| 5 | `base_url` trong CMS trỏ đúng Worker | `admin/config.yml` trong repository website |
| 6 | Tài khoản GitHub có quyền Write trên repository | GitHub → repo → Settings → Collaborators |
