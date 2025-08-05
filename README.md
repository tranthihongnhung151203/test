# AI-Integrate Webhook with Dialogflow CX

## 1. Giới thiệu dự án

Dự án này là một backend webhook tích hợp với Dialogflow CX, dùng để xử lý các intent như booking phòng, đánh giá dịch vụ, đặt dịch vụ trong khách sạn,...

Công nghệ sử dụng:

* **Python 3.12**
* **Flask** làm web framework
* Kết nối cơ sở dữ liệu thông qua file `db.py`


## 2. Cấu trúc thư mục

AI-integrate/
└── src/
    ├── app.py                # File chạy chính
    ├── db.py                 # Kết nối cơ sở dữ liệu
    ├── pre_stay/            # Logic xử lý trước khi lưu trú
    │   └── activities.py       # Xử lý booking phòng
    │   └── booking.py
    │   └── cancel.py
    │   └── contact.py
    │   └── edit.py
    │   └── promotion.py
    │   └── reviews.py
    │   └── room_infornation.py
    │   └── services.py
    │   └── utils.py
    ├── in_stay/             # Trong quá trình lưu trú
    │   └── service.py       # Xử lý đặt dịch vụ
        └── check_out.py
    ├── post_stay/           # Sau khi lưu trú
    │   └── review.py        # Xử lý đánh giá
    ├
    └── __pycache__/         # Cache file Python (tự sinh ra)


## 3. Yêu cầu hệ thống

* Python >= 3.10
* pip (Python package installer)
* Postman để test API (tuỳ chọn)
* Google Dialogflow CX


## 4. Cài đặt & chạy dự án

### Bước 1: Clone project

cd AI-integrate

### Bước 2: Tạo môi trường ảo

python -m venv venv
venv\Scripts\activate  # Windows
source venv/bin/activate  # Trên macOS/Linux

### Bước 3: Cài đặt thư viện

pip install flask

### Bước 4: Chạy server

cd src
python app.py

* Sau đó, bạn sẽ thấy dòng:

Running on http://127.0.0.1:5000


## 5. Kiểm thử với Postman

### URL:

POST http://127.0.0.1:5000/webhook

### Ví dụ body test (Tag: `booking`)

```json
{
  "fulfillmentInfo": {
    "tag": "booking"
  },
  "sessionInfo": {
    "session": "projects/demo-project/sessions/demo-session"
  },
  "parameters": {
    "destination": "Cát Bà",
    "room_type": "villa",
    "checkin_date": "10/08/2024",
    "checkout_date": "12/08/2024",
    "adults": 2,
    "children": 2,
    "name": "Nguyễn Văn A",
    "email": "a@gmail.com",
    "phone": "0987654321",
    "budget": 5000000,
    "hotel_class": 4
  },
  "languageCode": "vi"
}
```

Kết quả (ví dụ):

```json
{
  "fulfillment_response": {
    "messages": [
      {
        "text": {
          "text": [
            "✅ Đặt phòng thành công! Mã booking: #123. Cảm ơn bạn đã đặt phòng tại Cát Bà Resort."
          ]
        }
      }
    ]
  }
}
```

---

## 6. Chi tiết các tag xử lý
* booking_tags
* cancel_tags
* edit_tags
* available_room_tags

## 7. Ghi chú cho người mới

**Để test đúng**, bạn phải:

* Luôn gửi đúng header: `Content-Type: application/json`
* Gửi đúng tag và các parameter cần thiết
* Kiểm tra xem server có đang chạy ở `http://127.0.0.1:5000` chưa

📌 Nếu gặp lỗi `ModuleNotFoundError: No module named 'db'`, hãy chạy bằng:

python -m src.app


## Ghi chú cho tài liệu

Tài liệu này đã được viết để người **chưa biết gì cũng có thể cài và chạy**, từ bước tạo venv, cài Flask, chạy server, test bằng Postman. Nếu bạn là người đọc mới, chỉ cần làm đúng từng bước là có thể chạy được.
