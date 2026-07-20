# BUỔI 6: JavaScript nền — biến, kiểu, hàm, mảng/object, DOM query

> **Thời lượng gợi ý:** 4–5 giờ tự học (lý thuyết + Console + Phiếu 06)  
> **Tiên quyết:** Buổi 5 — dashboard CampusMart tĩnh hoàn chỉnh (responsive, `.cm-grid` 8 card, stats placeholder)  
> **Kết quả đầu ra:** Tách `data.js` + `app.js`; viết hàm tính toán trên dataset; dùng `map`/`filter`/`reduce`/`find`; query DOM và đổ số liệu sống vào trang  
> **Phiếu học tập:** [Phiếu 06](./worksheets/phieu-06-js-nen.md) — chuỗi **CampusMart UI** · repo `cse391-cm-06`  
> **Dataset:** [CANONICAL-DATA §3, §5](./worksheets/CANONICAL-DATA.md) — **8 sản phẩm**, `inventoryValue = 41380000`, `stockLevel` exact

---

## 0. Gợi ý lịch trình giảng (cho GV)

| Phút | Nội dung | Gợi ý thao tác |
|------|----------|----------------|
| 0–10 | Ôn P05 + hỏi “8 card gõ tay — thêm SP thứ 9 sửa mấy chỗ?” | Chiếu trang P05 → ghi câu trả lời lên bảng |
| 10–20 | Vì sao cần JS; tách DATA vs DOM (ASCII) | Vẽ sơ đồ 2 cột trên bảng |
| 20–35 | Nhúng `<script>`, `defer`, mở Console, `console.log` | Live-code `thu-js.html`; SV mở F12 |
| 35–50 | `const`/`let`, `typeof`, template literal, `==` vs `===` | **Thử ngay Console** — dự đoán trước khi Enter |
| 50–65 | Object + Array; dot vs bracket; mảng `products` CANONICAL | Gõ `data.js` — không copy-paste cả file |
| 65–85 | Hàm: `lineTotal`, `formatVnd`, `stockLevel` + bảng 8 SKU | SV tính tay KB-01 → đối chiếu Console |
| 85–105 | Bộ tứ `map`/`filter`/`reduce`/`find`; pipeline ASCII | Demo `inventoryValue` → **41380000** |
| 105–120 | DOM query sâu: `querySelector`, `dataset`, `textContent` | Ghi stats vào `#stats` trên trang thật |
| 120–140 | **Thực hành Phiếu 06 mục 2** | SV tách `data.js` + `app.js`, 5 hàm helper |
| 140–150 | Checkpoint + giao về nhà P06 mục 3 | Badge stockLevel, báo cáo 3 DM, video OBS |

> Trên lớp **ưu tiên xong 5 hàm + `inventoryValue` đúng 41380000**. Stats DOM và badge có thể hoàn thiện về nhà.

---

## 1. Mục tiêu buổi học

Sau Buổi 6, bạn có thể:

1. Giải thích vì sao trang tĩnh cần JavaScript — tách **dữ liệu** khỏi **giao diện**.
2. Nhúng JS đúng cách: `<script src>` cuối `body` hoặc `defer`; thứ tự `data.js` trước `app.js`.
3. Dùng Console làm vòng lặp **dự đoán → chạy → quan sát**; đọc lỗi đỏ cơ bản.
4. Khai báo biến `const`/`let`; dùng `typeof`; phân biệt string, number, boolean, `null`, `undefined`.
5. Viết template literal; so sánh bằng `===` (tránh bẫy `==`).
6. Mô tả cấu trúc object sản phẩm CampusMart; truy cập thuộc tính bằng `.` và `[]`.
7. Viết hàm declaration và arrow; tính `lineTotal`, `formatVnd`, `stockLevel` (chuỗi exact CANONICAL).
8. Dùng `map`, `filter`, `reduce`, `find` trên mảng 8 sản phẩm; tính `inventoryValue` và `countByCategory`.
9. Query DOM (`querySelector`/`querySelectorAll`); ghi an toàn bằng `textContent`; đọc `dataset`.
10. Hoàn thành [Phiếu 06](./worksheets/phieu-06-js-nen.md): stats sống + badge tồn kho trên card.

---

## 2. Vì sao cần JavaScript?

Sau Buổi 5, CampusMart đã đẹp và responsive — nhưng vẫn **tĩnh**:

```
Buổi 1–5 CampusMart:
  ✓ 8 card HTML gõ tay trong .cm-grid
  ✓ Bảng tồn kho, stats placeholder (#stats)
  ✗ Thêm SP thứ 9 → sửa card + bảng + tổng kho (3+ chỗ)
  ✗ Filter danh mục → chưa chạy (chỉ có <select> trang trí)
  ✗ Tổng kho 41380000 → gõ cứng, dễ lệch khi đổi qty
```

**JavaScript trả lời:** đặt dữ liệu vào **một mảng JS**, viết **hàm tính toán**, rồi **đổ kết quả vào DOM**.

### 2.1. Sơ đồ tách DATA vs DOM (ASCII bắt buộc)

```
                    TRƯỚC (HTML tĩnh)                SAU (JS — Buổi 6→7)
              ┌─────────────────────────┐      ┌─────────────────────────┐
              │  HTML ghi 8 card tay    │      │  data.js                │
              │  HTML ghi tổng kho tay  │      │  products = [ 8 obj ]   │
              │  Sửa qty → sửa 3 chỗ    │      │  categories = [ 3 obj ] │
              └─────────────────────────┘      └───────────┬─────────────┘
                                                           │
                                                           │  app.js
                                                           │  lineTotal()
                                                           │  inventoryValue()
                                                           │  stockLevel()
                                                           ▼
              ┌─────────────────────────────────────────────────────────┐
              │                    DOM (trang đang mở)                   │
              │  #stats          ← textContent = "So san pham = 8"      │
              │  .cm-card × 8    ← (Buổi 7: render tự sinh)             │
              │  .cm-stock       ← badge "Du" / "Sap het" / "Can nhap"    │
              └─────────────────────────────────────────────────────────┘

   Một nguồn sự thật (single source of truth)  →  UI luôn khớp dữ liệu
```

> Đây cũng là tư tưởng React (Buổi 9+): **UI = f(dữ liệu)**. Buổi 6 là bước đầu bằng JS thuần.

## 3. Nhúng JS & Console — người bạn số một

### 3.1. Ba cách nhúng (chỉ dùng external từ P06)

```
┌─────────────────────────────────────────────────────────────────┐
│  1. INLINE     <button onclick="alert('hi')">  ← demo nhanh    │
│     └─ Tránh trong môn — khó bảo trì, khó autograder           │
│                                                                 │
│  2. INTERNAL   <script> const x = 1; </script>  ← thử Console │
│     └─ Chỉ khi học thuộc lý thuyết 1 trang                     │
│                                                                 │
│  3. EXTERNAL   <script src="app.js"></script>  ← ★ CHUẨN P06 ★ │
│     └─ Tách data.js (dữ liệu) + app.js (logic)                 │
└─────────────────────────────────────────────────────────────────┘
```

### 3.2. Vị trí script — cuối body hoặc `defer`

```html
<!DOCTYPE html>
<html lang="vi">
<head>
  <meta charset="UTF-8" />
  <title>CampusMart — JS (Buoi 6)</title>
  <!-- Cách B: script trong head + defer -->
  <!-- <script src="data.js" defer></script> -->
  <!-- <script src="app.js" defer></script> -->
</head>
<body>
  <main>
    <section id="stats" data-testid="cm-stats"></section>
    <div class="cm-grid" data-testid="cm-product-table">
      <!-- 8 card tĩnh từ P05 — Buổi 7 sẽ xóa và render -->
    </div>
  </main>

  <!-- Cách A (khuyến nghị P06): cuối body, data.js TRƯỚC app.js -->
  <script src="data.js"></script>
  <script src="app.js"></script>
</body>
</html>
```

```
Thứ tự load:
  1. HTML parse → DOM có #stats, .cm-card
  2. data.js   → biến global products, categories
  3. app.js    → gọi hàm, query DOM, ghi stats

Script TRƯỚC khi DOM sẵn sàng → querySelector trả null → lỗi kinh điển SV
```

| Cách | DOM khi JS chạy? | Ghi chú |
|------|:----------------:|---------|
| `<script>` cuối `body` | Có | **Khuyến nghị P06** |
| `<script defer>` trong `head` | Có (sau parse HTML) | Tương đương |
| `<script>` đầu `body` / giữa HTML | Có thể chưa | **Tránh** |

### 3.3. Console — quy trình học JS

```
F12 (hoặc Cmd+Option+I) → tab Console

Quy trình CSE391:
  ① Viết 1–3 dòng JS
  ② Dự đoán kết quả (ghi ra giấy)
  ③ Enter / reload trang
  ④ So sánh — sai → đọc lỗi đỏ, sửa, lặp lại
  ⑤ Đúng 3 lần liên tiếp → mới gắn vào DOM
```

```js
console.log("CampusMart JS OK");
console.log(products.length);       // sau khi có data.js
```

### Thử ngay Console — script load

Mở trang P06, gõ từng dòng (dự đoán trước):

```js
typeof products;           // ?  → "object" (mảng cũng là object)
Array.isArray(products);   // ?  → true
products === undefined;    // ?  → false (nếu data.js load OK)
document.querySelector("#stats") === null;  // ?  → false (nếu có #stats)
```

---

## 4. Biến & kiểu dữ liệu

### 4.1. `const` vs `let` — quy tắc môn CSE391

```js
const brand = "CampusMart";     // không gán lại — MẶC ĐỊNH dùng const
const products = [ /* ... */ ]; // mảng const vẫn push/sửa phần tử được
let visibleCount = 8;           // sẽ gán lại sau filter (Buổi 7)

// var — cũ, phạm vi function, hoisting gây bug — KHÔNG dùng
```

| Khai báo | Gán lại? | Khi nào dùng |
|----------|:--------:|--------------|
| `const` | Không | Hầu hết biến, mảng, object, hàm arrow |
| `let` | Có | Biến đếm, kết quả filter thay đổi |
| `var` | Có | **Không dùng** |

### 4.2. Kiểu cơ bản + `typeof`

```js
const price = 1890000;              // number
const name = "Keychron K2";         // string
const inStock = true;               // boolean
const nothing = null;               // null — “cố ý rỗng”
let notSet;                         // undefined — chưa gán

typeof price;      // "number"
typeof name;       // "string"
typeof inStock;    // "boolean"
typeof nothing;    // "object"  ← bẫy JS kinh điển!
typeof notSet;     // "undefined"
typeof products;   // "object"
```

### 4.3. Template literal

```js
const sku = "KB-01";
const price = 1890000;

// Cũ — nối chuỗi +
const old = sku + " gia " + price + " VND";

// Mới — template literal (ưu tiên)
const line = `${sku} gia ${price} VND`;
const stats = `So san pham = ${products.length}`;
const report = `Tong gia tri kho = ${inventoryValue(products)}`;
```

### 4.4. So sánh `==` vs `===` — bảng bẫy

| Biểu thức | `==` (ép kiểu) | `===` (strict) | Ghi chú |
|-----------|:--------------:|:--------------:|---------|
| `5 == "5"` | `true` | `false` | **Luôn dùng `===`** |
| `0 == false` | `true` | `false` | |
| `null == undefined` | `true` | `false` | |
| `"" == 0` | `true` | `false` | |
| `2 === 2` | — | `true` | So sánh danh mục |
| `p.category_id === 1` | — | `true` | Filter Ban phim |

> Quy tắc vàng P06: **`===` và `!==` cho mọi so sánh.** Giống tinh thần so sánh strict trong PHP.

### Thử ngay Console — kiểu & so sánh

Dự đoán từng dòng:

```js
typeof null;                    // ?
typeof [];                      // ?
"3" + 1;                        // ?
"3" - 1;                        // ?
5 == "5";                       // ?
5 === "5";                      // ?
`${2 + 3} SP`;                  // ?
```

Đáp án: `"object"`, `"object"`, `"31"`, `2`, `true`, `false`, `"5 SP"`.

---

## 5. Object & Array — cấu trúc CampusMart

### 5.1. Object — một sản phẩm

```js
const p = {
  sku: "KB-01",
  name: "Keychron K2",
  category_id: 1,
  price: 1890000,
  qty: 3,
};

// Dot notation — khi biết tên thuộc tính
console.log(p.name);        // "Keychron K2"
console.log(p.price);         // 1890000

// Bracket notation — khi key là biến hoặc có ký tự đặc biệt
const field = "sku";
console.log(p[field]);        // "KB-01"
console.log(p["category_id"]); // 1
```

| Cách truy cập | Ví dụ | Dùng khi |
|---------------|-------|----------|
| Dot `.` | `p.name` | Key cố định, hợp lệ identifier |
| Bracket `[]` | `p["sku"]`, `p[field]` | Key động, có `_` hoặc số |

### 5.2. Array — danh sách sản phẩm

```js
const products = [ p1, p2, /* ... */ ];
console.log(products.length);   // 8
console.log(products[0]);       // object đầu tiên
console.log(products[0].sku);   // "KB-01"

// Duyệt — for...of (Buổi 6) trước khi học map (mục 7)
for (const item of products) {
  console.log(item.sku, item.name);
}
```

### 5.3. File `data.js` — dataset đầy đủ CANONICAL §5

P06: **không** dùng `export` — script tag tạo biến global.

```js
// data.js — CampusMart canonical (Buoi 6)
// KHONG export — load qua <script src="data.js">

const categories = [
  { id: 1, name: "Ban phim" },
  { id: 2, name: "Chuot" },
  { id: 3, name: "Man hinh" },
];

const products = [
  { sku: "KB-01", name: "Keychron K2",    category_id: 1, price: 1890000, qty: 3 },
  { sku: "KB-02", name: "Akko 3087",      category_id: 1, price: 1290000, qty: 5 },
  { sku: "KB-03", name: "Leopold FC660M", category_id: 1, price: 2750000, qty: 2 },
  { sku: "MS-01", name: "Logitech M331",  category_id: 2, price:  290000, qty: 10 },
  { sku: "MS-02", name: "Razer Viper",    category_id: 2, price:  990000, qty: 4 },
  { sku: "MS-03", name: "Xiaomi Silent",  category_id: 2, price:  250000, qty: 8 },
  { sku: "MN-01", name: "Dell 24 inch",   category_id: 3, price: 3200000, qty: 2 },
  { sku: "MN-02", name: "LG UltraFine",   category_id: 3, price: 8500000, qty: 1 },
];
```

### 5.4. Bảng đối chiếu nhanh — 8 SKU

| SKU | name | category_id | price | qty | lineTotal |
|-----|------|:-----------:|------:|----:|----------:|
| KB-01 | Keychron K2 | 1 | 1890000 | 3 | 5670000 |
| KB-02 | Akko 3087 | 1 | 1290000 | 5 | 6450000 |
| KB-03 | Leopold FC660M | 1 | 2750000 | 2 | 5500000 |
| MS-01 | Logitech M331 | 2 | 290000 | 10 | 2900000 |
| MS-02 | Razer Viper | 2 | 990000 | 4 | 3960000 |
| MS-03 | Xiaomi Silent | 2 | 250000 | 8 | 2000000 |
| MN-01 | Dell 24 inch | 3 | 3200000 | 2 | 6400000 |
| MN-02 | LG UltraFine | 3 | 8500000 | 1 | 8500000 |
| **Tổng** | | | | | **41380000** |

| Danh mục | count | value (tổng lineTotal) |
|----------|:-----:|-----------------------:|
| Ban phim (id=1) | 3 | 17620000 |
| Chuot (id=2) | 3 | 8860000 |
| Man hinh (id=3) | 2 | 14900000 |

### Thử ngay Console — object & array

```js
products[7].name;                    // ? → "LG UltraFine"
products.filter(p => p.category_id === 2).length;  // ? → 3
products.find(p => p.sku === "MN-02").price;       // ? → 8500000
categories.find(c => c.id === 3).name;             // ? → "Man hinh"
```

---

## 6. Hàm — đóng gói logic tính toán

### 6.1. Function declaration vs arrow

```js
function lineTotal(product) {
  return product.price * product.qty;
}

const formatVnd = (n) => `${n} VND`;

console.log(lineTotal(products[0]));  // 5670000  (KB-01)
console.log(formatVnd(41380000));    // "41380000 VND"
```

### 6.2. `stockLevel(qty)` — chuỗi exact CANONICAL §3

```js
function stockLevel(qty) {
  if (qty >= 5) return "Du";
  if (qty >= 2) return "Sap het";
  return "Can nhap";
}
```

| Điều kiện | Output exact |
|-----------|--------------|
| `qty >= 5` | `Du` |
| `qty >= 2` | `Sap het` |
| else | `Can nhap` |

**Bảng đầy đủ 8 SKU — phải khớp autograder:**

| sku | qty | stockLevel(qty) |
|-----|----:|-----------------|
| KB-01 | 3 | Sap het |
| KB-02 | 5 | Du |
| KB-03 | 2 | Sap het |
| MS-01 | 10 | Du |
| MS-02 | 4 | Sap het |
| MS-03 | 8 | Du |
| MN-01 | 2 | Sap het |
| MN-02 | 1 | Can nhap |

> Sai **một dấu cách** (`"Sap het"` vs `"Sap  het"`) → autograder trượt. Copy exact từ bảng.

### 6.3. Các helper bắt buộc Phiếu 06

```js
function inventoryValue(list) {
  return list.reduce((sum, p) => sum + lineTotal(p), 0);
}

function findProductBySku(list, sku) {
  return list.find((p) => p.sku === sku);
}

function countByCategory(list, categoryId) {
  return list.filter((p) => p.category_id === categoryId).length;
}
```

### Thử ngay Console — hàm CampusMart

```js
lineTotal(products[0]);              // ? → 5670000
inventoryValue(products);            // ? → 41380000
stockLevel(10);                      // ? → "Du"
stockLevel(3);                       // ? → "Sap het"
stockLevel(1);                       // ? → "Can nhap"
findProductBySku(products, "MN-02").name;  // ? → "LG UltraFine"
countByCategory(products, 1);        // ? → 3
countByCategory(products, 3);        // ? → 2
```

---

## 7. Bộ tứ mảng — `map` / `filter` / `reduce` / `find`

### 7.1. Pipeline ASCII (học thuộc)

```
                         products (mảng 8 phần tử)
                                    │
        ┌───────────────────────────┼───────────────────────────┐
        │                           │                           │
        ▼                           ▼                           ▼
    .filter(dk)                 .map(bien)                  .find(dk)
    lọc nhiều                   biến đổi từng               tìm MỘT
    → mảng con                  → mảng cùng size            → 1 object / undefined
        │                           │
        │                           │
        └───────────┬───────────────┘
                    ▼
               .reduce(gop, giaTriKhoiTao)
               gom về MỘT giá trị (số, chuỗi, object...)
                    │
                    ▼
              inventoryValue = 41380000
```

| Method | Input | Output | Câu hỏi trả lời |
|--------|-------|--------|-----------------|
| `filter` | mảng | mảng (≤ size) | “Ai thỏa điều kiện?” |
| `map` | mảng | mảng (cùng size) | “Mỗi phần tử thành gì?” |
| `reduce` | mảng | **một** giá trị | “Tổng / gom lại?” |
| `find` | mảng | một phần tử / `undefined` | “Cái đầu tiên thỏa?” |

### 7.2. `filter` — lọc theo danh mục

```js
// Ban phim — category_id === 1
const keyboards = products.filter((p) => p.category_id === 1);
console.log(keyboards.length);           // 3
console.log(keyboards.map((p) => p.sku)); // ["KB-01","KB-02","KB-03"]

// Chuot — category_id === 2
const mice = products.filter((p) => p.category_id === 2);
console.log(mice.length);                // 3

// Man hinh — category_id === 3
const monitors = products.filter((p) => p.category_id === 3);
console.log(monitors.length);            // 2
```

### 7.3. `map` — biến đổi từng phần tử

```js
// Chỉ lấy tên
const names = products.map((p) => p.name);

// Tạo chuỗi báo cáo từng dòng
const lines = products.map(
  (p) => `${p.sku}: ${p.name} — ${stockLevel(p.qty)}`
);
```

### 7.4. `reduce` — tổng giá trị kho

```js
function inventoryValue(list) {
  return list.reduce((sum, p) => sum + p.price * p.qty, 0);
}

console.log(inventoryValue(products)); // 41380000 — PHẢI đúng exact

// Giá trị theo danh mục
function categoryValue(list, categoryId) {
  return list
    .filter((p) => p.category_id === categoryId)
    .reduce((sum, p) => sum + lineTotal(p), 0);
}

console.log(categoryValue(products, 1)); // 17620000 (Ban phim)
console.log(categoryValue(products, 2)); // 8860000  (Chuot)
console.log(categoryValue(products, 3)); // 14900000 (Man hinh)
```

### 7.5. `find` — tra cứu SKU

```js
const mn02 = products.find((p) => p.sku === "MN-02");
console.log(mn02.name);    // "LG UltraFine"

const missing = products.find((p) => p.sku === "XX-99");
console.log(missing);      // undefined — luôn kiểm tra trước khi .name
```

### 7.6. Báo cáo 3 danh mục — mẫu Console (Phiếu 06)

```js
function printCategoryReport(list) {
  for (const cat of categories) {
    const count = countByCategory(list, cat.id);
    const value = categoryValue(list, cat.id);
    console.log(`${cat.name}: ${count} SP, gia tri ${value}`);
  }
}

printCategoryReport(products);
// Ban phim: 3 SP, gia tri 17620000
// Chuot: 3 SP, gia tri 8860000
// Man hinh: 2 SP, gia tri 14900000
```

### Thử ngay Console — bộ tứ generic (ôn lý thuyết)

Trước khi quay lại CampusMart, luyện với mảng số:

```js
const nums = [3, 1, 4, 1, 5];
// Dự đoán TRƯỚC khi Enter:
console.log(nums.filter((n) => n > 2));           // ?
console.log(nums.map((n) => n * 10));             // ?
console.log(nums.reduce((s, n) => s + n, 0));     // ?
console.log(nums.find((n) => n % 2 === 0));       // ?
```

Đáp án: `[3, 4, 5]`, `[30, 10, 40, 10, 50]`, `14`, `4`.

---

## 8. DOM query SÂU — cầu nối JS ↔ HTML

Buổi 3 bạn đã học **CSS selector**. Buổi 6 dùng **cùng cú pháp selector** trong `querySelector` — một API, hai mục đích.

### 8.1. `querySelector` vs `querySelectorAll`

```js
// MỘT phần tử — trả về Element hoặc null
const statsEl = document.querySelector("#stats");
const brandEl = document.querySelector('[data-testid="cm-brand"]');
const firstCard = document.querySelector(".cm-card");

// NHIỀU phần tử — trả về NodeList (giống mảng, không phải Array)
const cards = document.querySelectorAll(".cm-card");
const rows = document.querySelectorAll('[data-testid="cm-product-row"]');
```

| API | Trả về | Không tìm thấy | Dùng khi |
|-----|--------|----------------|----------|
| `querySelector(sel)` | 1 Element | `null` | Ghi stats, lấy 1 hook |
| `querySelectorAll(sel)` | NodeList | NodeList rỗng (length=0) | Duyệt card, badge |
| `getElementById("stats")` | 1 Element | `null` | Tương đương `#stats` |

### 8.2. `textContent` vs `innerHTML` — bảng so sánh

| | `textContent` | `innerHTML` |
|---|---------------|-------------|
| Nội dung | Chỉ text thuần | Text + thẻ HTML |
| XSS / injection | An toàn — tự escape | **Nguy hiểm** nếu data user |
| `<b>hi</b>` gán vào | Hiện literal `<b>hi</b>` | Render **hi** đậm |
| P06 ghi stats | **Bắt buộc** | Tránh |
| Buổi 7 render card | Dùng `createElement` | Hoặc innerHTML template (cẩn thận) |

```js
const el = document.querySelector("#stats");

// ĐÚNG — P06
el.textContent = `So san pham = ${products.length}\nTong gia tri kho = ${inventoryValue(products)}`;

// SAI — không cần HTML cho số liệu
el.innerHTML = "<strong>8</strong> san pham";  // tránh pattern này P06
```

> Quy tắc: dữ liệu từ mảng / người dùng → **`textContent`** hoặc `createElement`. Tương đương `htmlspecialchars()` PHP.

### 8.3. `dataset` — đọc `data-*` từ HTML

HTML P05 mỗi card có `data-sku`:

```html
<article class="cm-card" data-testid="cm-product-row" data-sku="KB-01">
  <h3>Keychron K2</h3>
  ...
</article>
```

```js
const card = document.querySelector('.cm-card[data-sku="KB-01"]');
console.log(card.dataset.sku);      // "KB-01"
console.log(card.dataset.testid);   // "cm-product-row" — data-testid → dataset.testid

// Nối data-sku → object trong mảng
const p = findProductBySku(products, card.dataset.sku);
console.log(p.name, stockLevel(p.qty));  // Keychron K2 Sap het
```

| HTML attribute | `element.dataset` |
|----------------|-------------------|
| `data-sku="KB-01"` | `.sku` → `"KB-01"` |
| `data-testid="cm-stats"` | `.testid` |
| `data-category-id="2"` | `.categoryId` (camelCase) |

### 8.4. `forEach` trên NodeList

`querySelectorAll` trả NodeList — **không có** `.map()` trực tiếp (trừ browser mới). Dùng `forEach`:

```js
document.querySelectorAll(".cm-card").forEach((card) => {
  const sku = card.dataset.sku;
  const p = findProductBySku(products, sku);
  if (!p) {
    console.warn("Khong tim thay SKU:", sku);
    return;
  }

  const badge = document.createElement("p");
  badge.className = "cm-stock";
  badge.textContent = stockLevel(p.qty);  // "Du" | "Sap het" | "Can nhap"
  card.appendChild(badge);
});
```

### 8.5. Cập nhật section `#stats`

HTML mẫu (giữ từ P05 hoặc thêm):

```html
<section id="stats" data-testid="cm-stats" aria-live="polite"></section>
```

```js
// app.js — sau khi định nghĩa hàm helper
function updateStats() {
  const el = document.querySelector("#stats");
  if (!el) {
    console.error("Khong tim thay #stats");
    return;
  }
  const total = inventoryValue(products);
  el.textContent = `So san pham = ${products.length}\nTong gia tri kho = ${total}`;
}

updateStats();
// Kỳ vọng exact:
// So san pham = 8
// Tong gia tri kho = 41380000
```

Gọi `updateStats()` và `attachStockBadges()` (mục 8.4) cuối `app.js` — xem chi tiết [Phiếu 06](./worksheets/phieu-06-js-nen.md).

### Thử ngay Console — DOM

Trên trang P06 đang mở:

```js
document.querySelectorAll(".cm-card").length;     // ? → 8
document.querySelector("#stats").textContent;     // ? → có 2 dòng stats
document.querySelector('[data-sku="MN-02"]') !== null;  // ? → true
typeof document.querySelectorAll(".cm-x");        // ? → "object" (NodeList)
```

---

## 9. Hình ảnh nhớ (memory images)

Dán 4 dòng này đầu vở phần JS:

```
① DATA (mảng) = sổ kho trong két sắt — sửa một chỗ, mọi tính toán theo
② Hàm = máy tính Casio — bỏ số vào, nhận kết quả ra, không nhớ bừa biến global
③ map/filter/reduce/find = dây chuyền lọc đóng gói trong kho campus
④ querySelector = ngón tay chỉ đúng ô trên bảng HTML; textContent = phấn viết chữ, không vẽ graffiti HTML
```

---

## 10. Câu hỏi tự kiểm tra

1. Vì sao P06 tách `data.js` và `app.js` thay vì ghi hết inline?
2. `typeof null` trả về gì? Vì sao đó là bẫy?
3. `5 == "5"` và `5 === "5"` — kết quả mỗi biểu thức?
4. Viết điều kiện `stockLevel`: qty=4, qty=2, qty=1 → chuỗi nào?
5. `inventoryValue(products)` bằng bao nhiêu? Tính tay KB-01 + KB-02.
6. `countByCategory(products, 2)` bằng mấy? Danh mục đó gồm SKU nào?
7. Khác nhau giữa `querySelector` và `querySelectorAll`?
8. Vì sao ghi stats bằng `textContent` thay vì `innerHTML`?
9. `products.find(p => p.sku === "XX")` trả về gì? Gọi `.name` ngay có an toàn không?
10. Script đặt đầu `<head>` không `defer` — `querySelector("#stats")` có thể lỗi vì sao?

### Gợi ý đáp án

1. Bảo trì; autograder tìm file; data tách khỏi logic.  
2. `"object"` — bug lịch sử JS; dùng `=== null` hoặc `Array.isArray()`.  
3. `true` và `false`.  
4. `"Sap het"`, `"Sap het"`, `"Can nhap"`.  
5. **41380000**; 5670000 + 6450000 = 12120000 (2 SP đầu).  
6. **3**; MS-01, MS-02, MS-03.  
7. Một phần tử / `null` vs NodeList nhiều phần tử.  
8. Tránh XSS; số liệu không cần HTML; tự escape.  
9. `undefined`; **không** an toàn — cần `if (p)` trước.  
10. DOM chưa parse xong → `#stats` chưa tồn tại → `null`.

---

## 11. Lỗi thường gặp (chi tiết)

| # | Triệu chứng | Nguyên nhân | Cách xử lý |
|---|-------------|-------------|------------|
| 1 | `products is not defined` | `app.js` trước `data.js` | Đảo thứ tự script |
| 2 | `querySelector` → `null` | Script chạy trước DOM | Cuối body hoặc `defer` |
| 3 | `Cannot read properties of null` | Gọi `.textContent` trên null | `if (el)` trước khi ghi |
| 4 | `inventoryValue` ≠ 41380000 | Sai price/qty trong data.js | Đối chiếu CANONICAL §5 |
| 5 | Autograder stockLevel fail | `"Sap het "` thừa space | Copy exact 3 chuỗi |
| 6 | `reduce` → `NaN` | Quên seed `0` | `.reduce(fn, 0)` |
| 7 | Filter luôn rỗng | `category_id == "1"` (string) | Dùng `=== 1` (number) |
| 8 | Badge không xuất hiện | Card thiếu `data-sku` | Thêm attribute từ P05 |
| 9 | `find(...).name` crash | SKU không tồn tại | Kiểm tra `undefined` |
| 10 | NodeList `.map is not a function` | NodeList ≠ Array | Dùng `forEach` hoặc `[...nodes].map` |
| 11 | Console không thấy log | Quên mở F12 / filter Hide | Bật Console, level Default |
| 12 | Sửa `const` báo lỗi | Gán lại biến const | Đổi sang `let` nếu cần |
| 13 | `innerHTML` stats bị escape lạ | Nhầm API | Chuyển sang `textContent` |

---

## 12. Checklist trước Buổi 7

- [ ] `data.js` + `app.js` external; thứ tự load đúng
- [ ] `<title>` exact: `CampusMart — JS (Buoi 6)`
- [ ] 8 sản phẩm khớp CANONICAL §5; `inventoryValue(products) === 41380000`
- [ ] `stockLevel` trả đúng 3 chuỗi; bảng 8 SKU khớp
- [ ] 5 hàm: `lineTotal`, `inventoryValue`, `stockLevel`, `findProductBySku`, `countByCategory`
- [ ] `#stats` hiển thị `So san pham = 8` và `Tong gia tri kho = 41380000`
- [ ] 8 badge `.cm-stock` trên card; đúng stockLevel từng SKU
- [ ] Console in báo cáo 3 DM: 3/17620000, 3/8860000, 2/14900000
- [ ] Comment `CM_EXPECT` theo Phiếu 06
- [ ] Xong [Phiếu 06](./worksheets/phieu-06-js-nen.md)

---

## 13. Bài tập trên lớp & về nhà (chuỗi CampusMart UI)

| Phần | Làm gì | Chi tiết |
|------|--------|----------|
| **Trên lớp (~20')** | `data.js` 8 SP + 5 hàm + kiểm tra Console | [Phiếu 06 — mục 2](./worksheets/phieu-06-js-nen.md) |
| **Về nhà (~25–40')** | Stats DOM + badge stock + báo cáo DM + video | [Phiếu 06 — mục 3](./worksheets/phieu-06-js-nen.md) |

Repo nộp: **`cse391-cm-06`** · video OBS 3 khung (375 / 700 / 1200px).

---

## 14. Cầu nối sang Buổi 7

```
Buổi 6: TÍNH từ mảng + GHI số liệu vào chỗ có sẵn (#stats, badge)
Buổi 7: TỰ SINH toàn bộ card từ mảng + FILTER + EVENT

  render(products)     → xóa HTML card tĩnh, createElement từng card
  addEventListener     → select filter đổi → render(mảng con)
  event loop (ASCII)   → hiểu click chạy sau khi nào
```

Bạn đã có **mảng + hàm + query DOM**. Buổi 7 thêm **render pipeline** và **sự kiện** — CampusMart bắt đầu “sống” thật sự.

→ [Buổi 7](./07-buoi-07-dom-events-render-filter.md) · [Phiếu 07](./worksheets/phieu-07-dom-render-filter.md)

---

*CSE391 – Ebook Frontend CampusMart UI | Buổi 6/12*
