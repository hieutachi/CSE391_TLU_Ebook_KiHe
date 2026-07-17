# BUỔI 1: Web & HTML ngữ nghĩa

> **Thời lượng gợi ý:** 3–4 giờ tự học  
> **Tiên quyết:** Biết dùng trình duyệt, tạo/lưu file văn bản, tài khoản GitHub  
> **Kết quả đầu ra:** Hiểu Client–Server & DOM tree; viết được trang HTML5 ngữ nghĩa với heading, list, link, image, table  
> **Phiếu học tập liên quan:** [Phiếu 01](./worksheets/phieu-01-html-ngu-nghia.md) — chuỗi **CampusMart UI**

---

## 1. Mục tiêu buổi học

Sau Buổi 1, bạn có thể:

1. Giải thích Frontend nằm ở đâu trong một ứng dụng Web.
2. Vẽ và giải thích mô hình Client–Server + cây DOM bằng sơ đồ ASCII.
3. Tạo file HTML5 đúng khung (`DOCTYPE`, `html`, `head`, `body`).
4. Dùng thẻ ngữ nghĩa: `header`, `nav`, `main`, `section`, `article`, `footer`.
5. Dựng bảng / danh sách sản phẩm tĩnh cho CampusMart (heading, list, link, image, table).

---

## 2. Vì sao cần HTML ngữ nghĩa?

Khi bạn mở trang bán hàng campus, trình duyệt nhận về **HTML** (cấu trúc), **CSS** (trang trí), **JavaScript** (hành vi). HTML không chỉ “cho đẹp mắt” — nó mô tả **ý nghĩa** từng vùng: đâu là menu, đâu là nội dung chính, đâu là chân trang.

> Công cụ tìm kiếm, trình đọc màn hình (screen reader), và **Autograder** đều “đọc” cấu trúc.  
> Thẻ `<div>` thuần túy không nói được gì; thẻ `<nav>` / `<main>` thì có.

CampusMart sẽ đi từ **HTML tĩnh** (Buổi 1–2) → **CSS layout** → **JS tương tác** → **React SPA**. Buổi 1 dựng **bộ xương** đúng nghĩa — mọi buổi sau gắn lên xương này.

---

## 3. Web hoạt động thế nào? (Client – Server)

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

### Frontend vs Backend (nhắc nhanh)

| | Frontend (môn này) | Backend (CSE485) |
|---|--------------------|------------------|
| Chạy ở đâu? | Trình duyệt (Client) | Máy chủ (Server) |
| Ngôn ngữ | HTML, CSS, JS, React | PHP, Laravel, … |
| Nhiệm vụ | Hiển thị, tương tác | Logic, CSDL, API |
| Dataset CampusMart / MiniShop | Cùng 8 SP + 3 DM | Cùng số liệu, lưu DB |

**Câu hỏi tự kiểm tra:** Nếu chỉ mở file `index.html` bằng double-click (`file://`) thì khác gì mở qua Live Server (`http://localhost:…`)?  
→ Một số API trình duyệt (module ES, fetch…) bị hạn chế trên `file://`. Học kỳ này ưu tiên **Live Server** hoặc mở qua server tĩnh.

---

## 4. Trình duyệt dựng trang: DOM tree

Khi nhận HTML, trình duyệt phân tích (parse) thành **cây DOM** (Document Object Model) — mỗi thẻ là một nút.

```
document
└── html
    ├── head
    │   ├── meta charset
    │   ├── title
    │   └── link (css) / script (sau này)
    └── body
        ├── header
        │   ├── h1 (CampusMart)
        │   └── nav
        │       ├── a (Trang chu)
        │       ├── a (San pham)
        │       └── a (Gioi thieu)
        ├── main
        │   ├── section#categories
        │   │   └── ul > li × 3
        │   └── section#products
        │       └── table / article cards
        └── footer
```

> Từ Buổi 6, JavaScript sẽ `querySelector` trên cây này. Cấu trúc sạch → code JS đỡ rối.

---

## 5. Khung HTML5 tối thiểu

```html
<!DOCTYPE html>
<html lang="vi">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>CampusMart — Catalog (Buoi 1)</title>
</head>
<body>
  <!-- Nội dung nhìn thấy nằm ở đây -->
  <h1>CampusMart</h1>
</body>
</html>
```

| Thành phần | Vì sao bắt buộc |
|------------|-----------------|
| `<!DOCTYPE html>` | Báo trình duyệt dùng chế độ chuẩn HTML5 |
| `lang="vi"` | Gợi ý ngôn ngữ (a11y + SEO) |
| `charset="UTF-8"` | Tiếng Việt không lỗi font |
| `viewport` | Nền tảng cho responsive (Buổi 5) |
| `<title>` | Tab trình duyệt + EXPECT `page_title` |

---

## 6. HTML ngữ nghĩa — xương trang CampusMart

### 6.1. Landmark chính

```html
<body>
  <header data-testid="cm-brand-wrap">
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
    <!-- nội dung chính -->
  </main>

  <footer>
    <p>CSE391 — CampusMart UI — Buoi 1</p>
  </footer>
</body>
```

```
┌──────────────────────────────────────────┐
│  header  │  h1 CampusMart  +  nav        │
├──────────────────────────────────────────┤
│  main    │  categories + products        │
├──────────────────────────────────────────┤
│  footer  │  bản quyền / mã môn           │
└──────────────────────────────────────────┘
```

### 6.2. Heading phân cấp

- Một trang **một** `<h1>` (thương hiệu / tiêu đề chính).
- `<h2>` cho từng section (“Danh muc”, “San pham”).
- Không nhảy từ `h1` → `h3` bỏ `h2`.

### 6.3. List — danh mục

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

### 6.4. Link & Image

```html
<!-- Link nội bộ (nhảy section) -->
<a href="#products">Xem san pham</a>

<!-- Ảnh: luôn có alt mô tả -->
<img
  src="assets/kb-01.jpg"
  alt="Ban phim Keychron K2"
  width="120"
  height="80"
/>
```

> P01: nếu chưa có ảnh thật, dùng placeholder (ví dụ https://placehold.co/120x80) **hoặc** bỏ `src` ảnh nhưng vẫn giữ cột/ô dành sẵn — phiếu sẽ ghi rõ.

### 6.5. Table — catalog sản phẩm

Bảng phù hợp dữ liệu dạng lưới (SKU, tên, giá, số lượng):

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
      <!-- … các dòng khác theo CANONICAL-DATA -->
    </tbody>
  </table>
</section>
```

**Vì sao `data-sku` / `data-testid`?**  
Autograder sau này đếm dòng và kiểm tra SKU mà không phụ thuộc CSS class đổi tên lung tung.

---

## 7. Bài thực hành minh họa trong buổi (không thay phiếu)

Tạo thư mục `cse391-buoi1/` và gõ lại (rút gọn — chỉ 2 sản phẩm để luyện tay):

```html
<!DOCTYPE html>
<html lang="vi">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
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

Mở bằng Live Server → Inspect (F12) → tab **Elements**: nhìn cây DOM khớp sơ đồ mục 4.

---

## 8. Lỗi thường gặp

| Triệu chứng | Nguyên nhân thường gặp | Cách xử lý |
|-------------|------------------------|------------|
| Tiếng Việt lỗi font | Thiếu `charset=UTF-8` | Thêm meta charset đầu `<head>` |
| Click link không nhảy | Sai `id` / `href` | `href="#products"` khớp `id="products"` |
| Bảng “vỡ” cột | Thiếu `<th>` / số `<td>` lệch | Đếm cột thead = tbody |
| Autograder không thấy SP | Thiếu `data-sku` / testid | Bám CANONICAL-DATA §4 |
| Chỉ dùng `<div>` | Quên ngữ nghĩa | Đổi sang `header`/`nav`/`main`/`footer` |

---

## 9. Checklist trước khi sang buổi sau

- [ ] Giải thích được Client–Server và DOM tree (ASCII)
- [ ] Tạo được HTML5 với `lang`, charset, viewport, title
- [ ] Có landmark: `header`, `nav`, `main`, `footer`
- [ ] Biết dùng `h1`/`h2`, `ul`/`li`, `a`, `img` (alt), `table`
- [ ] Biết gắn `data-testid` / `data-sku` theo quy ước CampusMart
- [ ] Đã làm xong [Phiếu 01](./worksheets/phieu-01-html-ngu-nghia.md) (trên lớp + về nhà)

---

## 10. Bài tập trên lớp & về nhà (chuỗi CampusMart UI)

Toàn bộ lớp đi một đề tài xuyên suốt: **CampusMart UI** (danh mục + sản phẩm).

| Phần | Làm gì | Chi tiết |
|------|--------|----------|
| **Trên lớp (~20')** | Skeleton ngữ nghĩa + 3 DM + bảng 4 SP | [Phiếu 01 — mục 2](./worksheets/phieu-01-html-ngu-nghia.md) |
| **Về nhà (~25–40')** | Đủ 8 SP, tên danh mục chữ, EXPECT + testid | [Phiếu 01 — mục 3](./worksheets/phieu-01-html-ngu-nghia.md) |

Nộp repo riêng `cse391-cm-01` + video OBS (camera mặt) theo box cuối phiếu.

---

## 11. Cầu nối sang Buổi 2

Bạn đã có **bộ xương HTML** catalog. Buổi 2 bổ sung **Form** (đăng ký nhận tin / liên hệ), **Media**, và **a11y cơ bản** (label, alt, landmark) — vẫn cùng CampusMart, không đổi SKU.

→ Tiếp: [Buổi 2](./02-buoi-02-html-form-media.md) · [Phiếu 02](./worksheets/phieu-02-form-media.md)

---

*CSE391 – Ebook Frontend CampusMart UI | Buổi 1/12*
