# Phiếu 04 — Flexbox & Grid: nav ngang + lưới card CampusMart

| | |
|---|---|
| **Buổi** | 4 |
| **Thời lượng** | ≥ 45 phút (trên lớp ~20' + về nhà ~25–40') |
| **Repo** | `cse391-cm-04` |
| **Nhận từ Phiếu 03** | `styles.css` tokens + bảng/form đã style |
| **Mang sang Phiếu 05** | Header flex + `.cm-grid` 8 card |
| **Dataset** | [`CANONICAL-DATA.md`](./CANONICAL-DATA.md) — §7 class, §8.2 title |

### Chuẩn đầu ra (CLO)

1. Header `.cm-header` dùng flex `space-between` (brand trái, nav phải).  
2. Nav `.cm-nav` xếp ngang bằng flex + `gap` (không float, không inline-block).  
3. Dựng `.cm-grid` bằng `repeat(auto-fill, minmax(220px, 1fr))`.  
4. 8 card `.cm-card` đủ: ảnh, tên, danh mục chữ, giá, qty + `data-sku`.  
5. Giải thích được (video) trục chính/phụ trên chính layout của mình.

---

## 1. Tóm tắt lý thuyết (đọc lại trước khi code)

- Flex: 1 chiều; property đặt trên **container**; `justify-content` = trục chính, `align-items` = trục phụ; dùng `gap`.
- Grid: 2 chiều; `grid-template-columns: repeat(auto-fill, minmax(220px, 1fr))`.
- Card giữ `data-testid="cm-product-row"` + `data-sku` như dòng bảng.

Chi tiết: [Buổi 4](../04-buoi-04-css-flexbox-grid.md).

---

## 2. Bài trên lớp (~20 phút) — Header + nav ngang

### Yêu cầu

Copy từ repo Phiếu 03 sang `cse391-cm-04`, rồi:

1. Đổi `<title>` exact: **`CampusMart — Layout (Buoi 4)`**.
2. `.cm-header`: `display: flex; justify-content: space-between; align-items: center;`.
3. `.cm-nav ul`: `display: flex; gap: 1.5rem; list-style: none; margin: 0; padding: 0;`.
4. Link nav giữ đủ 4 nhãn (Trang chu / San pham / Gioi thieu / Dang ky).

### Output kỳ vọng (trên lớp)

```
┌────────────────────────────────────────────────────────┐
│ CampusMart          Trang chu  San pham  Gioi thieu  Dang ky │
└────────────────────────────────────────────────────────┘
```

**Checkpoint giảng viên:** DevTools hiện badge `flex` trên header và `ul`; nav ngang, canh giữa dọc.

---

## 3. Bài về nhà (~25–40 phút) — Lưới card 8 sản phẩm

Tiếp tục **cùng repo**.

### Nhiệm vụ A — Section card grid

Thêm (không xóa bảng cũ — bảng chuyển xuống dưới thành "phụ lục dữ liệu"):

```html
<section id="catalog" aria-labelledby="catalog-heading">
  <h2 id="catalog-heading">Catalog</h2>
  <div class="cm-grid" data-testid="cm-product-table">
    <!-- 8 card, mỗi card: -->
    <article class="cm-card" data-testid="cm-product-row" data-sku="KB-01">
      ...
    </article>
  </div>
</section>
```

> Từ phiếu này, `data-testid="cm-product-table"` gắn trên **container lưới** (bảng phụ lục bỏ testid đi để không đếm trùng).

Mỗi card đủ: ảnh (placeholder được), `h3` tên, dòng danh mục chữ (`.cm-card-cat`), giá (`.cm-card-price`, số thô ví dụ `1890000`), `So luong: N`.

Dữ liệu đúng 8 SP CORE trong [CANONICAL-DATA §2](./CANONICAL-DATA.md).

### Nhiệm vụ B — Grid + card styles

1. `.cm-grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(220px, 1fr)); gap: 1rem; }`
2. `.cm-card`: nền trắng, viền `1px solid #e2e8f0`, bo `8px`, padding `1rem`, bên trong `flex-direction: column; gap: 0.5rem`.
3. Giá: đậm + màu `var(--cm-primary)`.

### Nhiệm vụ C — Footer flex

Footer 2 phần: trái `CSE391 — CampusMart UI`, phải link `Len dau trang` (`#home`) — dùng flex `space-between`.

### Nhiệm vụ D — Marker

```html
<!-- CM_EXPECT product_count=8 card_count=8 grid=cm-grid inventory_value=41380000 -->
```

---

## 4. OUTPUT / EXPECT

| Key | Value bắt buộc |
|-----|----------------|
| `page_title` | `CampusMart — Layout (Buoi 4)` |
| `css_contains..cm-header` | `display: flex` + `space-between` |
| `css_contains..cm-grid` | `repeat(auto-fill, minmax(` |
| `card_count` (`.cm-card`) | 8 |
| `cards.data-sku` | đủ 8 SKU CORE |
| `card.category_text` | chữ (Ban phim/Chuot/Man hinh) — không số thô |
| `testid.cm-product-table` | trên container `.cm-grid` (duy nhất 1) |
| Giữ từ P01–03 | tokens, form, landmark |
| `CM_EXPECT` comment | như Nhiệm vụ D |

---

## 5. Box nộp bài

```
╔══════════════════════════════════════════════════════════════╗
║  NỘP PHIẾU 04                                                ║
╠══════════════════════════════════════════════════════════════╣
║  1. GitHub repo RIÊNG: cse391-cm-04                          ║
║  2. Video OBS + camera mặt:                                  ║
║     - Kéo hẹp cửa sổ: lưới tự giảm số cột (auto-fill)        ║
║     - DevTools badge grid trên .cm-grid                      ║
║     - Đếm 8 card + chỉ data-sku trên 1 card                  ║
║  3. Thuyết trình 30–60s: trục chính/phụ của header là gì?    ║
╚══════════════════════════════════════════════════════════════╝
```
