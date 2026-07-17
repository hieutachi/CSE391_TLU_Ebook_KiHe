# BUỔI 3: CSS cơ bản — Selector, Cascade, Box Model, Đơn vị

> **Thời lượng gợi ý:** 3–4 giờ tự học  
> **Tiên quyết:** Buổi 1–2 (trang CampusMart đủ nội dung + form)  
> **Kết quả đầu ra:** Viết được stylesheet ngoài, hiểu vì sao một rule "thắng" rule khác, vẽ và tính được box model  
> **Phiếu học tập liên quan:** [Phiếu 03](./worksheets/phieu-03-css-co-ban.md) — chuỗi **CampusMart UI**

---

## 1. Mục tiêu buổi học

Sau Buổi 3, bạn có thể:

1. Nối CSS vào HTML đúng cách (external stylesheet) và giải thích 3 nơi đặt CSS.
2. Dùng selector: tag, class, id, descendant, pseudo-class (`:hover`, `:focus`).
3. Giải thích cascade + specificity: rule nào thắng và vì sao.
4. Vẽ box model, phân biệt `margin` / `border` / `padding` / `content`, dùng `box-sizing: border-box`.
5. Chọn đúng đơn vị: `px`, `%`, `rem`, `vw/vh`.
6. Style bảng sản phẩm CampusMart bằng CSS variables (design tokens).

---

## 2. Vì sao cần hiểu Cascade thay vì "thử đến khi đẹp"?

90% thời gian debug CSS của người mới là câu hỏi: *"Tại sao style của tôi không ăn?"*. Câu trả lời hầu như luôn nằm ở **cascade** — cơ chế trình duyệt quyết định giá trị cuối cùng khi nhiều rule cùng nhắm một phần tử. Hiểu cascade một lần, bạn tiết kiệm hàng chục giờ "thử `!important`".

---

## 3. Ba nơi đặt CSS — chọn external

```
1. Inline      <p style="color:red">        ← tránh: khó bảo trì
2. Internal    <style> ... </style>          ← demo nhanh
3. External    <link rel="stylesheet"        ← CHUẨN của môn này
                     href="styles.css">
```

```html
<head>
  ...
  <link rel="stylesheet" href="styles.css" />
</head>
```

Từ Phiếu 03 trở đi, mọi style nằm trong **`styles.css`** — máy chấm sẽ tìm file này.

---

## 4. Selector — trỏ đúng phần tử

```css
/* Tag */         table   { border-collapse: collapse; }
/* Class */       .cm-table { width: 100%; }
/* Id */          #subscribe { margin-top: 2rem; }
/* Descendant */  nav a   { text-decoration: none; }
/* Nhiều mục */   h1, h2  { font-family: system-ui, sans-serif; }
/* Pseudo */      nav a:hover  { text-decoration: underline; }
                  input:focus  { outline: 2px solid #0f766e; }
/* Attribute */   tr[data-sku="KB-01"] { background: #fefce8; }
```

> **Quy ước môn:** ưu tiên **class** (`.cm-...`). Id dùng cho anchor/JS; hạn chế style theo id.

---

## 5. Cascade & Specificity (ASCII bắt buộc nhớ)

Khi nhiều rule cùng nhắm một phần tử, trình duyệt xử theo thứ tự:

```
                 CASCADE — ai thắng?
┌────────────────────────────────────────────────────┐
│ 1. Importance   !important  >  bình thường         │
│ 2. Specificity  inline(1000) > id(100)             │
│                 > class/attr/pseudo(10) > tag(1)   │
│ 3. Source order rule sau thắng rule trước          │
└────────────────────────────────────────────────────┘

Ví dụ tính specificity:
  nav a            → 0-0-2   (2 tag)
  .cm-nav a        → 0-1-1   (1 class + 1 tag)   ← thắng
  #menu a          → 1-0-1   (1 id + 1 tag)      ← thắng nữa
```

```css
/* Cùng nhắm thẻ <a> trong nav */
nav a { color: black; }          /* specificity 0-0-2 */
.cm-nav a { color: #0f766e; }    /* specificity 0-1-1 → THẮNG */
```

**Kế thừa (inheritance):** một số thuộc tính chảy từ cha xuống con (`color`, `font-family`); một số không (`border`, `margin`). Đặt font một lần ở `body` là đủ:

```css
body {
  font-family: system-ui, -apple-system, "Segoe UI", sans-serif;
  color: #1f2937;
}
```

---

## 6. Box Model — mọi phần tử là một cái hộp

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

### Vấn đề kinh điển và `border-box`

```css
/* Mặc định (content-box): width KHÔNG gồm padding/border */
.box { width: 200px; padding: 20px; border: 2px solid; }
/* → chiếm thực tế 200 + 40 + 4 = 244px  → layout vỡ */

/* Chuẩn môn này: đặt một lần đầu file */
*, *::before, *::after { box-sizing: border-box; }
/* → width 200px là 200px trọn gói */
```

Kiểm chứng bằng DevTools: chọn phần tử → tab **Computed** → sơ đồ box model tương tác.

---

## 7. Đơn vị — dùng gì, khi nào

| Đơn vị | Bản chất | Dùng cho |
|--------|----------|----------|
| `px` | Điểm ảnh cố định | border, chi tiết nhỏ |
| `rem` | Bội số font gốc (16px) | font-size, spacing (chuẩn môn) |
| `%` | Theo cha | width cột, ảnh co giãn |
| `vw`/`vh` | % khung nhìn | hero section, full-height |

```css
h1 { font-size: 2rem; }       /* 32px, phóng theo cài đặt người dùng */
.cm-table { width: 100%; }
```

---

## 8. CSS variables — design tokens của CampusMart

Khóa 2 token trong [`CANONICAL-DATA.md` §8.1](./worksheets/CANONICAL-DATA.md):

```css
:root {
  --cm-primary: #0f766e;   /* xanh CampusMart */
  --cm-bg: #f8fafc;        /* nền trang */
}

body { background: var(--cm-bg); }
.cm-header { background: var(--cm-primary); color: white; }
a { color: var(--cm-primary); }
```

Lợi ích: đổi màu thương hiệu **một chỗ**, cả trang đổi theo — đúng tinh thần "single source of truth" như dataset.

---

## 9. Bài thực hành minh họa trong buổi (không thay phiếu)

Tạo `demo-cascade.html` + `demo.css`, tự dự đoán trước khi mở trình duyệt:

```css
p { color: blue; }
.note { color: green; }
#warn { color: red; }
```

```html
<p>1: màu gì?</p>
<p class="note">2: màu gì?</p>
<p class="note" id="warn">3: màu gì?</p>
<p class="note" style="color: purple">4: màu gì?</p>
```

Đáp án tự kiểm: 1-blue, 2-green, 3-red, 4-purple. Giải thích được bằng bảng specificity ở mục 5 là đạt.

---

## 10. Lỗi thường gặp

| Triệu chứng | Nguyên nhân | Cách xử lý |
|-------------|-------------|------------|
| CSS "không ăn" | Sai đường dẫn `href` / cache | F12 → Network xem `styles.css` có load 200 |
| Style bị đè khó hiểu | Rule khác specificity cao hơn | DevTools → Styles xem rule bị gạch |
| Lạm dụng `!important` | Chữa cháy specificity | Xóa đi, viết selector đúng cấp |
| Width vỡ khi thêm padding | Quên `border-box` | Reset `box-sizing` đầu file |
| Font chữ mỗi nơi một kiểu | Đặt font từng phần tử | Đặt ở `body`, để kế thừa |

---

## 11. Checklist trước khi sang buổi sau

- [ ] `styles.css` external, load thành công
- [ ] Giải thích được rule nào thắng trong ví dụ mục 9
- [ ] Vẽ được box model + giải thích `border-box`
- [ ] Dùng `rem` cho font/spacing, `%` cho bảng
- [ ] Có `:root` với `--cm-primary`, `--cm-bg`
- [ ] Đã làm xong [Phiếu 03](./worksheets/phieu-03-css-co-ban.md)

---

## 12. Bài tập trên lớp & về nhà (chuỗi CampusMart UI)

| Phần | Làm gì | Chi tiết |
|------|--------|----------|
| **Trên lớp (~20')** | Nối `styles.css`, tokens, style bảng SP | [Phiếu 03 — mục 2](./worksheets/phieu-03-css-co-ban.md) |
| **Về nhà (~25–40')** | Header/nav/form styling + zebra + hover | [Phiếu 03 — mục 3](./worksheets/phieu-03-css-co-ban.md) |

Nộp repo riêng `cse391-cm-03` + video OBS theo box cuối phiếu.

---

## 13. Cầu nối sang Buổi 4

Bạn đã style được **từng phần tử**. Nhưng xếp các phần tử **cạnh nhau** (nav ngang, lưới card sản phẩm) cần công cụ layout thật sự: **Flexbox** và **Grid** — chủ đề Buổi 4.

→ Tiếp: [Buổi 4](./04-buoi-04-css-flexbox-grid.md) · [Phiếu 04](./worksheets/phieu-04-flexbox-grid.md)

---

*CSE391 – Ebook Frontend CampusMart UI | Buổi 3/12*
