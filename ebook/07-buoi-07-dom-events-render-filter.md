# BUỔI 7: DOM Events — render list từ mảng, filter danh mục

> **Thời lượng gợi ý:** 4–5 giờ tự học (lý thuyết + Console + Phiếu 07)  
> **Tiên quyết:** [Buổi 6](./06-buoi-06-javascript-nen.md) — `data.js` + 5 hàm helper (`stockLevel`, `inventoryValue`…)  
> **Kết quả đầu ra:** JS tự sinh card từ mảng; bắt sự kiện `change`/`click`; lọc danh mục 8/3/3/2; sort giá tăng dần; event delegation cơ bản  
> **Phiếu học tập:** [Phiếu 07](./worksheets/phieu-07-dom-render-filter.md) — chuỗi **CampusMart UI** · repo `cse391-cm-07`  
> **Dataset:** [CANONICAL-DATA §3, §5](./worksheets/CANONICAL-DATA.md) — **8 sản phẩm**, filter **8/3/3/2**, `stockLevel` exact, `inventoryValue = 41380000`

---

## 0. Gợi ý lịch trình giảng (cho GV)

| Phút | Nội dung | Gợi ý thao tác |
|------|----------|----------------|
| 0–10 | Ôn P06: mảng `products`, 5 hàm, stats DOM | Chiếu Console `inventoryValue(products)` → **41380000** |
| 10–25 | Vì sao render từ mảng; pipeline ASCII | Vẽ `products → filter → render → DOM` trên bảng |
| 25–40 | Event loop ASCII (call stack + queue) | Demo click liên tục + `console.log` thứ tự |
| 40–55 | `addEventListener`; bảng click/change/input/submit | Live-code listener trên `<select>` demo |
| 55–75 | HTML khung rỗng + `render(list)` sâu | SV xóa 8 card tĩnh; viết `createElement` từng phần |
| 75–90 | `categoryName` helper; badge `stockLevel` exact | Đối chiếu bảng 8 SKU CANONICAL §3 |
| 90–105 | Filter + bẫy `Number()` — bảng `"2"` vs `2` | SV cố tình bỏ `Number()` → thấy 0 card |
| 105–115 | Visible count `Hien thi: N san pham` | Đọc to 8 → 3 → 3 → 2 trên lớp |
| 115–130 | Sort bất biến `[...products].sort`; MS-03 đầu | Bấm "Gia tang dan" → card đầu **250000** |
| 130–140 | Event delegation `closest` trên `.cm-grid` | Click card → `console.log` SKU |
| 140–150 | Checkpoint + giao về nhà P07 mục 3 | Video OBS: filter 4 option + sort + delegation |

> Trên lớp **ưu tiên xong `render(products)` + filter đúng 8/3/3/2**. Sort và delegation có thể hoàn thiện về nhà.

---

## 1. Mục tiêu buổi học

Sau Buổi 7, bạn có thể:

1. Giải thích vì sao **render từ mảng** thay card HTML tĩnh — một nguồn sự thật, filter = render lại mảng con.
2. Vẽ được pipeline ASCII: `products → filter → render → DOM`.
3. Mô tả **event loop** ở mức call stack + hàng đợi sự kiện — JS một luồng, xử lý tuần tự.
4. Bắt sự kiện bằng `addEventListener` (`change`, `click`, `input`, `submit`) — không dùng inline `onclick`.
5. Viết hàm `render(list)`: xóa grid, `createElement` + `textContent`, cập nhật `Hien thi: N san pham`.
6. Viết helper `categoryName(id)` tra cứu tên danh mục từ mảng `categories`.
7. Lọc theo `<select>` với **`Number(value)`** — tránh bẫy so sánh chuỗi vs số.
8. Xác nhận filter đúng **8 / 3 / 3 / 2** (Tất cả / Ban phim / Chuot / Man hinh).
9. Sort giá tăng dần bằng **`[...products].sort`** — không phá thứ tự mảng gốc; card đầu **MS-03 (250000)**.
10. Dùng **event delegation**: một listener trên `.cm-grid`, `e.target.closest(".cm-card")`.
11. Liên kết `map`/`filter`/`reduce` với render pipeline — cùng tư duy, khác output.
12. Hoàn thành [Phiếu 07](./worksheets/phieu-07-dom-render-filter.md): grid rỗng trong source, 8 card do JS sinh.

---

## 2. Vì sao "render từ mảng" là bước ngoặt?

Đến Phiếu 06, card vẫn là **HTML gõ tay** — JS chỉ gắn thêm badge và stats. Hôm nay đảo ngược hoàn toàn:

```
Buổi 1–5:  8 card viết tay trong .cm-grid
Buổi 6:    JS đọc mảng → tính toán → ghi vào chỗ có sẵn (#stats, badge)
Buổi 7:    HTML chỉ chừa KHUNG RỖNG → JS SINH TOÀN BỘ card từ mảng
```

### 2.1. Vấn đề card tĩnh

```
Thêm sản phẩm thứ 9:
  ✗ HTML tĩnh → sửa card + bảng + tổng kho (3+ chỗ)
  ✗ Filter "Chuot" → phải ẩn/hiện từng card bằng CSS class — dễ sót
  ✗ Sort giá → sắp xếp DOM bằng tay — không bền

Render từ mảng:
  ✓ Sửa data.js → reload → UI tự khớp
  ✓ Filter = products.filter(...) → render(mảng con)
  ✓ Sort = [...list].sort(...) → render(mảng đã sort)
```

### 2.2. Render pipeline ASCII (học thuộc)

```
┌─────────────────────────────────────────────────────────────────────────┐
│                    RENDER PIPELINE — CampusMart P07                      │
└─────────────────────────────────────────────────────────────────────────┘

   data.js                          app.js                         DOM
 ┌──────────────┐              ┌─────────────────┐           ┌──────────────┐
 │ products[8]  │─────────────►│ filter (tùy chọn)│           │ .cm-grid     │
 │ categories[3]│              │       │         │           │ (rỗng ban    │
 └──────────────┘              │       ▼         │           │  đầu)        │
                               │  list (0..8)    │           └──────┬───────┘
                               │       │         │                  │
                               │       ▼         │                  │
                               │  render(list)   │──────────────────►│
                               │  · innerHTML="" │           8 card sinh ra
                               │  · createElement│           + visible count
                               │  · textContent  │           "Hien thi: N..."
                               │  · appendChild  │
                               └─────────────────┘

   Luồng filter:
     products (8) ──filter(cat=2)──► mảng con (3) ──render──► .cm-grid còn 3 card
                                      │
                                      └──► countEl: "Hien thi: 3 san pham"
```

> **UI = f(dữ liệu).** Filter không "ẩn" card — nó **vẽ lại** từ mảng con. React (Buổi 9+) chỉ tự động hóa bước này.

### Thử ngay Console — nhận diện trạng thái P06 vs P07

Trên trang Phiếu 06 (card tĩnh):

```js
// View-source vs runtime
document.querySelector(".cm-grid").innerHTML.length > 0;  // true — HTML có sẵn card
document.querySelectorAll('[data-testid="cm-product-row"]').length;  // ? → 8
```

Sau khi chuyển P07 (grid rỗng + render):

```js
// Sau render(products):
document.querySelectorAll(".cm-card").length;  // ? → 8
document.querySelector('[data-testid="cm-visible-count"]').textContent;
// ? → "Hien thi: 8 san pham"
```

---

## 3. Event loop — trang web "lắng nghe"

Trình duyệt không "chờ" bạn bấm chuột bằng cách dừng hẳn JS. Mô hình thực tế phức tạp hơn, nhưng mức **P07** cần hiểu:

### 3.1. Sơ đồ call stack + hàng đợi (ASCII chi tiết)

```
┌──────────────────────────────────────────────────────────────────────────┐
│  JAVASCRIPT CHỈ CÓ MỘT LUỒNG — làm từng việc một, không song song thật   │
└──────────────────────────────────────────────────────────────────────────┘

  THỜI ĐIỂM T0 — trang vừa load
  ═══════════════════════════════

    Call Stack (đang chạy)              Task Queue (chờ)
    ┌─────────────────────┐             ┌─────────────────────┐
    │ render(products)    │             │ (trống)             │
    │  createElement...   │             └─────────────────────┘
    │  appendChild...     │
    └─────────────────────┘

  THỜI ĐIỂM T1 — user bấm select TRONG LÚC render đang chạy
  ═══════════════════════════════════════════════════════════

    Call Stack                         Task Queue
    ┌─────────────────────┐             ┌─────────────────────┐
    │ render(products)    │             │ change trên select  │ ← xếp hàng
    │  ... vẫn đang vẽ    │             └─────────────────────┘
    └─────────────────────┘
         │
         │  render XONG → stack rỗng
         ▼

  THỜI ĐIỂM T2 — event loop lấy việc tiếp theo
  ════════════════════════════════════════════════

    Call Stack                         Task Queue
    ┌─────────────────────┐             ┌─────────────────────┐
    │ handler change      │             │ (trống)             │
    │  filter + render(3) │             └─────────────────────┘
    └─────────────────────┘

  THỜI ĐIỂM T3 — user bấm liên tiếp 3 lần sort
  ════════════════════════════════════════════════

    Call Stack                         Task Queue
    ┌─────────────────────┐             ┌─────────────────────┐
    │ handler click #1    │             │ click #2              │
    └─────────────────────┘             │ click #3              │
                                        └─────────────────────┘
         xử lý xong #1 → lấy #2 → xong → lấy #3
         (KHÔNG mất click — chỉ xếp hàng)
```

### 3.2. Ba hệ quả thực tế cho lập trình viên

```
① Handler phải NHANH
   └─ Đừng lặp 1 triệu lần trong click — cả trang đứng hình
   └─ CampusMart: filter + render 8 card → vài ms, OK

② Thứ tự log không phải lúc nào cũng "trực quan"
   └─ console.log trong handler chạy SAU khi stack rảnh

③ Một luồng → không cần lock khi sửa DOM
   └─ Nhưng vẫn tránh race: luôn render từ currentList, không patch lẻ tẻ
```

### 3.3. Ví dụ minh họa thứ tự thực thi

```js
console.log("A — trước listener");

document.querySelector("#sort-price")?.addEventListener("click", () => {
  console.log("C — trong handler click");
});

console.log("B — sau listener");

// In ra: A → B → (user bấm) → C
// addEventListener chỉ ĐĂNG KÝ — không chạy handler ngay
```

### Thử ngay Console — quan sát event loop

Mở trang P07, dán vào Console:

```js
console.log("1");
setTimeout(() => console.log("3 — timeout"), 0);
console.log("2");
// Dự đoán TRƯỚC Enter: thứ tự 1, 2, 3
// setTimeout 0ms vẫn vào queue — chạy SAU stack hiện tại
```

---

## 4. `addEventListener` — bắt sự kiện đúng cách

### 4.1. Tại sao không dùng inline `onclick`?

```html
<!-- TRÁNH trong môn -->
<button onclick="sortProducts()">Sort</button>

<!-- CHUẨN P07 -->
<button id="sort-price">Gia tang dan</button>
```

| | Inline `onclick` | `addEventListener` |
|---|------------------|---------------------|
| Tách HTML/JS | Khó | **Có** |
| Nhiều handler cùng sự kiện | Khó | **Có** |
| Autograder / bảo trì | Kém | **Tốt** |
| Event delegation | Khó gom | **Dễ** trên cha |

### 4.2. Cú pháp cơ bản

```js
const select = document.querySelector('[data-testid="cm-filter-category"]');

select.addEventListener("change", (event) => {
  console.log("Gia tri moi:", event.target.value); // "all" | "1" | "2" | "3"
});
```

- **`event`** (hoặc `e`): object mô tả sự kiện.
- **`event.target`**: phần tử **phát sinh** sự kiện (thường là `<select>`).
- **`event.currentTarget`**: phần tử **gắn listener** (cũng là select nếu gắn trực tiếp).

### 4.3. Bảng sự kiện thường gặp (P07 → P12)

| Sự kiện | Phát khi | Ví dụ CampusMart | Buổi |
|---------|----------|------------------|------|
| `click` | bấm chuột / Enter trên nút | Nút sort giá, xóa SP (P12) | **P07** |
| `change` | select / checkbox / input **đổi xong** | Filter danh mục | **P07** |
| `input` | gõ **từng phím** | Ô tìm kiếm theo tên | P10 |
| `submit` | form gửi (Enter hoặc nút submit) | Form thêm SP (P08) | P08 |
| `keydown` | nhấn phím | Shortcut (nâng cao) | — |

> **`change` vs `input`:** Filter `<select>` dùng `change` — chỉ fire khi chọn xong option. Ô search dùng `input` — fire mỗi ký tự.

### 4.4. Gắn listener sau khi DOM sẵn sàng

```js
// Cuối app.js — sau khi query được select và grid
function initEvents() {
  const select = document.querySelector('[data-testid="cm-filter-category"]');
  const sortBtn = document.querySelector("#sort-price");

  if (!select) {
    console.error("Thieu cm-filter-category");
    return;
  }

  select.addEventListener("change", onFilterChange);
  sortBtn?.addEventListener("click", onSortClick);
}

initEvents();
```

### Thử ngay Console — thử listener tạm

```js
const sel = document.querySelector('[data-testid="cm-filter-category"]');
sel?.addEventListener("change", (e) => {
  console.log("Ban chon:", e.target.value, "— kieu:", typeof e.target.value);
});
// Đổi option → luôn thấy typeof "string" — đó là gốc bẫy Number()
```

---

## 5. Render pipeline — từ HTML khung đến `render(list)`

### 5.1. HTML skeleton — grid rỗng

Xóa 8 card tĩnh P05/P06. Giữ layout và testid:

```html
<!DOCTYPE html>
<html lang="vi">
<head>
  <meta charset="UTF-8" />
  <title>CampusMart — Filter (Buoi 7)</title>
  <link rel="stylesheet" href="styles.css" />
</head>
<body>
  <main>
    <section id="stats" data-testid="cm-stats" aria-live="polite"></section>

    <label for="filter-cat">Loc theo danh muc</label>
    <select id="filter-cat" data-testid="cm-filter-category">
      <option value="all">Tat ca</option>
      <option value="1">Ban phim</option>
      <option value="2">Chuot</option>
      <option value="3">Man hinh</option>
    </select>

    <p data-testid="cm-visible-count"></p>

    <button type="button" id="sort-price">Gia tang dan</button>

    <!-- ★ KHUNG RỖNG — card do JS sinh ★ -->
    <div class="cm-grid" data-testid="cm-product-table"></div>
  </main>

  <script src="data.js"></script>
  <script src="app.js"></script>
</body>
</html>
```

```
Kiểm tra nhanh:
  Ctrl+U (View Source) → .cm-grid KHÔNG có <article>
  F12 Elements         → sau load CÓ 8 <article class="cm-card">
```

### 5.2. Helper `categoryName(id)`

```js
function categoryName(id) {
  const c = categories.find((cat) => cat.id === id);
  return c ? c.name : "?";
}

// Thử ngay:
categoryName(1);  // "Ban phim"
categoryName(2);  // "Chuot"
categoryName(99); // "?" — id không tồn tại
```

| category_id | categoryName() | SKU thuộc DM |
|:-----------:|----------------|--------------|
| 1 | Ban phim | KB-01, KB-02, KB-03 |
| 2 | Chuot | MS-01, MS-02, MS-03 |
| 3 | Man hinh | MN-01, MN-02 |

### 5.3. Hàm `render(list)` — trái tim Phiếu 07

```js
const grid = document.querySelector('[data-testid="cm-product-table"]');
const countEl = document.querySelector('[data-testid="cm-visible-count"]');

function render(list) {
  if (!grid || !countEl) {
    console.error("Thieu grid hoac countEl");
    return;
  }

  // Bước 1: xóa sạch — tránh nhân đôi card
  grid.innerHTML = "";

  // Bước 2: duyệt mảng, sinh từng card
  for (const p of list) {
    const card = document.createElement("article");
    card.className = "cm-card";
    card.dataset.testid = "cm-product-row";
    card.dataset.sku = p.sku;

    const h3 = document.createElement("h3");
    h3.textContent = p.name;

    const cat = document.createElement("p");
    cat.className = "cm-card-cat";
    cat.textContent = categoryName(p.category_id);

    const price = document.createElement("p");
    price.className = "cm-card-price";
    price.textContent = String(p.price);  // số thô — autograder ưu tiên 1890000

    const stock = document.createElement("p");
    stock.className = "cm-stock";
    stock.textContent = stockLevel(p.qty);  // exact: Du | Sap het | Can nhap

    card.append(h3, cat, price, stock);
    grid.appendChild(card);
  }

  // Bước 3: visible count — chuỗi EXACT
  countEl.textContent = `Hien thi: ${list.length} san pham`;
}
```

### 5.4. Vì sao `createElement` + `textContent`?

| Cách | Ưu | Nhược | P07 |
|------|----|-------|-----|
| `createElement` + `textContent` | An toàn XSS; rõ cấu trúc | Dài hơn | **Khuyến nghị** |
| Template literal + `innerHTML` | Ngắn | XSS nếu tên SP do user nhập (P11) | Tránh |
| `cloneNode` từ template `<template>` | Tái sử dụng | Cần HTML mẫu ẩn | Nâng cao |

> Buổi 11 user nhập tên SP — `textContent` miễn nhiễm `<script>alert(1)</script>` trong tên.

### 5.5. Bảng `stockLevel` — phải khớp CANONICAL §3

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

### 5.6. Khởi chạy lần đầu

```js
// Cuối app.js — sau định nghĩa render + helpers từ P06
render(products);  // 8 card + "Hien thi: 8 san pham"

// Stats vẫn giữ từ P06 (nếu có updateStats):
updateStats();     // So san pham = 8, Tong gia tri kho = 41380000
```

### Thử ngay Console — render thử nhanh

Không sửa file — trên trang đang mở:

```js
// Render chỉ 2 SP đầu vào grid (sau khi đã có hàm render)
render(products.slice(0, 2));
document.querySelector('[data-testid="cm-visible-count"]').textContent;
// ? → "Hien thi: 2 san pham"

render(products);  // khôi phục 8
```

---

## 6. Filter danh mục + bẫy `Number()`

### 6.1. Luồng filter nối vào render

```js
let currentList = products;  // trạng thái danh sách đang hiển thị

function onFilterChange(event) {
  const v = event.target.value;

  if (v === "all") {
    currentList = products;
  } else {
    const catId = Number(v);
    currentList = products.filter((p) => p.category_id === catId);
  }

  render(currentList);
}
```

### 6.2. Bảng filter EXACT — autograder P07

| Option `<select>` | `value` | `category_id` | Số card (N) | SKU hiển thị |
|-------------------|---------|:-------------:|:-----------:|--------------|
| Tat ca | `all` | — | **8** | KB-01…MN-02 |
| Ban phim | `1` | 1 | **3** | KB-01, KB-02, KB-03 |
| Chuot | `2` | 2 | **3** | MS-01, MS-02, MS-03 |
| Man hinh | `3` | 3 | **2** | MN-01, MN-02 |

```
Visible count tương ứng (chuỗi EXACT):
  Tat ca     → Hien thi: 8 san pham
  Ban phim   → Hien thi: 3 san pham
  Chuot      → Hien thi: 3 san pham
  Man hinh   → Hien thi: 2 san pham
```

### 6.3. Bẫy `Number()` — bảng so sánh string vs number

HTML `<option value="2">` luôn trả **chuỗi** `"2"`. Trong `data.js`, `category_id` là **number** `2`.

| Biểu thức | Kết quả | Filter Chuot ra mấy card? |
|----------|:-------:|:-------------------------:|
| `"2" === 2` | `false` | **0** — bug kinh điển |
| `"2" == 2` | `true` | 3 — nguy hiểm, đừng dùng == |
| `Number("2") === 2` | `true` | **3** — chuẩn P07 |
| `parseInt("2", 10) === 2` | `true` | 3 — OK |
| `+"2" === 2` | `true` | 3 — OK (unary plus) |

```js
// SAI — filter luôn rỗng
products.filter((p) => p.category_id === select.value);

// ĐÚNG
products.filter((p) => p.category_id === Number(select.value));
```

### 6.4. Kiểm tra bằng Console

```js
const v = document.querySelector('[data-testid="cm-filter-category"]').value;
console.log(v, typeof v);                    // "2" "string"
console.log(v === 2);                        // false
console.log(Number(v) === 2);                // true

products.filter((p) => p.category_id === Number(v)).map((p) => p.sku);
// ? → ["MS-01", "MS-02", "MS-03"]
```

### 6.5. Giá trị kho theo danh mục (ôn P06 — không đổi khi filter UI)

Filter chỉ đổi **card hiển thị**, không đổi tổng kho gốc:

| Danh mục | Count | inventoryValue (subset) |
|----------|:-----:|------------------------:|
| Ban phim | 3 | 17620000 |
| Chuot | 3 | 8860000 |
| Man hinh | 2 | 14900000 |
| **Tất cả** | **8** | **41380000** |

### Thử ngay Console — mô phỏng filter không DOM

```js
function filterByCategory(list, value) {
  if (value === "all") return list;
  return list.filter((p) => p.category_id === Number(value));
}

["all", "1", "2", "3"].forEach((v) => {
  const n = filterByCategory(products, v).length;
  console.log(v, "→", n, "san pham");
});
// all → 8, 1 → 3, 2 → 3, 3 → 2
```

---

## 7. Sort giá — bất biến dữ liệu gốc

### 7.1. Vì sao `[...products].sort`?

`Array.prototype.sort()` **sửa tại chỗ** (mutate) mảng gốc:

```js
const before = products.map((p) => p.sku);
products.sort((a, b) => a.price - b.price);  // NGUY HIỂM
const after = products.map((p) => p.sku);
console.log(before.join() === after.join());  // false — thứ tự gốc mất
```

**Chuẩn P07:** spread tạo bản sao nông (shallow copy):

```js
function onSortClick() {
  const sorted = [...currentList].sort((a, b) => a.price - b.price);
  render(sorted);
  // currentList KHÔNG đổi — filter "Chuot" vẫn đúng 3 SP gốc
}
```

### 7.2. Comparator `(a, b) => a.price - b.price`

| a.price | b.price | a.price - b.price | Thứ tự |
|--------:|--------:|:-----------------:|--------|
| 250000 | 8500000 | âm | a trước b (tăng dần) |
| 8500000 | 250000 | dương | b trước a |

### 7.3. Kết quả sort "Tat ca" — MS-03 đầu tiên

Giá tăng dần toàn bộ 8 SP:

| Thứ tự | sku | price |
|:------:|-----|------:|
| 1 | **MS-03** | **250000** |
| 2 | MS-01 | 290000 |
| 3 | MS-02 | 990000 |
| 4 | KB-02 | 1290000 |
| 5 | KB-01 | 1890000 |
| 6 | KB-03 | 2750000 |
| 7 | MN-01 | 3200000 |
| 8 | MN-02 | 8500000 |

> Autograder: `first_sorted_sku = MS-03` khi **Tat ca + Gia tang dan**.

### 7.4. Sort tôn trọng filter đang chọn

```
User chọn Chuot (3 SP) → currentList = [MS-01, MS-02, MS-03]
Bấm sort → sort TRONG 3 SP đó:
  MS-03 (250000) → MS-01 (290000) → MS-02 (990000)
Card đầu vẫn MS-03 — nhưng chỉ 3 card trên lưới
```

```js
// Gợi ý tổ chức state
let currentList = products;

function onFilterChange(e) { /* ... cập nhật currentList ... */ render(currentList); }
function onSortClick() {
  render([...currentList].sort((a, b) => a.price - b.price));
}
```

### Thử ngay Console — sort không phá gốc

```js
const skusBefore = products.map((p) => p.sku).join(",");
const sorted = [...products].sort((a, b) => a.price - b.price);
console.log(sorted[0].sku, sorted[0].price);  // MS-03 250000
console.log(products.map((p) => p.sku).join(",") === skusBefore);  // true — gốc intact
```

---

## 8. Event delegation — một listener cho mọi card

### 8.1. Vấn đề: card sinh sau khi gắn listener

```js
// SAI — 0 listener nếu chạy TRƯỚC render
document.querySelectorAll(".cm-card").forEach((card) => {
  card.addEventListener("click", () => console.log(card.dataset.sku));
});
render(products);  // 8 card mới — KHÔNG có listener
```

Card P07 được **tạo bên trong** `render()` — mỗi lần filter còn **xóa và tạo lại**. Gắn listener từng card = lặp lại, dễ sót, tốn bộ nhớ.

### 8.2. Giải pháp: nghe trên cha `.cm-grid`

```
User click vào <h3> bên trong card
              │
              ▼
         event.target = <h3>
              │
              ▼
    e.target.closest(".cm-card")  →  <article> cha gần nhất
              │
              ▼
         card.dataset.sku  →  "KB-01"
```

```js
grid.addEventListener("click", (e) => {
  const card = e.target.closest(".cm-card");
  if (!card) return;  // click vùng trống grid — bỏ qua

  console.log("Ban vua bam card:", card.dataset.sku);
  // P12: nút Xoa trong card cũng dùng pattern này
});
```

### 8.3. `closest` vs `querySelector`

| API | Hướng tìm | Dùng khi |
|-----|-----------|----------|
| `element.closest(sel)` | Từ node hiện tại **lên** cha | Click con → tìm card bọc ngoài |
| `element.querySelector(sel)` | Từ node hiện tại **xuống** con | Tìm nút Xoa trong card |

### Thử ngay Console — kiểm tra closest

Sau render, click card hoặc chạy:

```js
const h3 = document.querySelector(".cm-card h3");
h3?.closest(".cm-card")?.dataset.sku;  // SKU card chứa h3 đó
```

---

## 9. `map` / `filter` / `reduce` — cùng tư duy, khác output với render

Buổi 6 học bộ tứ trên **mảng thuần**. Buổi 7 thêm bước cuối: **đổ mảng ra DOM**.

### 9.1. Sơ đồ kết nối (ASCII)

```
                    products (8)
                          │
     ┌────────────────────┼────────────────────┐
     │                    │                    │
     ▼                    ▼                    ▼
 .filter(dk)          .map(bien)           .find(sku)
 mảng con             mảng cùng size       1 object
     │                    │
     │                    │
     └────────┬───────────┘
              │
              ▼
         .reduce(gop)  ──► inventoryValue = 41380000  (P06 — một SỐ)
              │
              │
     ┌────────┴────────┐
     │                 │
     ▼                 ▼
 render(list)     for + createElement
 mảng → DOM       (tương đương map thủ công)
     │
     ▼
 .cm-grid + count "Hien thi: N san pham"
```

### 9.2. Bảng đối chiếu

| Thao tác | Input | Output | Side effect DOM? |
|----------|-------|--------|:----------------:|
| `filter` | mảng 8 | mảng 0–8 | Không |
| `map` | mảng 8 | mảng 8 (biến đổi) | Không |
| `reduce` | mảng 8 | **một** số/chuỗi | Không |
| `render(list)` | mảng 0–8 | `undefined` | **Có** — vẽ card |

### 9.3. Pipeline đầy đủ CampusMart P07

```js
// 1. Lọc (filter thuần)
const mice = products.filter((p) => p.category_id === 2);  // 3 SP

// 2. Sort bản sao (không mutate)
const sorted = [...mice].sort((a, b) => a.price - b.price);

// 3. Render (map → DOM)
render(sorted);
// → 3 card, "Hien thi: 3 san pham", card đầu MS-03
```

### Thử ngay Console — pipeline một dòng

```js
render(
  [...products]
    .filter((p) => p.category_id === 1)
    .sort((a, b) => a.price - b.price)
);
// 3 card Ban phim, giá tăng: KB-02 → KB-01 → KB-03
document.querySelector('[data-testid="cm-visible-count"]').textContent;
// "Hien thi: 3 san pham"
render(products);  // reset
```

---

## 10. Marker EXPECT & thứ tự khởi động

```html
<!-- CM_EXPECT product_count=8 filter_counts=8,3,3,2 first_sorted_sku=MS-03 -->
```

Cuối `app.js`: `render(products)` → `initEvents()` (filter + sort + delegation) → `updateStats()` nếu giữ từ P06. Chi tiết từng hàm: mục 5–8 và [Phiếu 07](./worksheets/phieu-07-dom-render-filter.md).

---

## 11. Hình ảnh nhớ (memory images)

Dán 4 dòng này đầu vở phần DOM Events:

```
① Render = máy in 3D — mảng là file CAD, .cm-grid là khung, mỗi lần in xóa sạch rồi in lại
② Event loop = quầy một nhân viên — xếp hàng click/change, xong việc hiện tại mới gọi số tiếp
③ Number(select.value) = cầu nối — HTML nói "2" (chữ), JS data nói 2 (số), không cầu = filter rỗng
④ Delegation = micro loa tại .cm-grid — một loa phủ mọi card, kể card sinh sau và sau mỗi lần render
```

---

## 12. Câu hỏi tự kiểm tra

1. View-source `.cm-grid` rỗng nhưng trang có 8 card — giải thích vì sao?
2. Filter "Chuot" ra 0 card — nguyên nhân phổ biến nhất là gì?
3. `"3" === 3` và `Number("3") === 3` — kết quả mỗi biểu thức?
4. Sau sort, `products[0].sku` có luôn là MS-03 không? Vì sao?
5. Visible count khi chọn Man hinh phải là chuỗi gì (exact)?
6. Vì sao dùng `change` cho `<select>` thay vì `input`?
7. Event delegation giải quyết vấn đề gì khi card được tạo trong `render()`?
8. Khác nhau giữa `event.target` và `event.currentTarget`?
9. `render` có mutate mảng `products` gốc không?
10. Card đầu sau sort "Tat ca" — sku và price?
11. `inventoryValue(products)` trên trang filter Chuot vẫn bằng bao nhiêu?
12. Vì sao badge dùng `stockLevel(p.qty)` thay vì if/else viết lại?

### Gợi ý đáp án

1. Card do JS `createElement` sau load — không có trong HTML nguồn.  
2. Quên `Number()` — so sánh `"2" === 2` false.  
3. `false` và `true`.  
4. **Không** nếu dùng `[...currentList].sort` — chỉ bản sao sort; gốc giữ thứ tự ban đầu.  
5. **`Hien thi: 2 san pham`**.  
6. `change` fire khi chọn xong option; `input` fire mỗi ký tự (cho text field).  
7. Listener gắn một lần trên cha; card mới/xóa không cần gắn lại.  
8. `target` = phần tử click; `currentTarget` = phần tử gắn listener.  
9. **Không** — `render` chỉ đọc `list` và ghi DOM.  
10. **MS-03**, **250000**.  
11. **41380000** — stats tính trên mảng gốc 8 SP.  
12. Một nguồn exact CANONICAL; autograder; tránh typo chuỗi.

---

## 13. Lỗi thường gặp (chi tiết)

| # | Triệu chứng | Nguyên nhân | Cách xử lý |
|---|-------------|-------------|------------|
| 1 | Filter DM ra **0 card** | `"2" === 2` false | `Number(select.value)` |
| 2 | Filter đúng nhưng count sai | Quên cập nhật `countEl` trong `render` | Ghi `Hien thi: ${list.length} san pham` cuối render |
| 3 | Card **nhân đôi** mỗi lần filter | Quên `grid.innerHTML = ""` | Xóa trước vòng for |
| 4 | Sort xong filter loạn | `products.sort()` mutate gốc | `[...currentList].sort(...)` |
| 5 | Sort không ra MS-03 đầu | Sort desc nhầm `(b,a)` | `(a,b) => a.price - b.price` |
| 6 | View-source vẫn thấy 8 card | Chưa xóa HTML tĩnh P05 | Xóa `<article>` trong `.cm-grid` |
| 7 | Autograder thiếu testid | Quên `dataset.testid = "cm-product-row"` | Gắn trong render |
| 8 | Badge stockLevel fail | Typo `"Sap het"` / thừa space | Copy exact CANONICAL §3 |
| 9 | Click card không log SKU | Listener gắn từng card trước render | Delegation trên `.cm-grid` |
| 10 | `closest is not a function` | Gọi trên text node | Dùng `e.target.closest` (Element) |
| 11 | `products is not defined` | Thứ tự script sai | `data.js` trước `app.js` |
| 12 | Stats mất sau chuyển P07 | Quên gọi `updateStats()` | Gọi sau `render(products)` |
| 13 | Sort 3 SP Chuot — card đầu sai | Sort `products` thay vì `currentList` | Sort phạm vi đang hiển thị |
| 14 | `inventoryValue` đổi khi filter | Nhầm subset với gốc | Stats luôn từ `products` (8) |

---

## 14. Checklist trước Buổi 8

- [ ] `<title>` exact: `CampusMart — Filter (Buoi 7)`
- [ ] View-source: `.cm-grid` **rỗng**; DevTools: **8 card** do JS
- [ ] `render(list)` vẽ lại lưới từ mảng bất kỳ; `createElement` + `textContent`
- [ ] Card có `data-testid="cm-product-row"`, `data-sku`, tên DM chữ, giá số thô, badge `stockLevel` exact
- [ ] Filter 4 mốc: **all=8**, Ban phim=**3**, Chuot=**3**, Man hinh=**2**
- [ ] Visible count exact: `Hien thi: 8 san pham` (và 3/3/2 khi lọc)
- [ ] Sort giá tăng dần: `[...currentList].sort`; **MS-03 (250000)** đầu khi Tat ca
- [ ] Mảng `products` gốc **không đổi thứ tự** sau sort
- [ ] Delegation: click card → `console.log` SKU
- [ ] Stats (nếu giữ P06): `So san pham = 8`, `Tong gia tri kho = 41380000`
- [ ] Comment `CM_EXPECT product_count=8 filter_counts=8,3,3,2 first_sorted_sku=MS-03`
- [ ] Xong [Phiếu 07](./worksheets/phieu-07-dom-render-filter.md)

---

## 15. Bài tập trên lớp & về nhà (chuỗi CampusMart UI)

| Phần | Làm gì | Chi tiết |
|------|--------|----------|
| **Trên lớp (~20')** | Xóa card tĩnh; `render(products)` | [Phiếu 07 — mục 2](./worksheets/phieu-07-dom-render-filter.md) |
| **Về nhà (~25–40')** | Filter + visible count + sort + delegation | [Phiếu 07 — mục 3](./worksheets/phieu-07-dom-render-filter.md) |

Repo nộp: **`cse391-cm-07`** · video OBS: view-source grid rỗng → lọc 4 option đọc count → sort MS-03 đầu → click card in SKU.

Thuyết trình 30–60s: **Vì sao phải `Number(select.value)`?**

---

## 16. Cầu nối sang Buổi 8

```
Buổi 7 đạt được:
  ✓ Trang TƯƠNG TÁC — filter, sort, click card
  ✓ render(list) — pattern UI = f(data)
  ✓ Event loop + delegation — nền cho CRUD

Buổi 7 CHƯA có:
  ✗ Dashboard THÊM sản phẩm mới (form submit)
  ✗ Dữ liệu MẤT khi F5 — chưa localStorage
  ✗ Code dồn app.js — chưa ES Module tách file
  ✗ Validate form — chưa báo lỗi field
```

Trang đã "sống" — nhưng mỗi lần reload quay về 8 SP gốc, và **không thêm được sản phẩm từ UI**. Buổi 8: **ES Module** (`import`/`export`), **localStorage** (nhớ filter + danh sách), **validate form** JS — ghép thành mốc 1: **Dashboard CRUD có Form Thêm**.

→ Tiếp: [Buổi 8](./08-buoi-08-module-localstorage-validate.md) · [Phiếu 08](./worksheets/phieu-08-module-localstorage.md)

---

*CSE391 – Ebook Frontend CampusMart UI | Buổi 7/12*
