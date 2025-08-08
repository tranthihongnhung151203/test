# Tài liệu chức năng chi tiết: booking.py

---

## 1. Mục đích

Quản lý toàn bộ luồng đặt phòng khách sạn qua chatbot, bao gồm:
- Nhận thông tin khách hàng.
- Tìm phòng phù hợp.
- Chọn dịch vụ/tour bổ sung.
- Xác nhận thông tin.
- Chọn phương thức thanh toán.
- Lưu thông tin đặt phòng vào nhiều bảng liên quan trong cơ sở dữ liệu.
- Hỗ trợ kiểm tra mã giảm giá, gợi ý ưu đãi, truy vấn lịch sử đặt phòng.

---

## 2. Các hàm chính & chức năng

### 2.1. `extract_booking_info(text, context_param=None)`
- **Chức năng:** Trích xuất thông tin đặt phòng từ câu nhập tự do của người dùng.
- **Input:**  
  - `text`: Chuỗi nhập tự do từ người dùng.
  - `context_param`: (tùy chọn) chỉ định trường cần ưu tiên trích xuất.
- **Output:** Dict chứa các trường: destination, room_type, checkin_date, checkout_date, adults, children, name, email, phone, budget, hotel_class.

---

### 2.2. `get_activities_for_room(room_id)`
- **Chức năng:** Lấy danh sách dịch vụ/tour theo phòng.
- **Input:** `room_id` (ID phòng).
- **Output:** List các dịch vụ/tour phù hợp với phòng.

---

### 2.3. `save_full_booking(session)`
- **Chức năng:** Lưu thông tin đặt phòng vào các bảng DB (pm_user, pm_booking, pm_booking_room, pm_booking_activity).
- **Input:** `session` (dict thông tin đặt phòng đã hoàn chỉnh).
- **Output:** booking_id và chuỗi xác nhận đặt phòng hoặc thông báo lỗi.

---

### 2.4. `booking_webhook(request)`
- **Chức năng:** Xử lý toàn bộ luồng hội thoại đặt phòng với người dùng.
- **Input:**  
  - `request`: dict chứa thông tin session, intent, text, parameters từ Dialogflow/chatbot.
- **Output:** Dict fulfillment_response cho chatbot.
- **Luồng xử lý:**
  1. Xác định ngôn ngữ, dịch input nếu cần.
  2. Hỏi lần lượt: điểm đến → ngày nhận/trả phòng → số người lớn/trẻ em → ngân sách → số sao.
  3. Tìm phòng phù hợp, cho phép chọn phòng.
  4. Nếu có dịch vụ/tour, cho phép chọn nhiều dịch vụ, nhập ngày sử dụng, số người.
  5. Hỏi thông tin khách hàng (họ tên, email, địa chỉ, SĐT...).
  6. Xác nhận lại toàn bộ thông tin.
  7. Chọn phương thức thanh toán.
  8. Lưu booking vào DB, trả về xác nhận thành công.

---

### 2.5. `search_rooms_for_booking(session, session_id=None)`
- **Chức năng:** Tìm kiếm phòng phù hợp với yêu cầu người dùng, kiểm tra phòng trống thực tế.
- **Input:**  
  - `session`: dict thông tin đặt phòng hiện tại.
  - `session_id`: (tùy chọn) để lưu danh sách phòng vào session.
- **Output:** Chuỗi mô tả danh sách phòng phù hợp hoặc thông báo lỗi.

---

### 2.6. `create_booking_confirmation(session)`
- **Chức năng:** Tạo nội dung xác nhận lại thông tin đặt phòng (không insert DB).
- **Input:** `session` (dict thông tin đặt phòng đã hoàn chỉnh).
- **Output:** Chuỗi xác nhận đặt phòng.

---

### 2.7. Các hàm phụ trợ khác

- **`get_activity_sessions(activity_id)`**: Lấy lịch tour còn trống cho dịch vụ/tour.
- **`get_activity_session_hours(session_id)`**: Lấy danh sách giờ chạy tour còn trống.
- **`add_selected_activities(session, selected_indexes, activities)`**: Hỗ trợ chọn nhiều dịch vụ/tour cùng lúc.
- **`check_coupon(coupon_code, user_id=None)`**: Kiểm tra mã giảm giá.
- **`suggest_coupon_for_user(user_id=None, total_amount=0)`**: Gợi ý mã giảm giá nếu đủ điều kiện.
- **`get_user_booking_history(email=None, phone=None)`**: Truy vấn lịch sử đặt phòng, dịch vụ, tour theo email/SĐT.

---

## 3. Lưu ý & Best Practices

- **Đa ngôn ngữ:** Tự động dịch prompt và phản hồi nếu người dùng không dùng tiếng Việt.
- **Kiểm tra hợp lệ:** Kiểm tra định dạng ngày, email, SĐT, số người, ngân sách, số sao, v.v.
- **Lọc phòng trống thực tế:** Kiểm tra phòng bị khóa hoặc đã có booking trùng ngày.
- **Chọn nhiều dịch vụ:** Hỗ trợ chọn nhiều dịch vụ/tour, kiểm tra tổng số người sử dụng.
- **Xử lý lỗi:** Trả về thông báo rõ ràng nếu có lỗi kết nối, không tìm thấy phòng, dịch vụ, hoặc lỗi khi lưu booking.
- **Bảo mật:** Không log thông tin nhạy cảm của người dùng.
- **Giới hạn kết quả:** Danh sách phòng trả về tối đa 10 phòng phù hợp.

---

## 4. Sơ đồ luồng xử lý (tóm tắt)

```
[Nhận request]
      |
      v
[Xác định ngôn ngữ] --(dịch nếu cần)--> [Khởi tạo session]
      |
      v
[Hỏi điểm đến] -> [Ngày nhận phòng] -> [Ngày trả phòng] -> [Số người lớn] -> [Số trẻ em] -> [Ngân sách] -> [Số sao]
      |
      v
[Hiển thị danh sách phòng] -> [Chọn phòng]
      |
      v
[Hiển thị dịch vụ/tour] -> [Chọn dịch vụ/tour (có thể nhiều lần)]
      |
      v
[Nhập thông tin khách hàng (từng trường)] -> [Xác nhận thông tin]
      |
      v
[Chọn phương thức thanh toán]
      |
      v
[Lưu booking vào DB]
      |
      v
[Trả về mã booking, xác nhận thành công]
      |
      v
[Xóa session]
```

---

## 5. Ví dụ phản hồi

**Khi hỏi điểm đến:**
```json
{
  "fulfillment_response": {
    "messages": [
      {"text": {"text": ["Bạn muốn đi đâu? (Cát Bà, Đại Lải, Ha Tien resort)"]}}
    ]
  }
}
```

**Khi xác nhận thành công:**
```json
{
  "fulfillment_response": {
    "messages": [
      {"text": {"text": [
        "✅ Đặt phòng thành công!\n\nMã đặt phòng: #12345\nPhòng: Deluxe\nGiá phòng: 2,000,000 VNĐ/đêm\nTổng thanh toán: 4,000,000 VNĐ\nPhương thức thanh toán: chuyển khoản\nChúng tôi sẽ liên hệ xác nhận và hướng dẫn thanh toán. Xin cảm ơn!"
      ]}}
    ]
  }
}
```

---

## 6. Checklist kiểm thử

- Đặt phòng thiếu thông tin, nhập sai định dạng ngày/email/SĐT.
- Đặt phòng trùng ngày, không còn phòng phù hợp.
- Đặt nhiều dịch vụ/tour cùng lúc.
- Lỗi kết nối DB, lỗi khi lưu booking.
- Kiểm tra phản hồi đa ngôn ngữ, fallback message khi dịch lỗi.
- Kiểm tra bảo mật: không log thông tin nhạy cảm.

---

## 7. Liên hệ & bảo trì

- Mọi thắc mắc, liên hệ nhóm phát triển AI-integrate.
- Đảm bảo cập nhật khi có thay đổi về cấu trúc DB hoặc quy trình nghiệp vụ
