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

- **3 nơi CSS:** inline / internal / **external (bắt buộc P03)**.
- Cascade: `!important` > specificity (inline > id > class > tag) > thứ tự viết sau.
- Specificity — **tính tay:** `.cm-table tbody tr:hover` → (0,3,2).
- Box model: margin / border / padding / content; `border-box` reset đầu file.
- Đơn vị: `rem` spacing/font, `%` width bảng, `px` border.
- Tokens `:root` + `var(--cm-primary)`.
- Demo 4 đoạn `<p>`: blue / green / red / purple — hiểu vì sao.

Chi tiết: [Buổi 3](../03-buoi-03-css-co-ban.md).

---

## 2. Bài trên lớp (~20 phút) — Tokens + bảng sản phẩm

### Yêu cầu

Copy trang từ repo Phiếu 02 sang `cse391-cm-03`, rồi:

1. Đổi `<title>` exact: **`CampusMart — CSS (Buoi 3)`**.
2. Tạo `styles.css` và `<link rel="stylesheet" href="styles.css" />` trong `<head>`. Đầu file:

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

### Cách thử trên lớp

| # | Thử | Dự đoán | Quan sát |
|---|-----|---------|----------|
| T1 | F12 → Network → reload | `styles.css` status **200** | Tab Network |
| T2 | Elements → `<html>` → Computed | `background` ≈ `#f8fafc` | body dùng `--cm-bg` |
| T3 | Inspect thead `<th>` | Nền `#0f766e` | var(--cm-primary) |
| T4 | View Source | **Không** có `style="..."` trên thẻ HTML | css_external |
| T5 | Đổi tạm `--cm-primary: #b91c1c` → reload | Thead đỏ | token hoạt động |

**Nếu T1 = 404:** kiểm tra `styles.css` cùng thư mục `index.html` và `href` đúng tên file.

### Output kỳ vọng (trên lớp)

Bảng 8 SP có header màu thương hiệu, ô thoáng, nền trang `#f8fafc` — không còn "bảng thô".

**Checkpoint giảng viên:** DevTools → Network: `styles.css` 200; `:root` có đủ 2 token.

---

## 3. Bài về nhà (~25–40 phút) — Header, form, trạng thái tương tác

Tiếp tục **cùng repo** `cse391-cm-03`.

### Nhiệm vụ A — Header + nav

1. Bọc header (hoặc thêm class) dùng `.cm-header`: nền `var(--cm-primary)`, chữ trắng, padding `1rem`.
2. Nav dùng class `.cm-nav`: bỏ chấm đầu dòng (`list-style: none`), link trắng, bỏ gạch chân; `:hover` gạch chân trở lại.

> Buổi này nav vẫn xếp dọc — sang Phiếu 04 mới ép ngang bằng Flexbox. **Đừng dùng `float`.**

#### Cách thử Nhiệm vụ A

| # | Thử | Kỳ vọng |
|---|-----|---------|
| A1 | Inspect `.cm-header` | `background-color` từ `var(--cm-primary)` |
| A2 | Hover link nav | Gạch chân xuất hiện |
| A3 | Search `float` trong `styles.css` | Không có |

### Nhiệm vụ B — Zebra + hover cho bảng

```css
.cm-table tbody tr:nth-child(even) {
  background: #f1f5f9;
}

.cm-table tbody tr:hover {
  background: #ccfbf1;
}
```

#### Cách thử Nhiệm vụ B

| # | Thử | Kỳ vọng |
|---|-----|---------|
| B1 | Di chuột lần lượt 8 dòng | Hover màu `#ccfbf1` (hoặc tương tự) |
| B2 | Dòng chẵn (2,4,6,8) | Nền zebra khác dòng lẻ |
| B3 | DevTools → chọn `tr` → Styles | Rule `:hover` không bị gạch (trừ khi cố !important) |

**Chuẩn bị video — trả lời miệng:**

1. Selector `tbody tr:hover` specificity = **(0, 2, 2)** hay **(0, 3, 2)** nếu viết `.cm-table tbody tr:hover`?
2. Nếu thêm `tr { background: white !important; }` thì hover còn không?

*(Đáp: (0,3,2); !important làm hover mất.)*

### Nhiệm vụ C — Form + focus

1. Input/select/textarea: `width: 100%` (tối đa `24rem`), padding `0.5rem`, viền `1px solid #cbd5e1`, bo góc `4px`.
2. `:focus`: `outline: 2px solid var(--cm-primary)` — **không** được `outline: none` trơ.
3. Nút submit: nền `var(--cm-primary)`, chữ trắng, `:hover` tối hơn (tự chọn màu).

#### Cách thử Nhiệm vụ C

| # | Thử | Kỳ vọng |
|---|-----|---------|
| C1 | Tab vào ô Email | Thấy outline xanh `#0f766e` |
| C2 | Search `outline: none` | Không có trên input/select |
| C3 | Hover nút Dang ky | Màu đậm hơn |

### Nhiệm vụ D — Box model (video bắt buộc quay)

1. DevTools → chọn một `<td>`.
2. Tab **Computed** → sơ đồ box model.
3. Chỉ ra: content / padding / border / margin.

#### Cách thử border-box

| # | Thử | Kỳ vọng |
|---|-----|---------|
| D1 | Computed → `box-sizing` | `border-box` |
| D2 | (Tùy chọn) demo `demo-box.html` Buổi 3 mục 10 | Hiểu width 200 + padding |

### Nhiệm vụ E — Marker

```html
<!-- CM_EXPECT product_count=8 inventory_value=41380000 css_external=true tokens=cm-primary,cm-bg -->
```

#### Cách thử Nhiệm vụ E

View Source → `CM_EXPECT` có `css_external=true` và `tokens=cm-primary,cm-bg`.

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
| `CM_EXPECT` comment | như Nhiệm vụ E |

### Checklist tự chấm trước nộp

```
[ ] title exact P03
[ ] styles.css + link 200
[ ] :root 2 token exact
[ ] border-box global
[ ] .cm-table + thead + zebra + hover
[ ] .cm-header + .cm-nav
[ ] input :focus nhìn thấy
[ ] Không inline style
[ ] Giữ 8 SP, form, figure P02
[ ] CM_EXPECT đủ key
```

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
║     - Tab vào input → thấy outline focus                     ║
║     - Đổi --cm-primary sang màu khác → cả trang đổi theo     ║
║  3. Thuyết trình 30–60s: giải thích 1 tình huống cascade     ║
║     (VD: vì sao thead thắng tr, hoặc demo 4 đoạn p)          ║
╚══════════════════════════════════════════════════════════════╝
```

> **Nhắc:** Không dùng Bootstrap/Tailwind ở phiếu này — mục tiêu là hiểu cascade và box model bằng tay.

---

## 6. Gợi ý câu trả lời cascade (video)

Chọn **một** tình huống trong bài của bạn:

| Tình huống | Giải thích gợi ý |
|------------|------------------|
| thead màu primary | `.cm-table thead` specificity (0,1,1) thắng `td` (0,0,1) |
| hover thắng zebra | `:hover` cùng specificity với nth-child — rule sau hoặc pseudo cụ thể hơn |
| Demo 4 đoạn p | id thắng class; inline thắng stylesheet |

Nói trong **30–60 giây**: selector nào thắng, specificity bao nhiêu, màu cuối cùng.

---

## 7. Lỗi thường gặp khi nộp (tránh)

| Lỗi | Hậu quả | Cách tránh |
|-----|---------|------------|
| CSS trong `<style>` | css_external fail | Chuyển hết sang file |
| Token sai hex | autograder trừ | Copy CANONICAL §8.1 |
| Quên link CSS | Trang trắng xấu | Network 200 |
| outline: none | focus_visible fail | `:focus { outline: 2px solid … }` |
| Xóa form/figure P02 | mất EXPECT | Chỉ thêm CSS |

---

*CSE391 – Phiếu 03 | CampusMart UI*
