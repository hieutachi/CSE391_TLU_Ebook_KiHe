# BUỔI 3: CSS cơ bản — Selector, Cascade, Box Model, Đơn vị

> **Thời lượng gợi ý:** 3–4 giờ tự học (lý thuyết + thử máy + Phiếu 03)  
> **Tiên quyết:** Buổi 1–2 (trang CampusMart đủ nội dung + form + media)  
> **Kết quả đầu ra:** Viết được stylesheet ngoài; hiểu cascade/specificity; vẽ và tính box model; dùng CSS variables CampusMart  
> **Phiếu học tập:** [Phiếu 03](./worksheets/phieu-03-css-co-ban.md) — chuỗi **CampusMart UI**  
> **Dataset:** [CANONICAL-DATA §8.1](./worksheets/CANONICAL-DATA.md) — `--cm-primary: #0f766e`, `--cm-bg: #f8fafc`

---

## 0. Gợi ý lịch trình giảng (cho GV)

| Phút | Nội dung | Gợi ý thao tác |
|------|----------|----------------|
| 0–10 | Ôn P02 + hỏi “CampusMart xấu vì sao?” | Chiếu trang P02 chưa CSS |
| 10–25 | 3 nơi đặt CSS; external bắt buộc; `<link>` | Tạo `styles.css` trống → link |
| 25–45 | Selector + demo cascade 4 đoạn `<p>` | SV dự đoán màu trước khi mở browser |
| 45–60 | Specificity — tính tay trên bảng | Viết (0,1,1) trên bảng |
| 60–75 | Box model ASCII + `border-box` + thử width+padding | DevTools Computed |
| 75–90 | rem / % / px; `:root` tokens CampusMart | Đổi `--cm-primary` → cả trang đổi |
| 90–110 | **Thực hành Phiếu 03 mục 2** | Style `.cm-table` |
| 110–120 | Giao về nhà P03 mục 3 | Header, zebra, focus |

---

## 1. Mục tiêu buổi học

Sau Buổi 3, bạn có thể:

1. Nối CSS external qua `<link>`; giải thích 3 nơi đặt CSS và vì sao môn này bắt buộc external.
2. Dùng selector: tag, class, id, descendant, pseudo-class (`:hover`, `:focus`).
3. Giải thích **cascade + specificity** — rule nào thắng; **tính tay** ví dụ (0, n, n).
4. Vẽ box model; phân biệt margin/border/padding/content; dùng `box-sizing: border-box`.
5. Chọn đơn vị `px`, `%`, `rem`; khai báo tokens `--cm-primary`, `--cm-bg`.
6. Style bảng + form CampusMart; giải thích demo cascade 4 đoạn `<p>`.

---

## 2. Nhắc lại — vì sao cần CSS ngay sau Form?

```
Buổi 2 CampusMart:
  ✓ Bảng 8 SP, form đăng ký, 8 ảnh figure
  ✗ Màu mặc định trình duyệt, nav dọc, bảng “thô”

Buổi 3 trả lời câu hỏi:
  "Style của tôi viết rồi — vì sao KHÔNG ăn?"
  → Cascade + specificity + đường dẫn file
```

**Hình ảnh nhớ:**

```
HTML = xương + nội dung (Buổi 1–2)
CSS  = quần áo + sơn tường + khoảng cách giữa đồ đạc (Buổi 3)
       Flex/Grid = sắp kệ ngang/dọc (Buổi 4)
```

> 90% debug CSS của người mới = *"Tại sao style không ăn?"* — câu trả lời hầu như luôn ở **cascade**, không phải “trình duyệt hỏng”.

---

## 3. Ba nơi đặt CSS — external bắt buộc

```
┌─────────────────────────────────────────────────────────────┐
│  1. INLINE     <p style="color:red">                        │
│     └─ Một thuộc tính một chỗ — KHÓ bảo trì, tránh          │
│                                                             │
│  2. INTERNAL   <head><style> … </style></head>              │
│     └─ Demo nhanh 1 trang — không scale cả site            │
│                                                             │
│  3. EXTERNAL   <link rel="stylesheet" href="styles.css">    │
│     └─ ★ CHUẔN môn CSE391 từ P03 trở đi ★                  │
└─────────────────────────────────────────────────────────────┘
```

### 3.1. Cách nối external (bắt buộc P03)

```html
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>CampusMart — CSS (Buoi 3)</title>
  <link rel="stylesheet" href="styles.css" />
</head>
```

```
index.html ──link──► styles.css
                         │
                    mọi rule CSS nằm ở đây
                    autograder P03 tìm file này
```

### 3.2. So sánh — trước / sau external

| | Inline / internal rải rác | External `styles.css` |
|---|---------------------------|------------------------|
| Sửa màu thương hiệu | Tìm từng thẻ | Sửa `:root` một chỗ |
| Cache trình duyệt | Khó | File CSS cache riêng |
| Autograder P03 | Khó phát hiện | `css_external=true` |
| Inline style attribute | Có | **Cấm** P03 |

### 3.3. Kiểm tra file load (DevTools)

F12 → **Network** → reload → tìm `styles.css` → Status **200**.

| Status | Nguyên nhân |
|--------|-------------|
| 200 | OK |
| 404 | Sai `href` hoặc thiếu file |
| (blocked) | Đường dẫn / CORS hiếm với file tĩnh |

---

## 4. Selector — trỏ đúng phần tử

```css
/* Tag */              table { border-collapse: collapse; }
/* Class (ưu tiên) */  .cm-table { width: 100%; }
/* Id (hạn chế) */     #subscribe { margin-top: 2rem; }
/* Descendant */       nav a { text-decoration: none; }
/* Nhiều mục */        h1, h2 { font-family: system-ui, sans-serif; }
/* Pseudo-class */     nav a:hover { text-decoration: underline; }
                       input:focus { outline: 2px solid #0f766e; }
/* Attribute */        tr[data-sku="KB-01"] { background: #fefce8; }
```

### 4.1. Quy ước CampusMart

| Selector | Dùng khi | Ví dụ |
|----------|----------|-------|
| `.cm-*` class | Style tái sử dụng | `.cm-table`, `.cm-header` |
| `#id` | Anchor, hook JS | `#subscribe`, `#products` |
| tag | Reset chung | `body`, `table` |
| `[data-*]` | Highlight 1 SKU demo | `[data-sku="KB-01"]` |

> **Không** style hàng loạt bằng `#id` — specificity quá cao, khó override sau này.

### 4.2. Pseudo-class hay dùng P03

| Pseudo | Nghĩa | CampusMart |
|--------|-------|------------|
| `:hover` | Chuột đậu | Hàng bảng, link nav |
| `:focus` | Bàn phím/tab | Input form — **bắt buộc nhìn thấy** |
| `:focus-visible` | Focus bàn phím (hiện đại) | Thay `:focus` khi cần |
| `:nth-child(even)` | Hàng chẵn | Zebra table |

---

## 5. Cascade & Specificity — ai thắng?

### 5.1. Hình ảnh nhớ — thi đua quy tắc

```
Nhiều rule cùng nhắm 1 phần tử → trình duyệt “thi đua”:

  Bước 1: !important?        (vượt rào — tránh lạm dụng)
  Bước 2: Specificity cao hơn? (điểm số selector)
  Bước 3: Cùng điểm → rule VIẾT SAU thắng (thứ tự file)
```

### 5.2. Sơ đồ CASCADE (ASCII bắt buộc)

```
                 CASCADE — ai thắng?
┌────────────────────────────────────────────────────┐
│ 1. Importance   !important  >  bình thường         │
│ 2. Specificity  inline style > id > class > tag    │
│                 (tính điểm — xem bảng dưới)        │
│ 3. Source order rule SAU thắng rule TRƯỚC          │
│    (cùng specificity, cùng file)                   │
└────────────────────────────────────────────────────┘
```

### 5.3. Cách tính specificity (tính tay)

Ghi dạng **(a, b, c)** hoặc **a-b-c**:

| Thành phần | a (inline) | b (id) | c (class/attr/pseudo) | d (tag) |
|------------|:----------:|:------:|:---------------------:|:-------:|
| `style=""` | 1 | — | — | — |
| `#id` | | +1 | | |
| `.class`, `[attr]`, `:hover` | | | +1 | |
| `element` | | | | +1 |

**Ví dụ tính tay:**

| Selector | Tính | Kết quả |
|----------|------|---------|
| `p` | 0 id, 0 class, 1 tag | **(0,0,1)** |
| `.note` | 0 id, 1 class, 0 tag | **(0,1,0)** |
| `p.note` | 0 id, 1 class, 1 tag | **(0,1,1)** |
| `#warn` | 1 id | **(1,0,0)** |
| `#warn.note` | 1 id, 1 class | **(1,1,0)** |
| `nav a` | 0 id, 0 class, 2 tag | **(0,0,2)** |
| `.cm-nav a` | 0 id, 1 class, 1 tag | **(0,1,1)** |
| `.cm-table tbody tr:hover` | 0 id, 3 class/pseudo, 2 tag | **(0,3,2)** |

**So sánh:** `(1,0,0)` thắng `(0,3,2)` vì **cột id** ưu tiên hơn.

### 5.4. Demo cascade kinh điển — 4 đoạn `<p>` (giải thích sâu)

**File `demo-cascade.html` + `demo.css` — tự gõ, dự đoán TRƯỚC khi mở trình duyệt:**

```css
/* demo.css */
p { color: blue; }
.note { color: green; }
#warn { color: red; }
```

```html
<!-- demo-cascade.html -->
<link rel="stylesheet" href="demo.css" />
<p>1: mau gi?</p>
<p class="note">2: mau gi?</p>
<p class="note" id="warn">3: mau gi?</p>
<p class="note" style="color: purple">4: mau gi?</p>
```

#### Phân tích từng dòng

| # | HTML | Rule thắng | Specificity | Màu | Vì sao |
|---|------|------------|-------------|-----|--------|
| 1 | `<p>` | `p { color: blue }` | (0,0,1) | **blue** | Chỉ khớp tag |
| 2 | `<p class="note">` | `.note { color: green }` | (0,1,0) | **green** | Class thắng tag |
| 3 | `<p class="note" id="warn">` | `#warn { color: red }` | (1,0,0) | **red** | Id thắng class và tag |
| 4 | `<p … style="color: purple">` | inline `color: purple` | (1,0,0,0) inline | **purple** | Inline thắng stylesheet (trừ !important) |

**Đoạn 3 — tranh luận hay gặp:** Cả `.note` và `#warn` cùng nhắm — id **(1,0,0)** > class **(0,1,0)** → **red**, không phải green.

**Đoạn 4 — bài học P03:** Inline style **bị cấm** trên trang nộp — autograder kiểm `css_external=true`. Demo chỉ để hiểu cascade.

#### Thử mở rộng — thứ tự trong file

Thêm vào cuối `demo.css`:

```css
.note { color: orange; }  /* cùng (0,1,0) với .note green — rule SAU thắng */
```

Đoạn 2 giờ **orange** (không còn green) — minh họa **bước 3 cascade**.

#### Thử `!important` (biết để tránh)

```css
p { color: blue !important; }
```

Đoạn 1–3 có thể đều xanh dương — **đừng dùng** để “chữa cháy” bài CampusMart.

### 5.5. Kế thừa (inheritance)

```
Một số thuộc tính CHẢY từ cha → con:
  color, font-family, font-size (thường)

Một số KHÔNG kế thừa:
  margin, padding, border, width, background
```

```css
body {
  font-family: system-ui, -apple-system, "Segoe UI", sans-serif;
  color: #1f2937;
}
/* Mọi phần tử con “nhận” font + màu chữ — không cần lặp lại */
```

---

## 6. Box Model — mọi phần tử là một cái hộp

### 6.1. ASCII bắt buộc

```
┌─────────────────────────────────────────────┐
│                  MARGIN (ngoài, trong suốt) │
│   ┌─────────────────────────────────────┐   │
│   │            BORDER (viền)            │   │
│   │   ┌─────────────────────────────┐   │   │
│   │   │      PADDING (đệm trong)    │   │   │
│   │   │   ┌─────────────────────┐   │   │   │
│   │   │   │      CONTENT        │   │   │   │
│   │   │   │  (text / ảnh / …)   │   │   │   │
│   │   │   └─────────────────────┘   │   │   │
│   │   └─────────────────────────────┘   │   │
│   └─────────────────────────────────────┘   │
└─────────────────────────────────────────────┘
```

### 6.2. `content-box` vs `border-box`

**Mặc định (`content-box`):**

```css
.box {
  width: 200px;
  padding: 20px;      /* +40px ngang */
  border: 2px solid;  /* +4px ngang */
}
/* Chiều ngang THỰC = 200 + 40 + 4 = 244px → layout VỠ */
```

**Chuẩn môn (`border-box`):**

```css
*, *::before, *::after { box-sizing: border-box; }
.box {
  width: 200px;
  padding: 20px;
  border: 2px solid;
}
/* Chiều ngang THỰC = 200px (padding+border nằm TRONG) */
```

| | content-box | border-box |
|---|-------------|--------------|
| `width: 200px` + `padding: 20px` | ~240px+ | **200px** |
| Dự đoán layout | Dễ vỡ | Ổn định |
| P03 | Không dùng | **Bắt buộc** đầu file |

### 6.3. Shorthand margin/padding

```css
padding: 0.5rem 0.75rem;  /* trên-dưới | trái-phải */
margin: 0 auto;             /* căn giữa block (Buổi 4+) */
```

---

## 7. Đơn vị — px, %, rem (và vw/vh)

| Đơn vị | Bản chất | Dùng cho CampusMart |
|--------|----------|---------------------|
| `px` | Điểm ảnh cố định | `border: 1px`, outline |
| `rem` | × font-size gốc `html` (16px) | `font-size`, `padding` — **chuẩn môn** |
| `%` | Theo **cha trực tiếp** | `.cm-table { width: 100%; }` |
| `vw`/`vh` | % viewport | Preview Buổi 5 responsive |

```css
html { font-size: 100%; }   /* 16px mặc định */
h1 { font-size: 2rem; }     /* 32px */
.cm-table th, .cm-table td { padding: 0.5rem 0.75rem; }
```

### 7.1. rem vs px — so sánh

| | px | rem |
|---|----|-----|
| Người dùng phóng chữ hệ thống | Không scale | **Scale theo** |
| Tính nhẩm | Trực tiếp | 1rem ≈ 16px |
| P03 spacing | OK chi tiết | **Ưu tiên** |

---

## 8. CSS Variables — design tokens CampusMart

Khóa trong [CANONICAL-DATA §8.1](./worksheets/CANONICAL-DATA.md):

```css
*, *::before, *::after { box-sizing: border-box; }

:root {
  --cm-primary: #0f766e;   /* xanh thương hiệu CampusMart */
  --cm-bg: #f8fafc;        /* nền trang */
}

body {
  margin: 0;
  font-family: system-ui, -apple-system, "Segoe UI", sans-serif;
  background: var(--cm-bg);
  color: #1f2937;
}

.cm-header {
  background: var(--cm-primary);
  color: white;
  padding: 1rem;
}

a { color: var(--cm-primary); }

.cm-table thead {
  background: var(--cm-primary);
  color: white;
}

input:focus,
select:focus,
textarea:focus {
  outline: 2px solid var(--cm-primary);
}
```

```
:root = “tủ màu trung tâm” — single source of truth
       đổi --cm-primary một chỗ → header, link, thead, focus đổi theo
```

| Token | Value exact | Dùng ở |
|-------|-------------|--------|
| `--cm-primary` | `#0f766e` | header, thead, link, focus, nút |
| `--cm-bg` | `#f8fafc` | `body` background |

Breakpoint (P05): mobile `640px`, desktop `1024px` — nhắc trước.

### 8.1. Thử token — đổi một chỗ, cả trang đổi theo

| Bước | Thao tác | Quan sát |
|------|----------|----------|
| 1 | Mở `styles.css`, đổi `--cm-primary: #0f766e` → `#1d4ed8` | — |
| 2 | Reload trang | Header, thead, link, focus, nút **cùng** xanh mới |
| 3 | Đổi lại `#0f766e` trước nộp | Khớp CANONICAL |

Đây là lý do dùng **design tokens** thay vì hard-code `#0f766e` ở 10 selector rải rác.

---

## 9. Style CampusMart — bảng + form (mẫu P03)

### 9.1. Bảng `.cm-table` (trên lớp)

```css
.cm-table {
  width: 100%;
  border-collapse: collapse;
}

.cm-table th,
.cm-table td {
  padding: 0.5rem 0.75rem;
  border-bottom: 1px solid #e2e8f0;
  text-align: left;
}

.cm-table thead {
  background: var(--cm-primary);
  color: white;
}
```

### 9.2. Zebra + hover (về nhà)

```css
.cm-table tbody tr:nth-child(even) {
  background: #f1f5f9;
}

.cm-table tbody tr:hover {
  background: #ccfbf1;
}
```

**Câu hỏi cascade (chuẩn bị video):**

- `tbody tr:hover` có specificity **(0, 2, 2)** — 2 pseudo/class + 2 tag.
- Nếu thêm `tr { background: white !important; }` → hover **mất tác dụng** (!important thắng).

### 9.3. Nav + form (về nhà)

```css
.cm-nav {
  list-style: none;
  padding: 0;
}

.cm-nav a {
  color: white;
  text-decoration: none;
}

.cm-nav a:hover {
  text-decoration: underline;
}

input,
select,
textarea {
  width: 100%;
  max-width: 24rem;
  padding: 0.5rem;
  border: 1px solid #cbd5e1;
  border-radius: 4px;
}

button[type="submit"] {
  background: var(--cm-primary);
  color: white;
  border: none;
  padding: 0.5rem 1rem;
  border-radius: 4px;
  cursor: pointer;
}

button[type="submit"]:hover {
  background: #0d9488; /* teal đậm hơn — tự chọn */
}
```

> Buổi 3 nav vẫn **dọc** — Buổi 4 Flexbox ép ngang. **Không** dùng `float`.

---

## 10. Thử ngay trên máy (dự đoán → chạy → DevTools)

### Thử A — Demo cascade 4 đoạn `<p>`

1. Tạo `demo-cascade.html` + `demo.css` mục 5.4.  
2. **Viết dự đoán màu 1–4 vào vở.**  
3. Mở Live Server → so sánh.  
4. F12 → chọn `<p>` thứ 3 → tab **Styles** → xem rule bị **gạch ngang** (overridden).

### Thử B — Box model width + padding

Tạo `demo-box.html`:

```html
<style>
  .a { box-sizing: content-box; width: 200px; padding: 20px; border: 2px solid #000; background: #fecaca; }
  .b { box-sizing: border-box;  width: 200px; padding: 20px; border: 2px solid #000; background: #bbf7d0; }
</style>
<div class="a">content-box</div>
<div class="b">border-box</div>
```

| Dự đoán | Quan sát Computed |
|---------|-------------------|
| Hộp đỏ rộng hơn 200px? | Đo width thực trong DevTools |
| Hộp xanh đúng 200px? | border-box |

### Thử C — `styles.css` load

1. Link `styles.css` vào CampusMart P03.  
2. Network → reload → **200**.  
3. Đổi `--cm-primary` thành `#b91c1c` → reload → header + thead đỏ.

### Thử D — Focus ring

Tab vào input form → phải thấy outline `--cm-primary`.  
**Không** được `outline: none` without replacement.

### Thử E — Specificity tay

Cho selector `.cm-table tbody tr:hover` — tự tính (0,3,2) rồi đối chiếu mục 5.3.

---

## 11. Câu hỏi tự kiểm tra

1. Ba nơi đặt CSS? Vì sao P03 bắt buộc external?
2. Specificity của `#subscribe .cm-nav a`? (Gợi ý: (1,1,1))
3. Demo đoạn `<p class="note" id="warn">` — màu cuối cùng nếu không inline?
4. `border-box` khác `content-box` ở điểm nào?
5. `1.5rem` padding tương đương bao nhiêu px (gốc 16px)?
6. `--cm-primary` dùng ở những chỗ nào trên trang bạn?
7. Vì sao không lạm dụng `!important`?
8. `:hover` trên `tr` có specificity bao nhiêu nếu viết `.cm-table tbody tr:hover`?

---

## 12. Gợi ý đáp án

1. Inline, internal, external; bảo trì + autograder.  
2. (1,1,1).  
3. **red** — id thắng class.  
4. border-box: width gồm padding+border.  
5. 24px.  
6. header, thead, link, focus, button…  
7. Phá cascade, khó override sau.  
8. (0,3,2).

---

## 13. Lỗi thường gặp (chi tiết)

| # | Triệu chứng | Nguyên nhân | Cách xử lý |
|---|-------------|-------------|------------|
| 1 | CSS không ăn | Sai `href` / 404 | Network tab |
| 2 | Rule bị gạch | Specificity thấp hơn | DevTools Styles |
| 3 | Màu không đổi khi sửa token | Không dùng `var()` | Thay hex bằng `var(--cm-primary)` |
| 4 | Width vỡ khi thêm padding | Quên border-box | Reset đầu file |
| 5 | Focus không thấy | `outline: none` | `:focus { outline: 2px solid … }` |
| 6 | Zebra không chạy | Selector sai | `.cm-table tbody tr:nth-child(even)` |
| 7 | Inline style trên HTML | Copy template | Xóa `style=""`, chuyển CSS file |
| 8 | Bootstrap/Tailwind P03 | Nhảy cóc | CSS thuần — hiểu cascade |
| 9 | Font mỗi nơi một kiểu | Không đặt `body` | Một lần trên body |
| 10 | Cache CSS cũ | Ctrl+F5 hard reload | — |

---

## 14. Checklist trước Buổi 4

- [ ] `styles.css` external, load 200
- [ ] `:root` có `--cm-primary`, `--cm-bg` exact
- [ ] `box-sizing: border-box` global
- [ ] Giải thích được demo cascade 4 đoạn `<p>`
- [ ] `.cm-table` full-width, thead màu thương hiệu, zebra + hover
- [ ] Input `:focus` nhìn thấy; không inline style
- [ ] Giữ P01–02: 8 SP, form, figure, testid
- [ ] Xong [Phiếu 03](./worksheets/phieu-03-css-co-ban.md)

---

## 15. Bài tập (chuỗi CampusMart UI)

| Phần | Làm gì | Chi tiết |
|------|--------|----------|
| **Trên lớp (~20')** | Link CSS + tokens + bảng | [Phiếu 03 — mục 2](./worksheets/phieu-03-css-co-ban.md) |
| **Về nhà (~25–40')** | Header, nav, form, zebra | [Phiếu 03 — mục 3](./worksheets/phieu-03-css-co-ban.md) |

Repo: **`cse391-cm-03`** + video OBS.

---

## 16. Cầu nối sang Buổi 4 (Flexbox & Grid)

```
Buổi 3: Style TỪNG phần tử (màu, padding, border)
Buổi 4: XẾP phần tử CẠNH NHAU — nav ngang, lưới card .cm-grid

  display: flex;   → hàng nav, căn giữa
  display: grid;   → lưới sản phẩm responsive
```

Class `.cm-grid`, `.cm-card` đã khóa CANONICAL §7 — chuẩn bị từ P03 (HTML giữ nguyên, P04 thêm layout).

→ [Buổi 4](./04-buoi-04-css-flexbox-grid.md) · [Phiếu 04](./worksheets/phieu-04-flexbox-grid.md)

---

*CSE391 – Ebook Frontend CampusMart UI | Buổi 3/12*
