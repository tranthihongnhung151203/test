# Tài liệu chức năng: activities.py

## Mục đích
Cung cấp webhook xử lý truy vấn về các hoạt động (activity services) cho khách sạn, trả về danh sách hoạt động phù hợp dựa trên yêu cầu người dùng.

## Hàm chính: `activity_services_webhook(request)`

### Chức năng
- Nhận request từ Dialogflow hoặc hệ thống chatbot.
- Phân tích ngôn ngữ, trích xuất từ khóa dịch vụ từ tin nhắn người dùng.
- Truy vấn cơ sở dữ liệu để lấy danh sách hoạt động phù hợp.
- Lọc, chuẩn hóa, loại bỏ dấu tiếng Việt để tăng độ chính xác tìm kiếm.
- Trả về danh sách hoạt động (có thể dịch sang ngôn ngữ người dùng).

### Tham số đầu vào
- `request`: dict, chứa thông tin truy vấn từ người dùng (có thể gồm trường `text` hoặc `queryInput.text.text`).

### Quy trình xử lý
1. **Kết nối database:**
   - Sử dụng `get_db_connection()` để kết nối DB.
   - Nếu không kết nối được, trả về thông báo lỗi.
2. **Xác định ngôn ngữ:**
   - Dùng `get_language_code(request)` để lấy mã ngôn ngữ.
3. **Trích xuất message & entity:**
   - Lấy nội dung tin nhắn người dùng.
   - Dùng `extract_entities(user_message)` để lấy các từ khóa dịch vụ.
4. **Tạo truy vấn SQL:**
   - Nếu có từ khóa dịch vụ, tạo điều kiện LIKE cho tiêu đề và phụ đề hoạt động.
   - Nếu không có, lấy toàn bộ hoạt động (giới hạn 100).
5. **Lọc kết quả nâng cao:**
   - Chuẩn hóa, loại bỏ dấu tiếng Việt để so khớp từ khóa tốt hơn.
   - Loại bỏ hoạt động trùng lặp theo ID.
6. **Tạo phản hồi:**
   - Nếu không có hoạt động phù hợp, trả về thông báo.
   - Nếu có, tạo danh sách hoạt động với các thông tin: tiêu đề, mô tả, chi tiết, thời gian, giá, sức chứa.
   - Nếu ngôn ngữ không phải tiếng Việt, dịch kết quả sang ngôn ngữ người dùng.
7. **Đóng kết nối và trả về kết quả:**

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

### Các hàm/phụ trợ sử dụng
- `get_db_connection()`: Kết nối DB.
- `get_language_code(request)`: Lấy mã ngôn ngữ.
- `extract_entities(user_message)`: Trích xuất từ khóa dịch vụ.
- `row_to_dict(act)`: Chuyển kết quả DB thành dict.
- `format_currency(price)`: Định dạng giá tiền.
- `translate_text(text, lang_code)`: Dịch nội dung sang ngôn ngữ khác.

### Lưu ý
- Hỗ trợ đa ngôn ngữ, tự động dịch phản hồi nếu người dùng không dùng tiếng Việt.
- Lọc trùng lặp hoạt động theo ID.
- Có xử lý loại bỏ dấu tiếng Việt để tăng độ chính xác tìm kiếm. 
