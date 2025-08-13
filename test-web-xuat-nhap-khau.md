# Báo cáo Test Web Xuất Nhập Khẩu 📚

## 1️⃣ Meta cơ bản & SEO
  - `<title>` → **Tiêu đề trang**
     
      - *Ví dụ*: Xuất nhập khẩu là gì? Thông tin tổng hợp từ A - Z cho người mới
      - **Tại sao quan trọng:** Hiển thị trên tab trình duyệt, kết quả Google, và giúp người đọc biết nội dung chính.
      - **Chuẩn SEO:** chứa từ khóa chính, dài vừa phải, hấp dẫn để tăng CTR.
  - `<meta name="description">` → **Mô tả trang**
    
      - Google thường hiển thị đoạn này dưới title.
      - Nội dung phải ngắn, rõ ràng, chứa từ khóa, kích thích người đọc click.
  - `<link rel="canonical" href="...">` → **URL chuẩn**
    
      - Tránh bị trùng lặp nội dung nếu cùng bài viết xuất hiện ở nhiều URL.
      - Google ưu tiên URL này khi index.
  
  - `<meta name="keywords" content="...">` → **Thẻ từ khóa (Meta Keywords)**
      - *ưu tiên tìm kiếm:* content="xuất nhập khẩu, logistics, thủ tục hải quan"


## 2️⃣ Open Graph (Facebook, LinkedIn…)
- Đây là các thẻ để khi share link lên mạng xã hội, hiển thị đẹp.

  - `<meta property="og:title">` → tiêu đề khi share.

  - `<meta property="og:description">` → mô tả khi share.

  - `<meta property="og:url">` → URL chính khi share.

  - `<meta property="og:image">` → ảnh hiển thị khi share (nên chuẩn 800x600).

  - `<meta property="og:site_name">` → tên website hiển thị cùng link.

  - `<meta property="og:type">` → kiểu nội dung, bài viết hay website.

  - `<meta property="article:published_time">` → ngày xuất bản, giúp hiển thị rich snippet, tăng uy tín.

  - `<meta property="og:locale" content="vi_VN" />` → **Xác định ngôn ngữ và khu vực của nội dung cho mạng xã hội**
      - Giúp Facebook, LinkedIn, Twitter hiển thị nội dung phù hợp với locale.
  - `<meta property="og:type" content="article" />` → Xác định loại nội dung là `article` (bài viết).
      - Giúp Open Graph và mạng xã hội hiển thị đúng kiểu nội dung.

📌 **Ý nghĩa:**  Nếu bạn share bài viết “Xuất nhập khẩu là gì?” trên Facebook, LinkedIn… thì nó sẽ hiển thị đẹp, đúng title, mô tả, ảnh, thay vì chỉ show URL

## 3️⃣ Twitter Card
- Giống Open Graph nhưng **riêng cho Twitter.**

  - `<meta name="twitter:card">` → chọn kiểu hiển thị: summary_large_image sẽ hiển thị ảnh lớn đẹp.

  - `<meta name="twitter:label1">` + `<meta name="twitter:data1">` → hiển thị tác giả.

  - `<meta name="twitter:label2">` + `<meta name="twitter:data2">` → hiển thị thời gian đọc.

📌 **Ý nghĩa:** Khi share lên Twitter, bài viết sẽ hiện cả ảnh, tên tác giả, thời gian đọc, giúp người đọc tin tưởng và click nhiều hơn.

## 4️⃣ Robots & Geo Tags
  - `<meta name="robots" content="index,follow">` → Google biết index hay không.

  - `<meta name="geo.placename" content="Ha Noi, Viet Nam">` → định vị khu vực.

  - `<meta name="geo.region" content="VN-HN">`

  - `<meta name="geo.position" content="21.030624;105.782431">`

  - `<meta name="ICBM" content="21.030624, 105.782431">`
## 1. Trang Chủ 🏠

- **URL:** `/trang-chu`  
- **Title:** `Web Xuất Nhập Khẩu - Quản lý xuất nhập hàng hóa`  
- **Meta Description:** `Trang web quản lý xuất nhập khẩu, logistics và kho vận.`  
- **Meta Keywords:** `xuất nhập khẩu, logistics, quản lý hàng hóa`  
- **Canonical URL:** `https://www.example.com/trang-chu`  
- **Mục đích trang:** Giới thiệu tổng quan về dịch vụ, link đến các trang sản phẩm và liên hệ.  
- **SEO Notes:** Title < 60 ký tự, description < 160 ký tự, chứa từ khóa chính.

---

## 2. Trang Sản Phẩm Xuất Khẩu

- **URL:** `/san-pham-xuat-khau`  
- **Title:** `Sản phẩm Xuất Khẩu - [Tên sản phẩm]`  
- **Meta Description:** `Danh sách sản phẩm xuất khẩu chất lượng cao, giá cả cập nhật.`  
- **Meta Keywords:** `sản phẩm xuất khẩu, xuất khẩu, hàng hóa`  
- **Canonical URL:** `https://www.example.com/san-pham-xuat-khau`  
- **Mục đích trang:** Liệt kê các sản phẩm xuất khẩu, hỗ trợ tìm kiếm và lọc sản phẩm.  
- **SEO Notes:** Nếu có nhiều sản phẩm, nên dùng pagination với canonical page.  

**Keyword gợi ý cho tìm kiếm:** `gạo xuất khẩu`, `cà phê xuất khẩu`, `thực phẩm xuất khẩu`.

---

## 3. Trang Sản Phẩm Nhập Khẩu

- **URL:** `/san-pham-nhap-khau`  
- **Title:** `Sản phẩm Nhập Khẩu - [Tên sản phẩm]`  
- **Meta Description:** `Các sản phẩm nhập khẩu chính hãng, đầy đủ thông tin chi tiết.`  
- **Meta Keywords:** `sản phẩm nhập khẩu, nhập khẩu, kiểm tra hàng`  
- **Canonical URL:** `https://www.example.com/san-pham-nhap-khau`  
- **Mục đích trang:** Hiển thị danh sách sản phẩm nhập khẩu, hỗ trợ tìm kiếm.  
- **SEO Notes:** Giống trang xuất khẩu, chú ý canonical nếu dùng filter.

**Keyword gợi ý:** `hàng nhập khẩu`, `thiết bị nhập khẩu`, `đồ gia dụng nhập khẩu`.

---

## 4. Trang Chi Tiết Sản Phẩm

- **URL:** `/chi-tiet-san-pham/[ten-san-pham]`  
- **Title:** `[Tên sản phẩm] - Xuất Nhập Khẩu`  
- **Meta Description:** `Thông tin chi tiết sản phẩm, giá, xuất xứ và cách đặt hàng.`  
- **Meta Keywords:** `sản phẩm xuất nhập khẩu, chi tiết sản phẩm, giá cả`  
- **Canonical URL:** `https://www.example.com/chi-tiet-san-pham/[ten-san-pham]`  
- **Mục đích trang:** Cung cấp thông tin chi tiết, giúp khách hàng quyết định mua hàng.  
- **SEO Notes:** URL nên chứa keyword, title chứa tên sản phẩm, H1 trùng với tên sản phẩm.

---

## 5. Trang Quản Lý Đơn Hàng

- **URL:** `/quan-ly-don-hang`  
- **Title:** `Quản lý đơn hàng xuất nhập`  
- **Meta Description:** `Theo dõi trạng thái đơn hàng xuất nhập khẩu, vận chuyển nhanh.`  
- **Meta Keywords:** `quản lý đơn hàng, tracking đơn hàng, logistics`  
- **Canonical URL:** `https://www.example.com/quan-ly-don-hang`  
- **Mục đích trang:** Dùng để tracking và quản lý đơn hàng.  
- **SEO Notes:** Nếu trang dành cho admin hoặc user nội bộ, dùng `noindex` để không index trên Google.

---

## 6. Trang Báo Cáo Xuất Nhập

- **URL:** `/bao-cao-xuat-nhap`  
- **Title:** `Báo cáo Xuất Nhập Khẩu`  
- **Meta Description:** `Báo cáo thống kê xuất nhập khẩu, doanh thu và hàng tồn kho.`  
- **Meta Keywords:** `báo cáo xuất nhập khẩu, thống kê, kho vận`  
- **Canonical URL:** `https://www.example.com/bao-cao-xuat-nhap`  
- **Mục đích trang:** Thống kê dữ liệu xuất nhập.  
- **SEO Notes:** Dùng canonical nếu có nhiều filter, có thể dùng `noindex` nếu báo cáo nội bộ.

---

## 7. Trang Liên Hệ

- **URL:** `/lien-he`  
- **Title:** `Liên hệ - Web Xuất Nhập Khẩu`  
- **Meta Description:** `Liên hệ với chúng tôi để được hỗ trợ xuất nhập khẩu nhanh chóng.`  
- **Meta Keywords:** `liên hệ, support, xuất nhập khẩu`  
- **Canonical URL:** `https://www.example.com/lien-he`  
- **Mục đích trang:** Form liên hệ, thông tin hotline, email.  
- **SEO Notes:** Title ngắn gọn, description hấp dẫn.

---
## 4️⃣ Meta Tags chuẩn SEO
| Trang        | Title                                    | Description                              | Keywords                                        |
|-------------|-----------------------------------------|------------------------------------------|------------------------------------------------|
| Trang chủ   | Web Xuất Nhập Khẩu | Công ty ABC      | Trang giới thiệu dịch vụ xuất nhập khẩu | xuất khẩu, nhập khẩu, logistics               |
| Sản phẩm    | Sản phẩm Xuất Nhập Khẩu | ABC            | Danh sách sản phẩm xuất nhập khẩu       | gạo, cà phê, sản phẩm xuất khẩu               |
| Dịch vụ     | Dịch vụ Logistics | ABC                 | Dịch vụ vận chuyển và thủ tục hải quan | logistics, vận chuyển, thủ tục hải quan      |
| Liên hệ    | Liên hệ Công ty ABC                        | Thông tin liên hệ, đặt hàng, hỗ trợ    | liên hệ, đặt hàng, hỗ trợ khách hàng          |
| Blog        | Tin tức Xuất Nhập Khẩu | ABC              | Tin tức, thị trường xuất nhập khẩu      | tin tức xuất nhập khẩu, thị trường quốc tế   |

### Open Graph & Social Meta
- `<meta property="og:title" content="...">`
- `<meta property="og:description" content="...">`
- `<meta property="og:image" content="...">`
- `<meta property="og:url" content="...">`

### Canonical
- `<link rel="canonical" href="URL chuẩn của trang">`

## 5️⃣ Chức năng kiểm thử
- Form xuất nhập dữ liệu
- Tìm kiếm và lọc sản phẩm
- Upload/download file
- Responsive trên các thiết bị
- Tốc độ tải trang và SEO score

## 6️⃣ Kiểm thử bằng View Source / Inspect
1. **Kiểm tra thẻ `<title>`**
   - Xem tiêu đề trang có đúng SEO không.
   - Ví dụ: `<title>Web Xuất Nhập Khẩu | Công ty ABC</title>`

2. **Kiểm tra thẻ `<meta>`**
   - `<meta name="description" content="Mô tả chuẩn SEO về web xuất nhập khẩu">`
   - `<meta name="keywords" content="xuất khẩu, nhập khẩu, logistics, gạo, cà phê">`
   - `<meta name="robots" content="index, follow">`

3. **Kiểm tra Open Graph & Social Meta**
   - `<meta property="og:title" content="Web Xuất Nhập Khẩu | ABC">`
   - `<meta property="og:description" content="Trang web giới thiệu sản phẩm xuất nhập khẩu">`
   - `<meta property="og:image" content="https://example.com/og-image.jpg">`
   - `<meta property="og:url" content="https://example.com">`

4. **Kiểm tra Canonical**
   - `<link rel="canonical" href="https://example.com/trang-chu">`

5. **Kiểm tra Heading**
   - H1 chỉ xuất hiện 1 lần trên trang.
   - H2/H3 dùng cho các phân đoạn nội dung, sản phẩm, bài viết.

6. **Kiểm tra hình ảnh**
   - `<img src="..." alt="mô tả hình ảnh chuẩn SEO">`

