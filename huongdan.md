# AI-Integrate Webhook with Dialogflow CX

## 1. Giới thiệu dự án  :star2:

Dự án này là một backend webhook mạnh mẽ, được xây dựng bằng Python và Flask, tích hợp với nền tảng chatbot Dialogflow CX của Google. Mục tiêu của dự án là tự động hóa các tác vụ tương tác cơ bản với khách hàng trong lĩnh vực khách sạn, bao gồm:

* **Xử lý các yêu cầu đặt phòng**

* **Cung cấp thông tin và đặt các dịch vụ nội bộ**

* **Tiếp nhận phản hồi và đánh giá từ khách hàng**

* **Cung cấp các chương trình khuyến mãi và thông tin khách sạn**

Dự án này đóng vai trò như một bộ não, nhận dữ liệu từ chatbot và xử lý logic nghiệp vụ, sau đó phản hồi lại cho khách hàng một cách thông minh và nhanh chóng.

:computer: **Công nghệ sử dụng:**

* **Python 3.12**
* **Flask** làm web framework
* Kết nối cơ sở dữ liệu thông qua file `db.py`


## 2. Cấu trúc thư mục  :gear:
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
## 3. Yêu cầu hệ thống :bulb:

* Python >= 3.10
* pip (Python package installer)
* Postman để test API (tuỳ chọn)
* Google Dialogflow CX

## 4. Chuẩn bị môi trường 
:point_right: Trước khi bắt đầu, hãy chắc chắn bạn đã cài đặt và thiết lập xong các công cụ sau:

* **Tài khoản Google:** Cần thiết để sử dụng các dịch vụ của Google Cloud, bao gồm cả Dialogflow CX.

* **Python 3:** Tải và cài đặt Python từ trang chủ python.org. Trong quá trình cài đặt, hãy nhớ tick vào ô "Add Python to PATH".

* **Visual Studio Code (hoặc Text Editor bất kỳ):** Một trình soạn thảo mã nguồn để viết code Python. VS Code được khuyên dùng vì có nhiều tiện ích hỗ trợ.

* **Tạo dự án trên Google Cloud:**

  * Truy cập Google Cloud Console.

  * Tạo một dự án mới (ví dụ: hotel-chatbot-project).

  * Kích hoạt thanh toán (Billing) cho dự án. Dialogflow CX có bậc miễn phí, nhưng Google yêu cầu kích hoạt thanh toán để sử dụng API.

  * Trong dự án của bạn, tìm và Enable API "Dialogflow API".

### Phần 1: :building_construction: Xây dựng Chatbot cơ bản với Dialogflow CX
   
 **1. Tạo Intent:**
   
   * tạo intent trong *manage*
   
   * thêm các *Training phrases*

 **2. Thiết kế luồng gọi Webhook:**
   
   * vào *Build-> Default Start Flow -> Start Page*
   
   * tạo *Routes* mới -> chọn intent -> *Transition -> Page*: tạo trang

 **3. Cấu hình Page để gọi Webhook:**

   * nhấp vào page vừa tạo -> phần *Entry fulfillment*:  Đây là nơi chúng ta sẽ ra lệnh cho Dialogflow gọi đến server Flask ngay khi cuộc trò chuyện đi vào trang này

 :warning:  **Quan trọng:** Trong phần *Tag*, nhập một cái tên định danh cho yêu cầu này. Server Flask sẽ dùng tag này để biết phải làm gì.
   
### Phần 2. Cài đặt

 **Bước 1: Clone project**
 
    git clone <link dự án>

    cd AI-integrate

**Bước 2: Tạo môi trường ảo**

    python -m venv venv

    venv\Scripts\activate  # Windows

    source venv/bin/activate  # Trên macOS/Linux

**Bước 3: Cài đặt thư viện**

    pip install flask



 ### Phần 3: Xây dựng Webhook với Python Flask
  
 :memo: 
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
 ### Phần 4.Chạy server
     cd src
* Trước tiên kích hoạt môi trường ảo :sparkles:
  
       venv\Scripts\activate
* xác nhận đã kích hoạt được môi trường ảo, **ví dụ:**
  
       (.venv) D:\TEST AI-integrate\AI-integrate>
* chạy file app.py
  
       python app.py
* khi chạy dự án bạn sẽ thấy dòng:

:arrow_forward: Running on http://127.0.0.1:5000


## 5.Cơ sở dữ liệu   :file_cabinet:
### Phần 1. Cài đặt và Cấu hình MySQL qua XAMPP  :hammer_and_wrench:
**1. Tải và Cài đặt XAMPP**
* :arrow_down: **Tải xuống:** Truy cập trang chủ của Apache Friends: https://www.apachefriends.org/download.html

* **Cài đặt:** Chạy file cài đặt và làm theo hướng dẫn. Bạn có thể chọn các thành phần muốn cài đặt, nhưng MySQL là bắt buộc.
  
**2. Khởi động :rocket: Apache và MySQL**
* Mở **XAMPP Control Panel**.

* Nhấn nút **Start** :arrow_forward: bên cạnh**Apache** và **MySQL**.

* Đảm bảo cả hai module đều chạy thành công (màu xanh lá cây) :green_circle: . Nếu có lỗi :x:, hãy kiểm tra các cổng (port) có bị xung đột với ứng dụng khác không (ví dụ: Skype, IIS, v.v.).

**3. Truy cập phpMyAdmin**
* Khi MySQL đã chạy, bạn có thể truy cập giao diện quản lý cơ sở dữ liệu phpMyAdmin bằng cách:

    * :globe_with_meridians: Mở trình duyệt web.

    * Truy cập địa chỉ: http://localhost/phpmyadmin

* Đây là nơi bạn có thể quản lý các database, bảng, và dữ liệu của mình.

**4. Tạo Database mới trong phpMyAdmin**

* Tạo Database :exclamation:  *thủ công vì không dùng init_db()*,  bước này là bắt buộc để database và bảng của bạn tồn tại trước khi code Python cố gắng kết nối.
  
    * Trong phpMyAdmin, ở thanh bên trái, nhấp vào **New** (hoặc tab Databases ở trên cùng).
  
    *  Nhập tên database của bạn (ví dụ: flamingo_db).

    * Chọn **Collation** (thường là utf8mb4_unicode_ci hoặc utf8_general_ci để hỗ trợ tiếng Việt).

    * Nhấn **Create** :heavy_plus_sign:

**5.Tạo bảng bookings trong flamingo_db**(sau khi đã tạo được Database)

* Sau khi tạo database flamingo_db, nhấp vào tên database đó ở thanh bên trái.

* Bạn sẽ thấy mục **"Create table"**. Nhập tên bảng là **bookings** và số lượng cột (ví dụ: 7 cột cho các trường cơ bản).

* Nhấn Create. :heavy_plus_sign:

* Điền thông tin cho từng cột như sau:

Tên cột|	Kiểu dữ liệu	|Độ dài/Giá trị|	Thuộc tính	|Index|	A.I.
------|-------------|------------|-----------|---------|-------
id|	INT	|11|	UNSIGNED|	PRIMARY|	✅
destination|	VARCHAR|	255			
room_type|	VARCHAR|	255			
checkin_date|	DATE				
checkout_date|	DATE				
adults|	INT|	11			
children|	INT|	11

 :exclamation: Lưu ý:

 * Chọn **PRIMARY** cho cột id và đánh dấu A.I. (Auto Increment) để nó tự động tăng.

 * **VARCHAR(255)** là đủ cho các chuỗi ngắn.

 * **DATE** là kiểu dữ liệu phù hợp cho ngày tháng.

 * Nhấn **Save**  :floppy_disk: để tạo bảng.

### Phần 2. Cấu hình Kết nối trong Dự án Python  :link:
**1. Cài đặt :gear: thư viện MySQL Connector**

* Nếu bạn chưa cài đặt, hãy kích hoạt môi trường ảo của dự án và cài đặt thư viện mysql-connector-python:
  

        cd D:\AI-integrate
  
        venv\Scripts\activate  # Đối với Windows
  
        source venv/bin/activate # Đối với macOS/Linux

        pip install mysql-connector-python

**2. Cấu hình DB_CONFIG trong src/db.py**

* Mở file src/db.py và đảm bảo phần DB_CONFIG của bạn chính xác với cài đặt MySQL của XAMPP.

    * **host**: Luôn là 'localhost' hoặc '127.0.0.1' cho môi trường cục bộ.

    * **user**: Mặc định là 'root'.

    * **password**: Điền mật khẩu bạn đã đặt cho user root trong MySQL (hoặc để trống nếu bạn chưa đặt mật khẩu).

    * **database**: Tên database bạn đã tạo trong phpMyAdmin (ví dụ: 'flamingo_db')
 
  

  **file db.py**  :memo:
```python

import mysql.connector

DB_CONFIG = {
    'host': 'localhost',
    'user': 'root',
    'password': 'your_new_password', # <-- ĐIỀN MẬT KHẨU CỦA BẠN VÀO ĐÂY
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
  

## 6. Chi tiết các tag xử lý   :label:


|**Tag** |**Mô tả**
------------------------------|----------------------------------
|booking_tags|Xử lý các yêu cầu khởi tạo hoặc liên quan đến luồng đặt phòng.
|cancel_tags|Xử lý các yêu cầu hủy đặt phòng.
|available_room_tags|Xử lý các yêu cầu kiểm tra phòng trống.
|edit_tags|Xử lý các yêu cầu cập nhật thông tin đặt phòng.
|promotion_offers|Xử lý các yêu cầu về chương trình khuyến mãi.
|hotel_services|Xử lý các yêu cầu về dịch vụ chung của khách sạn.
|activity_services|Xử lý các yêu cầu về dịch vụ hoạt động/giải trí.
|contact_information|Xử lý các yêu cầu về thông tin liên hệ của khách sạn.
|room_information|Xử lý các yêu cầu về thông tin chi tiết các loại phòng.
|check_out|Xử lý quy trình trả phòng cho khách.
|extend_stay|Xử lý yêu cầu gia hạn thời gian lưu trú.
|invoice_info|Xử lý yêu cầu về thông tin hóa đơn.
|book_service|Xử lý yêu cầu đặt một dịch vụ cụ thể trong khách sạn.
|add_service|Xử lý yêu cầu thêm một dịch vụ vào đặt phòng hiện có.
|list_internal_service|Xử lý yêu cầu liệt kê các dịch vụ nội bộ.
|get_articles|Xử lý yêu cầu truy xuất danh sách các bài viết/FAQ.
|get_article_detail|Xử lý yêu cầu truy xuất chi tiết của một bài viết.
|search_articles|Xử lý yêu cầu tìm kiếm các bài viết/FAQ theo từ khóa.

:pushpin: **Cách Sử Dụng Tag**
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
