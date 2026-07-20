# BUỔI 8: Module JS, localStorage, Form validate — Dashboard có Form Thêm

> **Thời lượng gợi ý:** 4–5 giờ tự học (lý thuyết + thử máy + Phiếu 08)  
> **Tiên quyết:** Buổi 7 — `render(list)` + filter danh mục + sort hoạt động trên mảng 8 sản phẩm  
> **Kết quả đầu ra:** Tách ES Module 3 file; localStorage nhớ filter + subscriber; validate form đăng ký; **Dashboard CampusMart có Form Thêm sản phẩm** — **mốc JS thuần CRUD với Create**  
> **Phiếu học tập:** [Phiếu 08](./worksheets/phieu-08-module-localstorage.md) — chuỗi **CampusMart UI** · repo `cse391-cm-08`  
> **Dataset:** [CANONICAL-DATA §8.4–8.7](./worksheets/CANONICAL-DATA.md) — chuỗi lỗi exact, 3 localStorage keys, ca kiểm **MN-03 → 50380000**

---

## 0. Gợi ý lịch trình giảng (cho GV)

| Phút | Nội dung | Gợi ý thao tác |
|------|----------|----------------|
| 0–10 | Ôn P07 + hỏi “F5 filter có giữ không?” | Chiếu trang P07 → chọn Chuot → F5 → mất → ghi câu trả lời |
| 10–25 | Vì sao Module + Storage (ASCII before/after) | Vẽ sơ đồ 3 file trên bảng |
| 25–45 | ES Module: export/import, `type="module"`, Live Server | Live-code tách `data.js` / `helpers.js` / `app.js` |
| 45–60 | Bảng so sánh global vs module; lỗi CORS `file://` | Demo mở file trực tiếp → lỗi → bật Live Server |
| 60–80 | localStorage: diagram, 3 keys, stringify/parse | DevTools Application tab; ghi `cm_filter` |
| 80–95 | Pattern nhớ filter qua F5 | SV chọn Chuot → F5 → vẫn Chuot |
| 95–115 | Form validate đăng ký: preventDefault + 2 quy tắc | Live-code handler; đọc chuỗi exact §8.4 |
| 115–140 | **Form Thêm sản phẩm ★ mốc JS thuần** | Chiếu Dashboard ASCII; validate 3 quy tắc |
| 140–155 | `products.push` → `render` + `renderStats`; ca MN-03 | Demo thêm MN-03 → đọc to **9 / 3 / 50380000** |
| 155–165 | §6.1 push vs React; §6.2 login giả (bonus) | Bảng so sánh immutable spread |
| 165–180 | **Thực hành Phiếu 08 mục 2–3** | Checkpoint module + filter persist |
| 180–190 | Checklist + cầu nối Buổi 9 | Giao về nhà Form Thêm + video OBS |

> Trên lớp **ưu tiên xong tách module + filter persist qua F5**. Form validate và Form Thêm có thể hoàn thiện về nhà — nhưng ca **MN-03 → 50380000** là điểm khóa mốc JS thuần.

---

## 1. Mục tiêu buổi học

Sau Buổi 8, bạn có thể:

1. Giải thích vì sao cần **tách file ES Module** thay vì một `app.js` toàn cục phình to.
2. Dùng `export` / `import` với `<script type="module">`; biết bắt buộc chạy qua **Live Server** (không `file://`).
3. So sánh biến **global** (Buổi 6–7) với biến **module scope** (Buổi 8).
4. Lưu, đọc, xóa dữ liệu **localStorage** bằng `JSON.stringify` / `JSON.parse`; phòng `null` bằng `??`.
5. Ghi nhớ trạng thái filter qua F5 với key `cm_filter`.
6. Lưu subscriber hợp lệ vào key `cm_subscribers` (JSON array).
7. Chặn submit mặc định (`preventDefault`), validate form đăng ký 2 quy tắc với **chuỗi lỗi exact**.
8. Viết hàm `renderStats()` gọi lại được sau mỗi thay đổi dữ liệu.
9. **Hoàn thành mốc JS thuần: Dashboard CampusMart phục vụ CRUD với Form Thêm sản phẩm** — validate 3 quy tắc → `products.push` → `render` + `renderStats`.
10. Chạy ca kiểm chuẩn **MN-03**: 9 card · filter Man hinh = 3 · tổng kho **50380000**.
11. (Điểm cộng) Login giả với `cm_auth` — hiểu giới hạn bảo mật phía client.
12. Hoàn thành [Phiếu 08](./worksheets/phieu-08-module-localstorage.md).

### 1.1. Mốc quan trọng — Dashboard có Form Thêm

```
╔══════════════════════════════════════════════════════════════════╗
║  ★ MỐC JS THUẦN (Buổi 8) ★                                      ║
║                                                                  ║
║  Dashboard CampusMart = stats + filter + grid + Form Thêm        ║
║                                                                  ║
║  Bạn đã có:                                                      ║
║    ✓ Read  — render danh sách từ mảng (P07)                      ║
║    ✓ Filter — lọc theo danh mục + nhớ qua F5 (P08)               ║
║    ✓ Create — Form Thêm sản phẩm (P08) ← MỚI                     ║
║                                                                  ║
║  Chưa có (sang React P09–P12):                                   ║
║    ✗ Update / Delete                                             ║
║    ✗ CRUD thực thể thứ 2 (category)                              ║
║    ✗ Render tự động khi state đổi (React lo)                     ║
╚══════════════════════════════════════════════════════════════════╝
```

> Đây là **đầu ra tối thiểu** của giai đoạn JS thuần: một dashboard phục vụ công tác CRUD, **có Form Thêm**. Buổi 9 chuyển sang React để hoàn chỉnh ứng dụng.

---

## 2. Vì sao Module + Storage?

Sau Buổi 7, CampusMart đã “sống” — nhưng còn hai cơn đau:

### 2.1. Cơn đau 1 — `app.js` phình to, biến toàn cục

```
Buổi 7 (một file app.js):
  ✓ render(list) hoạt động
  ✓ filter + sort hoạt động
  ✗ data + helpers + render + events trộn một chỗ
  ✗ Biến global dễ bị ghi đè nhầm giữa các hàm
  ✗ Khó autograder tìm đúng hàm trong file dài
```

### 2.2. Cơn đau 2 — F5 là mất hết

```
Buổi 7:
  ✓ Chọn filter "Chuot" → lưới 3 card
  ✗ F5 → select về "Tat ca", lưới 8 card
  ✗ Form đăng ký vừa nhập → biến mất (chưa lưu đâu cả)
  ✗ Sản phẩm vừa thêm bằng Form Thêm → F5 về 8 (chấp nhận ở mốc này)
```

**ES Module** giải quyết (1): mỗi file một nhiệm vụ, chỉ “xuất” thứ cần chia sẻ.  
**localStorage** giải quyết (2): kho key-value nhỏ nằm ngay trong trình duyệt — sống qua F5.

### 2.3. Sơ đồ before/after tách module (ASCII bắt buộc)

```
                    TRƯỚC (Buổi 6–7)              SAU (Buổi 8 — ES Module)
              ┌─────────────────────────┐      ┌─────────────────────────┐
              │  data.js                │      │  data.js                │
              │  products (global)      │      │  export products        │
              │  categories (global)    │      │  export categories      │
              └───────────┬─────────────┘      └───────────┬─────────────┘
                          │                                │ import
              ┌───────────▼─────────────┐      ┌───────────▼─────────────┐
              │  app.js                 │      │  helpers.js             │
              │  helpers trộn chung     │      │  export stockLevel      │
              │  render + filter + sort │      │  export inventoryValue  │
              │  (mọi thứ, toàn cục)    │      │  export lineTotal ...   │
              └─────────────────────────┘      └───────────┬─────────────┘
                                                           │ import
                                               ┌───────────▼─────────────┐
                                               │  app.js (entry duy nhất)│
                                               │  render + filter        │
                                               │  validate + Form Thêm   │
                                               │  localStorage I/O       │
                                               └─────────────────────────┘

   HTML: 2 script thường              HTML: 1 script type="module"
         data.js → app.js                    app.js (import chain)
```

> Một entry duy nhất trong HTML — mọi dependency đi theo chuỗi `import`. Giống tinh thần “component tree” sau này trong React.

---

## 3. ES Module — deep dive

### 3.1. Cú pháp export / import

```js
// data.js — chỉ chứa dữ liệu CANONICAL
export const categories = [
  { id: 1, name: "Ban phim" },
  { id: 2, name: "Chuot" },
  { id: 3, name: "Man hinh" },
];

export const products = [
  { sku: "KB-01", name: "Keychron K2", category_id: 1, price: 1890000, qty: 3 },
  // ... 7 sản phẩm còn lại — khớp CANONICAL §5
];
```

```js
// helpers.js — 5 hàm từ Phiếu 06, mỗi hàm thêm export
export function lineTotal(price, qty) { return price * qty; }
export function inventoryValue(list) {
  return list.reduce((sum, p) => sum + lineTotal(p.price, p.qty), 0);
}
export function stockLevel(qty) {
  if (qty <= 1) return "Can nhap";
  if (qty <= 4) return "Sap het";
  return "Du";
}
export function findProductBySku(list, sku) { return list.find((p) => p.sku === sku); }
export function countByCategory(list, catId) {
  return list.filter((p) => p.category_id === catId).length;
}
```

```js
// app.js — điểm vào duy nhất
import { categories, products } from "./data.js";
import {
  stockLevel,
  inventoryValue,
  lineTotal,
  findProductBySku,
  countByCategory,
} from "./helpers.js";

// ... render, filter, validate, Form Thêm ...
```

```html
<!-- index.html — CHỈ còn MỘT thẻ script -->
<script type="module" src="app.js"></script>
```

### 3.2. Ba điều bắt buộc nhớ

| # | Quy tắc | Hậu quả nếu vi phạm |
|---|---------|---------------------|
| 1 | Thẻ script phải có `type="module"` | `Uncaught SyntaxError: Cannot use import statement` |
| 2 | Đường dẫn import có `./` và đuôi `.js` | `Failed to resolve module specifier` |
| 3 | Chạy qua HTTP server (Live Server) | CORS / module blocked khi mở `file://` |

### 3.3. Live Server — bắt buộc từ Buổi 8

```
┌─────────────────────────────────────────────────────────────────┐
│  CÁCH MỞ TRANG          │  Module load?  │  Ghi chú             │
├─────────────────────────┼────────────────┼──────────────────────┤
│  Double-click index.html│  ✗ LỖI CORS    │  file:// — CẤM       │
│  VS Code Live Server    │  ✓ OK          │  http://127.0.0.1:…  │
│  python -m http.server  │  ✓ OK          │  http://localhost:…  │
└─────────────────────────────────────────────────────────────────┘

Quy trình VS Code:
  1. Cài extension "Live Server"
  2. Chuột phải index.html → "Open with Live Server"
  3. Thanh địa chỉ phải bắt đầu http:// — KHÔNG phải file://
```

### 3.4. Bảng so sánh: global script vs ES Module

| Khía cạnh | Global (P06–P07) | ES Module (P08) |
|-----------|------------------|-----------------|
| Khai báo dữ liệu | `const products = [...]` | `export const products = [...]` |
| Dùng hàm helper | Gọi trực tiếp (cùng scope) | `import { fn } from "./helpers.js"` |
| HTML script tags | 2 tag: `data.js` + `app.js` | 1 tag: `type="module" src="app.js"` |
| Biến trên `window` | Có — Console gọi `products` | Không — module scope riêng |
| Thứ tự load | Phải data trước app | `import` tự resolve dependency |
| Chạy `file://` | Được (P06–P07) | **Không** — cần server |
| Debug Console | `products.length` trực tiếp | Cần `window.debugProducts = products` tạm |
| Autograder | Tìm biến global | Tìm `export` trong từng file |

### Thử ngay Console — module scope

Sau khi trang load qua Live Server:

```js
// Thử gọi trực tiếp — sẽ lỗi vì module scope
products;                    // ReferenceError: products is not defined

// Cách debug tạm (thêm vào cuối app.js khi dev):
// window.debugProducts = products;
// Rồi F5 → Console:
window.debugProducts.length; // ? → 8
```

---

## 4. localStorage — bộ nhớ sống qua F5

### 4.1. localStorage là gì?

**localStorage** là API của trình duyệt — lưu cặp **key → value** dạng **chuỗi**, gắn với **origin** (domain + port). Dữ liệu **sống qua F5**, đóng tab, mở lại — cho đến khi user xóa cache hoặc gọi `removeItem`.

```
┌─────────────────────────────────────────────────────────────────┐
│                    TRÌNH DUYỆT (origin)                          │
│                                                                 │
│   ┌─────────────┐         ┌─────────────────────────────────┐   │
│   │   Trang web │  read/  │  localStorage (key-value store) │   │
│   │   (JS app)  │◄───────►│                                 │   │
│   └─────────────┘  write  │  "cm_filter"      → "2"         │   │
│                           │  "cm_subscribers" → '[{...}]'   │   │
│                           │  "cm_auth"        → "true"      │   │
│                           └─────────────────────────────────┘   │
│                                      │                          │
│                                      │ F5 / đóng tab / mở lại   │
│                                      ▼                          │
│                              Dữ liệu VẪN CÒN                    │
└─────────────────────────────────────────────────────────────────┘

   DevTools → Application → Local Storage → chọn origin → xem/sửa/xóa tay
```

### 4.2. Ba key của môn (CANONICAL §8.5)

| Key | Kiểu value | Nội dung | Dùng khi |
|-----|------------|----------|----------|
| `cm_filter` | Chuỗi thường | `"all"` \| `"1"` \| `"2"` \| `"3"` | Nhớ danh mục filter đang chọn |
| `cm_subscribers` | JSON string | `[{name, email, category_id}, ...]` | Danh sách đăng ký nhận tin |
| `cm_auth` | Chuỗi | `"true"` sau login đúng | Login giả (bonus) |

> **Lưu ý:** `products` sau Form Thêm **không** lưu localStorage ở mốc P08 — F5 về 8 sản phẩm CORE. Persist sản phẩm sang P11 React / backend CSE485.

### 4.3. JSON.stringify / JSON.parse — bắt buộc với object/array

localStorage **chỉ nhận chuỗi**. Object/array phải stringify khi ghi, parse khi đọc.

```js
// GHI — object → chuỗi JSON
const subscriber = { name: "An", email: "an@tlu.edu.vn", category_id: 1 };
localStorage.setItem("cm_subscribers", JSON.stringify([subscriber]));

// ĐỌC — chuỗi → object (PHÒNG null lần đầu!)
const raw = localStorage.getItem("cm_subscribers");
const subs = JSON.parse(raw ?? "[]");   // lần đầu raw = null → parse "[]" → []

// XÓA một key
localStorage.removeItem("cm_auth");

// XÓA toàn bộ (cẩn thận!)
localStorage.clear();
```

### 4.4. Pattern “nhớ filter” qua F5

```js
const select = document.querySelector('[data-testid="cm-filter-category"]');

function applyFilter(value) {
  const list =
    value === "all"
      ? products
      : products.filter((p) => p.category_id === Number(value));
  render(list);
}

// Khi user đổi filter — GHI localStorage TRƯỚC hoặc SAU render đều OK
select.addEventListener("change", () => {
  const v = select.value;
  localStorage.setItem("cm_filter", v);
  applyFilter(v);
});

// Khi tải trang — ĐỌC localStorage, khôi phục select + render
const saved = localStorage.getItem("cm_filter") ?? "all";
select.value = saved;
applyFilter(saved);
```

```
Luồng filter persist:

  User chọn "Chuot" (value="2")
       │
       ▼
  setItem("cm_filter", "2")
       │
       ▼
  applyFilter("2") → render 3 card
       │
       ▼
  User bấm F5
       │
       ▼
  getItem("cm_filter") → "2"
       │
       ▼
  select.value = "2" + applyFilter("2") → vẫn 3 card ✓
```

> **Giới hạn:** chỉ lưu chuỗi (~5MB/origin); không bảo mật; không thay backend (CSE485). Luôn `JSON.parse(getItem(...) ?? "[]")` — tránh crash khi key chưa tồn tại.

### Thử ngay Console — localStorage

Trên trang bất kỳ (Live Server):

```js
localStorage.setItem("demo", JSON.stringify({ a: 1, b: [2, 3] }));
JSON.parse(localStorage.getItem("demo"));     // {a: 1, b: [2, 3]}
// F5 trang → chạy lại dòng trên → vẫn còn!
localStorage.removeItem("demo");
localStorage.getItem("demo");                 // null
```

---

## 5. Form validate — form đăng ký nhận tin

Form đăng ký từ Phiếu 02 (`data-testid="cm-subscribe-form"`) đã có HTML `required` — đó là **tầng 0**. Buổi 8 thêm **tầng 1 JS**: quy tắc riêng + thông báo tiếng Việt exact + không reload trang + lưu localStorage.

### 5.1. Luồng preventDefault (ASCII bắt buộc)

```
  User bấm "Dang ky" (type=submit)
           │
           ▼
  ┌────────────────────────────────────┐
  │  Trình duyệt MUỐN: reload / gửi   │
  │  form theo action mặc định         │
  └────────────────┬───────────────────┘
                   │
                   ▼
  e.preventDefault()  ◄── dòng ĐẦU TIÊN handler submit
                   │
                   ▼
  ┌────────────────────────────────────┐
  │  Thu thập: name, email, category   │
  │  Trim chuỗi; ép category_id Number │
  └────────────────┬───────────────────┘
                   │
                   ▼
  ┌────────────────────────────────────┐
  │  Kiểm tra quy tắc:                 │
  │    name.length < 2  → lỗi 1        │
  │    email regex fail → lỗi 2        │
  └────────┬───────────────┬───────────┘
           │ có lỗi       │ hợp lệ
           ▼               ▼
  Ghi #form-msg         push vào
  class cm-error        cm_subscribers
  errors.join(". ")    msg "Dang ky thanh cong"
  KHÔNG lưu           class cm-success
                      form.reset()
```

### 5.2. Bảng quy tắc validate — chuỗi exact §8.4

| # | Điều kiện lỗi | Chuỗi exact (copy nguyên văn) |
|---|---------------|--------------------------------|
| 1 | `name.trim().length < 2` | `Ten toi thieu 2 ky tu` |
| 2 | Email không khớp `/^\S+@\S+\.\S+$/` | `Email khong hop le` |
| — | Hợp lệ | `Dang ky thanh cong` |

> Autograder so sánh **nguyên văn** — không thêm dấu chấm, không viết hoa khác, không dấu tiếng Việt.

### 5.3. Code handler (khung)

```js
form.addEventListener("submit", (e) => {
  e.preventDefault();
  const name = form.elements.name.value.trim();
  const email = form.elements.email.value.trim();

  const errors = [];
  if (name.length < 2) errors.push("Ten toi thieu 2 ky tu");
  if (!/^\S+@\S+\.\S+$/.test(email)) errors.push("Email khong hop le");

  if (errors.length > 0) {
    msgEl.textContent = errors.join(". ");
    msgEl.className = "cm-error";
    return;
  }

  const subs = JSON.parse(localStorage.getItem("cm_subscribers") ?? "[]");
  subs.push({ name, email, category_id: Number(form.elements.category_id.value) });
  localStorage.setItem("cm_subscribers", JSON.stringify(subs));

  msgEl.textContent = "Dang ky thanh cong";
  msgEl.className = "cm-success";
  form.reset();
});
```

| Input | `#form-msg` |
|-------|-------------|
| name=`A`, email=`bad` | `Ten toi thieu 2 ky tu. Email khong hop le` |
| name=`An`, email=`an@tlu.edu.vn` | `Dang ky thanh cong` |

---

## 6. Form Thêm sản phẩm ★ MỐC JS THUẦN

Đây là **trọng tâm Buổi 8** — ghép mọi thứ đã học: module + mảng + render + validate + stats. Trang của bạn từ giờ là **Dashboard CampusMart hoàn chỉnh ở mức JS thuần**: stats, filter, danh sách card, và **Form Thêm**.

### 6.0. Sơ đồ Dashboard lớn (ASCII bắt buộc)

```
┌─────────────────── DASHBOARD CampusMart (JS thuần — Buổi 8) ───────────────────┐
│                                                                                 │
│  ┌─ STATS (#stats, data-testid=cm-stats) ─────────────────────────────────────┐ │
│  │  So san pham = 8                                                           │ │
│  │  Tong gia tri kho = 41380000          ← renderStats() tính từ products[]   │ │
│  └────────────────────────────────────────────────────────────────────────────┘ │
│                                                                                 │
│  ┌─ FILTER BAR ───────────────────────────────────────────────────────────────┐ │
│  │  [Loc danh muc ▼]  data-testid=cm-filter-category                        │ │
│  │       │ cm_filter persist → localStorage                                   │ │
│  │  [Gia tang dan]    sort (giữ từ P07)                                       │ │
│  │  Hien thi: 8 san pham   data-testid=cm-visible-count                     │ │
│  └────────────────────────────────────────────────────────────────────────────┘ │
│                                                                                 │
│  ┌─ PRODUCT GRID (.cm-grid, data-testid=cm-product-table) ────────────────────┐ │
│  │  ┌────────┐ ┌────────┐ ┌────────┐ ┌────────┐                               │ │
│  │  │ KB-01  │ │ KB-02  │ │ KB-03  │ │ MS-01  │  ← render(list)             │ │
│  │  │ Ban... │ │ Ban... │ │ Ban... │ │ Chuot  │    createElement + textContent│ │
│  │  └────────┘ └────────┘ └────────┘ └────────┘                               │ │
│  │  ┌────────┐ ┌────────┐ ┌────────┐ ┌────────┐                               │ │
│  │  │ MS-02  │ │ MS-03  │ │ MN-01  │ │ MN-02  │                               │ │
│  │  └────────┘ └────────┘ └────────┘ └────────┘                               │ │
│  └────────────────────────────────────────────────────────────────────────────┘ │
│                                                                                 │
│  ┌─ FORM THÊM (#add-product) ─────────────────────────────────────────────────┐ │
│  │  <h2>Them san pham</h2>                                                    │ │
│  │  <form data-testid="cm-product-form">                                      │ │
│  │    [sku    ] [ten san pham ]                                               │ │
│  │    [danh muc ▼ -- Chon danh muc --]  ← 3 option + placeholder             │ │
│  │    [gia   ] [so luong]                                                     │ │
│  │    [Them san pham]                                                         │ │
│  │  </form>                                                                   │ │
│  │  <p id="product-form-msg" aria-live="polite"></p>                          │ │
│  │                                                                            │ │
│  │  submit → validate 3 quy tắc                                               │ │
│  │         → products.push(item)                                              │ │
│  │         → render(products) + renderStats()                                 │ │
│  │         → form.reset()                                                     │ │
│  └────────────────────────────────────────────────────────────────────────────┘ │
│                                                                                 │
│  ┌─ FORM ĐĂNG KÝ (P02, validate §5) ────────────────────────────────────────┐ │
│  │  cm-subscribe-form + cm-subscriber-list (localStorage)                     │ │
│  └────────────────────────────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────────────────────────────┘
```

### HTML Form Thêm (`cm-product-form`)

```html
<section id="add-product">
  <h2>Them san pham</h2>
  <form data-testid="cm-product-form">
    <input name="sku" type="text" required />
    <input name="name" type="text" required />
    <select name="category_id" required>
      <option value="">-- Chon danh muc --</option>
      <option value="1">Ban phim</option>
      <option value="2">Chuot</option>
      <option value="3">Man hinh</option>
    </select>
    <input name="price" type="number" min="0" required />
    <input name="qty" type="number" min="0" required />
    <button type="submit">Them san pham</button>
  </form>
  <p id="product-form-msg" aria-live="polite"></p>
</section>
```

### Bảng validate Form Thêm — chuỗi exact §8.4

| # | Điều kiện lỗi | Chuỗi exact |
|---|---------------|-------------|
| 1 | `sku` / `name` rỗng HOẶC `category_id` chưa chọn | `Thieu thong tin bat buoc` |
| 2 | `Number(price) <= 0` | `Gia phai lon hon 0` |
| 3 | SKU đã tồn tại trong `products` | `SKU da ton tai` |

Thứ tự kiểm tra: **1 → 2 → 3**. Nhiều lỗi cùng lúc → nối bằng `. ` (giống form đăng ký).

### Handler Form Thêm

```js
const addForm = document.querySelector('[data-testid="cm-product-form"]');
const formMsg = document.querySelector("#product-form-msg");

addForm.addEventListener("submit", (e) => {
  e.preventDefault();
  const f = addForm.elements;

  const item = {
    sku: f.sku.value.trim(),
    name: f.name.value.trim(),
    category_id: Number(f.category_id.value),
    price: Number(f.price.value),
    qty: Number(f.qty.value || 0),
  };

  const errors = [];
  if (!item.sku || !item.name || !f.category_id.value)
    errors.push("Thieu thong tin bat buoc");
  if (item.price <= 0) errors.push("Gia phai lon hon 0");
  if (products.some((p) => p.sku === item.sku)) errors.push("SKU da ton tai");

  if (errors.length > 0) {
    formMsg.textContent = errors.join(". ");
    formMsg.className = "cm-error";
    return;
  }

  products.push(item);
  render(products);
  renderStats();   // bọc code stats P06 thành hàm gọi lại được
  addForm.reset();
});
```

### Ca kiểm chuẩn MN-03 (CANONICAL §8.7)

Thêm sản phẩm với dữ liệu exact:

| Field | Value |
|-------|-------|
| sku | `MN-03` |
| name | `AOC 27 inch` |
| category_id | `3` (Man hinh) |
| price | `4500000` |
| qty | `2` |

**Kết quả bắt buộc sau khi submit thành công:**

| Kiểm tra | Giá trị | Cách verify |
|----------|--------:|-------------|
| Card ở filter **Tat ca** | **9** | Đếm `.cm-card` hoặc `Hien thi: 9 san pham` |
| Filter **Man hinh** | **3** card | MN-01, MN-02, MN-03 |
| Stats `Tong gia tri kho` | **50380000** | `#stats` textContent |

**Tính tay:**

```
Tổng CORE_8 =                         41380000
Thêm MN-03: 4500000 × 2 =              +9000000
───────────────────────────────────────────────
TỔNG SAU CREATE =                     50380000
```

**Ca lỗi bổ sung:** nhập lại SKU `KB-01` → `SKU da ton tai`, vẫn 9 card, tổng không đổi.

**F5 sau Create:** về 8 sản phẩm CORE — **chấp nhận** ở mốc JS thuần (dữ liệu sống trong RAM).

### 6.1. `push` (JS thuần) vs `setState` (React) — bảng đối chiếu

Cùng bài toán “thêm sản phẩm”, hai paradigm khác nhau:

| Khía cạnh | JS thuần (P08) | React (P11) |
|-----------|------------------|-------------|
| Cập nhật dữ liệu | `products.push(item)` — **mutate** mảng gốc | `setProducts([...products, item])` — **immutable** |
| Render lại UI | **Tự gọi** `render()` + `renderStats()` | React **tự** re-render khi state đổi |
| Quên gọi render? | UI **cũ** — bug hay gặp SV | Không xảy ra — framework lo |
| Mảng gốc | Bị sửa trực tiếp | Bản sao mới mỗi lần setState |
| Sort/filter sau push | Dùng mảng đã mutate | State mới trigger pipeline |
| Học được gì | Hiểu **tại sao** cần render lại | Hiểu **declarative UI = f(state)** |

```js
// JS thuần — mutate + manual render
products.push(item);
render(products);
renderStats();

// React — immutable + auto render (P11 preview)
setProducts((prev) => [...prev, item]);
// JSX tự cập nhật ProductGrid + Stats
```

> Câu hỏi thuyết trình video P08: *“Vì sao sau `push` phải tự gọi `render()`?”* — Đáp: JS không biết dữ liệu đổi; DOM không tự cập nhật. React trả lời bằng reactive state.

---

### 6.2. Login giả — bonus (điểm cộng)

Form `data-testid="cm-login-form"` — **UI demo**, không phải bảo mật thật.

| Input | Kết quả |
|-------|---------|
| username=`admin`, password=`CampusMart@01` | `localStorage.setItem("cm_auth", "true")` + msg `Dang nhap thanh cong` |
| Sai credential | msg `Sai tai khoan hoac mat khau` |

```js
const loginForm = document.querySelector('[data-testid="cm-login-form"]');
const loginMsg = document.querySelector("#login-msg");

loginForm.addEventListener("submit", (e) => {
  e.preventDefault();
  const user = loginForm.elements.username.value.trim();
  const pass = loginForm.elements.password.value;

  if (user === "admin" && pass === "CampusMart@01") {
    localStorage.setItem("cm_auth", "true");
    loginMsg.textContent = "Dang nhap thanh cong";
    loginMsg.className = "cm-success";
  } else {
    loginMsg.textContent = "Sai tai khoan hoac mat khau";
    loginMsg.className = "cm-error";
  }
});
```

> **Cảnh báo:** mật khẩu nằm plaintext trong JS — ai mở Sources đều thấy. Auth thật phải ở **server** (CSE485 Session/JWT). Làm bonus nếu còn thời gian; ghi vào README repo.

---

## 7. Bài thực hành minh họa trong buổi (không thay phiếu)

Console trên trang Live Server:

```js
localStorage.setItem("demo", JSON.stringify({ a: 1 }));
JSON.parse(localStorage.getItem("demo"));      // {a: 1}
// F5 → chạy lại → vẫn còn!
localStorage.removeItem("demo");
```

Mở **DevTools → Application → Local Storage** — quan sát key xuất hiện/biến mất. Demo filter: chọn Chuot → kiểm `cm_filter` = `"2"` → F5 → select vẫn Chuot.

---

## 8. Hình ảnh nhớ (memory images)

Dán 5 dòng này đầu vở phần Buổi 8:

```
① ES Module = phòng ban trong công ty — mỗi file một nhiệm vụ, cửa export/import nối dây
② localStorage = tủ locker trong trình duyệt — ghi chú nhỏ sống qua F5, không phải kho hàng thật
③ JSON.stringify/parse = đóng gói/bóc hàng — object không đi thẳng vào locker, phải bọc chuỗi
④ preventDefault = giành micro từ trình duyệt — "để tôi xử lý submit, đừng reload"
⑤ Form Thêm + push + render = thêm hàng vào kệ + gọi nhân viên dán lại nhãn — React sau này tự dán
```

---

## 9. Câu hỏi tự kiểm tra

1. Vì sao Buổi 8 bắt buộc Live Server?
2. `localStorage.setItem("x", {a:1})` — có lưu object đúng không?
3. Ba key localStorage của môn là gì?
4. Vì sao `submit` cần `e.preventDefault()` dòng đầu?
5. Form đăng ký: name=`A`, email=`bad` — thông báo exact?
6. Form Thêm submit rỗng / SKU `KB-01` trùng — chuỗi exact?
7. Sau thêm MN-03: bao nhiêu card? Man hinh? Tổng kho?
8. Vì sao sau `products.push` phải gọi `render()` + `renderStats()`?
9. F5 sau Form Thêm — còn 9 card không? Vì sao?
10. Login giả: credential và key lưu gì?

### Gợi ý đáp án

1. ES Module bị chặn trên `file://`.  
2. Không — lưu `"[object Object]"`; dùng `JSON.stringify`.  
3. `cm_filter`, `cm_subscribers`, `cm_auth`.  
4. Chặn reload; giành quyền validate JS.  
5. `Ten toi thieu 2 ky tu. Email khong hop le`.  
6. `Thieu thong tin bat buoc` / `SKU da ton tai`.  
7. **9** / **3** / **50380000**.  
8. JS không reactive — DOM không tự cập nhật.  
9. Không — products chỉ trong RAM.  
10. `admin` / `CampusMart@01` → `cm_auth="true"`.

---

## 10. Lỗi thường gặp (chi tiết)

| # | Triệu chứng | Nguyên nhân | Cách xử lý |
|---|-------------|-------------|------------|
| 1 | `Cannot use import statement outside a module` | Thiếu `type="module"` | Sửa thẻ `<script>` |
| 2 | CORS / Failed to fetch module | Mở `file://` | Bật **Live Server** |
| 3 | `Failed to resolve module specifier "data.js"` | Thiếu `./` hoặc `.js` | `from "./data.js"` |
| 4 | `products is not defined` trong Console | Module scope | `window.debugProducts = products` tạm |
| 5 | `JSON.parse` crash lần đầu | Key chưa tồn tại → `null` | `?? "[]"` trước parse |
| 6 | Form reload cả trang | Quên `preventDefault` | Dòng đầu handler submit |
| 7 | Lỗi validate sai chuỗi | Typo / thêm dấu | Copy exact CANONICAL §8.4 |
| 8 | Thêm SP ok nhưng stats cũ | Quên `renderStats()` | Bọc stats P06 thành hàm |
| 9 | Thêm SP ok nhưng grid cũ | Quên `render(products)` | Gọi sau mỗi push |
| 10 | Filter mất sau F5 | Chưa setItem/getItem | Pattern §4.4 |
| 11 | MN-03 tổng ≠ 50380000 | Quên renderStats / sai số | Đối chiếu §6.5 |
| 12 | Subscriber list rỗng sau F5 | Chưa đọc localStorage lúc load | Gọi render init |

---

## 11. Checklist trước Buổi 9

- [ ] Repo `cse391-cm-08` riêng; copy từ P07
- [ ] `<title>` exact: `CampusMart — Module & Storage (Buoi 8)`
- [ ] 3 file module: `data.js` / `helpers.js` / `app.js` có export/import
- [ ] HTML chỉ 1 script: `<script type="module" src="app.js"></script>`
- [ ] Trang chạy qua Live Server (http://, không file://)
- [ ] Filter persist: chọn Chuot → F5 → vẫn Chuot + 3 card (`cm_filter`)
- [ ] Form đăng ký: 2 lỗi exact + `Dang ky thanh cong` + `cm_subscribers`
- [ ] `#form-msg` + `aria-live="polite"`; class `cm-error` / `cm-success`
- [ ] Form Thêm `data-testid="cm-product-form"`: 5 field + nút `Them san pham`
- [ ] Validate Form Thêm: 3 chuỗi exact §8.4
- [ ] **`renderStats()` gọi lại được** sau Form Thêm
- [ ] **Ca MN-03: 9 card · Man hinh 3 · tổng 50380000**
- [ ] Giữ chức năng P07: render 8 card, filter 8/3/3/2, sort MS-03 đầu
- [ ] Comment `CM_EXPECT` theo Phiếu 08
- [ ] Xong [Phiếu 08](./worksheets/phieu-08-module-localstorage.md)

```html
<!-- CM_EXPECT product_count=8 create_sku=MN-03 count_after=9 inventory_after=50380000 modules=data.js,helpers.js,app.js -->
```

---

## 12. Bài tập trên lớp & về nhà (chuỗi CampusMart UI)

| Phần | Làm gì | Chi tiết |
|------|--------|----------|
| **Trên lớp (~20')** | Tách module + nhớ filter qua F5 | [Phiếu 08 — mục 2](./worksheets/phieu-08-module-localstorage.md) |
| **Về nhà (~25–40')** | Validate form đăng ký + **Form Thêm sản phẩm** | [Phiếu 08 — mục 3](./worksheets/phieu-08-module-localstorage.md) |

Repo nộp: **`cse391-cm-08`** · video OBS kịch bản:

```
1. Chọn Chuot → F5 → vẫn Chuot (Application tab mở kèm)
2. Form đăng ký: 2 lỗi exact → đăng ký hợp lệ
3. Form Thêm: submit rỗng / giá 0 / KB-01 trùng → 3 lỗi exact
4. Thêm MN-03 → đọc to 3 số: 9 / 3 / 50380000
5. Thuyết trình 30–60s: vì sao sau push phải gọi render()?
```

---

## 13. Cầu nối sang Buổi 9

```
Buổi 6:  TÍNH từ mảng + GHI stats vào DOM
Buổi 7:  TỰ SINH card + FILTER + EVENT
Buổi 8:  MODULE + localStorage + VALIDATE + FORM THÊM ★ mốc JS thuần ★

  ┌─────────────────────────────────────────────────────────────┐
  │  Dashboard JS thuần ĐÃ CÓ:                                  │
  │    Read   — render(list)                                    │
  │    Filter — + persist cm_filter                             │
  │    Create — Form Thêm + push + render + renderStats         │
  │                                                             │
  │  Dashboard JS thuần CHƯA CÓ:                                │
  │    Update / Delete                                          │
  │    Render tự động (phải gọi tay render)                     │
  │    Persist sản phẩm qua F5                                  │
  └─────────────────────────────────────────────────────────────┘

Buổi 9: REACT — Vite, JSX, component, props
  UI = f(props/state) — không còn gọi render() tay
  P09–P12: hoàn chỉnh ứng dụng CRUD (C+R+U+D, 2 thực thể)

  Cùng ca MN-03 ở P11 React — so sánh push vs setState để thấy mình được gì
```

**Mốc JS thuần đã đạt.** Buổi 9 bước vào React — nơi “render lại” trở thành tự động, và dashboard này nâng thành **ứng dụng CRUD hoàn chỉnh**.

→ [Buổi 9](./09-buoi-09-react-khoi-dau.md) · [Phiếu 09](./worksheets/phieu-09-react-props.md)

---

*CSE391 – Ebook Frontend CampusMart UI | Buổi 8/12*
