# Tài liệu chức năng: cancel.py

---

## 1. Mục đích

Quản lý toàn bộ luồng hủy đặt phòng khách sạn qua chatbot, bao gồm:
- Tiếp nhận yêu cầu hủy booking từ người dùng qua mã booking, email hoặc số điện thoại.
- Truy vấn và xác thực thông tin booking.
- Hiển thị danh sách booking nếu có nhiều booking liên quan.
- Xác nhận lại thao tác hủy với người dùng.
- Thực hiện hủy booking và các dữ liệu liên quan trong database.
- Đa ngôn ngữ, hỗ trợ fallback message khi dịch lỗi.

---

## 2. Các hàm chính & chức năng

### 2.1. `normalize_phone(phone)`
- **Chức năng:** Chuẩn hóa số điện thoại về định dạng chuẩn (bắt đầu bằng 0, loại bỏ ký tự không phải số).
- **Input:** Số điện thoại (str/int/float).
- **Output:** Số điện thoại chuẩn hóa (str) hoặc None nếu không hợp lệ.

---

### 2.2. `extract_cancel_info(text, parameters=None)`
- **Chức năng:** Trích xuất thông tin hủy booking từ văn bản hoặc parameters (mã booking, email, số điện thoại).
- **Input:**  
  - `text`: Chuỗi nhập tự do từ người dùng.
  - `parameters`: dict (tùy chọn) chứa các trường đã nhận diện.
- **Output:** Dict gồm `booking_id`, `email`, `phone`.

---

### 2.3. `find_booking_by_id(booking_id)`
- **Chức năng:** Truy vấn thông tin chi tiết của một booking theo mã booking.
- **Input:** `booking_id` (str/int).
- **Output:** Dict thông tin booking hoặc None nếu không tìm thấy.

---

### 2.4. `find_bookings_by_contact(email=None, phone=None)`
- **Chức năng:** Truy vấn danh sách booking theo email hoặc số điện thoại.
- **Input:**  
  - `email`: Email khách hàng.
  - `phone`: Số điện thoại khách hàng.
- **Output:** List các dict booking phù hợp.

---

### 2.5. `cancel_booking(booking_id)`
- **Chức năng:** Thực hiện hủy booking và xóa các dữ liệu liên quan trong các bảng DB.
- **Input:** `booking_id` (str/int).
- **Output:** Tuple (success: bool, message: str).

---

### 2.6. `format_booking_info(booking)`
- **Chức năng:** Định dạng thông tin booking thành chuỗi hiển thị cho người dùng.
- **Input:** Dict thông tin booking.
- **Output:** Chuỗi mô tả booking.

---

### 2.7. `format_bookings_list(bookings)`
- **Chức năng:** Định dạng danh sách booking thành chuỗi hiển thị cho người dùng.
- **Input:** List các dict booking.
- **Output:** Chuỗi danh sách booking.

---

### 2.8. `get_cancel_prompt(key, lang_code)`
- **Chức năng:** Lấy prompt hội thoại theo ngôn ngữ và bước xử lý.
- **Input:**  
  - `key`: Tên bước hoặc trạng thái.
  - `lang_code`: Mã ngôn ngữ (`vi`, `en`...).
- **Output:** Chuỗi prompt phù hợp.

---

### 2.9. `cancel_webhook(request)`
- **Chức năng:** Xử lý toàn bộ luồng hội thoại hủy booking với người dùng.
- **Input:**  
  - `request`: dict chứa thông tin session, intent, text, parameters từ Dialogflow/chatbot.
- **Output:** Dict fulfillment_response cho chatbot, cập nhật session.

- **Luồng xử lý:**
  1. Xác định ngôn ngữ người dùng, dịch input sang tiếng Việt nếu cần.
  2. Trích xuất thông tin từ user_input (mã booking, email, SĐT).
  3. Nếu có mã booking, tìm booking và xác nhận lại với user.
  4. Nếu chỉ có email/SĐT, truy vấn danh sách booking, hiển thị danh sách để chọn.
  5. Khi user xác nhận mã booking, hiển thị thông tin chi tiết và hỏi xác nhận hủy.
  6. Nếu user xác nhận, thực hiện hủy booking, xóa dữ liệu liên quan, trả về thông báo thành công.
  7. Nếu user không xác nhận, hủy bỏ quá trình hủy booking.
  8. Hỗ trợ đa ngôn ngữ, dịch prompt và phản hồi nếu cần.

---

## 3. Lưu ý & Best Practices

- **Đa ngôn ngữ:**  
  Tự động dịch prompt và phản hồi nếu người dùng không dùng tiếng Việt. Nếu dịch lỗi hoặc ngôn ngữ không hỗ trợ, trả về fallback message bằng tiếng Việt hoặc tiếng Anh.
- **Bảo mật:**  
  Không log thông tin nhạy cảm (email, SĐT, mã booking) ra console/log file.  
  Đảm bảo truy vấn SQL luôn dùng tham số hóa để tránh SQL Injection.
- **Kiểm tra trạng thái booking:**  
  Chỉ cho phép hủy booking còn hiệu lực, chưa check-in, chưa quá hạn.
- **Xử lý lỗi:**  
  Trả về thông báo rõ ràng nếu không tìm thấy booking, booking đã hủy, hoặc lỗi khi xóa dữ liệu.
- **Quản lý session:**  
  Sử dụng dict `user_cancel_sessions` để lưu trạng thái hội thoại cho từng user/session.

---

## 4. Checklist kiểm thử

- [ ] Hủy booking bằng mã booking hợp lệ.
- [ ] Hủy booking bằng email hoặc số điện thoại (có nhiều booking).
- [ ] Nhập sai mã booking, email, SĐT.
- [ ] Hủy booking đã hết hạn hoặc đã check-in.
- [ ] Hủy booking đã bị hủy trước đó.
- [ ] Hủy booking với ngôn ngữ khác tiếng Việt (kiểm tra dịch và fallback message).
- [ ] Kiểm tra bảo mật: không log thông tin nhạy cảm ra console/log file.
- [ ] Kiểm tra trường hợp user xác nhận sai/thay đổi ý định khi hủy.
- [ ] Lỗi kết nối DB, lỗi khi xóa booking.
- [ ] Kiểm tra trường hợp nhập lại thông tin nhiều lần.

---

## 5. Ví dụ phản hồi

**Khi hỏi thông tin hủy:**
```json
{
  "fulfillment_response": {
    "messages": [
      {"text": {"text": ["🔍 **Hủy đặt phòng**\n\nVui lòng cung cấp một trong các thông tin:\n• Mã booking (ví dụ: #123)\n• Email đã đặt phòng\n• Số điện thoại đã đặt phòng"]}}
    ]
  }
}
```

**Khi xác nhận hủy thành công:**
```json
{
  "fulfillment_response": {
    "messages": [
      {"text": {"text": ["✅ **Hủy booking thành công!**\n\n🆔 **Mã booking:** #123\n🏨 **Khách sạn:** Cat Ba Resort\n📅 **Ngày hủy:** 10/08/2024 14:30\n\n💡 **Lưu ý:**\n• Hoàn tiền sẽ được xử lý trong 3-5 ngày làm việc\n• Liên hệ hotline nếu cần hỗ trợ: 0123 456 789\n• Cảm ơn bạn đã sử dụng dịch vụ của chúng tôi!"]}}
    ]
  }
}
```

---

## 6. Liên hệ & bảo trì

- Mọi thắc mắc, liên hệ nhóm phát triển AI-integrate.
- Đảm bảo cập nhật khi có thay đổi về cấu trúc DB hoặc quy trình
