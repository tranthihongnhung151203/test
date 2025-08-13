
# 🛠 Hướng dẫn Triển khai Dự án Lên Cloudflare Thông Qua GitHub (Kèm Kiểm Thử Chi Tiết)

---

## 1. Yêu cầu trước khi bắt đầu
- Tài khoản GitHub
- Tài khoản Cloudflare (miễn phí)
- Node.js đã cài đặt trên máy tính
- Git đã cài đặt

### Bước 1: Fork và Clone dự án
```
git clone https://github.com/Huy-Toan/library.git
cd library
npm install
```
**▶Kiểm thử:**  
✅ Chạy `npm start` hoặc `npm run dev` để kiểm tra dự án khởi động local không lỗi.

---

### Bước 2: Cài đặt Wrangler CLI
```
npm install -g wrangler
wrangler login
```
**▶Kiểm thử:**  
✅ Chạy `wrangler whoami` để xác nhận đăng nhập thành công.

⛅️ wrangler 4.29.0
───────────────────
Getting User settings...
👋 You are logged in with an OAuth Token, associated with the email intern@allxone.vn.

---

### Bước 3: Tạo D1 Database
```
wrangler d1 create react-fullstack-db
```
Lưu lại `database_id` trả về.  
**▶Kiểm thử:**  
✅ Chạy `wrangler d1 list` để xác nhận database được tạo.



---

### Bước 4: Tạo R2 Bucket
```
wrangler r2 bucket create react-fullstack-images
```
**▶Kiểm thử:**  
✅ Chạy `wrangler r2 bucket list` để xác nhận bucket tồn tại.

⛅️ wrangler 4.29.0
───────────────────
Listing buckets...
name:           book-image
creation_date:  2025-08-05T03:05:19.946Z

name:           react-fullstack-images
creation_date:  2025-08-05T06:11:22.600Z

---

### Bước 5: Cập nhật cấu hình `wrangler.toml`
Mở file `wrangler.toml`, thay `database_id` bằng ID ở bước 3:
```
[[d1_databases]]
binding = "DB"
database_name = "react-fullstack-db"
database_id = "PASTE_YOUR_NEW_DATABASE_ID_HERE"
```
**Kiểm thử:**  
✅ Chạy `wrangler check` để kiểm tra cấu hình hợp lệ.

---

### Bước 6: Setup Database Schema
Nếu có `init.sql`:
```
wrangler d1 execute react-fullstack-db --file=./init.sql
```
Hoặc dùng migrations:
```
wrangler d1 migrations apply react-fullstack-db
```
**▶Kiểm thử:**  
✅Chạy câu lệnh:
```
wrangler d1 execute react-fullstack-db --command="SELECT name FROM sqlite_master WHERE type='table';"
```
để kiểm tra schema đã tạo.


⛅️ wrangler 4.29.0
───────────────────
🌀 Executing on local database react-fullstack-db (03919115-6ef0-4baf-ac71-d75368caa049) from .wrangler\state\v3\d1:
🌀 To execute on your remote database, add a --remote flag to your wrangler command.
🚣 1 command executed successfully.
┌──────────────┐
│ name         │
├──────────────┤
│ _cf_METADATA │

---

### Bước 7: Test Local (Tùy chọn)
```
wrangler dev
```
hoặc
```
npm run dev
```
✅ **Kiểm thử:**  
Mở `http://localhost:8787`, kiểm tra chức năng, API và kết nối database.

---

### Bước 8: Commit và Push thay đổi
```
git add wrangler.toml
git commit -m "Update database ID for deployment"
git push origin main
```
**Kiểm thử:**  
Xác nhận trên GitHub repo đã nhận được thay đổi.

---
## Bước 9: Deploy qua Cloudflare Pages
✅Kiểm thử:
- Sau khi cấu hình xong, push code lên GitHub.
-Vào tab Actions trên GitHub repo, kiểm tra workflow “Deploy to Cloudflare Pages” chạy thành công (không báo lỗi đỏ).
- Truy cập domain Cloudflare Pages được cấp, xác nhận trang web hiển thị đúng.
- Nếu dùng Dashboard, kiểm tra trạng thái deploy là “Success”.


## Bước 10: Cấu hình Bindings
✅ Kiểm thử:
- Sau khi thêm bindings, deploy lại project.
- Vào Cloudflare Dashboard > Workers & Pages > Project > Functions > Logs hoặc dùng lệnh:
- Kiểm tra logs không có lỗi kiểu “binding not found” hoặc “undefined”.
- Gọi thử API sử dụng DB/R2 (ví dụ: endpoint lấy danh sách sách hoặc upload ảnh), xác nhận trả về dữ liệu đúng.


## Bước 11: Cấu hình Environment Variables và Secrets
✅ Kiểm thử:
- Trong Dashboard, vào Environment Variables, xác nhận các biến đã được thêm đúng.
- Deploy lại project.
- Thêm đoạn log tạm thời trong code để in giá trị biến (không in secret ra ngoài production).
- Gọi endpoint sử dụng biến này, xác nhận ứng dụng hoạt động đúng (ví dụ: truy cập file public R2, thao tác với R2 qua API).

## Bước 12: Setup Database trên Production
✅ Kiểm thử:
- Sau khi chạy migration hoặc execute SQL, kiểm tra schema:
- Đảm bảo các bảng đã được tạo đúng.
- Gọi API truy vấn dữ liệu, xác nhận không lỗi schema.

## Bước 13: Redeploy để áp dụng bindings và secrets
✅ Kiểm thử:
- Push một commit nhỏ (ví dụ: sửa README) hoặc nhấn Retry deployment trên Dashboard.
- Kiểm tra trạng thái deploy là “Success”.
- Truy cập lại ứng dụng, xác nhận các chức năng liên quan đến binding/secrets hoạt động đúng (ví dụ: truy vấn DB, upload ảnh, truy cập file public R2).


---


## Các lệnh hữu ích hỗ trợ quản lý
| Lệnh                             | Mục đích                        |
|---------------------------------|--------------------------------|
| `wrangler d1 list`               | Xem danh sách databases        |
| `wrangler r2 bucket list`        | Xem danh sách R2 buckets       |
| `wrangler d1 execute --command` | Query database                 |
| `wrangler tail`                  | Xem logs realtime worker       |
| `wrangler r2 object put`         | Upload file lên R2              |

---

## 🌟 Ưu điểm của quy trình triển khai
- **Mạng lưới toàn cầu Cloudflare Workers:** Tương tác rất nhanh, giảm độ trễ nhờ mạng edge toàn cầu.
- **Wrangler CLI quản lý hiệu quả:** Giúp thao tác đơn giản khi deploy và quản lý D1, R2.
- **Kiểm thử đầy đủ:** Kết hợp test thủ công và tự động với GitHub Actions, đảm bảo chất lượng code.
- **Tiết kiệm chi phí:** Free tier Cloudflare phù hợp cho nhiều dự án nhỏ và vừa.
- **Dễ dàng tích hợp CI/CD:** Tự động hóa deploy qua GitHub giúp quá trình phát triển chuyên nghiệp và tối ưu.

---

**Chúc mừng! Dự án của bạn đã sẵn sàng trên Cloudflare! 🚀**
