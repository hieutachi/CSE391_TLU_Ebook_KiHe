# Phiếu 06 — JavaScript nền: dữ liệu & chỉ số CampusMart

| | |
|---|---|
| **Buổi** | 6 |
| **Thời lượng** | ≥ 45 phút (trên lớp ~20' + về nhà ~25–40') |
| **Repo** | `cse391-cm-06` |
| **Nhận từ Phiếu 05** | Dashboard tĩnh hoàn chỉnh — 8 card HTML, hero, responsive |
| **Mang sang Phiếu 07** | `data.js` + `app.js` với 5 hàm helper + stats sống |
| **Dataset** | [`CANONICAL-DATA.md`](../worksheets/CANONICAL-DATA.md) — §3 `stockLevel`, §5 mảng 8 SP, tổng **41380000** |

### Chuẩn đầu ra (CLO)

1. Tách `data.js` (mảng canonical) và `app.js` (logic DOM + hàm).  
2. Viết đúng **5 hàm**: `lineTotal`, `inventoryValue`, `stockLevel`, `findProductBySku`, `countByCategory`.  
3. `stockLevel(qty)` trả chuỗi **exact** `Du` / `Sap het` / `Can nhap` — khớp bảng 8 SKU CANONICAL §3.  
4. Đổ số liệu sống vào DOM: `So san pham = 8`, `Tong gia tri kho = 41380000` (tính từ mảng, không gõ cứng).  
5. In báo cáo 3 danh mục Console: count **3/3/2**, value **17620000 / 8860000 / 14900000**.  
6. Gắn badge `.cm-stock` trên 8 card — chứng minh **single source of truth** khi sửa `data.js` + reload.

---

## 1. Tóm tắt lý thuyết (đọc lại trước khi code)

- **Tách DATA vs DOM:** mảng `products` là nguồn sự thật; HTML chỉ là khung hiển thị.
- `const` mặc định; template literal `` `${n} VND` ``; so sánh luôn dùng `===`.
- Bộ tứ mảng: `filter` lọc · `map` biến đổi · `reduce` gộp · `find` tìm một phần tử.
- Nhúng script **cuối `<body>`**: `data.js` trước, `app.js` sau (chưa dùng module ở phiếu này).
- Ghi DOM an toàn: **`textContent`** (không `innerHTML` cho dữ liệu từ mảng).
- `querySelector` / `querySelectorAll`; đọc `data-sku` qua `element.dataset.sku`.

Chi tiết + bảng 8 SKU: [Buổi 6](../06-buoi-06-javascript-nen.md).

---

## 2. Bài trên lớp (~20 phút) — `data.js` + 3 hàm + Console

### Yêu cầu

Copy repo Phiếu 05 sang `cse391-cm-06`, rồi:

1. Đổi `<title>` exact: **`CampusMart — JS (Buoi 6)`**.
2. Tạo `data.js` — **không** dùng `export` (module từ P08):

```js
const categories = [ /* 3 danh mục — copy CANONICAL §5 */ ];
const products = [ /* 8 sản phẩm — copy CANONICAL §5 */ ];
```

3. Tạo `app.js` với 3 hàm đầu:

```js
function lineTotal(p) {
  return p.price * p.qty;
}

function inventoryValue(list) {
  return list.reduce((sum, p) => sum + lineTotal(p), 0);
}

function stockLevel(qty) {
  if (qty >= 5) return "Du";
  if (qty >= 2) return "Sap het";
  return "Can nhap";
}

console.log(inventoryValue(products)); // PHẢI ra 41380000
console.log(stockLevel(10), stockLevel(3), stockLevel(1)); // Du Sap het Can nhap
```

4. Cuối `<body>`:

```html
<script src="data.js"></script>
<script src="app.js"></script>
```

### Cách thử trên lớp (DevTools Console)

| # | Thử | Thao tác | Kỳ vọng |
|---|-----|----------|---------|
| T1 | Mở Console | F12 → tab **Console** | Không lỗi đỏ |
| T2 | Tổng kho | Đọc dòng log đầu | Số **`41380000`** |
| T3 | stockLevel | Đọc dòng log thứ hai | **`Du` `Sap het` `Can nhap`** (3 từ, đúng thứ tự) |
| T4 | Gõ tay | `lineTotal(products[0])` + Enter | **`5670000`** (KB-01) |
| T5 | Gõ tay | `products.length` + Enter | **`8`** |
| T6 | Network | Reload → tab Network | `data.js` và `app.js` load 200 |

**Nếu T2 sai:** đối chiếu từng dòng `price × qty` với [CANONICAL §5](../worksheets/CANONICAL-DATA.md) — thường nhầm một SKU.

### Output kỳ vọng (trên lớp)

Console in `41380000` và `Du Sap het Can nhap` — không lỗi đỏ. Trang vẫn giống P05 (8 card tĩnh).

**Checkpoint giảng viên:** SV mở Console thấy đúng 2 dòng log; GV xem `data.js` đủ 8 SKU canonical.

---

## 3. Bài về nhà (~25–40 phút) — Stats sống + báo cáo + badge

Tiếp tục **cùng repo** `cse391-cm-06`.

### Nhiệm vụ A — Hai hàm còn lại

```js
function findProductBySku(list, sku) {
  return list.find((p) => p.sku === sku);
}

function countByCategory(list, categoryId) {
  return list.filter((p) => p.category_id === categoryId).length;
}
```

#### Cách thử chi tiết — Nhiệm vụ A

1. Reload trang (Ctrl+R / Cmd+R).
2. Console → gõ từng dòng, Enter sau mỗi dòng:

```js
findProductBySku(products, "MN-02").name
// Kỳ vọng: "LG UltraFine"

countByCategory(products, 1)
// Kỳ vọng: 3  (Ban phim)

countByCategory(products, 2)
// Kỳ vọng: 3  (Chuot)

countByCategory(products, 3)
// Kỳ vọng: 2  (Man hinh)

findProductBySku(products, "XX-99")
// Kỳ vọng: undefined (không nổ lỗi)
```

3. **Checkpoint A:** 4 lệnh đầu trả đúng số/chuỗi; không có `ReferenceError: products is not defined`.

---

### Nhiệm vụ B — Thanh stats sống trên trang

1. Thêm HTML (dưới hero hoặc trên lưới card):

```html
<section id="stats" data-testid="cm-stats" aria-live="polite"></section>
```

2. Trong `app.js`, sau các hàm helper:

```js
function updateStats() {
  const el = document.querySelector("#stats");
  if (!el) return;
  const total = inventoryValue(products);
  el.textContent =
    `So san pham = ${products.length}\nTong gia tri kho = ${total}`;
}

updateStats();
```

Hai số phải **tính từ mảng** — cấm gõ cứng `8` hoặc `41380000` trong chuỗi literal.

#### Cách thử chi tiết — Nhiệm vụ B

1. Reload trang.
2. DevTools → tab **Elements** → tìm `#stats` (Ctrl+F gõ `cm-stats`).
3. Trong panel Elements, đọc `textContent` của `#stats`:

```
So san pham = 8
Tong gia tri kho = 41380000
```

4. Console:

```js
document.querySelector("#stats").textContent.includes("41380000")
// true

document.querySelector("#stats").textContent.includes("So san pham = 8")
// true
```

5. **Thử sửa data (mốc quan trọng):**
   - Mở `data.js` → đổi `qty` của **KB-01** từ `3` → `10`.
   - Save → reload trang.
   - `#stats` phải đổi: tổng kho = **`41380000 + 7×1890000`** = **`42703000`** (hoặc tính lại bằng `inventoryValue(products)` trên Console).
   - **Đổi lại `qty: 3`** trước khi nộp — autograder cần **41380000**.

6. Quay video bước 5 — đây là bằng chứng “dữ liệu tách khỏi giao diện”.

---

### Nhiệm vụ C — Báo cáo 3 danh mục (Console)

In bảng 3 dòng — khuyến khích `console.table`:

| Danh muc | count | value |
|----------|------:|------:|
| Ban phim | 3 | **17620000** |
| Chuot | 3 | **8860000** |
| Man hinh | 2 | **14900000** |

Gợi ý code:

```js
categories.forEach((cat) => {
  const subset = products.filter((p) => p.category_id === cat.id);
  console.log(cat.name, subset.length, inventoryValue(subset));
});
```

Hoặc một lần `console.table(rows)` với `rows = categories.map(...)`.

#### Cách thử chi tiết — Nhiệm vụ C

1. Reload → chạy hàm báo cáo (hoặc để log tự chạy cuối `app.js`).
2. Console phải hiện **đúng 3 dòng** (hoặc bảng 3 hàng):

| Kiểm | count | value |
|------|------:|------:|
| Ban phim | 3 | 17620000 |
| Chuot | 3 | 8860000 |
| Man hinh | 2 | 14900000 |

3. Tự kiểm chéo:

```js
inventoryValue(products.filter(p => p.category_id === 1))
// 17620000

inventoryValue(products.filter(p => p.category_id === 2))
// 8860000

inventoryValue(products.filter(p => p.category_id === 3))
// 14900000

17620000 + 8860000 + 14900000
// 41380000  ← tổng 3 DM = tổng kho
```

4. **Checkpoint C:** tổng 3 cột `value` cộng lại = `inventoryValue(products)`.

---

### Nhiệm vụ D — Badge tồn kho trên card

Với mỗi `.cm-card` hiện có (HTML P05), thêm badge bằng JS:

```js
function attachStockBadges() {
  document.querySelectorAll(".cm-card").forEach((card) => {
    const p = findProductBySku(products, card.dataset.sku);
    if (!p) return;
    const badge = document.createElement("p");
    badge.className = "cm-stock";
    badge.textContent = stockLevel(p.qty);
    card.appendChild(badge);
  });
}

attachStockBadges();
```

**Bảng stockLevel bắt buộc — copy exact (một space sai → autograder trượt):**

| sku | qty | badge text |
|-----|----:|------------|
| KB-01 | 3 | Sap het |
| KB-02 | 5 | Du |
| KB-03 | 2 | Sap het |
| MS-01 | 10 | Du |
| MS-02 | 4 | Sap het |
| MS-03 | 8 | Du |
| MN-01 | 2 | Sap het |
| MN-02 | 1 | Can nhap |

#### Cách thử chi tiết — Nhiệm vụ D

1. Reload trang.
2. Elements → chọn card `data-sku="KB-01"` → tìm con `.cm-stock` → text **`Sap het`**.
3. Lặp nhanh 3 card đại diện:

| data-sku | Kỳ vọng `.cm-stock` |
|----------|---------------------|
| KB-02 | Du |
| MN-02 | Can nhap |
| MS-03 | Du |

4. Console đếm badge:

```js
document.querySelectorAll(".cm-stock").length
// 8
```

5. Console đối chiếu từng SKU (copy-paste):

```js
products.forEach((p) => {
  const card = document.querySelector(`.cm-card[data-sku="${p.sku}"]`);
  const badge = card?.querySelector(".cm-stock")?.textContent;
  console.log(p.sku, badge, badge === stockLevel(p.qty) ? "OK" : "FAIL");
});
// 8 dòng cuối đều "OK"
```

6. **Thử reload sau sửa qty:**
   - `data.js`: đổi **MN-02** `qty` từ `1` → `5`.
   - Reload → badge MN-02 phải đổi **`Can nhap` → `Du`** (không sửa HTML card).
   - Đổi lại `qty: 1` trước nộp.

---

### Nhiệm vụ E — Marker

```html
<!-- CM_EXPECT product_count=8 inventory_value=41380000 js_files=data.js,app.js -->
```

Đặt comment trước `</body>` hoặc cuối `<main>`.

---

## 4. OUTPUT / EXPECT

| Key | Value bắt buộc |
|-----|----------------|
| `page_title` | `CampusMart — JS (Buoi 6)` |
| `files` | `data.js`, `app.js` tách riêng; **không** `type="module"` |
| `inventoryValue(products)` | **41380000** |
| `stockLevel(10)/(3)/(1)` | `Du` / `Sap het` / `Can nhap` (exact) |
| `findProductBySku("MN-02").name` | `LG UltraFine` |
| `countByCategory(1/2/3)` | **3 / 3 / 2** |
| `category.value` | Ban phim **17620000** · Chuot **8860000** · Man hinh **14900000** |
| `stats.text` | chứa `So san pham = 8` và `Tong gia tri kho = 41380000` |
| `badge.cm-stock` | **8** badge, đúng bảng stockLevel §3 |
| `CM_EXPECT` | như Nhiệm vụ E |

### Checklist tự chấm trước nộp

```
[ ] title exact P06
[ ] data.js 8 SP + 3 DM khớp CANONICAL
[ ] 5 hàm: lineTotal, inventoryValue, stockLevel, findProductBySku, countByCategory
[ ] Console: 41380000 + stockLevel 3 mức
[ ] Console/table: 3 DM count + value đúng
[ ] #stats sống — sửa qty reload → số đổi
[ ] 8 badge .cm-stock đúng bảng 8 SKU
[ ] CM_EXPECT comment
[ ] Video: Console báo cáo + stats + badge + demo sửa data.js
```

---

## 5. Box nộp bài

```
╔══════════════════════════════════════════════════════════════╗
║  NỘP PHIẾU 06                                                ║
╠══════════════════════════════════════════════════════════════╣
║  1. GitHub repo RIÊNG: cse391-cm-06                          ║
║  2. Video OBS + camera mặt — kịch bản:                       ║
║     - Console: 41380000 + báo cáo 3 danh mục (3/3/2)       ║
║     - Trang: #stats So san pham=8 · Tong=41380000            ║
║     - 8 card có badge Du / Sap het / Can nhap đúng bảng      ║
║     - Sửa qty KB-01 trong data.js → reload → stats+badge đổi ║
║  3. Thuyết trình 30–60s: reduce trong inventoryValue làm gì? ║
╚══════════════════════════════════════════════════════════════╝
```

---

## 6. Lỗi thường gặp khi nộp (tránh)

| Lỗi | Triệu chứng | Cách xử lý |
|-----|-------------|------------|
| Script sai thứ tự | `products is not defined` | `data.js` trước `app.js` |
| Tổng ≠ 41380000 | Console log sai | Đối chiếu CANONICAL §5 từng SKU |
| stockLevel fail | Autograder trượt 1 SKU | Copy exact 3 chuỗi, không thừa space |
| Stats gõ cứng | Sửa data không đổi số | Dùng `products.length` + `inventoryValue()` |
| Thiếu `data-sku` trên card | Badge không gắn | Giữ attribute từ P04/P05 |
| Gọi `innerHTML` cho stats | XSS risk / autograder | Dùng `textContent` |
| Quên đổi lại qty sau thử | Nộp sai tổng | Reset CANONICAL trước push |

---

*CSE391 – Phiếu 06 | CampusMart UI — JS nền*
