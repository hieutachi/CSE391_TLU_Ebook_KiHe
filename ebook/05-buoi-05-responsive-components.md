# BUỔI 5: Responsive & UI Components tĩnh — Dashboard CampusMart

> **Thời lượng gợi ý:** 3–4 giờ tự học (lý thuyết + thử máy + Phiếu 05)  
> **Tiên quyết:** Buổi 4 (header flex + `.cm-grid` + 8 `.cm-card`)  
> **Kết quả đầu ra:** Dashboard tĩnh hoàn chỉnh trên 3 khung nhìn; mobile-first; hero; nav dọc→ngang; bảng cuộn ngang  
> **Phiếu học tập:** [Phiếu 05](./worksheets/phieu-05-responsive-campusmart.md) — chuỗi **CampusMart UI**  
> **Dataset:** [CANONICAL-DATA §8.1–8.2](./worksheets/CANONICAL-DATA.md) — breakpoint **640/1024**, hero exact, `max-width: 1120px`

---

## 0. Gợi ý lịch trình giảng (cho GV)

| Phút | Nội dung | Gợi ý thao tác |
|------|----------|----------------|
| 0–10 | Ôn P04 + mở DevTools 375px — “vỡ ở đâu?” | Chiếu nav ngang chật mobile |
| 10–25 | Mobile-first + meta viewport; ASCII 3 khung | Vẽ 375 / 700 / 1200 trên bảng |
| 25–40 | `@media (min-width: 640px)` nav + grid 2 cột | SV thêm query đầu tiên |
| 40–55 | `@media (min-width: 1024px)` grid 4 cột + `main` 1120px | So sánh desktop-first (sai) |
| 55–70 | Hero `.cm-hero` + `.cm-btn` exact CANONICAL | Live-code section hero |
| 70–85 | `.table-wrap` + `overflow-x: auto` | Demo bảng không tràn body |
| 85–100 | **Thực hành Phiếu 05 mục 2** | Checkpoint 375px |
| 100–120 | Giao về nhà — quay video 3 khung | 375 / 700 / 1200 |

> Thuật ngữ môn: **dashboard tĩnh hoàn chỉnh** (không gọi “landing”). Đây là nền HTML/CSS trước JS Buổi 6.

---

## 1. Mục tiêu buổi học

Sau Buổi 5, bạn có thể:

1. Giải thích **mobile-first** và vai trò `<meta name="viewport">`.
2. Viết 2 media query đúng breakpoint: **`min-width: 640px`** và **`min-width: 1024px`**.
3. Điều khiển `.cm-grid`: **1 → 2 → 4** cột theo khung nhìn.
4. Làm nav **dọc trên mobile**, **ngang từ tablet**.
5. Dựng hero với heading + nút **exact** CANONICAL.
6. Bọc bảng trong `.table-wrap` — cuộn ngang, không vỡ layout.
7. Giới hạn nội dung `main` **`max-width: 1120px`** căn giữa desktop.
8. Hoàn thiện **dashboard tĩnh** CampusMart (header, hero, grid, bảng, form) — sẵn sàng cho JS P06.

---

## 2. Nhắc lại — dashboard sau Buổi 4

```
Buổi 4:
  ✓ Header flex, nav ngang, lưới 8 card
  ✗ Mobile: nav chật, card có thể quá nhiều cột
  ✗ Chưa hero, chưa “khung” desktop
  ✗ Bảng inventory vẫn tràn ngang màn nhỏ

Buổi 5 = “cắt layout” theo 3 khung:
  📱 ~375px  mobile
  📱 ~700px  tablet (≥640)
  🖥 ~1200px desktop (≥1024)
```

**Hình ảnh nhớ:**

```
Buổi 4 = dựng nội thất một cửa hàng (kệ + biển)
Buổi 5 = cửa hàng co giãn theo 3 kích cỡ phòng:
           phòng nhỏ (1 cột) → phòng vừa (2 cột) → phòng lớn (4 cột)
```

---

## 3. Mobile-first & viewport

### 3.1. Meta viewport (bắt buộc — giữ từ P01)

```html
<meta name="viewport" content="width=device-width, initial-scale=1.0" />
```

| Không viewport | Có viewport |
|----------------|-------------|
| Mobile zoom trang “desktop thu nhỏ” | 1 CSS px ≈ 1 viewport logic |
| Media query vô nghĩa | `@media` khớp thiết bị thật |

### 3.2. Mobile-first vs desktop-first — so sánh bảng

| | Mobile-first ★ môn | Desktop-first (tránh) |
|---|---------------------|------------------------|
| CSS gốc | Style **màn nhỏ** trước | Style desktop, `@media max-width` sửa |
| Query | `min-width: 640px` **thêm** | `max-width: 639px` **sửa** |
| Tư duy | Progressive enhancement | Graceful degradation |
| P05 | **Bắt buộc** | Không chấm |

```
Mobile-first (ASCII):

  BASE (0px+)     @640px+           @1024px+
  ┌─────┐         ┌────┬────┐       ┌──┬──┬──┬──┐
  │  1  │   →     │ 1  │ 2  │   →   │1 │2 │3 │4 │
  └─────┘         └────┴────┘       └──┴──┴──┴──┘
  nav dọc         nav ngang         main max 1120
  grid 1fr        grid 2 cột        grid 4 cột
```

### 3.3. Breakpoint CANONICAL — exact

| Token | Value | Ý nghĩa |
|-------|-------|---------|
| Mobile base | `< 640px` | CSS mặc định, không query |
| Tablet | `min-width: 640px` | Nav ngang, grid 2 cột |
| Desktop | `min-width: 1024px` | Grid 4 cột, main 1120px |

**Không** tự đổi sang `768px` / `1280px` — autograder khóa **640** và **1024**.

---

## 4. Ba khung thử — ASCII bắt buộc (DevTools)

```
┌─────────────────────────────────────────────────────────────────────────┐
│  KHUNG 1 — MOBILE ~375px (iPhone SE logic)                              │
├─────────────────────────────────────────────────────────────────────────┤
│  [≡] CampusMart                    (header stack / nav dọc)             │
│  ┌─────────────────────────────────┐                                    │
│  │ Hero: Do choi cong nghe...      │                                    │
│  │ [ Xem catalog ]                 │                                    │
│  └─────────────────────────────────┘                                    │
│  ┌───────────────┐                                                      │
│  │   card KB-01  │  ← grid 1 cột                                       │
│  └───────────────┘                                                      │
│  ┌─ table-wrap ─ scroll → → → ─────────────────┐                        │
│  │ SKU | Ten | Gia | ... (cuộn ngang)          │                        │
│  └─────────────────────────────────────────────┘                        │
└─────────────────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────────────────┐
│  KHUNG 2 — TABLET ~700px  (≥640px)                                      │
├─────────────────────────────────────────────────────────────────────────┤
│  CampusMart          [Danh muc] [San pham] [Dang ky]  ← nav ngang       │
│  ┌────────────┐ ┌────────────┐                                          │
│  │  card 1    │ │  card 2    │  ← grid 2 cột                            │
│  └────────────┘ └────────────┘                                          │
└─────────────────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────────────────┐
│  KHUNG 3 — DESKTOP ~1200px  (≥1024px)                                   │
├─────────────────────────────────────────────────────────────────────────┤
│        ┌──────────── main max-width 1120px ────────────┐                │
│        │ CampusMart     nav ngang                      │                │
│        │ Hero + [Xem catalog]                          │                │
│        │ ┌──┐ ┌──┐ ┌──┐ ┌──┐  ← grid 4 cột            │                │
│        │ └──┘ └──┘ └──┘ └──┘                          │                │
│        │ table-wrap (ít cần cuộn hơn)                  │                │
│        └───────────────────────────────────────────────┘                │
└─────────────────────────────────────────────────────────────────────────┘
```

Video nộp P05: quay lần lượt **375 / 700 / 1200** (Pixel 5 preset + custom).

---

## 5. Media query — grid 1 → 2 → 4

### 5.1. CSS mobile-first (BASE + 2 query)

```css
/* BASE — mobile (<640px): 1 cột */
.cm-grid {
  display: grid;
  grid-template-columns: 1fr;
  gap: 1rem;
}

/* Tablet ≥640px — 2 cột */
@media (min-width: 640px) {
  .cm-grid {
    grid-template-columns: repeat(2, 1fr);
  }
}

/* Desktop ≥1024px — 4 cột */
@media (min-width: 1024px) {
  .cm-grid {
    grid-template-columns: repeat(4, 1fr);
  }
}
```

| Viewport | `grid-template-columns` | Autograder `grid.cols` |
|----------|-------------------------|------------------------|
| < 640px | `1fr` | 1 |
| ≥ 640px | `repeat(2, 1fr)` | 2 |
| ≥ 1024px | `repeat(4, 1fr)` | 4 |

> P04 dùng `auto-fill minmax(220px)` — P05 **override** bằng query trên để khóa 1/2/4. Có thể giữ `minmax` trong query desktop nếu muốn — autograder kiểm `repeat(2` và `repeat(4`.

### 5.2. P04 vs P05 — grid

| | P04 | P05 |
|---|-----|-----|
| Cột | `auto-fill minmax(220px, 1fr)` | **1 / 2 / 4** cố định theo query |
| Responsive | Co giãn tự nhiên | **Breakpoint khóa** |
| Mục đích | Học Grid | Học **media query** |

---

## 6. Nav dọc → ngang

### 6.1. BASE mobile — nav dọc

```css
.cm-nav ul {
  display: flex;
  flex-direction: column;
  gap: 0.75rem;
  list-style: none;
  margin: 0;
  padding: 0;
}

.cm-header {
  display: flex;
  flex-direction: column;
  align-items: flex-start;
  gap: 1rem;
  /* giữ màu primary P03 */
}
```

### 6.2. Tablet+ — nav ngang, header một hàng

```css
@media (min-width: 640px) {
  .cm-header {
    flex-direction: row;
    justify-content: space-between;
    align-items: center;
  }

  .cm-nav ul {
    flex-direction: row;
    flex-wrap: wrap;
    gap: 1.5rem;
  }
}
```

### 6.3. So sánh nav theo khung

| Khung | Header | Nav `flex-direction` |
|-------|--------|----------------------|
| 375px | Stack (logo trên, nav dưới) | `column` |
| 700px+ | Một hàng `space-between` | `row` |
| 1200px | Giống tablet, trong main 1120 | `row` |

---

## 7. Hero section — CANONICAL exact

### 7.1. HTML hero

```html
<section class="cm-hero" id="hero">
  <h2>Do choi cong nghe cho sinh vien</h2>
  <a href="#products" class="cm-btn">Xem catalog</a>
</section>
```

| Key autograder | Value **exact** (không dấu, không đổi chữ) |
|----------------|---------------------------------------------|
| `hero.heading` | `Do choi cong nghe cho sinh vien` |
| `hero.btn` | `Xem catalog` |

### 7.2. CSS hero gợi ý

```css
.cm-hero {
  background: linear-gradient(135deg, #0f766e 0%, #0d9488 100%);
  color: white;
  padding: 2rem 1rem;
  text-align: center;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 1rem;
}

.cm-hero h2 {
  margin: 0;
  font-size: 1.5rem;
  max-width: 20ch;
}

.cm-btn {
  display: inline-block;
  background: white;
  color: var(--cm-primary);
  padding: 0.5rem 1.25rem;
  border-radius: 6px;
  text-decoration: none;
  font-weight: 600;
}

.cm-btn:hover {
  background: #f0fdfa;
}
```

### 7.3. Hình ảnh nhớ

```
Hero = biển quảng cáo đầu dashboard:
  h2 = câu chào sinh viên (exact 1 dòng CANONICAL)
  .cm-btn = nút dẫn xuống #products — không submit form
```

---

## 8. Main container — `max-width: 1120px`

```css
.cm-main {
  width: 100%;
  max-width: 1120px;
  margin: 0 auto;
  padding: 1rem;
}
```

```html
<main class="cm-main" id="main-content">
  <!-- hero có thể ngoài main full-bleed HOẶC trong main — nhất quán 1 cách -->
  ...
</main>
```

| Autograder | Value |
|------------|-------|
| `main.max-width` | `1120px` |

Desktop: nội dung **không** dính sát mép màn 1920px.

---

## 9. Bảng inventory — `.table-wrap` overflow

### 9.1. Vấn đề mobile

```
Bảng 8 cột trên 375px:
┌─────────────────────────────────── body tràn → → →
│ SKU | Ten | DM | Gia | SL | ... (vỡ layout)
```

### 9.2. Giải pháp — wrapper cuộn ngang

```html
<div class="table-wrap">
  <table class="cm-table" data-testid="cm-inventory-table">
    <!-- 8 dòng CORE — giữ từ P01 -->
  </table>
</div>
```

```css
.table-wrap {
  width: 100%;
  overflow-x: auto;
  -webkit-overflow-scrolling: touch;
}

.cm-table {
  min-width: 640px; /* đảm bảo đủ cột — cuộn trong wrap */
}
```

### 9.3. ASCII table-wrap

```
Mobile:
┌─────────────────────────┐
│ .table-wrap             │
│ ┌───────────────────────┼──→ cuộn ngang
│ │ table (min-width)     │
│ └───────────────────────┘
└─────────────────────────┘
Body KHÔNG tràn — chỉ vùng bảng cuộn
```

### 9.4. Phân vai testid sau P05

| testid | Gắn trên | Phiếu |
|--------|----------|-------|
| `cm-product-table` | `.cm-grid` (card catalog) | P04+ |
| `cm-inventory-table` | `<table>` chi tiết kho (nếu có) | P05 gợi ý |
| `cm-subscribe-form` | form đăng ký | P02+ |

---

## 10. Dashboard tĩnh hoàn chỉnh — checklist component

Một **dashboard tĩnh** CampusMart P05 gồm:

```
┌─ dashboard tĩnh (HTML + CSS, chưa JS) ─────────────────────┐
│ header.cm-header + nav.cm-nav (responsive)               │
│ section.cm-hero (heading + btn exact)                    │
│ section#categories (3 DM — P01)                          │
│ section#products (.cm-grid 8 card + testid)              │
│ section + .table-wrap (.cm-table inventory)            │
│ section#subscribe (form cm-subscribe-form)               │
│ footer                                                    │
└──────────────────────────────────────────────────────────┘
```

| Component | Class / id | Responsive P05 |
|-----------|------------|----------------|
| Header | `.cm-header` | stack → row @640 |
| Nav | `.cm-nav` | column → row @640 |
| Hero | `.cm-hero`, `.cm-btn` | padding/font scale (tùy) |
| Card grid | `.cm-grid` | 1 → 2 → 4 |
| Bảng | `.table-wrap` + `.cm-table` | overflow-x |
| Form | `#subscribe` | full-width mobile OK |

> **Không** gọi là “landing page marketing” — đây là **dashboard quản lý/xem catalog tĩnh**, nền cho JS render P06–08.

---

## 11. `<title>` và marker P05

```html
<title>CampusMart — Responsive (Buoi 5)</title>
```

```html
<!-- CM_EXPECT product_count=8 card_count=8 inventory_value=41380000 responsive=640,1024 -->
```

---

## 12. Thử ngay trên máy (DevTools 375 / 700 / 1200)

### Thử A — Bật Device Toolbar

1. F12 → icon **Toggle device toolbar** (Ctrl+Shift+M).  
2. Chọn **Responsive** hoặc **Pixel 5** (~393px).  
3. Ghi nhận: nav dọc? grid 1 cột?

### Thử B — Ba width exact (video P05)

| Width | Thiết lập | Kỳ vọng |
|-------|-----------|---------|
| **375** | Custom width | Nav dọc, grid 1 cột, bảng cuộn trong wrap |
| **700** | Custom width | Nav ngang, grid **2** cột |
| **1200** | Custom width | Grid **4** cột, main ≤ **1120px** giữa |

### Thử C — Kiểm tra @media trong Sources

Mở `styles.css` → tìm `(min-width: 640px)` và `(min-width: 1024px)` — **đủ 2 query**.

### Thử D — Hero exact

View Source → `<h2>` trong `.cm-hero` khớp từng ký tự heading CANONICAL.  
Nút `.cm-btn` text = `Xem catalog`.

### Thử E — table-wrap

375px → kéo ngang **trong** vùng bảng; **body** không xuất hiện thanh cuộn ngang toàn trang (hoặc tối thiểu).

### Thử F — Computed main

1200px → inspect `.cm-main` → `max-width: 1120px`, `margin-left/right: auto`.

---

## 13. Câu hỏi tự kiểm tra

1. Mobile-first nghĩa là viết CSS cho màn nào trước?
2. Hai breakpoint exact của môn? Vì sao không dùng 768px?
3. Grid P05 có bao nhiêu cột ở 700px và 1200px?
4. Nav mobile vs tablet khác nhau thế nào?
5. Hero heading và nút exact là gì?
6. `.table-wrap` giải quyết vấn đề gì?
7. `max-width: 1120px` đặt ở đâu? Tác dụng?
8. “Dashboard tĩnh” khác “landing” thế nào trong môn CSE391?

---

## 14. Gợi ý đáp án

1. Màn nhỏ trước (base), `min-width` thêm style lớn hơn.  
2. **640px**, **1024px** — khóa autograder/CANONICAL.  
3. 700px → **2** cột; 1200px → **4** cột.  
4. Mobile: nav `column`, header stack; ≥640: nav `row`, header một hàng.  
5. `Do choi cong nghe cho sinh vien` / `Xem catalog`.  
6. Bảng rộng cuộn trong wrap, không vỡ body.  
7. Trên `main` / `.cm-main` — căn giữa desktop.  
8. Dashboard = catalog + bảng + form quản lý tĩnh; landing = thuật ngữ marketing (tránh).

---

## 15. Lỗi thường gặp (chi tiết)

| # | Triệu chứng | Nguyên nhân | Cách xử lý |
|---|-------------|-------------|------------|
| 1 | Mobile vẫn 4 cột | Desktop-first / thiếu base 1fr | Reset base `.cm-grid { grid-template-columns: 1fr }` |
| 2 | Query không chạy | Sai `max-width` thay `min-width` | Đổi mobile-first |
| 3 | Nav ngang trên 375px | Chỉ có style P04 | Thêm base `flex-direction: column` |
| 4 | Hero fail autograder | Thêm dấu / đổi chữ heading | Copy exact CANONICAL |
| 5 | Body cuộn ngang | Bảng không wrap | Thêm `.table-wrap { overflow-x:auto }` |
| 6 | Main full 1920px | Thiếu max-width | `max-width: 1120px; margin: 0 auto` |
| 7 | Thiếu @media 1024 | Chỉ query 640 | Thêm query thứ 2 |
| 8 | Grid 2 cột @1200 | Thiếu query 1024 | `repeat(4, 1fr)` @1024 |
| 9 | Nút hero là `<button>` | Sai semantically OK but text lệch | `<a class="cm-btn">` + text exact |
| 10 | Xóa form/card P04 | Mất EXPECT | Chỉ thêm responsive |

---

## 16. Checklist trước Buổi 6 (JS)

- [ ] `<title>` exact P05: `CampusMart — Responsive (Buoi 5)`
- [ ] `@media (min-width: 640px)` và `(min-width: 1024px)` trong CSS
- [ ] Grid 1 → 2 → 4 cột
- [ ] Nav dọc mobile → ngang ≥640
- [ ] Hero heading + `.cm-btn` exact
- [ ] `main` / `.cm-main` max-width **1120px**
- [ ] `.table-wrap` + bảng inventory
- [ ] Dashboard tĩnh đủ section (header, hero, grid, bảng, form)
- [ ] Video quay 375 / 700 / 1200
- [ ] Xong [Phiếu 05](./worksheets/phieu-05-responsive-campusmart.md)

---

## 17. Bài tập (chuỗi CampusMart UI)

| Phần | Làm gì | Chi tiết |
|------|--------|----------|
| **Trên lớp (~20')** | Base mobile + query 640 (nav + grid 2) | [Phiếu 05 — mục 2](./worksheets/phieu-05-responsive-campusmart.md) |
| **Về nhà (~25–40')** | Hero + 1024 + table-wrap + video 3 khung | [Phiếu 05 — mục 3](./worksheets/phieu-05-responsive-campusmart.md) |

Repo: **`cse391-cm-05`** + video OBS.

---

## 18. Cầu nối sang Buổi 6 (JavaScript)

```
Buổi 5: Dashboard TĨNH — HTML/CSS responsive hoàn chỉnh
Buổi 6: JavaScript — query DOM, mảng/object, tính tổng kho từ data

  document.querySelector('.cm-grid')
  const products = [ { sku: 'KB-01', ... }, ... ]
  → đổ số liệu động (chuẩn bị thay card copy tay)
```

Giữ class `.cm-card`, `data-sku`, `data-testid` — JS Buổi 7 sẽ **render** thay vì HTML tĩnh 8 card.

→ [Buổi 6](./06-buoi-06-javascript-nen.md) · [Phiếu 06](./worksheets/phieu-06-js-nen.md)

---

*CSE391 – Ebook Frontend CampusMart UI | Buổi 5/12*
