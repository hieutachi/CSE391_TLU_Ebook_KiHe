# BUỔI 6: JavaScript nền — biến, kiểu, hàm, mảng/object, DOM query

> **Thời lượng gợi ý:** 4 giờ tự học  
> **Tiên quyết:** Buổi 5 (dashboard CampusMart tĩnh hoàn chỉnh)  
> **Kết quả đầu ra:** Viết được JS với `const/let`, hàm, mảng object; tính toán trên dataset CampusMart; query DOM và đổ số liệu vào trang  
> **Phiếu học tập liên quan:** [Phiếu 06](./worksheets/phieu-06-js-nen.md) — chuỗi **CampusMart UI**

---

## 1. Mục tiêu buổi học

Sau Buổi 6, bạn có thể:

1. Nhúng JS vào trang đúng cách (`<script src>` cuối body hoặc `defer`).
2. Dùng `const`/`let`, phân biệt kiểu: string, number, boolean, array, object.
3. Viết hàm (function declaration + arrow), tham số, giá trị trả về.
4. Thao tác mảng object bằng `for...of`, `map`, `filter`, `reduce`, `find`.
5. Query DOM (`querySelector`) và ghi nội dung (`textContent`).
6. Tính các chỉ số CampusMart: tổng kho, đếm theo danh mục, mức tồn (`stockLevel`).

---

## 2. Vì sao cần JavaScript?

Trang tĩnh của bạn có một điểm yếu chí mạng: muốn thêm sản phẩm thứ 9 phải sửa tay HTML ở bảng, ở card, ở tổng kho. **Dữ liệu và giao diện đang dính chặt nhau.** JavaScript tách chúng ra:

```
   DỮ LIỆU (mảng JS)          GIAO DIỆN (DOM)
  ┌──────────────────┐       ┌──────────────────┐
  │ products = [      │  JS   │  <div class=     │
  │  {sku, name,      │ ────► │   "cm-grid">     │
  │   price, qty}, …  │ render│   (card tự sinh) │
  │ ]                 │       │                  │
  └──────────────────┘       └──────────────────┘
   sửa MỘT chỗ                 giao diện tự cập nhật
```

Đây cũng chính là tư tưởng của React (Buổi 9+): UI là **hàm của dữ liệu**.

---

## 3. Nhúng JS & Console — người bạn số một

```html
<body>
  ...
  <script src="data.js"></script>
  <script src="app.js"></script>
</body>
```

Script đặt **cuối body** (hoặc trong `<head>` với thuộc tính `defer`) để DOM tồn tại trước khi JS chạy.

```js
console.log("CampusMart JS OK");   // F12 → tab Console
```

> Quy tắc học JS: **mọi thứ in ra Console trước**, thấy đúng rồi mới gắn vào DOM.

---

## 4. Biến & kiểu dữ liệu

```js
const brand = "CampusMart";  // const: không gán lại — mặc định dùng const
let visibleCount = 8;        // let: sẽ gán lại (đếm sau khi filter)
// var: cũ, KHÔNG dùng trong môn này

// Kiểu cơ bản
const price = 1890000;           // number
const name = "Keychron K2";      // string
const inStock = true;            // boolean
const nothing = null;            // null

// Template literal — nối chuỗi kiểu mới (ưu tiên)
console.log(`San pham ${name} gia ${price}`);
```

### Object & Array — dạng dữ liệu của cả môn học

```js
// Object: một "bản ghi" sản phẩm
const p = { sku: "KB-01", name: "Keychron K2", category_id: 1, price: 1890000, qty: 3 };
console.log(p.name);        // chấm
console.log(p["sku"]);      // ngoặc vuông

// Array of objects: cả catalog (đúng CANONICAL-DATA §5)
const products = [
  { sku: "KB-01", name: "Keychron K2", category_id: 1, price: 1890000, qty: 3 },
  { sku: "KB-02", name: "Akko 3087", category_id: 1, price: 1290000, qty: 5 },
  // ... đủ 8 phần tử
];
console.log(products.length);      // 8
console.log(products[0].sku);      // "KB-01"
```

---

## 5. Hàm — đóng gói logic tính toán

```js
// Function declaration
function lineTotal(product) {
  return product.price * product.qty;
}

// Arrow function — gọn, dùng nhiều trong callback
const formatVnd = (n) => `${n} VND`;

console.log(lineTotal(products[0]));   // 5670000
console.log(formatVnd(1890000));       // "1890000 VND"
```

### `stockLevel` — chuỗi exact theo CANONICAL §3

```js
function stockLevel(qty) {
  if (qty >= 5) return "Du";
  if (qty >= 2) return "Sap het";
  return "Can nhap";
}

stockLevel(10); // "Du"
stockLevel(3);  // "Sap het"
stockLevel(1);  // "Can nhap"
```

> Trả về **đúng chuỗi** `Du` / `Sap het` / `Can nhap` — Autograder so sánh exact, sai một dấu cách là trượt.

---

## 6. Bộ tứ mảng: `map` / `filter` / `reduce` / `find`

```
products ──filter(dk)──► mảng con      (lọc theo danh mục)
products ──map(bien_doi)──► mảng mới   (lấy tên, tạo HTML)
products ──reduce(gop)──► MỘT giá trị  (tổng kho)
products ──find(dk)──► MỘT phần tử     (tìm theo SKU)
```

```js
// filter: sản phẩm danh mục Chuot (category_id = 2)
const mice = products.filter((p) => p.category_id === 2);
console.log(mice.length); // 3

// map: chỉ lấy tên
const names = products.map((p) => p.name);

// reduce: tổng giá trị kho
const inventoryValue = products.reduce((sum, p) => sum + p.price * p.qty, 0);
console.log(inventoryValue); // 41380000

// find: một sản phẩm theo SKU
const mn02 = products.find((p) => p.sku === "MN-02");
console.log(mn02.name); // "LG UltraFine"
```

### Đếm theo danh mục (dùng cho báo cáo)

```js
function countByCategory(products, categoryId) {
  return products.filter((p) => p.category_id === categoryId).length;
}
countByCategory(products, 1); // 3  (Ban phim)
countByCategory(products, 3); // 2  (Man hinh)
```

---

## 7. DOM query — cầu nối JS ↔ HTML

```js
// Chọn MỘT phần tử (CSS selector quen thuộc từ Buổi 3!)
const statsEl = document.querySelector("#stats");
const brandEl = document.querySelector('[data-testid="cm-brand"]');

// Ghi nội dung
statsEl.textContent = `So san pham = ${products.length}`;

// Chọn NHIỀU phần tử
const cards = document.querySelectorAll(".cm-card");
console.log(cards.length); // 8
```

| API | Trả về | Dùng khi |
|-----|--------|----------|
| `querySelector(sel)` | phần tử đầu tiên / `null` | lấy 1 chỗ để ghi |
| `querySelectorAll(sel)` | NodeList | đếm, duyệt |
| `.textContent` | get/set text | ghi số liệu (an toàn, tự escape) |
| `.innerHTML` | get/set HTML | dựng markup — cẩn thận XSS, Buổi 7 |

> Ghi **dữ liệu người dùng nhập** luôn bằng `textContent`, không `innerHTML` — tương đương `htmlspecialchars` bên PHP.

---

## 8. Bài thực hành minh họa trong buổi (không thay phiếu)

Trang bất kỳ + console, làm chuỗi dự đoán-rồi-chạy:

```js
const nums = [3, 1, 4, 1, 5];
// Dự đoán từng dòng TRƯỚC khi chạy:
console.log(nums.filter((n) => n > 2));          // ?
console.log(nums.map((n) => n * 10));            // ?
console.log(nums.reduce((s, n) => s + n, 0));    // ?
console.log(nums.find((n) => n % 2 === 0));      // ?
```

Đáp án tự kiểm: `[3,4,5]`, `[30,10,40,10,50]`, `14`, `4`. Sai ở đâu → đọc lại mục 6.

---

## 9. Lỗi thường gặp

| Triệu chứng | Nguyên nhân | Cách xử lý |
|-------------|-------------|------------|
| `querySelector` trả `null` | Script chạy trước khi DOM có | Script cuối body hoặc `defer` |
| `Uncaught TypeError ... of undefined` | Truy cập thuộc tính của `null`/`undefined` | `console.log` biến ngay trước dòng lỗi |
| `reduce` ra `NaN` | Quên giá trị khởi tạo `0` | `reduce((s,p)=>..., 0)` |
| So sánh luôn sai | Dùng `==` với kiểu lẫn lộn | Luôn `===` (như PHP) |
| Sửa `const` bị lỗi | Gán lại biến const | Đổi sang `let` nếu thật sự cần gán lại |

---

## 10. Checklist trước khi sang buổi sau

- [ ] Console.log thành thạo; đọc được lỗi đỏ trong Console
- [ ] `const`/`let` đúng chỗ; template literal
- [ ] Viết được `lineTotal`, `stockLevel` (chuỗi exact), `inventoryValue`
- [ ] Dùng đúng `map`/`filter`/`reduce`/`find` cho 4 nhu cầu
- [ ] Query + ghi được số liệu vào DOM bằng `textContent`
- [ ] Đã làm xong [Phiếu 06](./worksheets/phieu-06-js-nen.md)

---

## 11. Bài tập trên lớp & về nhà (chuỗi CampusMart UI)

| Phần | Làm gì | Chi tiết |
|------|--------|----------|
| **Trên lớp (~20')** | `data.js` 8 SP + hàm tính, in Console | [Phiếu 06 — mục 2](./worksheets/phieu-06-js-nen.md) |
| **Về nhà (~25–40')** | Thanh stats sống trên trang + báo cáo DM | [Phiếu 06 — mục 3](./worksheets/phieu-06-js-nen.md) |

Nộp repo riêng `cse391-cm-06` + video OBS theo box cuối phiếu.

---

## 12. Cầu nối sang Buổi 7

Bạn đã **tính** được mọi thứ từ dữ liệu — nhưng 8 card vẫn là HTML gõ tay. Buổi 7: JS **tự sinh** card từ mảng (`render`), bắt sự kiện `click`/`change`, và làm bộ lọc danh mục thật sự hoạt động.

→ Tiếp: [Buổi 7](./07-buoi-07-dom-events-render-filter.md) · [Phiếu 07](./worksheets/phieu-07-dom-render-filter.md)

---

*CSE391 – Ebook Frontend CampusMart UI | Buổi 6/12*
