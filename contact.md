# Tài liệu chức năng: contact.py

---

## 1. Mục đích

Cung cấp thông tin liên hệ khách sạn cho người dùng qua chatbot, hỗ trợ đa ngôn ngữ, đảm bảo trả về thông tin chính xác, đầy đủ và thân thiện với người dùng.

---

## 2. Hàm chính

### `contact_information_webhook(request)`

#### Chức năng

- Nhận yêu cầu từ chatbot (Dialogflow hoặc hệ thống khác).
- Xác định ngôn ngữ người dùng, dịch input sang tiếng Việt nếu cần.
- Kết nối database, truy vấn thông tin khách sạn (bảng `pm_hotel`).
- Lấy các trường: tên khách sạn, địa chỉ, số điện thoại, email, website, hotline, giờ làm việc.
- Định dạng thông tin liên hệ thân thiện, dễ đọc.
- Dịch phản hồi sang ngôn ngữ người dùng nếu cần.
- Trả về kết quả đúng chuẩn fulfillment cho chatbot.

#### Input

- **request**: dict  
  - `text`: Nội dung tin nhắn người dùng (nếu gửi trực tiếp).
  - `queryInput.text.text`: Nội dung tin nhắn (chuẩn Dialogflow).
  - Các trường khác phục vụ xác định ngôn ngữ, session, v.v.

#### Output

- Dict chuẩn fulfillment_response cho chatbot, ví dụ:
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

## 3. Quy trình xử lý chi tiết

1. **Nhận request**  
   - Lấy nội dung tin nhắn từ trường `text` hoặc `queryInput.text.text`.

2. **Xác định ngôn ngữ người dùng**  
   - Sử dụng thư viện `langdetect` để xác định ngôn ngữ.
   - Nếu không phải tiếng Việt, dịch input sang tiếng Việt để xử lý.

3. **Kết nối database**  
   - Gọi `get_db_connection()`.
   - Nếu không kết nối được, trả về thông báo lỗi (dịch sang ngôn ngữ người dùng nếu cần).

4. **Truy vấn thông tin khách sạn**  
   - Truy vấn bảng `pm_hotel` với điều kiện `lang = 4` (tiếng Việt), lấy 1 bản ghi.
   - Nếu không có dữ liệu, trả về thông báo lỗi.

5. **Định dạng thông tin liên hệ**  
   - Lấy các trường: `title`, `address`, `phone`, `email`, `web`.
   - Định dạng nội dung thân thiện, có icon, hotline, giờ làm việc, hướng dẫn chat.

6. **Dịch phản hồi sang ngôn ngữ người dùng**  
   - Nếu ngôn ngữ không phải tiếng Việt, dịch toàn bộ nội dung phản hồi.

7. **Đóng kết nối và trả về kết quả**  
   - Đảm bảo đóng cursor và connection.
   - Trả về fulfillment_response cho chatbot.

---

## 4. Lưu ý & Best Practices

- **Đa ngôn ngữ:**  
  Tự động dịch phản hồi nếu người dùng không dùng tiếng Việt. Nếu dịch lỗi hoặc ngôn ngữ không hỗ trợ, trả về fallback message bằng tiếng Việt hoặc tiếng Anh.
- **Bảo mật:**  
  Không log thông tin nhạy cảm ra console/log file.
- **Xử lý lỗi:**  
  Trả về thông báo rõ ràng nếu không kết nối được DB hoặc không tìm thấy thông tin khách sạn.
- **Chuẩn hóa dữ liệu:**  
  Đảm bảo các trường trả về luôn có giá trị mặc định nếu thiếu (tránh lỗi None).

---

## 5. Checklist kiểm thử

- [ ] Nhận thông tin liên hệ khi hỏi bằng tiếng Việt.
- [ ] Nhận thông tin liên hệ khi hỏi bằng tiếng Anh/ngoại ngữ khác (kiểm tra dịch).
- [ ] Trường hợp không kết nối được DB.
- [ ] Trường hợp không có dữ liệu khách sạn trong DB.
- [ ] Kiểm tra bảo mật: không log thông tin nhạy cảm.
- [ ] Kiểm tra fallback message khi dịch lỗi/ngôn ngữ không hỗ trợ.

---

## 6. Ví dụ phản hồi

**Tiếng Việt:**
```
📞 Thông tin liên hệ:

🏨 Cat Ba Resort
📍 Địa chỉ: Đảo Cát Bà, Hải Phòng
📞 Điện thoại: 0225 3888 999
📧 Email: info@catbaresort.vn
🌐 Website: www.catbaresort.vn
📱 Hotline: +84 912 345 678
⏰ Giờ làm việc: 24/7

💬 Chat với chúng tôi để được hỗ trợ nhanh nhất!
```

**Tiếng Anh:**
```
📞 Contact information:

🏨 Cat Ba Resort
📍 Address: Cat Ba Island, Hai Phong
📞 Phone: 0225 3888 999
📧 Email: info@catbaresort.vn
🌐 Website: www.catbaresort.vn
📱 Hotline: +84 912 345 678
⏰ Working hours: 24/7

💬 Chat with us for the fastest support!
```

---

## 7. Liên hệ & bảo trì

- Mọi thắc mắc, liên hệ nhóm phát triển AI-integrate.
- Đảm bảo cập nhật khi có thay đổi về cấu trúc DB hoặc thông
