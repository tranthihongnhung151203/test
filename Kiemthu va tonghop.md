# 🛠 AI Course Advisor — Kế Hoạch Kiểm Thử & Xác Minh Cài Đặt (v1.2)
_Last updated: 2025-08-12_

Tài liệu này là một **checklist và kế hoạch kiểm thử** thực tế, sẵn sàng áp dụng cho sản phẩm **AI Course Advisor**.
Nó bao gồm: xác minh cài đặt, các bộ kiểm thử tính năng, mẫu ghi nhận đạt/không đạt, và hướng dẫn xử lý sự cố.
Được thiết kế để thực thi nhanh chóng và báo cáo rõ ràng.


---

## 0) Phạm Vi & Mục Tiêu
**1. Mục tiêu**
- Xác minh quá trình cài đặt sạch trên máy mới mà không có bước ẩn.
- Xác thực các tính năng cốt lõi  (tìm kiếm vector, trò chuyện, giọng nói, TTS, đa ngôn ngữ) hoạt động đúng như thiết kế.
- Phát hiện sớm lỗi hồi quy  thông qua các ca kiểm thử có thể lặp lại và script tự động.
  
**2. Ngoài phạm vi**
- Các bài kiểm thử phi chức năng (tải/độ chịu tải) vượt quá mức kiểm tra cơ bản.
- Kiểm thử UI chính xác từng pixel trên nhiều trình duyệt (chỉ thực hiện các kiểm tra cơ bản).

---
```mermaid
  flowchart TD
  A[Khởi động / Mở Streamlit] --> B[Cấu hình trang + CSS]
  B --> C[initialize_components @cache_resource]
  C --> C1[Init Multilingual Vector DB]
  C --> C2[Tải courses.json]
  C --> C3[Xây dựng FAISS index]
  C --> C4[Init OllamaClient]
  C --> D[Init session_state: messages, tts, voice flags]
  D --> E[Hiển thị Header & Sidebar]

  %% Sidebar
  E --> F{Ollama connected?}
  F -- Có --> F1[Hiển thị model list & set ollama_client.model]
  F -- Không --> F2[Hiển thị Demo Mode / Mock AI]
  E --> F3[Hiển thị DB info + language stats (nếu có)]
  E --> F4[Controls: TTS toggle, top_k slider, Reset Conversation]

  %% Voice init
  E --> G[Init Voice Processor]
  G --> G1{streamlit_mic_recorder import OK?}
  G1 -- Có --> G2[Render nút 🎤 hoạt động]
  G1 -- Không --> G3[Nút voice disabled + tooltip]

  %% Main layout
  E --> H[Main: Col1 Chat | Col2 Kết quả]

  %% Col1: Chat flow
  H --> I[Hiển thị lịch sử hội thoại]
  I --> I1{Có tts_pending chưa phát?}
  I1 -- Có --> I2[Play TTS → set played=true]
  I1 --> J[Khu nhập: text + 🎤]
  I2 --> J
  J --> J1{User bấm 🎤 và thu âm?}
  J1 -- Có --> J2[process_voice_input → set voice_text, voice_language, voice_processed → rerun]
  J1 -- Không --> K{User bấm Gửi?}
  K -- Không --> J
  K -- Có --> L[Detect language (voice_language nếu có, else auto_translator)]
  L --> M[Append user msg vào session_state.messages]
  M --> N[vector_db.search(query, top_k)]
  N --> O[Danh sách khóa học + score]
  O --> P{Ollama khả dụng?}
  P -- Có --> Q[ollama_client.generate_with_context(...)]
  P -- Không --> R[generate_enhanced_mock_response(...)]
  Q --> S[Append assistant msg]
  R --> S
  S --> T{TTS bật?}
  T -- Có --> T1[Set tts_pending(text, lang, slow, played=false)]
  T -- Không --> U
  T1 --> U[Lưu search_results vào session_state]
  U --> V[st.rerun() để vẽ lại]

  %% Col2: Kết quả
  H --> W{Có last_search_results?}
  W -- Có --> X[Danh sách kết quả: Expander, price, access, score, desc]
  W -- Không --> Y[Hiển thị hướng dẫn sử dụng + sample courses]

  %% Sidebar actions
  F4 --> RST{Reset Conversation?}
  RST -- Có --> RST1[Clear messages & tts_pending → rerun]

  %% Kết thúc
  V --> Z[Footer]
  Z --> END[Chờ nhập tiếp]
```



## 1) Ma Trận Môi Trường (Supported for this test cycle)
| OS            | Python | GPU | Browser          | Notes                                         |
|---------------|--------|-----|------------------|-----------------------------------------------|
| Windows 10/11 | 3.11.x | Bất kỳ | Chrome (phiên bản 120 trở lên)     | Cần bật quyền dùng microphone        |
| Ubuntu 22.04  | 3.11.x | Bất kỳ | Chrome/Chromium  | Cài ffmpeg bằng lệnh apt                         |
| macOS 13–14   | 3.11.x |Bất kỳ | Chrome/Safari    | Cài ffmpeg bằng Homebrew (brew)                        |


---

## 2) Danh Sách Kiểm Tra Cài Đặt Trước Khi Chạy ✅
Đánh dấu từng mục khi hoàn thành. Ghi lại lỗi/cảnh báo trong phần báo cáo.

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
  - **macOS/Linux**
    ```bash
    python3 -m venv venv
    source venv/bin/activate
    which python
    ```
  - Đảm bảo đường dẫn Python thuộc trong thư mục venv
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
  - Tải model chuẩn: `ollama pull llama3.2:latest`
  - Kiểm tra model có trong list: `ollama list`
---

## 3) Chế Độ Chạy
### A) Streamlit App
```bash
  streamlit run app.py
```
▶ open http://localhost:8501

**Kỳ vọng:** 🌟
- Phần header hiển thị “🎓 AI Course Advisor” renders
- Sidebar hiện “🔧 System Status” và danh sách model khi Ollama kết nối thành công
- Không xuất hiện bất kỳ lỗi đỏ (exception) nào

### B) Chạy API + Widget FastAPI (tùy chọn)
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

## 4) Tiêu chí chấp nhận (Go/No-Go)
- Cài đặt hoàn tất với **0 lỗi nghiêm trọng**
- Ứng dụng bật và phản hồi câu hỏi cơ bản trong **dưới 10 giây** trên laptop phát triển (chạy CPU được)
- Tìm kiếm vector top-k trả kết quả có ý nghĩa, dựa trên cơ sở dữ liệu (không có kết quả tưởng tượng)
- Nhập giọng nói (nếu có mic) được chuyển thành văn bản chính xác
- Text-to-Speech (TTS) phát lại phản hồi khi bật toggle
- Hỗ trợ đa ngôn ngữ: Tiếng Việt và Tiếng Anh trả lời đúng ngôn ngữ nhập

---

## 5) Bộ Kiểm Thử Tính Năng
Mỗi kiểm thử gồm: ID / Điều kiện ban đầu / Các bước thực hiện / Kỳ vọng / Trạng thái (✅/❌) / Bằng chứng (ảnh chụp màn hình hoặc log)
### 5.1 Chat Cốt Lõi
| ID       | Preconditions                       | Steps                                                                 | Kỳ vọng                                                                                          | Status | Evidence |
|---------|--------------------------------------|-----------------------------------------------------------------------|---------------------------------------------------------------------------------------------------|--------|----------|
| TC-CHAT-01 | App chạy            | Hỏi: `Tôi muốn học Python`                                            |AI trả lời tự nhiên bằng **Tiếng Việt**, khóa học liên quan trong DB                               |        |          |
| TC-CHAT-02 | Ollama bật/tắt       | Hỏi câu như trên khi Ollama bật và tắt|Nội dung chat phù hợp |        |          |

### 5.2 Tìm Kiếm Vector
| ID       | Preconditions                    | Steps                                         | Kỳ vọng                                                                 | Status | Evidence |
|----------|----------------------------------|-----------------------------------------------|--------------------------------------------------------------------------|--------|----------|
| TC-SEARCH-01 | DB tải đủ     | Tìm : `Khóa học AI machine learning`        | Có kết quả phù hợp, hiển thị đầy đủ   |        |          |
| TC-SEARCH-02 | Same                            | Tìm từ khóa không có trong DB (e.g., `korean cooking`) | Hiển thị không có kết quả, không sai lệch |        |          |

### 5.3 Phản Hồi Đa Ngôn Ngữ
| ID       | Preconditions | Steps                                                                       | Kỳ vọng                                                | Status | Evidence |
|----------|---------------|-----------------------------------------------------------------------------|---------------------------------------------------------|--------|----------|
| TC-ML-01 | —             | Hỏi VI: `Khóa học AI cho người mới`                                        | RTrả lời bằng tiếng Việt đúng                          |        |          |
| TC-ML-02 | —             | Hỏi EN: `Best Python course for beginners`                                 | Trả lời bằng tiếng Anh đúng                                           |        |          |
| TC-ML-03 | —             |Hỏi xen kẽ tiếng Việt và tiếng Anh	                                        | Phản hồi theo đúng ngôn ngữ từng câu                 |        |          |

### 5.4 Nhập Liệu Giọng Nói
| ID         | Preconditions                            | Steps                                                      | Kỳ vọng                                                | Status | Evidence |
|------------|------------------------------------------|------------------------------------------------------------|---------------------------------------------------------|--------|----------|
| TC-VOICE-01 | Đã cấp quyền mic + FFmpeg đã cài đặt | Click 🎤 → Nhấn 🎤 → nói “Tôi muốn học Python căn bản” → dừng nói     | Văn bản chuyển chính xác, hiển thị thông báo thành công   |        |          |
| TC-VOICE-02 |Đã cấp quyền mic + FFmpeg đã cài đặt                                     | Nói câu bằng tiếng Anh chứa thuật ngữ Machine Learning	                                | Phản hồi chính xác, ngôn ngữ là tiếng Anh            |        |          |
| TC-VOICE-03 | Không có mic                                   | 	Thành phần mic bị vô hiệu                    | Không bị treo hay lỗi	                                           |        |          |

### 5.5 Chuyển Văn Bản Thành Giọng Nói (TTS)
| ID       | Preconditions               | Steps                                  | Kỳ vọng                                   | Status | Evidence |
|----------|-----------------------------|----------------------------------------|--------------------------------------------|--------|----------|
| TC-TTS-01 | Bật chức năng TTS   | Hỏi một câu bất kỳ                      | Phát giọng mô tả có âm thanh, không lặp lại    |        |          |
| TC-TTS-02 | Câu trả lời dài                  | Hỏi về “Các khóa học machine learning” | Giọng đọc trọn vẹn, không bị cắt đoạn         |        |          |


---

## 6) Tự Động Hóa Nhanh
### 6.1 Sanity script (Unix-like)
```bash
#!/usr/bin/env bash
set -e
python --version
pip --version
ffmpeg -version | head -n 1
ollama --version || true
ollama list || true
python - <<'PY'
from vector_database import MultilingualVectorDatabase
db = MultilingualVectorDatabase()
db.load_courses_data('courses.json')
db.build_index()
print("Courses:", len(db.courses_data))
print("Search:", db.search("python", top_k=3)[:1])
PY
echo "✔ Sanity OK"
```

### 6.2 Pytest entry (if you add tests/)
```bash
pytest -q
```

---

## 7) Xử Lý Sự Cố (Expanded)
- **`No module named 'torch'`**
  ```bash
  pip install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cpu
  ```
- **`Ollama connection failed`**
  - Ensure server is running: `ollama serve`
  - Re-run query; check firewall/VPN
- **Voice input not working**
  - Confirm `ffmpeg -version`
  - Reinstall audio deps: `pip install --upgrade pydub openai-whisper`
  - Browser mic permissions (use HTTPS or `http://localhost`), then refresh
- **Model issues**
  - Remove & re-pull: `ollama rm llama3.2:latest && ollama pull llama3.2:latest`
  - Check RAM/VRAM availability
- **UI formatting/XSS concern**
  - App uses `unsafe_allow_html=True`; sanitize any user/DB content before render

---

## 8) Mẫu Báo Cáo Kiểm Thử
Fill this after executing tests.

**Execution Info**
- Date: `YYYY-MM-DD`
- Tester(s): `Name`
- Commit/Branch: `An @ <short-sha>`
- Environment: `Windows/macOS/Linux`, Python `3.11.x`

**Summary**
- Total tests: `N`
- Passed: `N_pass`
- Failed: `N_fail`
- Stability: `High / Medium / Low`

**Detailed Results**
| Item                  | Status (✅/❌) | Notes / Errors / Links to Evidence           |
|-----------------------|----------------|----------------------------------------------|
| Install (Python/Git)  |                |                                              |
| Clone/Branch          |                |                                              |
| Venv & Deps           |                |                                              |
| FFmpeg                |                |                                              |
| Ollama & Model        |                |                                              |
| Streamlit Run         |                |                                              |
| FastAPI + Widget      |                |                                              |
| Chat                  |                |                                              |
| Tìm Kiếm Vector         |                |                                              |
| Voice                 |                |                                              |
| TTS                   |                |                                              |
| Multilingual          |                |                                              |
| Settings/State        |                |                                              |

**Bugs / Issues**
- ID, Severity (Blocker/Major/Minor), Steps to Reproduce, Kỳ vọng, Actual, Logs/Screenshots

**Go/No-Go**
- ✅ Go to staging / 🚧 Fix blockers first

---

## 9) Rủi Ro & Giảm Thiểu
- **Streamlit CSS class changes** → Use more stable selectors; keep a smoke test that checks key UI text.
- **HTML injection** from DB → Sanitize with a whitelist (e.g., `bleach`). Avoid raw HTML for course fields.
- **Model availability** → Provide mock mode fallback (already implemented); document it clearly.
- **Voice/TTS on enterprise networks** → Document mic permissions and autoplay policies; provide manual play button.

---

## 10) Nhật Ký Thay Đổi
- **v1.2** — Align model to `llama3.2`, add acceptance criteria, add Settings/State tests, expand troubleshooting.
- **v1.1** — Added automation scripts and report template.
- **v1.0** — Initial checklist & feature suites.
