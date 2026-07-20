# Phiếu 07 — Render từ mảng + Filter danh mục CampusMart

| | |
|---|---|
| **Buổi** | 7 |
| **Thời lượng** | ≥ 45 phút (trên lớp ~20' + về nhà ~25–40') |
| **Repo** | `cse391-cm-07` |
| **Nhận từ Phiếu 06** | `data.js` + `app.js` với 5 hàm (`stockLevel`, `inventoryValue`…) |
| **Mang sang Phiếu 08** | `render(list)` + filter + sort + visible count |
| **Dataset** | [`CANONICAL-DATA.md`](../worksheets/CANONICAL-DATA.md) — §3 stockLevel, §8.3 testid filter/grid |

### Chuẩn đầu ra (CLO)

1. Xóa 8 card tĩnh; **`render(list)`** sinh toàn bộ card từ mảng JS.  
2. Card render giữ `data-testid="cm-product-row"`, `data-sku`, tên DM chữ, giá số thô, badge `stockLevel`.  
3. Filter `<select>` 4 option: **all=8**, Ban phim=**3**, Chuot=**3**, Man hinh=**2**.  
4. `cm-visible-count` hiển thị exact **`Hien thi: N san pham`** — N khớp số card.  
5. Sort giá tăng dần bằng **bản sao mảng** — card đầu **MS-03 (250000)**; không phá `products` gốc.  
6. Event delegation: click card → Console in SKU.

---

## 1. Tóm tắt lý thuyết (đọc lại trước khi code)

- **Render** = `grid.innerHTML = ""` → vòng lặp mảng → `createElement` + `textContent` → `appendChild`.
- HTML chỉ còn **khung rỗng** `.cm-grid`; mọi card do JS sinh sau khi load.
- Filter = tạo **mảng con** → gọi lại `render(list)` — không ẩn/hiện card bằng CSS.
- `select.value` là **chuỗi** `"2"`; `category_id` là **số** `2` → bắt buộc `Number(value)` khi so `===`.
- Sort: `[...list].sort((a,b) => a.price - b.price)` — spread tạo bản sao, không mutate gốc.
- **Delegation:** một listener trên `.cm-grid`, dùng `e.target.closest(".cm-card")`.

Chi tiết: [Buổi 7](../07-buoi-07-dom-events-render-filter.md).

---

## 2. Bài trên lớp (~20 phút) — Thay card tĩnh bằng `render(products)`

### Yêu cầu

Copy repo Phiếu 06 sang `cse391-cm-07`, rồi:

1. Đổi `<title>` exact: **`CampusMart — Filter (Buoi 7)`**.
2. **Xóa** 8 `<article class="cm-card">…</article>` trong HTML — `.cm-grid` để **rỗng**:

```html
<div class="cm-grid" data-testid="cm-product-table"></div>
```

3. Viết `render(list)` trong `app.js` (mỗi card):

```js
function categoryName(id) {
  const c = categories.find((c) => c.id === id);
  return c ? c.name : "?";
}

function render(list) {
  const grid = document.querySelector('[data-testid="cm-product-table"]');
  grid.innerHTML = "";

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
    price.textContent = String(p.price);

    const stock = document.createElement("p");
    stock.className = "cm-stock";
    stock.textContent = stockLevel(p.qty);

    card.append(h3, cat, price, stock);
    grid.appendChild(card);
  }
}

render(products);
```

4. Giữ `#stats` từ P06 (nếu có) — stats vẫn tính từ `products` gốc 8 phần tử.

### Cách thử trên lớp (DevTools)

| # | Thử | Thao tác | Kỳ vọng |
|---|-----|----------|---------|
| T1 | View Source | Ctrl+U / Cmd+Option+U | Trong `.cm-grid` **không** có `<article class="cm-card">` |
| T2 | Elements | F12 → inspect `.cm-grid` | **8** `<article.cm-card>` con |
| T3 | data-sku | Inspect card đầu | Có `data-sku="KB-01"` (hoặc SKU đầu mảng) |
| T4 | Badge | Card bất kỳ | `.cm-stock` = stockLevel đúng (vd KB-01 → `Sap het`) |
| T5 | Thử data | Tạm thêm 1 SP vào `data.js` → reload | **9** card → xóa SP thử trước nộp |

**Nếu T1 có card nhưng T2 không:** script lỗi — xem Console đỏ.

### Output kỳ vọng (trên lớp)

View-source: grid rỗng. Trang hiển thị: 8 card đầy đủ như P06 nhưng do JS sinh.

**Checkpoint giảng viên:** SV chứng minh source rỗng + Elements có 8 card; `data-sku` đủ 8 SKU canonical.

---

## 3. Bài về nhà (~25–40 phút) — Filter + count + sort + delegation

Tiếp tục **cùng repo** `cse391-cm-07`.

### Nhiệm vụ A — Bộ lọc danh mục

Thêm trên lưới (trong `#products`):

```html
<label for="filter-cat">Loc theo danh muc</label>
<select id="filter-cat" data-testid="cm-filter-category">
  <option value="all">Tat ca</option>
  <option value="1">Ban phim</option>
  <option value="2">Chuot</option>
  <option value="3">Man hinh</option>
</select>
<p data-testid="cm-visible-count"></p>
```

JS — gợi ý biến trạng thái:

```js
let currentList = products;
const select = document.querySelector('[data-testid="cm-filter-category"]');

select.addEventListener("change", () => {
  const v = select.value;
  currentList =
    v === "all"
      ? products
      : products.filter((p) => p.category_id === Number(v));
  render(currentList);
});
```

#### Cách thử chi tiết — Nhiệm vụ A

1. Reload trang → mặc định **Tat ca** → đếm card:

```js
document.querySelectorAll(".cm-card").length
// 8
```

2. Chọn lần lượt từng option — sau mỗi lần chọn, chạy Console:

| Option | Lệnh kiểm | Kỳ vọng |
|--------|-----------|---------|
| Tat ca | `.cm-card` count | **8** |
| Ban phim | count + SKU | **3** — KB-01, KB-02, KB-03 |
| Chuot | count + SKU | **3** — MS-01, MS-02, MS-03 |
| Man hinh | count + SKU | **2** — MN-01, MN-02 |

3. Kiểm SKU sau filter Chuot:

```js
[...document.querySelectorAll(".cm-card")].map(c => c.dataset.sku)
// ["MS-01","MS-02","MS-03"]  (thứ tự có thể khác, đủ 3 SKU)
```

4. **Demo bẫy Number (bắt buộc hiểu):** tạm sửa filter **sai** — bỏ `Number(v)`:

```js
// SAI — demo 30 giây rồi sửa lại
products.filter((p) => p.category_id === v)
```

Chọn **Chuot** → **0 card** (vì `"2" === 2` là `false`). Console:

```js
typeof select.value, select.value === 2
// "string" false
```

Sửa lại `Number(v)` → Chuot lại **3** card. Ghi nhớ cho thuyết trình video.

---

### Nhiệm vụ B — Visible count

Cuối hàm `render(list)`:

```js
const countEl = document.querySelector('[data-testid="cm-visible-count"]');
if (countEl) {
  countEl.textContent = `Hien thi: ${list.length} san pham`;
}
```

#### Cách thử chi tiết — Nhiệm vụ B

1. Reload → đọc text `[data-testid="cm-visible-count"]`:

```
Hien thi: 8 san pham
```

2. Lọc từng option — **đọc to** trên màn hình (không nhìn lưới):

| Chọn | Text exact |
|------|------------|
| Tat ca | `Hien thi: 8 san pham` |
| Ban phim | `Hien thi: 3 san pham` |
| Chuot | `Hien thi: 3 san pham` |
| Man hinh | `Hien thi: 2 san pham` |

3. Console chéo:

```js
const t = document.querySelector('[data-testid="cm-visible-count"]').textContent;
const n = document.querySelectorAll(".cm-card").length;
t === `Hien thi: ${n} san pham`  // true mọi lần đổi filter
```

---

### Nhiệm vụ C — Sort giá tăng dần

```html
<button type="button" id="sort-price">Gia tang dan</button>
```

```js
document.querySelector("#sort-price").addEventListener("click", () => {
  const sorted = [...currentList].sort((a, b) => a.price - b.price);
  render(sorted);
});
```

**Kỳ vọng ở Tat ca + sort:** card **đầu tiên** trong DOM = **MS-03**, giá **250000**; card **cuối** = **MN-02**, **8500000**.

Sort phải tôn trọng filter đang chọn (sort trong phạm vi `currentList`).

#### Cách thử chi tiết — Nhiệm vụ C

1. Chọn **Tat ca** → bấm **Gia tang dan**.
2. Elements → card đầu `.cm-grid`:

```js
document.querySelector(".cm-card").dataset.sku
// "MS-03"

document.querySelector(".cm-card .cm-card-price").textContent
// "250000"
```

3. Card cuối:

```js
const cards = document.querySelectorAll(".cm-card");
cards[cards.length - 1].dataset.sku
// "MN-02"
```

4. **Kiểm bất biến mảng gốc:**

```js
products.map(p => p.sku).join(",")
// Thứ tự gốc CANONICAL — KHÔNG phải MS-03 đầu tiên
// (sort chỉ đổi DOM, không .sort() trực tiếp products)
```

5. Chọn **Man hinh** (2 card) → sort → card đầu **MN-01** (6400000) < **MN-02** (8500000).

6. Visible count vẫn **`Hien thi: 2 san pham`** sau sort.

---

### Nhiệm vụ D — Event delegation (chuẩn bị P12)

```js
const grid = document.querySelector('[data-testid="cm-product-table"]');

grid.addEventListener("click", (e) => {
  const card = e.target.closest(".cm-card");
  if (!card) return;
  console.log("Ban vua bam card:", card.dataset.sku);
});
```

#### Cách thử chi tiết — Nhiệm vụ D

1. Console mở sẵn → click vào **tên** sản phẩm trên card KB-02.
2. Console in: `Ban vua bam card: KB-02` (SKU khớp card).
3. Click vùng trống trong `.cm-grid` (không trúng card) → **không** log.
4. Đổi filter → click card còn lại → log SKU mới — chứng minh **một listener** phục vụ mọi card render sau.

---

### Nhiệm vụ E — Marker

```html
<!-- CM_EXPECT product_count=8 filter_counts=8,3,3,2 first_sorted_sku=MS-03 -->
```

---

## 4. OUTPUT / EXPECT

| Key | Value bắt buộc |
|-----|----------------|
| `page_title` | `CampusMart — Filter (Buoi 7)` |
| `grid.source` | rỗng trong HTML nguồn; card sinh bằng JS |
| `render.card_count` | **8** (mặc định Tat ca) |
| `filter.all / .1 / .2 / .3` | **8 / 3 / 3 / 2** card |
| `filter.skus.1` | KB-01, KB-02, KB-03 |
| `filter.skus.2` | MS-01, MS-02, MS-03 |
| `filter.skus.3` | MN-01, MN-02 |
| `visible_count.text` | `Hien thi: N san pham` — N khớp bảng filter |
| `sort.first_sku` | **MS-03** @ Tat ca + sort |
| `sort.last_sku` | **MN-02** @ Tat ca + sort |
| `sort.immutable` | `products` gốc không đổi thứ tự |
| `card.badge` | stockLevel exact CANONICAL §3 |
| `delegation` | click card → log SKU |
| `CM_EXPECT` | như Nhiệm vụ E |

### Checklist tự chấm trước nộp

```
[ ] title exact P07
[ ] view-source: .cm-grid rỗng
[ ] render 8 card + data-sku + badge
[ ] filter 4 option → count 8/3/3/2
[ ] visible count text khớp từng lần lọc
[ ] sort Tat ca → MS-03 đầu, MN-02 cuối
[ ] products gốc không bị .sort() mutate
[ ] delegation click → console SKU
[ ] CM_EXPECT comment
[ ] Video đủ kịch bản box nộp
```

---

## 5. Box nộp bài

```
╔══════════════════════════════════════════════════════════════╗
║  NỘP PHIẾU 07                                                ║
╠══════════════════════════════════════════════════════════════╣
║  1. GitHub repo RIÊNG: cse391-cm-07                          ║
║  2. Video OBS + camera mặt — kịch bản:                       ║
║     - View-source: grid rỗng → trang vẫn 8 card               ║
║     - Lọc lần lượt 4 option, đọc to visible count            ║
║     - Demo Number trap (0 card) → sửa → 3 card Chuot          ║
║     - Bấm "Gia tang dan" → card đầu MS-03                    ║
║     - Click 1 card → console in SKU                          ║
║  3. Thuyết trình 30–60s: vì sao phải Number(select.value)?   ║
╚══════════════════════════════════════════════════════════════╝
```

---

## 6. Lỗi thường gặp khi nộp (tránh)

| Lỗi | Triệu chứng | Cách xử lý |
|-----|-------------|------------|
| Filter 0 card | `"2" === 2` false | `Number(select.value)` |
| Card nhân đôi mỗi filter | Quên `innerHTML = ""` | Xóa grid đầu `render` |
| Sort xong filter loạn | `.sort()` trên `products` | `[...currentList].sort(...)` |
| Visible count sai | Quên cập nhật cuối `render` | Ghi count mỗi lần render |
| Mất testid | Quên `dataset.testid` | `cm-product-row` trên card |
| Card còn trong HTML | View-source không rỗng | Xóa hết card tĩnh |
| innerHTML ghép tên SP | XSS risk P11+ | `createElement` + `textContent` |

---

*CSE391 – Phiếu 07 | CampusMart UI — Render & Filter*
