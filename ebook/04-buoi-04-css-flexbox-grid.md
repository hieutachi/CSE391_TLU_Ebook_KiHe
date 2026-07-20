# BUỔI 4: CSS Layout — Flexbox & Grid

> **Thời lượng gợi ý:** 3–4 giờ tự học (lý thuyết + thử máy + Phiếu 04)  
> **Tiên quyết:** Buổi 3 (`styles.css` + tokens + box model + bảng/form đã style)  
> **Kết quả đầu ra:** Header flex `space-between`, nav ngang, lưới `.cm-grid` 8 card `.cm-card` với `data-sku`; hiểu trục chính/phụ  
> **Phiếu học tập:** [Phiếu 04](./worksheets/phieu-04-flexbox-grid.md) — chuỗi **CampusMart UI**  
> **Dataset:** [CANONICAL-DATA §7–8](./worksheets/CANONICAL-DATA.md) — `.cm-grid`, `.cm-card`, `testid`, 8 SKU CORE

---

## 0. Gợi ý lịch trình giảng (cho GV)

| Phút | Nội dung | Gợi ý thao tác |
|------|----------|----------------|
| 0–10 | Ôn P03 + hỏi “Nav dọc, catalog chỉ bảng — thiếu gì?” | Chiếu trang P03 trên laptop |
| 10–25 | Flexbox: trục chính/phụ ASCII; `display:flex` | Vẽ trục trên bảng trước khi code |
| 25–40 | `.cm-header` `space-between`; `.cm-nav` flex + `gap` | Live-code header CampusMart |
| 40–55 | Grid: `.cm-grid` + `repeat(auto-fill, minmax(220px, 1fr))` | Giải thích `auto-fill` vs số cột cố định |
| 55–70 | Giải phẫu `.cm-card` + `data-sku` × 8 | SV copy 1 card → nhân 8 SKU |
| 70–85 | Event delegation preview (1 listener trên lưới) | Pseudocode — chưa bắt buộc JS P04 |
| 85–100 | **Thực hành Phiếu 04 mục 2** | Header + nav + 2 card mẫu |
| 100–120 | Giao về nhà P04 mục 3 (đủ 8 card) | Nhắc `data-testid="cm-product-table"` |

> Trên lớp **ưu tiên header flex + nav ngang + grid chạy với 2 card**. Hoàn thiện 8 card về nhà.

---

## 1. Mục tiêu buổi học

Sau Buổi 4, bạn có thể:

1. Bật Flexbox và điều khiển trục: `flex-direction`, `justify-content`, `align-items`, `gap`.
2. Vẽ và giải thích **trục chính (main axis)** vs **trục phụ (cross axis)**.
3. Dựng `.cm-header` flex `space-between` và `.cm-nav` flex ngang.
4. Phân biệt Flexbox (1 chiều) vs Grid (2 chiều) — chọn đúng công cụ.
5. Dựng `.cm-grid` bằng `grid-template-columns: repeat(auto-fill, minmax(220px, 1fr))`.
6. Tạo 8 `.cm-card` đủ SKU + `data-sku`; chuyển `data-testid="cm-product-table"` lên container lưới.
7. Nhìn trước **event delegation** trên `.cm-grid` (Buổi 7 sẽ code JS).

---

## 2. Nhắc lại — vì sao cần Flexbox/Grid ngay sau CSS cơ bản?

```
Buổi 3 CampusMart:
  ✓ Màu thương hiệu, bảng zebra, form focus
  ✗ Nav vẫn dọc (ul block)
  ✗ Catalog = 1 bảng dài — khó “quét” trên mobile

Buổi 4 trả lời:
  "Làm sao xếp logo | nav | nút CÙNG một hàng?"
  "Làm sao 8 sản phẩm tự xuống dòng đẹp?"
  → Flexbox (1 chiều) + Grid (lưới 2 chiều)
```

**Hình ảnh nhớ:**

```
Buổi 3 = sơn từng đồ (màu, padding, border)
Buổi 4 = kê kệ + xếp hàng:
           Flexbox = dàn hàng nav / header (1 chiều)
           Grid     = lưới card sản phẩm (2 chiều)
```

> Trước Flexbox/Grid, dev hay dùng `float` + `clearfix` — dễ vỡ layout. **P04 cấm `float` cho nav/card.**

---

## 3. Flexbox — layout một chiều

### 3.1. Mô hình trục (ASCII bắt buộc)

```
flex-direction: row  (mặc định — hàng ngang)

 main axis ───────────────────────────────►
┌──────────────────────────────────────────┐
│  ┌─────┐  ┌─────┐  ┌─────┐               │   ▲
│  │  A  │  │  B  │  │  C  │               │   │ cross axis
│  └─────┘  └─────┘  └─────┘               │   ▼
└──────────────────────────────────────────┘
   justify-content → căn theo TRỤC CHÍNH (ngang)
   align-items     → căn theo TRỤC PHỤ (dọc)


flex-direction: column  → HAI TRỤC ĐỔI CHỖ:

 main axis
    │
    ▼
┌─────────┐
│    A    │
│    B    │
│    C    │
└─────────┘
   justify-content → căn DỌC (trên/dưới)
   align-items     → căn NGANG (trái/phải)
```

**Câu hỏi nhanh (dự đoán trước khi mở DevTools):**

- Nav CampusMart dùng `row` hay `column`? → **`row`** (mục 4–5).
- Hero Buổi 5 (stack tiêu đề + nút) thường dùng `column` — nhắc trước.

### 3.2. Bật Flexbox

```css
.container {
  display: flex;   /* con trực tiếp trở thành flex item */
}
```

```
display: block (mặc định)     display: flex
┌─────┐                       ┌─────┐ ┌─────┐ ┌─────┐
│  A  │  A xếp dọc            │  A  │ │  B  │ │  C  │
├─────┤                       └─────┘ └─────┘ └─────┘
│  B  │
├─────┤
│  C  │
└─────┘
```

### 3.3. Thuộc tính container hay dùng

| Thuộc tính | Vai trò | CampusMart |
|------------|---------|------------|
| `flex-direction` | `row` / `column` | Nav: `row` |
| `justify-content` | Căn theo trục chính | Header: `space-between` |
| `align-items` | Căn theo trục phụ | Header: `center` |
| `gap` | Khoảng cách giữa item | Nav: `1rem`–`1.5rem` |
| `flex-wrap` | Xuống dòng khi thiếu chỗ | Nav mobile (P05) |

```css
.cm-nav ul {
  display: flex;
  flex-direction: row;
  gap: 1.5rem;
  justify-content: flex-end;
  align-items: center;
  list-style: none;
  margin: 0;
  padding: 0;
}
```

### 3.4. `justify-content` — so sánh bảng

| Giá trị | Hình dung (row) | Khi dùng |
|---------|-----------------|----------|
| `flex-start` | `[A][B][C]········` | Mặc định trái |
| `center` | `····[A][B][C]····` | Căn giữa nhóm |
| `flex-end` | `········[A][B][C]` | Dồn phải |
| `space-between` | `[A]······[B]······[C]` | **Header P04** |
| `space-around` | `·[A]··[B]··[C]·` | Ít dùng header |

### 3.5. Flex item — `flex: 1` (biết thêm)

```css
.cm-hero-text { flex: 1; }  /* chiếm phần còn lại — preview P05 */
```

Buổi 4 **chưa bắt buộc** — P05 hero có thể dùng.

---

## 4. Header CampusMart — flex `space-between`

### 4.1. Giải phẫu header (ASCII)

```
┌─────────────────────────────────────────────────────────────┐
│  .cm-header  (display: flex; justify-content: space-between)│
│                                                             │
│  ┌──────────────┐                    ┌──────────────────┐ │
│  │ Logo / h1    │                    │  nav .cm-nav     │ │
│  │ CampusMart   │                    │  Home | SP | DK  │ │
│  └──────────────┘                    └──────────────────┘ │
│       ▲                                        ▲            │
│   flex item 1                            flex item 2        │
└─────────────────────────────────────────────────────────────┘
```

### 4.2. CSS chuẩn P04

```css
.cm-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  background: var(--cm-primary);
  color: white;
  padding: 1rem;
  gap: 1rem;
}

.cm-header h1 {
  margin: 0;
  font-size: 1.25rem;
}
```

### 4.3. So sánh — trước / sau flex header

| | P03 (block) | P04 (flex) |
|---|-------------|------------|
| Logo + nav | Xếp dọc hoặc float hack | **Cùng hàng** |
| Căn hai đầu | Khó | `space-between` |
| Autograder | — | `css..cm-header` có `display:flex` + `space-between` |
| Float | Không | **Vẫn cấm** |

### 4.4. HTML gợi ý (giữ landmark P01–03)

```html
<header class="cm-header">
  <h1>CampusMart</h1>
  <nav class="cm-nav" aria-label="Menu chinh">
    <ul>
      <li><a href="#categories">Danh muc</a></li>
      <li><a href="#products">San pham</a></li>
      <li><a href="#subscribe">Dang ky</a></li>
    </ul>
  </nav>
</header>
```

> Giữ `aria-label` nav từ P02. **Không** xóa form / bảng cũ khi thêm lưới card.

---

## 5. Nav ngang — flex + gap

### 5.1. Hình ảnh nhớ

```
P03 nav = danh sách mua sắm viết dọc trên tờ giấy:
  • Danh muc
  • San pham
  • Dang ky

P04 nav = biển chỉ dẫn trên cùng một thanh:
  [ Danh muc ] [ San pham ] [ Dang ky ]
         ↑ gap thay margin từng li
```

### 5.2. CSS nav (P04 — luôn ngang; P05 thêm responsive)

```css
.cm-nav a {
  color: white;
  text-decoration: none;
}

.cm-nav a:hover {
  text-decoration: underline;
}

.cm-nav ul {
  display: flex;
  flex-direction: row;
  flex-wrap: wrap;
  gap: 1.5rem;
  list-style: none;
  margin: 0;
  padding: 0;
}
```

### 5.3. DevTools — badge `flex`

F12 → chọn `.cm-header` hoặc `ul` trong nav → badge **flex** trên Elements.  
Không thấy badge → chưa `display: flex`.

---

## 6. Grid — lưới `.cm-grid`

### 6.1. Flex vs Grid — so sánh bảng (bắt buộc)

| | Flexbox | CSS Grid |
|---|---------|----------|
| Chiều layout | **1 chiều** chính | **2 chiều** (hàng + cột) |
| CampusMart | Header, nav, hero stack | **Lưới 8 card** |
| Cột tự co giãn | Khó (phải `%` / wrap) | `minmax` + `auto-fill` |
| P04 dùng | `.cm-header`, `.cm-nav` | `.cm-grid` |

```
Chọn công cụ:
  "Xếp một hàng/cột"     → Flexbox
  "Lưới card đều nhau"   → Grid
```

### 6.2. Rule CANONICAL — exact

```css
.cm-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(220px, 1fr));
  gap: 1rem;
}
```

| Thành phần | Ý nghĩa |
|------------|---------|
| `repeat(auto-fill, …)` | Tự chèn cột vừa khung — **không** hard-code `4` ở P04 |
| `minmax(220px, 1fr)` | Mỗi cột ≥ 220px; phần thừa chia đều |
| `gap: 1rem` | Khe giữa card |

**Autograder P04:** `css..cm-grid` phải chứa `repeat(auto-fill, minmax(`.

### 6.3. ASCII — lưới co giãn

```
Viewport rộng (desktop sớm):
┌──────┐ ┌──────┐ ┌──────┐ ┌──────┐
│ KB-01│ │ KB-02│ │ KB-03│ │ MS-01│
└──────┘ └──────┘ └──────┘ └──────┘
┌──────┐ ┌──────┐ ┌──────┐ ┌──────┐
│ MS-02│ │ MS-03│ │ MN-01│ │ MN-02│
└──────┘ └──────┘ └──────┘ └──────┘

Viewport hẹp (mobile — trước P05 media query):
┌──────────────┐
│    KB-01     │
├──────────────┤
│    KB-02     │
└──────────────┘
  ... auto-fill tự 1 cột khi < ~220px
```

P05 sẽ **khóa** 1 → 2 → 4 cột bằng `@media`; P04 chỉ cần rule `minmax` chạy được.

### 6.4. Container + testid

```html
<section id="products">
  <h2>San pham noi bat</h2>
  <div class="cm-grid" data-testid="cm-product-table">
    <!-- 8 × .cm-card -->
  </div>
  <!-- Bảng .cm-table có thể giữ làm phụ lục / ẩn sau -->
</section>
```

| Quy tắc | Chi tiết |
|---------|----------|
| `data-testid="cm-product-table"` | **Duy nhất 1** — trên `.cm-grid`, không còn trên `<table>` |
| `card_count` | **8** `.cm-card` |
| CORE data | Giữ 8 SKU, `inventory_value=41380000` |

---

## 7. Giải phẫu `.cm-card` — anatomy

### 7.1. ASCII card

```
┌─────────────────────────────┐  ← article.cm-card[data-sku="KB-01"]
│  ┌─────────────────────┐    │
│  │      figure/img     │    │  ← ảnh SP (P02)
│  └─────────────────────┘    │
│  Keychron K2                │  ← .cm-card-title (hoặc h3)
│  Ban phim                   │  ← .cm-card-category (CHỮ, không "1")
│  1 890 000 d                │  ← .cm-card-price
└─────────────────────────────┘
```

### 7.2. HTML mẫu 1 card (copy × 8 SKU)

```html
<article class="cm-card" data-sku="KB-01">
  <figure>
    <img src="https://placehold.co/220x140" alt="Ban phim co Keychron K2" width="220" height="140" />
    <figcaption>KB-01</figcaption>
  </figure>
  <h3 class="cm-card-title">Keychron K2</h3>
  <p class="cm-card-category">Ban phim</p>
  <p class="cm-card-price">1 890 000 d</p>
</article>
```

### 7.3. Bảng 8 SKU — dữ liệu card (CANONICAL §2)

| data-sku | title | category (text) | price (hiển thị) |
|----------|-------|-----------------|------------------|
| KB-01 | Keychron K2 | Ban phim | 1 890 000 d |
| KB-02 | Akko 3087 | Ban phim | 1 290 000 d |
| KB-03 | Leopold FC660M | Ban phim | 2 750 000 d |
| MS-01 | Logitech M331 | Chuot | 290 000 d |
| MS-02 | Razer Viper | Chuot | 990 000 d |
| MS-03 | Xiaomi Silent | Chuot | 250 000 d |
| MN-01 | Dell 24 inch | Man hinh | 3 200 000 d |
| MN-02 | LG UltraFine | Man hinh | 8 500 000 d |

**Lỗi hay gặp:** `.cm-card-category` ghi `1` / `2` / `3` → autograder `card.category` **fail** (cần **tên** danh mục).

### 7.4. CSS card gợi ý

```css
.cm-card {
  background: white;
  border: 1px solid #e2e8f0;
  border-radius: 8px;
  padding: 0.75rem;
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
}

.cm-card figure {
  margin: 0;
}

.cm-card img {
  width: 100%;
  height: auto;
  display: block;
  border-radius: 4px;
}

.cm-card-title {
  margin: 0;
  font-size: 1rem;
}

.cm-card-category {
  margin: 0;
  color: #64748b;
  font-size: 0.875rem;
}

.cm-card-price {
  margin: 0;
  font-weight: 600;
  color: var(--cm-primary);
}

.cm-card:hover {
  box-shadow: 0 4px 12px rgba(15, 118, 110, 0.15);
}
```

Card dùng **flex column** bên trong — ví dụ Flex lồng Grid (container grid, item flex).

---

## 8. Bảng vs lưới card — migration P04

### 8.1. Chiến lược môn học

```
P01–P03: <table class="cm-table">  → nguồn dữ liệu, autograder row count
P04+:    <div class="cm-grid">     → trải nghiệm catalog chính
         testid chuyển lên .cm-grid
         Bảng có thể giữ (ẩn) hoặc bỏ sau khi chắc card đủ 8
```

### 8.2. So sánh UX

| | Bảng `.cm-table` | Lưới `.cm-grid` |
|---|------------------|-----------------|
| Mobile | Cuộn ngang khó đọc | Card stack tự nhiên |
| Quét mắt | Dòng dữ liệu | Khối sản phẩm |
| JS Buổi 7 | Parse `tr` | Query `.cm-card[data-sku]` |
| P04 bắt buộc | Giữ data CORE | **8 card** + testid |

---

## 9. Event delegation — preview (Buổi 7)

Chưa cần JS ở P04 — **đọc để hiểu vì sao gắn listener lên `.cm-grid`**.

### 9.1. Vấn đề nếu gắn từng card

```
8 card × 1 listener click = 8 handler
Thêm card (P11) = phải gắn lại từng cái
→ Dễ sót, khó bảo trì
```

### 9.2. Event delegation — hình ảnh nhớ

```
User click vào .cm-card-price
        │
        ▼ (bubble)
┌───────────────────────────────────┐
│  .cm-grid  ← 1 listener duy nhất  │
│    ┌─────────┐                      │
│    │ .cm-card│ ← event.target có   │
│    └─────────┘   thể là con bên trong│
└───────────────────────────────────┘

Handler:
  1. Kiểm tra e.target gần nhất .cm-card
  2. Đọc card.dataset.sku
  3. (Buổi 7) highlight / filter / log
```

### 9.3. Pseudocode (chưa nộp P04)

```javascript
// Buổi 7 — minh họa
document.querySelector('.cm-grid').addEventListener('click', (e) => {
  const card = e.target.closest('.cm-card');
  if (!card) return;
  console.log('Clicked SKU:', card.dataset.sku);
});
```

| Thuật ngữ | Nghĩa |
|-----------|-------|
| Bubble | Sự kiện nổi từ con → cha |
| `closest('.cm-card')` | Tìm card bọc click |
| Delegation | Cha xử lý thay cho nhiều con |

---

## 10. `<title>` và marker P04

```html
<title>CampusMart — Layout (Buoi 4)</title>
```

```html
<!-- CM_EXPECT product_count=8 card_count=8 inventory_value=41380000 layout=flex-grid -->
```

Giữ mọi `data-testid` từ P02 (`cm-subscribe-form`, …).

---

## 11. Thử ngay trên máy (dự đoán → chạy → DevTools)

### Thử A — Demo trục flex

Tạo `demo-flex.html`:

```html
<style>
  .row { display: flex; gap: 8px; margin-bottom: 1rem; padding: 8px; background: #f1f5f9; }
  .box { background: #0f766e; color: white; padding: 12px; }
</style>
<div class="row" style="justify-content: flex-start"><div class="box">A</div><div class="box">B</div></div>
<div class="row" style="justify-content: space-between"><div class="box">A</div><div class="box">B</div></div>
<div class="row" style="flex-direction: column; height: 120px; justify-content: center">
  <div class="box">A</div><div class="box">B</div>
</div>
```

| Dự đoán | Quan sát |
|---------|----------|
| Hàng 2: A trái, B phải? | `space-between` |
| Hàng 3: stack dọc giữa? | `column` + `justify-content: center` |

### Thử B — Grid minmax

```html
<style>
  .g { display: grid; grid-template-columns: repeat(auto-fill, minmax(220px, 1fr)); gap: 8px; }
  .c { background: #ccfbf1; padding: 24px; text-align: center; }
</style>
<div class="g">
  <div class="c">1</div><div class="c">2</div><div class="c">3</div><div class="c">4</div>
</div>
```

Thu hẹp cửa sổ → số cột giảm; mỗi ô ≥ 220px.

### Thử C — Header CampusMart

1. Copy P03 → `cse391-cm-04`.  
2. Thêm flex header + nav.  
3. Inspect `.cm-header` → Computed: `display: flex`, `justify-content: space-between`.

### Thử D — 8 card + testid

| Bước | Kiểm tra |
|------|----------|
| View Source | Đúng 8 `data-sku` từ KB-01 … MN-02 |
| Elements | Một `data-testid="cm-product-table"` trên `.cm-grid` |
| Search `float` | Không có trong `styles.css` |

### Thử E — Badge flex/grid

F12 → chọn `.cm-grid` → badge **grid**; `.cm-header` → **flex**.

---

## 12. Câu hỏi tự kiểm tra

1. Trục chính vs trục phụ — đổi gì khi `flex-direction: column`?
2. Vì sao header CampusMart dùng `space-between`?
3. Flexbox vs Grid — case nào dùng cái nào trên trang P04?
4. Giải thích `repeat(auto-fill, minmax(220px, 1fr))` bằng lời.
5. `data-testid="cm-product-table"` đặt ở đâu sau P04? Còn trên `<table>` không?
6. `.cm-card-category` ghi `Ban phim` hay `1`? Vì sao?
7. Event delegation giải quyết vấn đề gì khi có 8+ card?
8. Vì sao P04 cấm `float` cho nav?

---

## 13. Gợi ý đáp án

1. Main/cross hoán đổi; justify căn dọc, align căn ngang.  
2. Logo trái, nav phải — một rule, không margin thủ công.  
3. Flex: header, nav; Grid: `.cm-grid`.  
4. Tự fill cột ≥220px, chia đều phần rộng thừa.  
5. Trên `.cm-grid` duy nhất; không còn trên table.  
6. **Ban phim** — autograder đọc text category.  
7. Một listener trên cha; thêm card không gắn lại handler.  
8. Float dễ vỡ, khó a11y; flex là chuẩn layout hiện đại.

---

## 14. Lỗi thường gặp (chi tiết)

| # | Triệu chứng | Nguyên nhân | Cách xử lý |
|---|-------------|-------------|------------|
| 1 | Nav vẫn dọc | Quên `display:flex` trên `ul` | `.cm-nav ul { display:flex }` |
| 2 | Logo và nav dính nhau | Thiếu `space-between` | Header flex + justify |
| 3 | Grid 1 cột dù desktop rộng | Sai `minmax` / thiếu width cha | Kiểm tra `.cm-grid` rule exact |
| 4 | Card lệch chiều cao | Không flex column trong card | `.cm-card { display:flex; flex-direction:column }` |
| 5 | testid fail | Còn trên table hoặc trùng 2 chỗ | Chỉ trên `.cm-grid` |
| 6 | category fail | Ghi số `category_id` | Đổi tên DM: Ban phim, Chuot, Man hinh |
| 7 | Thiếu card | Copy chưa đủ 8 SKU | Đối chiếu bảng mục 7.3 |
| 8 | Layout vỡ mobile | Dùng width cố định px lớn | `minmax`, `%`, P05 query |
| 9 | Float + flex lẫn | Copy template cũ | Xóa `float`, dùng flex |
| 10 | Ảnh tràn card | Thiếu `img { width:100% }` | Thêm rule trên |

---

## 15. Checklist trước Buổi 5

- [ ] `<title>` exact P04: `CampusMart — Layout (Buoi 4)`
- [ ] `.cm-header` flex + `space-between`
- [ ] `.cm-nav` flex ngang + `gap`
- [ ] `.cm-grid` = `repeat(auto-fill, minmax(220px, 1fr))`
- [ ] 8 `.cm-card` + `data-sku` + category **chữ**
- [ ] `data-testid="cm-product-table"` trên `.cm-grid` (1 lần)
- [ ] Giữ form, tokens, CORE 41380000
- [ ] Không `float` layout
- [ ] Xong [Phiếu 04](./worksheets/phieu-04-flexbox-grid.md)

---

## 16. Bài tập (chuỗi CampusMart UI)

| Phần | Làm gì | Chi tiết |
|------|--------|----------|
| **Trên lớp (~20')** | Header flex + nav + 2 card mẫu | [Phiếu 04 — mục 2](./worksheets/phieu-04-flexbox-grid.md) |
| **Về nhà (~25–40')** | Đủ 8 card + testid + CM_EXPECT | [Phiếu 04 — mục 3](./worksheets/phieu-04-flexbox-grid.md) |

Repo: **`cse391-cm-04`** + video OBS.

---

## 17. Cầu nối sang Buổi 5 (Responsive)

```
Buổi 4: Layout flex/grid — desktop “một kích cỡ”
Buổi 5: Mobile-first + @media 640px / 1024px
         Nav dọc → ngang; grid 1 → 2 → 4 cột
         Hero + dashboard tĩnh hoàn chỉnh
         Bảng trong .table-wrap cuộn ngang
```

Breakpoint khóa: **640px** (tablet), **1024px** (desktop).  
Hero heading exact: `Do choi cong nghe cho sinh vien` — chuẩn bị HTML ở P05.

→ [Buổi 5](./05-buoi-05-responsive-components.md) · [Phiếu 05](./worksheets/phieu-05-responsive-campusmart.md)

---

*CSE391 – Ebook Frontend CampusMart UI | Buổi 4/12*
