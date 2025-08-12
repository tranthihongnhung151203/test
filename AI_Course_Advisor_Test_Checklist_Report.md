# 🛠 AI Course Advisor- Tổng hợp Báo Cáo Chi Tiết Và Bộ Checklist Kiểm Thử 

---
## 1) Phạm Vi & Mục Tiêu
**A. Mục tiêu**
- Xác minh quá trình cài đặt sạch trên máy mới mà không có bước ẩn.
- Xác thực các tính năng cốt lõi  (tìm kiếm vector, trò chuyện, giọng nói, TTS, đa ngôn ngữ) hoạt động đúng như thiết kế.
- Phát hiện sớm lỗi hồi quy  thông qua các ca kiểm thử có thể lặp lại và script tự động.
  
**B. Ngoài phạm vi**
- Các bài kiểm thử phi chức năng (tải/độ chịu tải) vượt quá mức kiểm tra cơ bản.
- Kiểm thử UI chính xác từng pixel trên nhiều trình duyệt (chỉ thực hiện các kiểm tra cơ bản).
  
## 2) Danh Sách Kiểm Tra Cài Đặt Trước Khi Chạy ✅

### 2.1 Kiểm Thử Cài Đặt & Môi Trường

- [ ] **Đã cài Git**
  - `git --version` → show version
- [ ] **Đã cài Python 3.11 trở lên**
  - `python --version` or `python3 --version` → `>= 3.11`
  - `pip --version`
- [ ] **Clone repository & checkout branch**
  ```bash
  git clone https://github.com/ITX-AI/AI-OSS-LLM.git
  cd AI-OSS-LLM
  git checkout An
  ```
- [ ] **Create & activate virtual environment**
  - **Windows**
    ```bash
    python -m venv venv
    venv\Scripts\activate
    where python
    ``` 
- [ ] **Nâng cấp pip và cài đặt các thư viện phụ thuộc**
  ```bash
  pip install --upgrade pip
  pip install -r requirements.txt
  ```
- [ ] **Cài đặt FFmpeg**
  - Windows: sử dụng bản chính thức hoặc `choco install ffmpeg`
  - macOS: `brew install ffmpeg`
  - Ubuntu: `sudo apt update && sudo apt install -y ffmpeg`
  - Verify: `ffmpeg -version`
- [ ] **Cài đặt Ollama và tải model**
  - Khởi động server: `ollama serve`
  - Tải model chuẩn: `ollama pull llama3.1:latest`
  - Kiểm tra model có trong list: `ollama list`
---
### 2.2 Chế Độ Chạy
#### A. Streamlit App
```bash
  streamlit run app.py
```
▶ open http://localhost:8501

**Kỳ vọng:** 🌟
- Phần header hiển thị “🎓 AI Course Advisor” renders
- Sidebar hiện “🔧 System Status” và danh sách model khi Ollama kết nối thành công
- Không xuất hiện bất kỳ lỗi đỏ (exception) nào

#### B. Chạy API + Widget FastAPI (tùy chọn)
```bash
# Terminal 1
ollama serve

# Terminal 2
python enhanced_api_server.py

```
▶ open http://localhost:8003/widget/enhanced

**Kỳ vọng:** 🌟
- API server hoạt động và Widget tải lên bình thường
- Chức năng chat cơ bản hoạt động ổn định

---
## 3. Kiểm Thử Ứng Dụng & Tính Năng

### 3.1 Bảng Kiểm Thử Tính Năng AI Course Advisor

| Tính Năng                                  | Mô Tả                                    | Kỳ Vọng                                         | Trạng Thái (✅/❌) | Ghi Chú              |
|-------------------------------------------|-----------------------------------------|-------------------------------------------------|-------------------|----------------------|
| Ứng dụng Streamlit                        | Chạy app, truy cập http://localhost:8501 | Ứng dụng chạy ổn, không lỗi giao diện            |                   |                      |
| FastAPI + Widget                         | Chạy API server và chat widget           | Widget hoạt động ổn định, chat cơ bản hoạt động   |                   |                      |
| Chat AI                                | Trả lời câu hỏi cơ bản "Tôi muốn học Python" | AI trả lời chính xác, logic, phù hợp với câu hỏi   |                   |                      |
| Tìm kiếm khóa học semantic               | Tìm khóa học theo ý định người dùng      | Trả về kết quả có liên quan, không sai lệch       |                   |                      |
| Voice Input                            | Nhận diện giọng nói (có mic)               | Input voice chuyển thành text chính xác            |                   |                      |
| Text-to-Speech                          | Đọc phản hồi AI khi bật TTS                | Âm thanh rõ ràng, đọc đúng nội dung                  |                   |                      |
| Hỗ trợ đa ngôn ngữ                      | Trả lời bằng tiếng Việt và tiếng Anh       | AI trả lời đúng ngôn ngữ tương ứng                    |                   |                      |
| Nút, bộ lọc, reset, thiết lập hệ thống  | Hoạt động bình thường, không bị lỗi        | Các nút bấm chức năng đúng, hệ thống reset hoạt động |                   |                      |

### 3.2 Chi tiết kết quả
- Ứng dụng Streamlit:  
  Mở trang http://localhost:8501, giao diện tải nhanh, không lỗi hiển thị.  

- FastAPI + Widget:  
  Chạy ổn định, widget chat hoạt động mượt, phản hồi nhanh.  

- Chat AI:  
  Trả lời chính xác câu hỏi "Tôi muốn học Python" với các khóa học liên quan.  

- Tìm kiếm khóa học semantic:  
  Tìm kiếm theo ý định trả về kết quả liên quan, không có kết quả lỗi hay sai lệch.  

- Voice Input:  
  Giọng nói được nhận dạng chính xác, text hiện trong hộp thoại đúng ngữ cảnh.  

- Text-to-Speech:  
  Phát âm rõ ràng, nghe dễ hiểu, không bị ngắt quãng hay lỗi âm thanh.  

- Hỗ trợ đa ngôn ngữ:  
  AI trả lời đúng tiếng Việt và tiếng Anh theo ngôn ngữ đầu vào.  

- Nút, bộ lọc, reset, thiết lập:  
  Các nút chức năng hoạt động đúng, reset lịch sử và trạng thái hệ thống thành công.

---


---
📌 Chúc bạn kiểm thử thành công và vận hành AI Course Advisor ổn định!  
Nếu cần phiên bản file định dạng hoặc mẫu chi tiết hơn, vui lòng thông báo.  
