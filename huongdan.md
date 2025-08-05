# AI-Integrate Webhook with Dialogflow CX

## 1. Giới thiệu dự án

Dự án này là một backend webhook mạnh mẽ, được xây dựng bằng Python và Flask, tích hợp với nền tảng chatbot Dialogflow CX của Google. Mục tiêu của dự án là tự động hóa các tác vụ tương tác cơ bản với khách hàng trong lĩnh vực khách sạn, bao gồm:

* **Xử lý các yêu cầu đặt phòng**

* **Cung cấp thông tin và đặt các dịch vụ nội bộ**

* **Tiếp nhận phản hồi và đánh giá từ khách hàng**

* **Cung cấp các chương trình khuyến mãi và thông tin khách sạn**

Dự án này đóng vai trò như một bộ não, nhận dữ liệu từ chatbot và xử lý logic nghiệp vụ, sau đó phản hồi lại cho khách hàng một cách thông minh và nhanh chóng.

**Công nghệ sử dụng:**

* **Python 3.12**
* **Flask** làm web framework
* Kết nối cơ sở dữ liệu thông qua file `db.py`


## 2. Cấu trúc thư mục
```
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

```
## 3. Yêu cầu hệ thống

* Python >= 3.10
* pip (Python package installer)
* Postman để test API (tuỳ chọn)
* Google Dialogflow CX

## 4. Chuẩn bị môi trường 
Trước khi bắt đầu, hãy chắc chắn bạn đã cài đặt và thiết lập xong các công cụ sau:

* **Tài khoản Google:** Cần thiết để sử dụng các dịch vụ của Google Cloud, bao gồm cả Dialogflow CX.

* **Python 3:** Tải và cài đặt Python từ trang chủ python.org. Trong quá trình cài đặt, hãy nhớ tick vào ô "Add Python to PATH".

* **Visual Studio Code (hoặc Text Editor bất kỳ):** Một trình soạn thảo mã nguồn để viết code Python. VS Code được khuyên dùng vì có nhiều tiện ích hỗ trợ.

* **Tạo dự án trên Google Cloud:**

  -Truy cập Google Cloud Console.

  -Tạo một dự án mới (ví dụ: hotel-chatbot-project).

  -Kích hoạt thanh toán (Billing) cho dự án. Dialogflow CX có bậc miễn phí, nhưng Google yêu cầu kích hoạt thanh toán để sử dụng API.

  -Trong dự án của bạn, tìm và Enable API "Dialogflow API".
### Phần 1. Cài đặt

 **Bước 1: Clone project**
 
    git clone <link dự án>

    cd AI-integrate

**Bước 2: Tạo môi trường ảo**

    python -m venv venv

    venv\Scripts\activate  # Windows

    source venv/bin/activate  # Trên macOS/Linux

**Bước 3: Cài đặt thư viện**

    pip install flask


 ### Phần 2: Xây dựng Chatbot cơ bản với Dialogflow CX
 * Chuẩn bị Dialogflow CX
 * Cấu hình Dialogflow CX
   
   **1. Tạo Intent:**
   
   -tạo intent trong **manage**
   
   -thêm các **Training phrases**

   **2. Thiết kế luồng gọi Webhook:**
   
   -vào **Build-> Default Start Flow -> Start Page**
   
   -tạo **Routes** mới -> chọn intent -> **Transition -> Page**: tạo trang

    **3. Cấu hình Page để gọi Webhook:**

   -nhấp vào page vừa tạo -> phần **Entry fulfillment**:  Đây là nơi chúng ta sẽ ra lệnh cho Dialogflow gọi đến server Flask ngay khi cuộc trò chuyện đi vào trang này

   -**Quan trọng:** Trong phần **Tag**, nhập một cái tên định danh cho yêu cầu này. Server Flask sẽ dùng tag này để biết phải làm gì.


 ### Phần 3: Xây dựng Webhook với Python Flask
**file db.py**
```python
import mysql.connector

DB_CONFIG = {
    'host': 'localhost',
    'user': 'root',
    'password': '',
    'database': 'flamingo_db'
}

def get_db_connection():
    try:
        connection = mysql.connector.connect(**DB_CONFIG)
        return connection
    except mysql.connector.Error as err:
        print(f"Lỗi kết nối database: {err}")
        return None 
```
**kết nối với MySQL** qua file db.py  
 
**file app.py**
```python
 import os
from flask import Flask, request, jsonify

**Khởi tạo Flask app**
app = Flask(__name__)

**Định nghĩa route cho webhook, chỉ chấp nhận phương thức POST**
@app.route('/webhook', methods=['POST'])
def webhook():
    """Hàm này xử lý tất cả các yêu cầu gửi đến từ Dialogflow."""
    
    # Lấy dữ liệu JSON từ request
    try:
        req = request.get_json(force=True)
    except Exception as e:
        # Nếu không có JSON, trả về lỗi
        return jsonify({"error": f"Invalid JSON received: {e}"}), 400

    # Lấy "tag" mà chúng ta đã đặt trong Dialogflow Fulfillment
    # Dùng .get() để tránh lỗi nếu key không tồn tại
    tag = req.get('fulfillmentInfo', {}).get('tag')
    
    response_text = ""
    
    if tag == ' ':
        response = (req)

    # Thêm các điều kiện 'elif tag == ...' khác ở đây cho các intent khác

    else:
        # Nếu không có tag nào khớp, trả về một câu trả lời mặc định
        response_text = "Xin lỗi, tôi không hiểu yêu cầu của bạn."

# Chạy server
if __name__ == '__main__':

    app.run( port=port, debug=True)
```
**Chạy server**

    cd src

    python app.py

* khi chạy dự án bạn sẽ thấy dòng:

Running on http://127.0.0.1:5000


## 6. Chi tiết các tag xử lý
**booking_tags**

 * booking
   
**cancel_tags**

* cancel_booking
* booking_id 
* phone 
* email
    
**edit_tags**

 * update_booking
 * booking_id
 * checkin_date
 * checkout_date
 * adults
 * children
    
**available_room_tags**

  * available_room
  * destination
  * checkin_date
  * checkout_date
  * room_type
  * adults
  * children
    
**Promotion và Dịch Vụ**

  * promotion_offers
  * hotel_services
  * activity_services
  * contact_information
  * room_information

**In-Stay**

  * check_out
  * extend_stay
  * invoice_info
  * book_service
  * add_service
  * list_internal_service
  * get_articles
  * get_article_detail
  * search_articles

**Cách Sử Dụng Tag**
-Trong hàm webhook(), các tag này được sử dụng để xác định hành động nào sẽ được thực hiện dựa trên yêu cầu từ Dialogflow. Mỗi tag sẽ dẫn đến một hàm xử lý cụ thể, ví dụ:

* Nếu tag là **'booking'**, hàm **booking_webhook(req)** sẽ được gọi.
* Nếu tag là **'cancel_booking'**, hàm **cancel_webhook(req)** sẽ được gọi.
* Tương tự cho các tag khác.
  
## 7. Ghi chú cho người mới

**Để test đúng**, bạn phải:

* Luôn gửi đúng header: `Content-Type: application/json`
* Gửi đúng tag và các parameter cần thiết
* Kiểm tra xem server có đang chạy ở `http://127.0.0.1:5000` chưa

📌 Nếu gặp lỗi `ModuleNotFoundError: No module named 'db'`, hãy chạy bằng:

python -m src.app


## Ghi chú cho tài liệu

Tài liệu này đã được viết để người **chưa biết gì cũng có thể cài và chạy**, từ bước tạo venv, cài Flask, chạy server, test bằng Postman. Nếu bạn là người đọc mới, chỉ cần làm đúng từng bước là có thể chạy được.
"# test" 
