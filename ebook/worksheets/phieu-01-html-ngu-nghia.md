# Phiếu 01 — HTML ngữ nghĩa & Catalog CampusMart

| | |
|---|---|
| **Buổi** | 1 — đọc kèm [Buổi 01](../01-buoi-01-web-html-ngu-nghia.md) |
| **Thời lượng** | ≥ 45 phút (trên lớp ~20' + về nhà ~25–40') |
| **Repo** | `cse391-cm-01` |
| **Nhận từ phiếu trước** | — (bắt đầu chuỗi CampusMart UI) |
| **Mang sang Phiếu 02** | `index.html` ngữ nghĩa + bảng/list SP + `data-testid` |
| **Dataset** | [`CANONICAL-DATA.md`](./CANONICAL-DATA.md) — `product_count_core = 8`, `inventory_value_core = **41380000**` |

### Chuẩn đầu ra (CLO)

1. Tạo trang HTML5 đúng DOCTYPE, `lang="vi"`, UTF-8, viewport.  
2. Dùng landmark: `header`, `nav`, `main`, `footer`.  
3. Hiển thị **3** categories + bảng sản phẩm đúng SKU (4 trên lớp → **8** về nhà).  
4. Map `category_id` thành **tên danh mục chữ** (không để số thô trên UI).  
5. Gắn `data-testid` / `data-sku` + comment `CM_EXPECT` theo hợp đồng chấm.

---

## 1. Tóm tắt lý thuyết (đọc lại trước khi code)

Chi tiết đầy đủ: [Buổi 1](../01-buoi-01-web-html-ngu-nghia.md). Dưới đây là bảng so sánh để nhìn nhanh.

### 1.1. Client vs Server vs Frontend vs Backend

| Khái niệm | Là gì? | Ví dụ P01 |
|-----------|--------|-----------|
| **Client** | Máy/trình duyệt của bạn | Chrome mở CampusMart |
| **Server** | Nơi trả file HTML | Live Server / static host |
| **Frontend** | HTML/CSS/JS chạy trên Client | Trang catalog bạn đang dựng |
| **Backend** | Logic/CSDL trên Server (CSE485) | MiniShop PHP — **cùng 8 SP** |

```
Trình duyệt ──GET index.html──► Live Server ──HTML──► DOM tree ──► bạn thấy trang
```

### 1.2. Tag / Element / Attribute

| Thuật ngữ | Nhớ 5 giây | Ví dụ CampusMart |
|-----------|------------|------------------|
| Tag | Nhãn mở/đóng | `<h1>`, `</h1>` |
| Element | Cả khối | `<h1 data-testid="cm-brand">CampusMart</h1>` |
| Attribute | Thông tin phụ | `data-sku="KB-01"`, `alt="…"` |

### 1.3. Landmark — đừng thay bằng `div`

| Đúng (ngữ nghĩa) | Sai thường gặp | Vì sao |
|------------------|----------------|--------|
| `<header>` | `<div class="header">` | Landmark / a11y / thói quen chấm |
| `<nav data-testid="cm-nav">` | `<div id="menu">` | Máy + người biết đây là menu |
| `<main>` | Nhiều “main” giả bằng div | Mỗi trang một `main` |
| `<footer>` | Quên chân trang | Đủ khung trang |

### 1.4. Heading · List · Link · Img · Table

| Thành phần | Quy tắc vàng P01 |
|------------|------------------|
| Heading | Một `h1` = `CampusMart`; section dùng `h2` |
| List DM | `ul[data-testid=cm-category-list]` — đúng 3 tên chữ |
| Link | `href="#products"` khớp `id="products"` |
| Ảnh | Có `img` + `alt` không rỗng (placeholder OK) |
| Table | `thead`/`tbody`; số cột `th` = số `td` |

### 1.5. Marker chấm máy (không đổi)

| Marker | Value / quy ước |
|--------|-----------------|
| `<title>` | `CampusMart — Catalog (Buoi 1)` |
| `data-testid` | `cm-brand`, `cm-nav`, `cm-category-list`, `cm-product-table`, `cm-product-row`, `cm-inventory-total` |
| `data-sku` | Trên mỗi `<tr>` sản phẩm |
| `CM_EXPECT` | `product_count=8 category_count=3 inventory_value=41380000` |
| Caption | `Danh sach san pham CORE` |
| Tổng kho | Text chứa `Tong gia tri kho = 41380000` |

---

## 2. Bài trên lớp (~20 phút) — Skeleton + catalog tối thiểu

### Yêu cầu tổng

Tạo repo / thư mục `cse391-cm-01/` với ít nhất:

```
cse391-cm-01/
├── index.html
└── README.md          (1–3 dòng: họ tên, MSSV, mục tiêu phiếu)
```

### Scaffold từng bước

#### Bước 1 — Khung HTML5 + title exact

1. Tạo `index.html`.
2. Gõ DOCTYPE, `html lang="vi"`, charset, viewport.
3. `<title>` **đúng:** `CampusMart — Catalog (Buoi 1)`.
4. Mở Live Server — **dự đoán** tab hiện đúng title → xác nhận.

#### Bước 2 — Landmark + brand + nav

1. Trong `body`: `header` → `h1` text **`CampusMart`** + `data-testid="cm-brand"`.
2. `nav` + `data-testid="cm-nav"` + 3 link nhãn exact: `Trang chu`, `San pham`, `Gioi thieu`.
3. Thêm `main` và `footer` (footer có 1 dòng ghi CSE391 / Buoi 1).
4. F12 → Elements: xác nhận có `header` / `nav` / `main` / `footer` (không chỉ `div`).

#### Bước 3 — 3 danh mục

1. Trong `main`, `section id="categories"`.
2. `ul data-testid="cm-category-list"` với đúng 3 `<li>`: `Ban phim`, `Chuot`, `Man hinh`.

#### Bước 4 — Bảng 4 SP (trên lớp)

1. `section id="products"` + `table data-testid="cm-product-table"`.
2. Cột: `SKU | Ten | Danh muc | Gia | So luong`.
3. **4** dòng đầu; mỗi `<tr>` có `data-testid="cm-product-row"` và `data-sku`.

### Input bắt buộc (trên lớp) — 4 SP

| sku | name | Danh muc (chữ) | price | qty |
|-----|------|----------------|------:|----:|
| KB-01 | Keychron K2 | Ban phim | 1890000 | 3 |
| KB-02 | Akko 3087 | Ban phim | 1290000 | 5 |
| MS-01 | Logitech M331 | Chuot | 290000 | 10 |
| MS-02 | Razer Viper | Chuot | 990000 | 4 |

### Output kỳ vọng (trên lớp)

- Tab trình duyệt hiện title chứa `CampusMart — Catalog (Buoi 1)`.
- Thấy brand + nav + 3 danh mục + **4** dòng SP.
- Inspect: landmark `header`, `nav`, `main`, `footer`.

**Checkpoint giảng viên:** mở `index.html` → đủ 4 SKU + 3 category + `data-testid` cơ bản.

---

## 3. Bài về nhà (~25–40 phút) — Đủ CORE_8 + EXPECT

Tiếp tục **cùng repo** `cse391-cm-01`, không tạo project mới.

### Nhiệm vụ A — Đủ 8 sản phẩm

Thêm đúng 4 dòng còn lại (giữ nguyên 4 dòng trên lớp):

| sku | name | Danh muc | price | qty |
|-----|------|----------|------:|----:|
| KB-03 | Leopold FC660M | Ban phim | 2750000 | 2 |
| MS-03 | Xiaomi Silent | Chuot | 250000 | 8 |
| MN-01 | Dell 24 inch | Man hinh | 3200000 | 2 |
| MN-02 | LG UltraFine | Man hinh | 8500000 | 1 |

### Nhiệm vụ B — Caption + tổng kho (tĩnh)

1. Thêm `<caption>` exact: `Danh sach san pham CORE`.
2. Dưới bảng, đoạn có `data-testid="cm-inventory-total"` với text chứa:

   `Tong gia tri kho = 41380000`

   (Tính tay `price * qty` — xem CANONICAL-DATA §9.)
3. Thêm dòng phụ: `So san pham = 8`.

### Nhiệm vụ C — Ảnh + link nội bộ

1. Trong `nav`, `href` nhảy tới `#products` / `#categories` / `#about` (tạo `id` tương ứng trên trang).
2. Ít nhất **một** `<img>` (có thể cạnh KB-01) với `alt` không rỗng (ví dụ `Ban phim Keychron K2`). Placeholder URL được chấp nhận.

### Nhiệm vụ D — Marker chấm máy

Cuối `</body>` (trước đóng body) thêm:

```html
<!-- CM_EXPECT product_count=8 category_count=3 inventory_value=41380000 -->
```

---

## 4. Cách thử / tự kiểm trước khi nộp

Làm lần lượt — **dự đoán → chạy → quan sát**:

| # | Việc thử | Kỳ vọng |
|---|----------|---------|
| 1 | Mở bằng **Live Server** (không dùng `file://` để nộp) | URL dạng `http://127.0.0.1:…` hoặc `http://localhost:…` |
| 2 | Nhìn tab title | Exact `CampusMart — Catalog (Buoi 1)` |
| 3 | Đếm `<li>` trong `[data-testid=cm-category-list]` | **3** — Ban phim, Chuot, Man hinh |
| 4 | Đếm `[data-testid=cm-product-row]` | **8** |
| 5 | Elements → tìm từng `data-sku` | KB-01…MN-02 đủ 8 mã |
| 6 | Click nav “San pham” | Cuộn tới `#products` |
| 7 | Đọc `[data-testid=cm-inventory-total]` | Có chuỗi `41380000` |
| 8 | View Source / Elements | Có comment `CM_EXPECT` đủ 3 key |
| 9 | Đếm cột 1 hàng SP | = số cột header (5) |
| 10 | Có ít nhất 1 `img[alt]` | `alt` không rỗng |

**Tự chấm nhanh tổng kho** (nếu lệch → sửa số liệu, đừng “làm tròn”):

```
5670000+6450000+5500000+2900000+3960000+2000000+6400000+8500000 = 41380000
```

---

## 5. OUTPUT / EXPECT

| Key | Value bắt buộc |
|-----|----------------|
| `page_title` | `CampusMart — Catalog (Buoi 1)` |
| `brand` | text `CampusMart` + `data-testid="cm-brand"` |
| `category_count` | 3 |
| `product_count` | 8 |
| `skus` | KB-01,KB-02,KB-03,MS-01,MS-02,MS-03,MN-01,MN-02 |
| `inventory_value` | 41380000 (trong `[data-testid=cm-inventory-total]`) |
| `caption` | `Danh sach san pham CORE` |
| `landmarks` | header, nav, main, footer |
| `CM_EXPECT` comment | có đủ 3 key ở trên |

Mỗi row sản phẩm:

```html
<tr data-testid="cm-product-row" data-sku="KB-01">...</tr>
```

---

## 6. Lỗi thường gặp (nhìn nhanh trước khi quay video)

| Lỗi | Cách tránh |
|-----|------------|
| Sai title (thêm dấu ố / thiếu gạch) | Copy exact từ bảng EXPECT |
| Thiếu `data-sku` | Mỗi `<tr>` SP một `data-sku` |
| Danh mục hiện số `1/2/3` | In tên chữ |
| Tổng ≠ 41380000 | Cộng lại §4 / CANONICAL §9 |
| Quên `CM_EXPECT` | Dán comment trước `</body>` |
| Repo tên sai | Đúng `cse391-cm-01` |

---

## 7. Box nộp bài (OBS)

```
╔══════════════════════════════════════════════════════════════╗
║  NỘP PHIẾU 01                                                ║
╠══════════════════════════════════════════════════════════════╣
║  1. GitHub repo RIÊNG: cse391-cm-01 (index.html + README)    ║
║  2. Video OBS + camera mặt:                                  ║
║     - Hiện title tab + brand CampusMart                      ║
║     - Scroll bảng đủ 8 dòng + Tong = 41380000                ║
║     - Mở DevTools Elements chỉ landmark header/nav/main      ║
║  3. Thuyết trình 30–60s: DOM tree là gì? Vì sao cần <nav>?   ║
╚══════════════════════════════════════════════════════════════╝
```

> **Nhắc:** Ebook này là hướng dẫn + phiếu — **không** nộp lại nội dung trong repo ebook. Tự gõ HTML. Không tạo folder `solution/`.
