# Phiếu 04 — Flexbox & Grid CampusMart

| | |
|---|---|
| **Buổi** | 4 |
| **Thời lượng** | ≥ 45 phút (trên lớp ~20' + về nhà ~25–40') |
| **Repo** | `cse391-cm-04` |
| **Nhận từ Phiếu 03** | `styles.css` tokens + bảng/form/header đã style |
| **Mang sang Phiếu 05** | Header flex, nav ngang, `.cm-grid` + ít nhất 2 card mẫu |
| **Dataset** | [`CANONICAL-DATA.md`](./CANONICAL-DATA.md) — §7 class layout, §8.2 title P04 |

### Chuẩn đầu ra (CLO)

1. `.cm-header` flex với `justify-content: space-between`.  
2. `.cm-nav` flex ngang + `gap`.  
3. `.cm-grid` = `repeat(auto-fill, minmax(220px, 1fr))`.  
4. **8** `.cm-card` đủ SKU + `data-sku`; category hiển thị **tên** danh mục (chữ).  
5. `data-testid="cm-product-table"` trên `.cm-grid` (duy nhất).  
6. Giữ CORE: 8 SP, `inventory_value=41380000`, testid form P02.

---

## 1. Tóm tắt lý thuyết (đọc lại trước khi code)

- Flexbox = layout **1 chiều**; trục chính / trục phụ (ASCII Buổi 4 mục 3.1).
- `justify-content: space-between` — logo trái, nav phải.
- Grid = lưới **2 chiều**; `.cm-grid` + `minmax(220px, 1fr)`.
- Giải phẫu `.cm-card`: figure, title, category, price.
- Event delegation preview: 1 listener trên `.cm-grid` (Buổi 7).
- **Cấm** `float` cho nav/card.

Chi tiết: [Buổi 4](../04-buoi-04-css-flexbox-grid.md).

---

## 2. Bài trên lớp (~20 phút) — Header flex + nav + grid mẫu

### Yêu cầu

Copy repo Phiếu 03 sang `cse391-cm-04`, rồi:

1. Đổi `<title>` exact: **`CampusMart — Layout (Buoi 4)`**.
2. Cập nhật `styles.css`:

```css
.cm-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  gap: 1rem;
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

.cm-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(220px, 1fr));
  gap: 1rem;
}
```

3. Thêm section `#products` (hoặc bổ sung trong section có sẵn):

```html
<div class="cm-grid" data-testid="cm-product-table">
  <article class="cm-card" data-sku="KB-01">
    <!-- figure + title + category + price — xem Buổi 4 mục 7 -->
  </article>
  <article class="cm-card" data-sku="KB-02">
    <!-- ... -->
  </article>
</div>
```

4. Trên lớp: tối thiểu **2 card** (KB-01, KB-02); category = `Ban phim` (không ghi `1`).

### Cách thử trên lớp

| # | Thử | Dự đoán | Quan sát |
|---|-----|---------|----------|
| T1 | Inspect `.cm-header` → Computed | `display: flex`, `justify-content: space-between` | DevTools |
| T2 | Inspect `ul` trong nav | `display: flex`, `flex-direction: row` | Badge **flex** |
| T3 | Inspect `.cm-grid` → Computed | `display: grid` | Badge **grid** |
| T4 | Search `styles.css` `minmax(220px` | Có trong rule `.cm-grid` | View file |
| T5 | Elements | `data-testid="cm-product-table"` trên `.cm-grid` | 1 lần duy nhất |
| T6 | Search `float` trong CSS | Không có | — |

**Nếu T2 fail:** quên `display:flex` trên `ul`, không phải chỉ trên `nav`.

### Output kỳ vọng (trên lớp)

Logo và nav **cùng hàng**; ít nhất 2 card trong lưới; grid co giãn khi thu hẹp cửa sổ.

**Checkpoint giảng viên:** header `space-between`; grid rule có `repeat(auto-fill, minmax(`.

---

## 3. Bài về nhà (~25–40 phút) — Đủ 8 card + CM_EXPECT

Tiếp tục **cùng repo** `cse391-cm-04`.

### Nhiệm vụ A — Hoàn thiện 8 `.cm-card`

Đủ SKU: KB-01 … MN-02 (bảng Buổi 4 mục 7.3). Mỗi card:

- `data-sku` exact  
- `.cm-card-title` = tên sản phẩm  
- `.cm-card-category` = **Ban phim** / **Chuot** / **Man hinh**  
- `.cm-card-price` = giá hiển thị (có thể format `1 890 000 d`)  
- `figure` + `alt` (giữ chuẩn P02)

#### Cách thử Nhiệm vụ A

| # | Thử | Kỳ vọng |
|---|-----|---------|
| A1 | View Source — đếm `class="cm-card"` | **8** |
| A2 | Đếm `data-sku=` | **8**, khớp CORE |
| A3 | Inspect card MS-01 | category text = `Chuot` |
| A4 | Tổng line_total CORE (tính tay) | **41380000** |

### Nhiệm vụ B — CSS card

Thêm style card (Buổi 4 mục 7.4): border, padding, hover nhẹ. Card có thể `display: flex; flex-direction: column` bên trong.

#### Cách thử Nhiệm vụ B

| # | Thử | Kỳ vọng |
|---|-----|---------|
| B1 | Thu hẹp cửa sổ ~400px | Card xuống dòng, không tràn ngang body |
| B2 | Hover 1 card | Box-shadow hoặc viền đổi (tùy CSS bạn) |

### Nhiệm vụ C — testid migration

- Gỡ `data-testid="cm-product-table"` khỏi `<table>` (nếu còn).  
- Chỉ giữ trên `.cm-grid`.

#### Cách thử Nhiệm vụ C

| # | Thử | Kỳ vọng |
|---|-----|---------|
| C1 | Search `cm-product-table` trong HTML | **1** kết quả |
| C2 | Element gắn testid | Là `div.cm-grid` |

### Nhiệm vụ D — Marker

```html
<!-- CM_EXPECT product_count=8 card_count=8 inventory_value=41380000 layout=flex-grid -->
```

#### Cách thử Nhiệm vụ D

View Source → có `card_count=8`, `layout=flex-grid`.

### Nhiệm vụ E — Video (chuẩn bị)

Quay ngắn: header flex; zoom `.cm-grid` Computed; click 1 card (nói sẽ dùng delegation Buổi 7).

---

## 4. OUTPUT / EXPECT

| Key | Value bắt buộc |
|-----|----------------|
| `page_title` | `CampusMart — Layout (Buoi 4)` |
| `css..cm-header` | `display: flex` + `space-between` |
| `css..cm-grid` | chứa `repeat(auto-fill, minmax(` |
| `card_count` | **8** (`.cm-card`) |
| `card.category` | tên DM **chữ**, không số thô |
| `testid.cm-product-table` | trên `.cm-grid`, duy nhất |
| Giữ từ P01–03 | 8 SKU bảng, form, tokens, `cm-subscribe-form` |
| `inventory_value` | **41380000** |
| `CM_EXPECT` | như Nhiệm vụ D |

### Checklist tự chấm trước nộp

```
[ ] title exact P04
[ ] header flex space-between
[ ] nav flex row + gap
[ ] cm-grid minmax(220px) auto-fill
[ ] 8 card + data-sku
[ ] category chữ (Ban phim / Chuot / Man hinh)
[ ] testid trên cm-grid only
[ ] không float layout
[ ] giữ form + tokens P03
[ ] CM_EXPECT đủ key
```

---

## 5. Box nộp bài

```
╔══════════════════════════════════════════════════════════════╗
║  NỘP PHIẾU 04                                                ║
╠══════════════════════════════════════════════════════════════╣
║  1. GitHub repo RIÊNG: cse391-cm-04 (index.html, styles.css)  ║
║  2. Video OBS + camera mặt:                                  ║
║     - Header: logo trái, nav phải (space-between)            ║
║     - DevTools: .cm-grid Computed → grid + minmax(220px)      ║
║     - Lưới 8 card scroll qua từng SKU                        ║
║     - (Tùy chọn) Giải thích event delegation 30s             ║
╚══════════════════════════════════════════════════════════════╝
```

> **Nhắc:** Không Bootstrap/Tailwind — mục tiêu hiểu flex/grid bằng CSS thuần.

---

## 6. Lỗi thường gặp khi nộp (tránh)

| Lỗi | Hậu quả | Cách tránh |
|-----|---------|------------|
| Category ghi `1` | card.category fail | Dùng `Ban phim` |
| testid vẫn trên table | testid fail | Chuyển lên `.cm-grid` |
| Thiếu card | card_count fail | Đủ 8 SKU |
| Grid cố định 4 cột | thiếu minmax P04 | Dùng auto-fill minmax — P05 mới query |
| Float nav | trừ điểm layout | Flex only |
| Xóa form P02 | mất EXPECT | Chỉ thêm grid |

---

*CSE391 – Phiếu 04 | CampusMart UI*
