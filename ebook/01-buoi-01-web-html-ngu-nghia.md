# BUỔI 1: Web & HTML ngữ nghĩa

> **Thời lượng gợi ý:** 3–4 giờ (lý thuyết + thực hành trên lớp) + Phiếu 01 về nhà  
> **Tiên quyết:** Biết dùng trình duyệt, tạo/lưu file văn bản (`.html`), tài khoản GitHub  
> **Kết quả đầu ra:** Hiểu Client–Server & DOM tree; viết được trang HTML5 ngữ nghĩa (heading, list, link, image, table) cho CampusMart  
> **Phiếu học tập:** [Phiếu 01](./worksheets/phieu-01-html-ngu-nghia.md) — chuỗi **CampusMart UI**  
> **Dataset:** [CANONICAL-DATA](./worksheets/CANONICAL-DATA.md) — `product_count_core = 8`, `inventory_value_core = **41380000**`  
> **Liên thông Backend:** Cùng 8 SKU / 3 danh mục với MiniShop (CSE485) — Frontend chỉ **hiển thị**, Backend sau này sẽ **lưu & tính**

---

## 0. Gợi ý lịch trình giảng (cho GV)

| Phút | Nội dung | Gợi ý thao tác |
|------|----------|----------------|
| 0–10 | Web dân dã + Client–Server | Hỏi: “Khi mở Shopee, máy bạn xin gì? Máy chủ trả gì?” → vẽ ASCII |
| 10–25 | Frontend vs Backend + CampusMart | Chiếu bảng so sánh + nhắc cùng số liệu MiniShop |
| 25–45 | HTML = xương; tag / element / attribute | LEGO + live-code 1 thẻ `h1` rồi Inspect |
| 45–70 | Khung HTML5 + landmark + DOM tree | Gõ khung tối thiểu; F12 Elements xem cây |
| 70–95 | List, link, img, table + `data-testid` | Live-code 2 dòng SP + 3 danh mục |
| 95–130 | **Thực hành trên lớp Phiếu 01 mục 2** | SV làm skeleton + 4 SP; GV checkpoint |
| 130–150 | DevTools / Live Server / lỗi thường gặp | Demo `file://` vs Live Server; giao về nhà mục 3 |

> Trên lớp **ưu tiên xong khung ngữ nghĩa + 4 SKU + testid cơ bản**. Đủ 8 SP + tổng kho + `CM_EXPECT` để về nhà (Phiếu 01 mục 3).

---

## 1. Mục tiêu buổi học

Sau Buổi 1, sinh viên có thể:

1. Giải thích **Web / Internet** theo ngôn ngữ đời thường: trình duyệt *xin* tài liệu, server *trả* tài liệu.
2. Vẽ và giải thích mô hình **Client–Server** + luồng mở trang CampusMart (ASCII).
3. Phân biệt **Frontend** (môn này) và **Backend** (CSE485 MiniShop) — cùng dataset, khác tầng.
4. Giải thích HTML là **xương trang**; phân biệt **tag / element / attribute**.
5. Đọc được **DOM tree** của CampusMart (`html` → `head`/`body` → `header`/`nav`/`main`/`footer`).
6. Tạo file HTML5 đúng khung: `DOCTYPE`, `lang`, `charset`, `viewport`, `title`.
7. Dùng landmark ngữ nghĩa, heading phân cấp, list, link, `img`+`alt`, bảng catalog.
8. Gắn `data-testid` / `data-sku` / comment `CM_EXPECT` theo hợp đồng chấm.
9. Dùng DevTools **Elements** như “X-quang”; ưu tiên Live Server thay vì `file://`.

---

## 2. Vì sao cần Buổi 1 — và vì sao HTML ngữ nghĩa?

Bạn đang học Frontend để dựng giao diện **CampusMart**: cửa hàng phụ kiện máy tính trong khuôn viên (bàn phím, chuột, màn hình).

Khi mở một trang bán hàng, trình duyệt nhận về ba “lớp”:

| Lớp | Vai trò đời thường | Vai trò kỹ thuật |
|-----|--------------------|------------------|
| **HTML** | Bộ xương / khung nhà | Cấu trúc & ý nghĩa vùng nội dung |
| **CSS** | Sơn, gạch, nội thất | Trang trí, bố cục, màu |
| **JavaScript** | Công tắc điện, chuông cửa | Hành vi, tương tác, cập nhật |

Buổi 1 chỉ dựng **xương đúng nghĩa**. CSS (Buổi 3–5) và JS/React (Buổi 6–12) sẽ gắn lên xương này. Nếu xương sai (toàn `<div>` vô nghĩa, thiếu landmark, nhảy heading), buổi sau sẽ sửa đắt.

> Công cụ tìm kiếm, trình đọc màn hình (screen reader), và **Autograder** đều “đọc” cấu trúc.  
> Thẻ `<div>` thuần túy không nói được gì; thẻ `<nav>` / `<main>` thì có.

**Hình ảnh nhớ (1 câu):** CampusMart Buổi 1 = **khung thép đã dựng** — chưa sơn, chưa lắp điện, nhưng đã biết đâu là cửa, đâu là phòng khách.

---

## 3. Web / Internet — giải thích dân dã

### 3.1. Internet ≠ “cái trang web”

- **Internet:** mạng máy tính toàn cầu (đường ống).
- **Web (WWW):** một dịch vụ trên Internet — trao đổi **tài liệu** (HTML, ảnh, CSS, JS…) bằng giao thức **HTTP/HTTPS**.
- **Trình duyệt (Chrome, Edge, Firefox, Safari):** phần mềm phía bạn — *xin* tài liệu rồi *vẽ* ra màn hình.
- **Server (máy chủ):** máy (hoặc dịch vụ) giữ file / chạy code — *nhận xin*, *trả về*.

Hình ảnh đời thường:

```
Bạn (khách)          Quán cafe (server)
   │                        │
   │  “Cho mình menu”       │
   ├───────────────────────►│
   │                        │  Đưa tờ menu (HTML)
   │◄───────────────────────┤
   │  Đọc menu, chọn món    │
```

Trên Web, “menu” thường là file `index.html` (và kèm CSS/JS/ảnh).

### 3.2. URL — địa chỉ để xin đúng tài liệu

Ví dụ sau này khi dùng Live Server:

```
http://127.0.0.1:5500/index.html
 │      │         │     │
 │      │         │     └── tên file
 │      │         └── cổng (port)
 │      └── máy (localhost = máy bạn)
 └── giao thức
```

**Câu hỏi tự kiểm tra:** Nếu chỉ có Internet nhưng không có trình duyệt, bạn còn “lướt web” được không?  
→ Vẫn có thể dùng công cụ khác (curl, app…), nhưng **trải nghiệm trang web** gắn với client hiểu HTML/CSS/JS — thường là trình duyệt.

---

## 4. Client – Server (ASCII bắt buộc nhớ)

### 4.1. Sơ đồ tổng quát

```
┌─────────────────┐                      ┌──────────────────────────┐
│   CLIENT        │                      │   SERVER                 │
│   (Trình duyệt) │                      │   (máy chủ / Live Server)│
│                 │   1. HTTP Request    │                          │
│  Chrome/Edge    │ ───────────────────► │  Nhận URL                │
│  Firefox/Safari │                      │  Đọc file .html/.css/.js │
│                 │   2. HTTP Response   │  (hoặc chạy Backend)     │
│  Dựng giao diện │ ◄─────────────────── │  Trả HTML / CSS / JS     │
└─────────────────┘                      └──────────────────────────┘
         ▲
         │
    Người dùng nhìn thấy Frontend ở đây
```

### 4.2. Luồng mở trang CampusMart (Buổi 1)

Ở Buổi 1, “server” thường chỉ là **Live Server** (hoặc bất kỳ static server nào) phục vụ file tĩnh — chưa có PHP/Laravel.

```
 Người dùng              Client                    Server (Live Server)
     │                     │                              │
     │  Mở CampusMart      │                              │
     ├────────────────────►│  GET /index.html             │
     │                     ├─────────────────────────────►│
     │                     │                              │ Đọc index.html
     │                     │  200 OK + HTML               │
     │                     │◄─────────────────────────────┤
     │                     │  (có thể xin thêm ảnh/CSS)   │
     │  Thấy catalog       │                              │
     │◄────────────────────┤                              │
```

### 4.3. Frontend vs Backend (liên thông CSE485)

| | Frontend (CSE391 — môn này) | Backend (CSE485 — MiniShop) |
|---|-----------------------------|-----------------------------|
| Chạy ở đâu? | Trình duyệt (Client) | Máy chủ (Server) |
| Ngôn ngữ giai đoạn đầu | HTML, CSS, JS, React | PHP, Laravel, … |
| Nhiệm vụ | Hiển thị, tương tác | Logic, CSDL, API, bảo mật |
| Dataset CampusMart / MiniShop | Cùng **8 SP + 3 DM**, tổng kho **41380000** | Cùng số liệu, lưu DB |
| Buổi 1 làm gì? | Trang HTML tĩnh đúng cấu trúc | (CSE485) mảng PHP / sau này DB |

> **Lưu ý sư phạm:** Học Frontend không có nghĩa là “không cần Backend”. Ngược lại: dữ liệu bạn hard-code hôm nay chính là thứ API sẽ trả về sau này. Giữ đúng SKU/giá từ đầu = đỡ lệch khi nối môn.

**Câu hỏi tự kiểm tra:** Nếu tắt Live Server rồi double-click `index.html` (`file://`), trang còn hiện không? Khác gì `http://localhost:…`?  
→ HTML tĩnh thường vẫn hiện, nhưng nhiều API trình duyệt (ES module, `fetch`, một số đường dẫn…) bị hạn chế trên `file://`. Học kỳ này **ưu tiên Live Server**.

**Hình ảnh nhớ:** Client = bàn ăn của khách; Server = bếp. HTML Buổi 1 = món đã nấu sẵn mang ra bàn (file tĩnh). Backend sau này = bếp nấu theo order.

---

## 5. HTML là gì? — xương, LEGO, và ba khái niệm dễ lẫn

### 5.1. Hình ảnh xương / LEGO

```
┌────────────────────────────────────────────┐
│  LEGO viên = thẻ HTML (<h1>, <p>, <table>) │
│  Cách xếp viên = cấu trúc trang (DOM)      │
│  Màu sơn viên = CSS (buổi sau)             │
│  Viên biết cử động = JavaScript (buổi sau) │
└────────────────────────────────────────────┘
```

HTML (**HyperText Markup Language**) không phải ngôn ngữ lập trình tính toán như PHP/JS. HTML là ngôn ngữ **đánh dấu**: nói với trình duyệt *đây là tiêu đề*, *đây là đoạn văn*, *đây là bảng sản phẩm*.

### 5.2. Tag vs Element vs Attribute

| Thuật ngữ | Nghĩa ngắn | Ví dụ |
|-----------|------------|--------|
| **Tag** | Nhãn mở/đóng | `<h1>`, `</h1>` |
| **Element** | Cả khối: mở + nội dung + đóng | `<h1>CampusMart</h1>` |
| **Attribute** | Thông tin phụ trên thẻ mở | `lang="vi"`, `data-sku="KB-01"` |

```html
<!-- Element = cả dòng dưới đây -->
<h1 data-testid="cm-brand">CampusMart</h1>
│ │  └──── attribute ────┘  └── nội dung └──│
│ └───────────── tag mở ────────────────────│
└─────────────────── tag đóng ──────────────┘
```

Một số thẻ **rỗng** (không nội dung): `<img>`, `<meta>`, `<br>` — trong HTML5 viết `<img … />` hoặc `<img …>` đều phổ biến.

### 5.3. Thử ngay trên máy — element đầu tiên

1. Tạo thư mục `cse391-buoi1-demo/` và file `hello.html`.
2. Gõ đúng:

```html
<!DOCTYPE html>
<html lang="vi">
<head>
  <meta charset="UTF-8" />
  <title>Thu nghiem</title>
</head>
<body>
  <!-- Dự đoán: trình duyệt sẽ hiện chữ to "CampusMart" -->
  <h1>CampusMart</h1>
</body>
</html>
```

3. **Dự đoán** trước khi mở: tab trình duyệt ghi gì? Trang hiện chữ cỡ nào?
4. Mở bằng Live Server → quan sát title tab + chữ `h1`.
5. Đổi thành `<p>CampusMart</p>` → lưu → reload. Chữ còn “to như tiêu đề” không?

**Quan sát kỳ vọng:** `h1` là heading cấp 1 (to, đậm theo style mặc định); `p` là đoạn văn. Cùng chữ, **ý nghĩa khác** → đây chính là ngữ nghĩa.

---

## 6. Trình duyệt dựng trang: DOM tree

Khi nhận HTML, trình duyệt **parse** thành **DOM** (Document Object Model) — cây nút. Mỗi element là một nút; nút con nằm trong nút cha.

### 6.1. DOM CampusMart (ASCII bắt buộc)

```
document
└── html
    ├── head
    │   ├── meta charset
    │   ├── meta viewport
    │   └── title  ("CampusMart — Catalog (Buoi 1)")
    └── body
        ├── header
        │   ├── h1#cm-brand  (CampusMart)
        │   └── nav#cm-nav
        │       └── ul
        │           ├── li > a  (Trang chu)
        │           ├── li > a  (San pham)
        │           └── li > a  (Gioi thieu)
        ├── main
        │   ├── section#categories
        │   │   ├── h2
        │   │   └── ul#cm-category-list
        │   │       ├── li  Ban phim
        │   │       ├── li  Chuot
        │   │       └── li  Man hinh
        │   └── section#products
        │       ├── h2
        │       └── table#cm-product-table
        │           ├── caption
        │           ├── thead > tr > th × 5
        │           └── tbody > tr#cm-product-row × 8
        └── footer
```

> Từ Buổi 6, JavaScript sẽ `document.querySelector('[data-testid="cm-product-table"]')` trên cây này.  
> Cấu trúc sạch → code JS đỡ rối; cấu trúc “div soup” → selector dễ gãy.

### 6.2. DevTools Elements = X-quang

1. Mở trang → nhấn `F12` (hoặc Chuột phải → Inspect).
2. Tab **Elements** (Chrome/Edge) / **Inspector** (Firefox).
3. Hover từng nút trên cây → vùng tương ứng trên trang được tô.

**Hình ảnh nhớ:** HTML file = bản vẽ kiến trúc; DOM = ngôi nhà đã dựng trong đầu trình duyệt; Elements = ống X-quang soi khung thép.

**Câu hỏi tự kiểm tra:** Nếu bạn sửa chữ trong tab Elements, file `.html` trên đĩa có đổi không?  
→ **Không.** Đó là sửa DOM đang sống trong bộ nhớ. Reload trang = parse lại từ file → sửa tạm mất. Muốn giữ thì phải sửa file nguồn.

---

## 7. Khung HTML5 tối thiểu — giải thích từng dòng

Title Exact theo CANONICAL (P01):

```html
<!DOCTYPE html>
<html lang="vi">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>CampusMart — Catalog (Buoi 1)</title>
</head>
<body>
  <!-- Mọi thứ người dùng nhìn thấy nằm trong body -->
  <h1>CampusMart</h1>
</body>
</html>
```

| Thành phần | Việc nó làm | Nếu thiếu thì sao? |
|------------|-------------|---------------------|
| `<!DOCTYPE html>` | Báo trình duyệt dùng chế độ chuẩn HTML5 | Có thể rơi “quirks mode” — layout lệch khó đoán |
| `<html lang="vi">` | Khai báo ngôn ngữ tài liệu | Screen reader / SEO kém gợi ý ngôn ngữ |
| `<meta charset="UTF-8">` | Bảng mã ký tự | Tiếng Việt dễ lỗi font |
| `<meta name="viewport" …>` | Nền tảng responsive (Buổi 5) | Mobile zoom/layout mặc định khó kiểm soát |
| `<title>…</title>` | Chữ trên tab + EXPECT `page_title` | Tab xấu; **máy chấm P01 có thể Fail** nếu sai exact |
| `<head>` | Metadata — thường không hiện trực tiếp | — |
| `<body>` | Nội dung nhìn thấy | — |

### 7.1. Thử ngay trên máy — charset

1. Tạm **xóa** dòng `charset` (chỉ trong file demo, không nộp vậy).
2. Gõ trong body: `<p>Ban phim Keychron — gia 1890000</p>`.
3. Quan sát nếu encoding lệch.
4. Thêm lại `<meta charset="UTF-8" />` **ngay đầu** `<head>` → reload.

**Quan sát:** UTF-8 + meta charset đúng chỗ = tiếng Việt ổn định. Đây là thói quen nghề, không phải “trang trí”.

### 7.2. Title exact — đừng sáng tạo

| Đúng (EXPECT) | Sai thường gặp |
|---------------|----------------|
| `CampusMart — Catalog (Buoi 1)` | `CampusMart Catalog Buoi 1` (thiếu gạch) |
| | `CampusMart — Catalog (Buổi 1)` (dấu ố) |
| | `Campus mart — catalog (buoi 1)` (hoa thường/space) |

Máy chấm so khớp chuỗi. **Copy đúng** từ CANONICAL / phiếu.

---

## 8. HTML ngữ nghĩa — xương trang CampusMart

### 8.1. Landmark chính — vì sao không chỉ dùng `div`?

| Thẻ | Ý nghĩa | CampusMart dùng thế nào |
|-----|---------|-------------------------|
| `header` | Phần đầu trang / đầu section | Brand + nav |
| `nav` | Điều hướng chính | 3 link: Trang chu / San pham / Gioi thieu |
| `main` | Nội dung chính **duy nhất** của trang | Categories + products |
| `section` | Khối chủ đề có heading | `#categories`, `#products`, `#about` |
| `article` | Khối nội dung độc lập (card sau này) | Preview — P01 ưu tiên `table` |
| `footer` | Chân trang | Mã môn / ghi chú buổi |
| `div` | Hộp **không** mang nghĩa | Chỉ khi cần bọc để CSS/JS, không thay landmark |

```
┌──────────────────────────────────────────┐
│  header  │  h1 CampusMart  +  nav        │
├──────────────────────────────────────────┤
│  main    │  categories + products        │
├──────────────────────────────────────────┤
│  footer  │  CSE391 — CampusMart UI       │
└──────────────────────────────────────────┘
```

Mẫu khung (bám testid CANONICAL):

```html
<body>
  <header>
    <!-- cm-brand: máy chấm tìm text CampusMart -->
    <h1 data-testid="cm-brand">CampusMart</h1>

    <nav data-testid="cm-nav" aria-label="Menu chinh">
      <ul>
        <li><a href="#home">Trang chu</a></li>
        <li><a href="#products">San pham</a></li>
        <li><a href="#about">Gioi thieu</a></li>
      </ul>
    </nav>
  </header>

  <main id="home">
    <!-- categories + products -->
  </main>

  <footer>
    <p>CSE391 — CampusMart UI — Buoi 1</p>
  </footer>
</body>
```

**Câu hỏi tự kiểm tra:** Thay mọi thứ bằng `<div class="header">`, `<div class="nav">`… trang nhìn có thể giống — nhưng khác gì với máy đọc / autograder / a11y?  
→ Class CSS **không** tạo landmark HTML. Screen reader và một số rule chấm dựa trên **tên thẻ** (hoặc role), không chỉ class.

**Hình ảnh nhớ:** `div` = hộp carton không nhãn; `nav` = hộp ghi chữ “MENU”. Cùng hình hộp, khác cách người và máy hiểu.

### 8.2. Heading hierarchy

Quy tắc vàng:

1. Mỗi trang **một** `<h1>` — thương hiệu / tiêu đề chính (`CampusMart`).
2. Mỗi `section` lớn dùng `<h2>` (“Danh muc”, “San pham”, “Gioi thieu”).
3. Không nhảy `h1` → `h3` bỏ `h2`.
4. Không dùng heading chỉ vì “chữ to” — muốn to mà không phải tiêu đề thì dùng CSS sau này.

```
h1 CampusMart
 ├── h2 Danh muc
 └── h2 San pham
      └── (sau này h3 cho nhóm nếu cần — P01 chưa bắt buộc)
```

### 8.3. List — danh mục (3 tên exact)

```html
<section id="categories" aria-labelledby="cat-heading">
  <h2 id="cat-heading">Danh muc</h2>
  <ul data-testid="cm-category-list">
    <li>Ban phim</li>
    <li>Chuot</li>
    <li>Man hinh</li>
  </ul>
</section>
```

| Loại list | Thẻ | Khi nào dùng |
|-----------|-----|--------------|
| Không thứ tự | `ul` > `li` | Menu, danh mục, tag |
| Có thứ tự | `ol` > `li` | Bước 1–2–3, xếp hạng |
| Mô tả | `dl` > `dt`/`dd` | Thuật ngữ — định nghĩa (ít dùng P01) |

**Map `category_id` → chữ (tư duy liên thông Backend):**

| category_id (trong data) | Tên hiện trên UI (exact) |
|-------------------------:|--------------------------|
| 1 | Ban phim |
| 2 | Chuot |
| 3 | Man hinh |

P01 hard-code **tên chữ** trên HTML. Không in số `1`, `2`, `3` làm nhãn danh mục.

### 8.4. Link nội bộ

```html
<!-- href="#products" phải khớp id="products" -->
<a href="#products">San pham</a>

<section id="products">…</section>
```

```
Click "San pham" ──► trình duyệt cuộn tới element id="products"
```

### 8.5. Image + alt

```html
<img
  src="https://placehold.co/120x80"
  alt="Ban phim Keychron K2"
  width="120"
  height="80"
/>
```

| Thuộc tính | Bắt buộc? | Ý nghĩa |
|------------|-----------|---------|
| `src` | Có (để có ảnh) | Đường dẫn / URL ảnh |
| `alt` | **Có** (a11y + thói quen nghề) | Mô tả thay thế khi ảnh lỗi / screen reader |
| `width`/`height` | Nên có | Giảm nhảy layout khi ảnh tải |

> P01: placeholder URL được chấp nhận. `alt` **không được rỗng** nếu có `img`.

### 8.6. Table — catalog sản phẩm

Bảng phù hợp dữ liệu dạng lưới (SKU, tên, giá, số lượng) — đúng tinh thần “catalog”.

```html
<section id="products" aria-labelledby="prod-heading">
  <h2 id="prod-heading">San pham</h2>
  <table data-testid="cm-product-table" class="cm-table">
    <caption>Danh sach san pham CORE</caption>
    <thead>
      <tr>
        <th scope="col">SKU</th>
        <th scope="col">Ten</th>
        <th scope="col">Danh muc</th>
        <th scope="col">Gia</th>
        <th scope="col">So luong</th>
      </tr>
    </thead>
    <tbody>
      <tr data-testid="cm-product-row" data-sku="KB-01">
        <td>KB-01</td>
        <td>Keychron K2</td>
        <td>Ban phim</td>
        <td>1890000</td>
        <td>3</td>
      </tr>
      <!-- … đủ 8 dòng theo CANONICAL — làm ở Phiếu 01 -->
    </tbody>
  </table>
</section>
```

Cột tối thiểu P01: `SKU | Ten | Danh muc | Gia | So luong`.

**Quy tắc vàng số cột:** số `<th>` trong `thead` = số `<td>` mỗi hàng `tbody`. Lệch một ô là bảng “vỡ”.

### 8.7. Section `#about` (link nav “Gioi thieu”)

Phiếu về nhà yêu cầu `href="#about"` khớp `id`. Thêm một section ngắn — không cần dài:

```html
<section id="about" aria-labelledby="about-heading">
  <h2 id="about-heading">Gioi thieu</h2>
  <p>
  CampusMart la du an mau CSE391 — cua hang phu kien may tinh trong khuon vien.
  Buoi 1 dung HTML tinh; CSS va JavaScript se bo sung o cac buoi sau.
  </p>
</section>
```

**Câu hỏi tự kiểm tra:** `id="about"` có được phép trùng với `id` khác trên trang không?  
→ **Không.** `id` phải duy nhất trong một document.

### 8.8. Thử ngay trên máy — heading & link

| Bước | Thao tác | Dự đoán | Quan sát |
|------|----------|---------|----------|
| 1 | Đặt hai `<h1>` trên trang | Cả hai đều “to” | Sai ngữ nghĩa — chỉ giữ một `h1` |
| 2 | Đổi `h2` “San pham” thành `h3` bỏ `h2` | Vẫn hiện chữ | Outline/a11y bị nhảy cấp |
| 3 | Sửa `href="#product"` (thiếu s) | Click không nhảy | Khớp ký tự `id` |

### 8.9. Comment HTML — ghi chú cho người & máy

```html
<!-- Day la ghi chu cho nguoi doc code — trinh duyet KHONG hien -->
<!-- CM_EXPECT ... -->  <!-- Dong nay la marker cho may cham -->
```

Comment không hiện trên trang, nhưng vẫn nằm trong source — Autograder đọc được `CM_EXPECT`.

---

## 9. `data-testid` / `data-sku` / `CM_EXPECT` — chuẩn bị máy chấm

Autograder và script chấm **không** muốn phụ thuộc vào class CSS bạn đổi tên lung tung. CampusMart khóa marker ổn định từ P01.

### 9.1. Bảng testid (CANONICAL §4)

| `data-testid` | Gắn vào | Ý nghĩa |
|---------------|---------|---------|
| `cm-brand` | `h1` brand | Text `CampusMart` |
| `cm-nav` | `nav` | Thanh điều hướng |
| `cm-category-list` | `ul` danh mục | Đếm 3 category |
| `cm-product-table` | `table` | Bảng catalog |
| `cm-product-row` | mỗi `tr` SP | Một dòng sản phẩm |
| `cm-inventory-total` | đoạn tổng kho | Text chứa `41380000` (về nhà) |

Kèm theo:

```html
<tr data-testid="cm-product-row" data-sku="KB-01">…</tr>
```

`data-sku` giúp chấm theo SKU, không phụ thuộc thứ tự hàng nếu rule cho phép.

### 9.2. Comment `CM_EXPECT`

Cuối `body` (trước `</body>`), phiếu về nhà yêu cầu:

```html
<!-- CM_EXPECT product_count=8 category_count=3 inventory_value=41380000 -->
```

| Prefix | Hệ | Ghi nhớ |
|--------|----|---------|
| `CM_EXPECT` | CampusMart (CSE391) | Frontend |
| `MS_EXPECT` | MiniShop (CSE485) | Backend — **đừng nhầm** |

### 9.3. Title + chuỗi nav exact

| Key | Value exact |
|-----|-------------|
| `page_title_p01` | `CampusMart — Catalog (Buoi 1)` |
| `brand_name` | `CampusMart` |
| `nav_home_label` | `Trang chu` |
| `nav_products_label` | `San pham` |
| `nav_about_label` | `Gioi thieu` |
| `table_caption_p01` | `Danh sach san pham CORE` |

### 9.4. Thử ngay trên máy — tìm testid bằng DevTools

1. Mở trang có `data-testid="cm-brand"`.
2. Elements → `Ctrl+F` / `Cmd+F` trong panel → gõ `cm-brand`.
3. **Dự đoán:** nhảy đúng nút `h1`.
4. Thử tìm `cm-product-row` — có bao nhiêu khớp sau khi làm đủ 8 SP?

**Hình ảnh nhớ:** `data-testid` = mã vạch sản phẩm trong siêu thị. Người mua (user) không cần thấy; máy kiểm kho (autograder) thì cần.

---

## 10. Live Server vs `file://` — và thói quen mở trang

| Cách mở | URL dạng | Nên dùng khi nào |
|---------|----------|------------------|
| Double-click file | `file:///Users/…/index.html` | Chỉ xem HTML cực đơn giản |
| **Live Server** (VS Code/Cursor) | `http://127.0.0.1:5500/…` | **Mặc định cả học kỳ** |
| `npx serve` / Python `-m http.server` | `http://localhost:…` | Tương đương static server |

Vì sao ưu tiên HTTP local:

1. Đường dẫn tài nguyên ổn định hơn.
2. Sẵn sàng cho ES module / `fetch` (Buổi 6–8).
3. Giống môi trường web thật hơn `file://`.

### 10.1. Thử ngay trên máy — so sánh hai cách mở

1. Mở cùng `index.html` bằng `file://` — ghi lại thanh địa chỉ.
2. Mở bằng Live Server — ghi lại thanh địa chỉ.
3. Quan sát: cả hai có hiện HTML không? Tab title có giống không?
4. Ghi vào README demo 1 dòng: “P01 dùng Live Server vì …”

### 10.2. DevTools — ba tab SV cần biết từ Buổi 1

| Tab | Dùng để làm gì (Buổi 1) |
|-----|---------------------------|
| **Elements** | Xem DOM tree, tìm `data-testid`, kiểm landmark |
| **Console** | (Preview) Sau này log JS; Buổi 1 chỉ mở thử, chưa bắt buộc |
| **Network** | (Preview) Xem file `.html` được tải khi reload |

**Thử ngay trên máy:** Reload trang → tab Network → lọc `Doc` → thấy `index.html` status `200`.  
**Dự đoán:** Nếu đổi tên file mà quên sửa URL Live Server → status `404`.

**Câu hỏi tự kiểm tra:** Tab Elements và View Page Source có giống hệt nhau không?  
→ Gần giống lúc mới load; sau khi bạn sửa DOM trong Elements thì **khác** file gốc cho đến khi reload.

---

## 11. Dataset CampusMart CORE_8 (nhìn trước Phiếu 01)

Không đổi số liệu. Tổng kiểm tra:

```
KB-01: 1890000*3 = 5670000
KB-02: 1290000*5 = 6450000
KB-03: 2750000*2 = 5500000
MS-01:  290000*10 = 2900000
MS-02:  990000*4  = 3960000
MS-03:  250000*8  = 2000000
MN-01: 3200000*2  = 6400000
MN-02: 8500000*1  = 8500000
────────────────────────────
TỔNG = 41380000
```

| sku | name | Danh muc | price | qty |
|-----|------|----------|------:|----:|
| KB-01 | Keychron K2 | Ban phim | 1890000 | 3 |
| KB-02 | Akko 3087 | Ban phim | 1290000 | 5 |
| KB-03 | Leopold FC660M | Ban phim | 2750000 | 2 |
| MS-01 | Logitech M331 | Chuot | 290000 | 10 |
| MS-02 | Razer Viper | Chuot | 990000 | 4 |
| MS-03 | Xiaomi Silent | Chuot | 250000 | 8 |
| MN-01 | Dell 24 inch | Man hinh | 3200000 | 2 |
| MN-02 | LG UltraFine | Man hinh | 8500000 | 1 |

**Trên lớp:** 4 SP đầu (KB-01, KB-02, MS-01, MS-02).  
**Về nhà:** đủ 8 + caption + `Tong gia tri kho = 41380000` + `CM_EXPECT`.

---

## 12. Bài thực hành minh họa trong buổi (KHÔNG thay phiếu)

> Mục này chỉ để **luyện tay trên lớp**. Nộp bài vẫn theo [Phiếu 01](./worksheets/phieu-01-html-ngu-nghia.md).

Tạo `cse391-buoi1/` (tên khác repo nộp) — chỉ **2 sản phẩm**:

```html
<!DOCTYPE html>
<html lang="vi">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <!-- Title demo — KHÔNG dùng title này để nộp P01 -->
  <title>Thuc hanh Buoi 1 — CampusMart</title>
</head>
<body>
  <header>
    <h1>CampusMart</h1>
    <nav aria-label="Menu chinh">
      <ul>
        <li><a href="#products">San pham</a></li>
      </ul>
    </nav>
  </header>

  <main>
    <section id="products">
      <h2>San pham mau</h2>
      <table>
        <thead>
          <tr>
            <th scope="col">SKU</th>
            <th scope="col">Ten</th>
            <th scope="col">Gia</th>
          </tr>
        </thead>
        <tbody>
          <!-- Hai dòng mẫu để hiểu data-sku — phiếu yêu cầu đủ cột & 4→8 SP -->
          <tr data-sku="KB-01">
            <td>KB-01</td>
            <td>Keychron K2</td>
            <td>1890000</td>
          </tr>
          <tr data-sku="MS-01">
            <td>MS-01</td>
            <td>Logitech M331</td>
            <td>290000</td>
          </tr>
        </tbody>
      </table>
    </section>
  </main>

  <footer>
    <p>Chi la bai minh hoa — lam du o Phieu 01</p>
  </footer>
</body>
</html>
```

### 12.1. Thử ngay trên máy (minh họa)

| Bước | Việc làm | Dự đoán | Quan sát |
|------|----------|---------|----------|
| 1 | Mở Live Server | Thấy brand + bảng 2 dòng | Tab title là bản demo |
| 2 | F12 → Elements | Cây có `header`/`main`/`footer` | Khớp ASCII mục 6 |
| 3 | Click link San pham | Cuộn tới `#products` | `id` khớp `href` |
| 4 | Đếm `<td>` hàng 1 | = 3 (SKU/Ten/Gia) | Trùng số `<th>` |

Sau đó **chuyển sang repo `cse391-cm-01`** và làm đúng Phiếu 01 (title exact, 4 SP trên lớp, testid đủ).

---

## 13. Lỗi thường gặp (bảng lớn)

| Triệu chứng | Nguyên nhân thường gặp | Cách xử lý |
|-------------|------------------------|------------|
| Tiếng Việt lỗi font | Thiếu `charset=UTF-8` hoặc đặt sai chỗ | Meta charset **dòng đầu** trong `<head>` |
| Title máy chấm Fail | Sai exact / thêm dấu / đổi hoa thường | Copy `CampusMart — Catalog (Buoi 1)` |
| Click nav không nhảy | `href="#products"` ≠ `id="products"` | Khớp từng ký tự |
| Bảng “vỡ” cột | Số `th` ≠ số `td` | Đếm lại thead/tbody |
| Autograder không thấy SP | Thiếu `data-sku` / `cm-product-row` | Bám CANONICAL §4 |
| Category_count sai | Thiếu/thừa `<li>`, sai tên | Đúng 3: Ban phim, Chuot, Man hinh |
| Hiện `1` `2` `3` thay tên DM | Quên map id → chữ | In **tên danh mục**, không in id |
| Tổng kho sai | Nhân `price*qty` nhầm / thiếu dòng | Cộng lại §11 → **41380000** |
| Chỉ dùng `<div>` | Quên ngữ nghĩa | Đổi `header`/`nav`/`main`/`footer` |
| `file://` lỗi tài nguyên sau này | Không dùng static server | Chuyển Live Server từ P01 |
| Nhầm `MS_EXPECT` | Copy từ ebook Backend | Dùng **`CM_EXPECT`** |
| Ảnh không hiện | Sai `src` / không mạng (placeholder) | Kiểm tra URL; vẫn giữ `alt` |
| `alt=""` | Nghĩ “không cần mô tả” | P01: `alt` không rỗng nếu có `img` |
| Hai `<h1>` | Brand + “San pham” đều h1 | Chỉ một `h1`; section dùng `h2` |
| Nested `table` / quên cấu trúc | Copy lệch | `table > caption? + thead + tbody` |
| Repo sai tên | Đặt `cse391-buoi1` để nộp | Nộp **`cse391-cm-01`** |

---

## 14. Tóm tắt bảng trắng (chốt buổi)

```
Internet/Web → Client xin / Server trả
     ↓
HTML = xương (ngữ nghĩa) → DOM tree
     ↓
Landmark: header · nav · main · footer
     ↓
Catalog: ul (3 DM) + table (8 SP) + testid
     ↓
EXPECT: title + CM_EXPECT + inventory 41380000
```

| Khái niệm | Một câu nhớ |
|-----------|-------------|
| Client | Trình duyệt xin & vẽ |
| Server | Nơi giữ/trả tài liệu |
| DOM | Cây nút sau khi parse HTML |
| Landmark | Vùng có nghĩa (`nav`, `main`…) |
| Attribute | Thông tin phụ (`data-sku`, `alt`…) |
| `data-testid` | Mã cho máy chấm |
| Live Server | Cách mở trang khuyến nghị |

### Checklist trước khi sang buổi sau / ra về

- [ ] Giải thích được Client–Server và DOM tree (vẽ lại ASCII không nhìn tài liệu)
- [ ] Tạo được HTML5 với `lang`, charset, viewport, title exact P01
- [ ] Có landmark: `header`, `nav`, `main`, `footer`
- [ ] Biết dùng `h1`/`h2`, `ul`/`li`, `a`, `img` (alt), `table`
- [ ] Biết gắn `data-testid` / `data-sku` theo quy ước CampusMart
- [ ] Phân biệt được bài minh họa 2 SP với **Phiếu 01** (repo `cse391-cm-01`)
- [ ] Đã làm / đang làm [Phiếu 01](./worksheets/phieu-01-html-ngu-nghia.md) (trên lớp mục 2 + về nhà mục 3)

---

## 15. Bài tập trên lớp & về nhà (chuỗi CampusMart UI)

Toàn bộ lớp đi một đề tài xuyên suốt: **CampusMart UI** (danh mục + sản phẩm).

| Phần | Làm gì | Chi tiết |
|------|--------|----------|
| **Trên lớp (~20')** | Skeleton ngữ nghĩa + 3 DM + bảng **4 SP** | [Phiếu 01 — mục 2](./worksheets/phieu-01-html-ngu-nghia.md) |
| **Về nhà (~25–40')** | Đủ **8 SP**, caption, tổng **41380000**, EXPECT + testid | [Phiếu 01 — mục 3](./worksheets/phieu-01-html-ngu-nghia.md) |

Nộp repo riêng `cse391-cm-01` + video OBS (camera mặt) theo box cuối phiếu.

### Câu hỏi thuyết trình gợi ý (30–60 giây trong video)

1. DOM tree là gì? (nói kèm hình cây `html → body → …`)
2. Vì sao cần `<nav>` thay vì `<div class="nav">`?
3. `data-testid` giúp gì khi chấm bài?

---

## 16. Cầu nối sang Buổi 2

Bạn đã có **bộ xương HTML** catalog CampusMart. Buổi 2 bổ sung:

- **Form** (đăng ký nhận tin / liên hệ) — vẫn ngữ nghĩa + `label`
- **Media** phong phú hơn
- **a11y cơ bản** (label, alt, landmark) — siết thói quen từ P01

SKU / giá / tổng kho CORE **không đổi**. Title sẽ đổi theo phiếu (`CampusMart — Form & Media (Buoi 2)`).

→ Tiếp: [Buổi 2](./02-buoi-02-html-form-media.md) · [Phiếu 02](./worksheets/phieu-02-form-media.md)

---

*CSE391 – Ebook Frontend CampusMart UI | Buổi 1/12*
