# Phiếu 01 — HTML ngữ nghĩa & Catalog CampusMart

| | |
|---|---|
| **Buổi** | 1 |
| **Thời lượng** | ≥ 45 phút (trên lớp ~20' + về nhà ~25–40') |
| **Repo** | `cse391-cm-01` |
| **Nhận từ phiếu trước** | — (bắt đầu chuỗi) |
| **Mang sang Phiếu 02** | `index.html` ngữ nghĩa + bảng/list SP + `data-testid` |
| **Dataset** | [`CANONICAL-DATA.md`](./CANONICAL-DATA.md) — `product_count_core = 8`, `inventory_value_core = 41380000` |

### Chuẩn đầu ra (CLO)

1. Tạo trang HTML5 đúng DOCTYPE, `lang="vi"`, UTF-8, viewport.  
2. Dùng landmark: `header`, `nav`, `main`, `footer`.  
3. Hiển thị **3** categories + bảng sản phẩm đúng SKU (4 trên lớp → **8** về nhà).  
4. Map `category_id` thành **tên danh mục chữ** (không để số thô trên UI).  
5. Gắn `data-testid` / `data-sku` + comment `CM_EXPECT` theo hợp đồng chấm.

---

## 1. Tóm tắt lý thuyết (đọc lại trước khi code)

- Frontend chạy trên Client; Server gửi HTML/CSS/JS.
- DOM = cây nút từ HTML; ngữ nghĩa giúp người + máy đọc đúng vùng.
- Landmark: `header` / `nav` / `main` / `footer`.
- Heading phân cấp; `ul`/`li`; `a` + `href`; `img` + `alt`; `table` + `th`/`td`.
- Marker chấm: `data-testid`, `data-sku`, `<!-- CM_EXPECT ... -->`.

Chi tiết: [Buổi 1](../01-buoi-01-web-html-ngu-nghia.md).

---

## 2. Bài trên lớp (~20 phút) — Skeleton + catalog tối thiểu

### Yêu cầu

Tạo repo / thư mục `cse391-cm-01/` với ít nhất:

```
cse391-cm-01/
├── index.html
└── README.md          (1–3 dòng: họ tên, MSSV, mục tiêu phiếu)
```

1. `index.html` — trang CampusMart đúng title: **`CampusMart — Catalog (Buoi 1)`**.
2. Có `header` + `h1` text **`CampusMart`** với `data-testid="cm-brand"`.
3. Có `nav` với `data-testid="cm-nav"` và 3 link nhãn exact: `Trang chu`, `San pham`, `Gioi thieu`.
4. Section danh mục: `data-testid="cm-category-list"` — đúng **3** tên: `Ban phim`, `Chuot`, `Man hinh`.
5. Bảng sản phẩm: `data-testid="cm-product-table"` — **4** dòng đầu (SKU dưới đây), mỗi `<tr>` có `data-testid="cm-product-row"` và `data-sku`.

### Input bắt buộc (trên lớp) — 4 SP

| sku | name | Danh muc (chữ) | price | qty |
|-----|------|----------------|------:|----:|
| KB-01 | Keychron K2 | Ban phim | 1890000 | 3 |
| KB-02 | Akko 3087 | Ban phim | 1290000 | 5 |
| MS-01 | Logitech M331 | Chuot | 290000 | 10 |
| MS-02 | Razer Viper | Chuot | 990000 | 4 |

Cột bảng tối thiểu: `SKU | Ten | Danh muc | Gia | So luong`.

### Output kỳ vọng (trên lớp)

- Tab trình duyệt hiện title chứa `CampusMart — Catalog (Buoi 1)`.
- Thấy brand + nav + 3 danh mục + **4** dòng SP.
- Inspect: có landmark `header`, `nav`, `main`, `footer`.

**Checkpoint giảng viên:** mở `index.html` → đủ 4 SKU + 3 category + `data-testid` cơ bản.

---

## 3. Bài về nhà (~25–40 phút) — Đủ CORE_8 + EXPECT

Tiếp tục **cùng repo**, không tạo project mới.

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
2. Dưới bảng, thêm đoạn có `data-testid="cm-inventory-total"` với text chứa tổng thành tiền:

   `Tong gia tri kho = 41380000`

   (Có thể tính tay: `price * qty` từng dòng — xem CANONICAL-DATA §9.)
3. Thêm dòng phụ: `So san pham = 8`.

### Nhiệm vụ C — Ảnh (tối thiểu) + link nội bộ

1. Trong `nav`, `href` nhảy tới `#products` / `#categories` / `#about` (tạo `id` tương ứng).
2. Ít nhất **một** `<img>` trong trang (có thể cạnh KB-01) với `alt` không rỗng (ví dụ `Ban phim Keychron K2`). Placeholder URL được chấp nhận.

### Nhiệm vụ D — Marker chấm máy

Cuối `</body>` (trước đóng body) thêm:

```html
<!-- CM_EXPECT product_count=8 category_count=3 inventory_value=41380000 -->
```

---

## 4. OUTPUT / EXPECT

| Key | Value bắt buộc |
|-----|----------------|
| `page_title` | `CampusMart — Catalog (Buoi 1)` |
| `brand` | text `CampusMart` + `data-testid="cm-brand"` |
| `category_count` | 3 |
| `product_count` | 8 |
| `skus` | KB-01,KB-02,KB-03,MS-01,MS-02,MS-03,MN-01,MN-02 |
| `inventory_value` | 41380000 (trong `#`/`[data-testid=cm-inventory-total]`) |
| `caption` | `Danh sach san pham CORE` |
| `landmarks` | header, nav, main, footer |
| `CM_EXPECT` comment | có đủ 3 key ở trên |

Mỗi row sản phẩm:

```html
<tr data-testid="cm-product-row" data-sku="KB-01">...</tr>
```

---

## 5. Box nộp bài

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

> **Nhắc:** Ebook này là hướng dẫn + phiếu — **không** nộp lại nội dung trong repo ebook. Tự gõ HTML.
