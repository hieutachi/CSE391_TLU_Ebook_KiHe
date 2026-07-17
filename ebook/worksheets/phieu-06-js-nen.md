# Phiếu 06 — JavaScript nền: dữ liệu & chỉ số CampusMart

| | |
|---|---|
| **Buổi** | 6 |
| **Thời lượng** | ≥ 45 phút (trên lớp ~20' + về nhà ~25–40') |
| **Repo** | `cse391-cm-06` |
| **Nhận từ Phiếu 05** | Dashboard tĩnh hoàn chỉnh |
| **Mang sang Phiếu 07** | `data.js` + `app.js` với các hàm helper |
| **Dataset** | [`CANONICAL-DATA.md`](./CANONICAL-DATA.md) — §5 mảng JS, §3 stockLevel |

### Chuẩn đầu ra (CLO)

1. Tách `data.js` (mảng canonical) và `app.js` (logic).  
2. Viết đúng 5 hàm: `lineTotal`, `inventoryValue`, `stockLevel`, `findProductBySku`, `countByCategory`.  
3. `stockLevel` trả chuỗi exact `Du` / `Sap het` / `Can nhap`.  
4. Đổ số liệu sống vào DOM: `So san pham = 8`, `Tong gia tri kho = 41380000`.  
5. In báo cáo 3 danh mục đúng count/value theo CANONICAL §3.

---

## 1. Tóm tắt lý thuyết (đọc lại trước khi code)

- `const` mặc định; template literal; luôn `===`.
- `filter` lọc, `map` biến đổi, `reduce` gộp, `find` tìm một.
- Script cuối body; console trước, DOM sau.
- `textContent` để ghi (an toàn); `querySelector` dùng CSS selector.

Chi tiết: [Buổi 6](../06-buoi-06-javascript-nen.md).

---

## 2. Bài trên lớp (~20 phút) — data.js + hàm + Console

### Yêu cầu

Copy từ repo Phiếu 05 sang `cse391-cm-06`, rồi:

1. Đổi `<title>` exact: **`CampusMart — JS (Buoi 6)`**.
2. Tạo `data.js`: khai báo `categories` (3) + `products` (8) **đúng nguyên văn** [CANONICAL-DATA §5](./CANONICAL-DATA.md) (bỏ từ khóa `export` — phiếu này chưa dùng module):

```js
const categories = [ /* 3 danh mục canonical */ ];
const products = [ /* 8 sản phẩm canonical */ ];
```

3. Tạo `app.js` với 3 hàm đầu + kiểm tra Console:

```js
function lineTotal(p) { /* price * qty */ }
function inventoryValue(list) { /* reduce về tổng lineTotal */ }
function stockLevel(qty) { /* Du | Sap het | Can nhap */ }

console.log(inventoryValue(products)); // PHẢI ra 41380000
console.log(stockLevel(10), stockLevel(3), stockLevel(1)); // Du Sap het Can nhap
```

4. Nhúng 2 script cuối `<body>` (đúng thứ tự: data trước, app sau).

### Output kỳ vọng (trên lớp)

Console in `41380000` và `Du Sap het Can nhap` — không lỗi đỏ.

**Checkpoint giảng viên:** mở Console thấy đúng 2 dòng trên; xem `data.js` đúng 8 SKU.

---

## 3. Bài về nhà (~25–40 phút) — Stats sống + báo cáo danh mục

Tiếp tục **cùng repo**.

### Nhiệm vụ A — Hai hàm còn lại

```js
function findProductBySku(list, sku) { /* find */ }
function countByCategory(list, categoryId) { /* filter + length */ }
```

Kiểm Console:

```js
console.log(findProductBySku(products, "MN-02").name); // "LG UltraFine"
console.log(countByCategory(products, 2));             // 3
```

### Nhiệm vụ B — Thanh stats sống trên trang

1. Thêm vào HTML (dưới hero):

```html
<section id="stats" data-testid="cm-inventory-total"></section>
```

2. `app.js` ghi 2 dòng bằng DOM (dùng `textContent` / tạo 2 thẻ `p`):

```
So san pham = 8
Tong gia tri kho = 41380000
```

Hai số này phải **tính từ mảng** (`products.length`, `inventoryValue(products)`) — không gõ cứng.

### Nhiệm vụ C — Báo cáo 3 danh mục (Console)

In ra Console dạng bảng (`console.table` khuyến khích):

| Danh muc | count | value |
|----------|------:|------:|
| Ban phim | 3 | 17620000 |
| Chuot | 3 | 8860000 |
| Man hinh | 2 | 14900000 |

Gợi ý: lặp `categories`, với mỗi category dùng `filter` + `inventoryValue` trên mảng con.

### Nhiệm vụ D — Badge tồn kho trên card (tĩnh → nửa sống)

Với mỗi card hiện có, thêm JS: query tất cả `.cm-card`, đọc `data-sku`, tìm product tương ứng, gắn thêm dòng `stockLevel` bằng `textContent`:

```js
document.querySelectorAll(".cm-card").forEach((card) => {
  const p = findProductBySku(products, card.dataset.sku);
  const badge = document.createElement("p");
  badge.className = "cm-stock";
  badge.textContent = stockLevel(p.qty); // "Du" | "Sap het" | "Can nhap"
  card.appendChild(badge);
});
```

### Nhiệm vụ E — Marker

```html
<!-- CM_EXPECT product_count=8 inventory_value=41380000 js_files=data.js,app.js -->
```

---

## 4. OUTPUT / EXPECT

| Key | Value bắt buộc |
|-----|----------------|
| `page_title` | `CampusMart — JS (Buoi 6)` |
| `files` | `data.js`, `app.js` tách riêng |
| `inventoryValue(products)` | 41380000 |
| `stockLevel(10)/(3)/(1)` | `Du` / `Sap het` / `Can nhap` (exact) |
| `findProductBySku("MN-02").name` | `LG UltraFine` |
| `countByCategory(2)` | 3 |
| `stats.text` | chứa `So san pham = 8` và `Tong gia tri kho = 41380000` |
| `badge.cm-stock` | 8 badge, đúng bảng stockLevel CANONICAL §3 |
| `CM_EXPECT` comment | như Nhiệm vụ E |

---

## 5. Box nộp bài

```
╔══════════════════════════════════════════════════════════════╗
║  NỘP PHIẾU 06                                                ║
╠══════════════════════════════════════════════════════════════╣
║  1. GitHub repo RIÊNG: cse391-cm-06                          ║
║  2. Video OBS + camera mặt:                                  ║
║     - Console: 41380000 + báo cáo 3 danh mục                 ║
║     - Trang: stats sống + badge Du/Sap het/Can nhap          ║
║     - Sửa qty KB-01 trong data.js → reload → số tự đổi       ║
║  3. Thuyết trình 30–60s: reduce hoạt động thế nào?           ║
╚══════════════════════════════════════════════════════════════╝
```

> **Điểm mấu chốt video:** bước "sửa data.js → số trên trang tự đổi" chứng minh dữ liệu đã tách khỏi giao diện.
