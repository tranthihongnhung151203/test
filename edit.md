# Tài liệu chức năng: edit.py

---

## 1. Mục đích

Cung cấp webhook cho phép người dùng **cập nhật thông tin đặt phòng** (booking) qua chatbot, bao gồm: tên khách, số điện thoại, loại phòng, ngày nhận/trả phòng. Đảm bảo quy trình xác thực, xác nhận, cập nhật và phản hồi đa ngôn ngữ, thân thiện với người dùng.

---

## 2. Hàm chính: `update_booking_webhook(request)`

### Chức năng

- Nhận request từ Dialogflow hoặc hệ thống chatbot.
- Xác định ngôn ngữ người dùng, dịch input nếu cần.
- Quản lý trạng thái hội thoại cập nhật booking theo session.
- Hỗ trợ các bước: nhập mã booking, xác nhận thông tin, chọn trường cần sửa, nhập giá trị mới, cập nhật DB.
- Định dạng thông tin booking trả về rõ ràng, có emoji.
- Hỗ trợ đa ngôn ngữ, tự động dịch phản hồi nếu người dùng không dùng tiếng Việt.
- Trả về thông báo thành công/lỗi cho người dùng.

### Tham số đầu vào

- `request`: dict, chứa thông tin truy vấn từ người dùng.
  - `text`: Nội dung tin nhắn người dùng.
  - `sessionInfo.session`: ID session hội thoại.
  - Các trường khác phục vụ xác định ngôn ngữ, session, v.v.

### Đầu ra

- Trả về dict dạng:
```json
{
  "fulfillment_response": {
    "messages": [
      {"text": {"text": ["<nội dung phản hồi>"]}}
    ]
  }
}
```

---

## 3. Các hàm/phụ trợ sử dụng

- `extract_update_info(text)`: Trích xuất mã booking và các trường cần cập nhật từ input tự do.
- `find_booking(booking_id)`: Truy vấn thông tin booking từ DB theo mã.
- `format_booking_info(booking)`: Định dạng thông tin booking trả về cho người dùng.
- `extract_entities(text)`: Trích xuất entity (loại phòng) từ text (nếu có).
- `translate_text(text, lang_code)`: Dịch nội dung sang ngôn ngữ khác.
- `row_to_dict(row)`: Chuyển kết quả DB thành dict.

---

## 4. Quy trình xử lý chi tiết

1. **Nhận request & xác định ngôn ngữ**
   - Lấy nội dung tin nhắn từ trường `text`.
   - Dùng `langdetect` để xác định ngôn ngữ, lưu vào session.
   - Nếu không phải tiếng Việt, dịch input sang tiếng Việt để xử lý.

2. **Reset luồng cập nhật**
   - Nếu người dùng nhập "sửa", "update", "cập nhật", reset session và yêu cầu nhập mã booking.

3. **Bước 1: Nhập mã booking**
   - Nếu chưa có `booking_id` trong session:
     - Trích xuất mã booking từ input.
     - Nếu có, truy vấn thông tin booking (`find_booking`).
     - Nếu tìm thấy, lưu vào session, hiển thị thông tin booking và hỏi trường muốn sửa.
     - Nếu không tìm thấy, trả về thông báo lỗi.
     - Nếu không nhập mã booking, yêu cầu nhập lại.

4. **Bước 2: Chọn trường cần sửa**
   - Nếu đã có booking, hỏi trường muốn sửa (tên, số điện thoại, phòng, checkin, checkout).
   - Nếu người dùng nhập trường hợp lệ, chuyển sang bước nhập giá trị mới.
   - Nếu không hợp lệ, yêu cầu nhập lại trường muốn sửa.

5. **Bước 3: Nhập giá trị mới và cập nhật**
   - Nhận giá trị mới từ người dùng.
   - Thực hiện cập nhật vào DB:
     - **Tên:** Cập nhật firstname, lastname.
     - **Số điện thoại:** Cập nhật trường phone.
     - **Phòng:** Cập nhật id_room trong bảng booking_room (theo số hoặc loại phòng).
     - **Checkin/Checkout:** Cập nhật from_date/to_date (chuyển đổi sang timestamp).
   - Nếu cập nhật thành công, trả về thông báo thành công.
   - Nếu lỗi (sai định dạng ngày, không tìm thấy phòng...), trả về thông báo lỗi.
   - Kết thúc session cập nhật.

6. **Mặc định**
   - Nếu không khớp luồng, yêu cầu nhập lại mã booking để bắt đầu.

---

## 5. Lưu ý & Best Practices

- **Đa ngôn ngữ:**  
  Tự động dịch prompt và phản hồi nếu người dùng không dùng tiếng Việt. Nếu dịch lỗi hoặc ngôn ngữ không hỗ trợ, trả về fallback message bằng tiếng Việt hoặc tiếng Anh.
- **Bảo mật:**  
  Không log thông tin nhạy cảm (email, SĐT, mã booking) ra console/log file.
  Đảm bảo truy vấn SQL luôn dùng tham số hóa để tránh SQL Injection.
- **Kiểm tra hợp lệ:**  
  Kiểm tra định dạng ngày (dd/mm/yyyy), số điện thoại, loại phòng.
- **Quản lý session:**  
  Sử dụng dict `user_update_sessions` để lưu trạng thái hội thoại cho từng user/session.
- **Xử lý lỗi:**  
  Trả về thông báo rõ ràng nếu không tìm thấy booking, sai định dạng, hoặc lỗi khi cập nhật.

---

## 6. Checklist kiểm thử

- [ ] Cập nhật tên khách thành công.
- [ ] Cập nhật số điện thoại thành công.
- [ ] Cập nhật loại phòng thành công (theo số hoặc loại phòng).
- [ ] Cập nhật ngày checkin/checkout đúng định dạng.
- [ ] Nhập sai định dạng ngày, kiểm tra phản hồi lỗi.
- [ ] Nhập mã booking không tồn tại.
- [ ] Nhập trường cần sửa không hợp lệ.
- [ ] Kiểm tra đa ngôn ngữ, fallback message khi dịch lỗi.
- [ ] Kiểm tra bảo mật: không log thông tin nhạy cảm.
- [ ] Kiểm tra reset luồng khi nhập "sửa", "update", "cập nhật".

---

## 7. Ví dụ phản hồi

**Khi yêu cầu nhập mã booking:**
```
🔍 Vui lòng cung cấp mã booking để bắt đầu sửa (ví dụ: #123)
```

**Khi hiển thị thông tin booking:**
```
📋 Thông tin hiện tại:
🆔 Mã booking: #123
👤 Khách: Nguyễn Văn A
📞 SĐT: 0987654321
🏨 Khách sạn: Cat Ba Resort - Deluxe
📍 Địa điểm: Cát Bà
📅 Check-in: 10/08/2024 → Check-out: 12/08/2024

💡 Bạn muốn sửa gì? (tên, số điện thoại, phòng, checkin, checkout)
```

**Khi cập nhật thành công:**
```
✅ Cập nhật thành công!
```

**Khi lỗi định dạng ngày:**
```
❌ Sai định dạng ngày. Vui lòng dùng dd/mm/yyyy.
```

---

## 8. Liên hệ & bảo trì

- Mọi thắc mắc, liên hệ nhóm phát triển AI-integrate.
- Đảm bảo cập nhật khi có thay đổi về cấu trúc DB hoặc quy trình nghiệp vụ.
