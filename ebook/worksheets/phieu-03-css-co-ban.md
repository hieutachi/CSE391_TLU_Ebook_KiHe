# Phiếu 03 — CSS cơ bản cho CampusMart

| | |
|---|---|
| **Buổi** | 3 |
| **Thời lượng** | ≥ 45 phút (trên lớp ~20' + về nhà ~25–40') |
| **Repo** | `cse391-cm-03` |
| **Nhận từ Phiếu 02** | Trang đủ nội dung: bảng 8 SP + form + media |
| **Mang sang Phiếu 04** | `styles.css` có tokens + bảng/form đã style |
| **Dataset** | [`CANONICAL-DATA.md`](./CANONICAL-DATA.md) — §8.1 tokens, §7 class bắt buộc |

### Chuẩn đầu ra (CLO)

1. Nối `styles.css` external; không dùng inline style.  
2. Khai báo đúng tokens `--cm-primary: #0f766e`, `--cm-bg: #f8fafc` trong `:root`.  
3. Reset `box-sizing: border-box` toàn cục.  
4. Style `.cm-table`: full-width, border-collapse, zebra rows, hover.  
5. Giải thích được (video) 1 tình huống cascade trong bài của mình.

---

## 1. Tóm tắt lý thuyết (đọc lại trước khi code)

- External stylesheet qua `<link>`; ưu tiên selector class `.cm-*`.
- Cascade: `!important` > specificity (inline > id > class > tag) > thứ tự.
- Box model: margin / border / padding / content; luôn `border-box`.
- Đơn vị: `rem` cho chữ + spacing, `%` cho bề rộng.
- Tokens trong `:root` + `var()`.

Chi tiết: [Buổi 3](../03-buoi-03-css-co-ban.md).

---

## 2. Bài trên lớp (~20 phút) — Tokens + bảng sản phẩm

### Yêu cầu

Copy trang từ repo Phiếu 02 sang `cse391-cm-03`, rồi:

1. Đổi `<title>` exact: **`CampusMart — CSS (Buoi 3)`**.
2. Tạo `styles.css` và `<link>` vào `<head>`. Đầu file:

```css
*, *::before, *::after { box-sizing: border-box; }

:root {
  --cm-primary: #0f766e;
  --cm-bg: #f8fafc;
}

body {
  margin: 0;
  font-family: system-ui, -apple-system, "Segoe UI", sans-serif;
  background: var(--cm-bg);
  color: #1f2937;
}
```

3. Style bảng sản phẩm (class `.cm-table` đã có từ P01):
   - `width: 100%`, `border-collapse: collapse`
   - `th`, `td`: padding `0.5rem 0.75rem`, viền dưới `1px solid #e2e8f0`
   - Hàng thead: nền `var(--cm-primary)`, chữ trắng

### Output kỳ vọng (trên lớp)

Bảng 8 SP có header màu thương hiệu, ô thoáng, chữ hệ thống — không còn "bảng thô".

**Checkpoint giảng viên:** DevTools → Elements: `styles.css` load 200; `:root` có đủ 2 token.

---

## 3. Bài về nhà (~25–40 phút) — Header, form, trạng thái tương tác

Tiếp tục **cùng repo**.

### Nhiệm vụ A — Header + nav

1. Header dùng class `.cm-header`: nền `var(--cm-primary)`, chữ trắng, padding `1rem`.
2. Nav dùng class `.cm-nav`: bỏ chấm đầu dòng (`list-style: none`), link trắng, bỏ gạch chân; `:hover` gạch chân trở lại.

> Buổi này nav vẫn xếp dọc — sang Phiếu 04 mới ép ngang bằng Flexbox. Đừng dùng `float`.

### Nhiệm vụ B — Zebra + hover cho bảng

```css
.cm-table tbody tr:nth-child(even) { background: #f1f5f9; }
.cm-table tbody tr:hover { background: #ccfbf1; }
```

Tự giải thích (chuẩn bị cho video): selector `tbody tr:hover` có specificity bao nhiêu? Nếu thêm `tr { background: white !important; }` chuyện gì xảy ra?

### Nhiệm vụ C — Form + focus

1. Input/select/textarea: `width: 100%` (tối đa `24rem`), padding `0.5rem`, viền `1px solid #cbd5e1`, bo góc `4px`.
2. `:focus`: outline `2px solid var(--cm-primary)` — **không** được `outline: none` chay.
3. Nút submit: nền `var(--cm-primary)`, chữ trắng, `:hover` tối hơn (tự chọn màu).

### Nhiệm vụ D — Marker

```html
<!-- CM_EXPECT product_count=8 inventory_value=41380000 css_external=true tokens=cm-primary,cm-bg -->
```

---

## 4. OUTPUT / EXPECT

| Key | Value bắt buộc |
|-----|----------------|
| `page_title` | `CampusMart — CSS (Buoi 3)` |
| `stylesheet` | `styles.css` external (không inline style attribute) |
| `css_contains.--cm-primary` | `#0f766e` |
| `css_contains.--cm-bg` | `#f8fafc` |
| `css_contains.box-sizing` | `border-box` |
| `css_contains..cm-table` | có rule `border-collapse` |
| `css_contains..cm-header` | nền dùng `var(--cm-primary)` |
| `focus_visible` | input `:focus` có outline nhìn thấy |
| Giữ từ P01–02 | 8 SKU, form, figure, landmark, testid |
| `CM_EXPECT` comment | như Nhiệm vụ D |

---

## 5. Box nộp bài

```
╔══════════════════════════════════════════════════════════════╗
║  NỘP PHIẾU 03                                                ║
╠══════════════════════════════════════════════════════════════╣
║  1. GitHub repo RIÊNG: cse391-cm-03 (index.html, styles.css) ║
║  2. Video OBS + camera mặt:                                  ║
║     - Bảng zebra + hover chạy thật                           ║
║     - DevTools Computed: chỉ box model 1 ô td                ║
║     - Đổi --cm-primary sang màu khác → cả trang đổi theo     ║
║  3. Thuyết trình 30–60s: giải thích 1 tình huống cascade     ║
╚══════════════════════════════════════════════════════════════╝
```

> **Nhắc:** Không dùng Bootstrap/Tailwind ở phiếu này — mục tiêu là hiểu cascade và box model bằng tay.
