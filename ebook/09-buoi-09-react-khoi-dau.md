# BUỔI 9: React khởi đầu — Vite, JSX, Component, Props

> **Thời lượng gợi ý:** 3–4 giờ tự học (lý thuyết + thử máy + Phiếu 09)  
> **Tiên quyết:** Buổi 6–8 (JS: render từ mảng, module, event). Node.js LTS đã cài  
> **Kết quả đầu ra:** Scaffold Vite + React; viết JSX; tách UI thành component; truyền props; `map` + `key` render 8 card CampusMart  
> **Phiếu học tập:** [Phiếu 09](./worksheets/phieu-09-react-props.md) — chuỗi **CampusMart UI**  
> **Dataset:** [CANONICAL-DATA §5, §8.2](./worksheets/CANONICAL-DATA.md) — title P09, CORE_8, `export` data.js

---

## 0. Gợi ý lịch trình giảng (cho GV)

| Phút | Nội dung | Gợi ý thao tác |
|------|----------|----------------|
| 0–10 | Ôn P08 + hỏi “Sau mỗi Create, bạn phải nhớ gọi gì?” | Chiếu `render()` + `renderStats()` P08 |
| 10–25 | **Hình ảnh nhớ** `UI = f(data)` + so sánh P08 vs React | Vẽ 2 cột trên bảng |
| 25–40 | Scaffold Vite live; sửa title; HMR | SV cùng gõ `npm create vite` |
| 40–55 | JSX bảng so HTML; demo `className` / `{}` | Phá thẻ → đọc lỗi Vite |
| 55–70 | Function component + **component tree ASCII** | Vẽ App → Header → … trên bảng |
| 70–85 | Props + ProductCard 1 SP | Đổi `products[0]` → `[5]` live |
| 85–100 | `map` + `key={sku}`; **Thử ngay** HMR | Console warning khi quên key |
| 100–120 | **Thực hành Phiếu 09 mục 2** | Checkpoint: 1 card nhận props |

> Trên lớp **ưu tiên**: Vite chạy + Header + 1 ProductCard props + hiểu `UI = f(data)`. Đủ 8 card + Stats có thể hoàn thiện về nhà.

---

## 1. Mục tiêu buổi học

Sau Buổi 9, bạn có thể:

1. Tạo và chạy project React bằng Vite (`npm create vite` + `npm run dev`).
2. Đọc/viết JSX; nêu ≥ 3 khác biệt JSX vs HTML (`className`, `{}`, đóng thẻ, `onClick`).
3. Viết function component PascalCase; ghép cây component CampusMart.
4. Truyền props từ cha xuống con; destructuring; hiểu props **read-only**.
5. Render danh sách bằng `map` + `key`; giải thích vì sao `key` = SKU, không dùng index.
6. Dựng lại CampusMart tĩnh (8 card + 3 DM + stats) bằng component — nền cho state Buổi 10.

---

## 2. Nhắc lại điểm đứng trước Buổi 9

Ở Buổi 8 (JS thuần), CampusMart đã có:

```
Dashboard P08 (mốc 1 — JS thuần)
  ├── Module: data.js + helpers.js + app.js
  ├── Render list + filter (P07 mang sang)
  ├── Form Thêm sản phẩm (Create) + validate chuỗi exact
  └── localStorage (filter / subscribers)
```

**Vấn đề sư phạm (tự hỏi):**

- Sau mỗi Create, bạn **phải nhớ** gọi `render()` rồi `renderStats()` — quên một cái → UI lệch data.
- Thêm filter + search + sort + form = thêm mớ “đồng bộ hóa DOM” dễ sai.
- `innerHTML = ""` rồi vẽ lại → mất focus input, mất vị trí cuộn.

```
Buổi 6–8 (JS thuần)              Buổi 9–12 (React)
─────────────────────────         ───────────────────────────────
Bạn tự vá DOM sau mỗi đổi    →   Bạn mô tả UI = f(data)
render() / renderStats()     →   setState → React vá DOM
Mốc 1: Dashboard + Create    →   Mốc 2 (P12): CRUD 2 thực thể
```

**Buổi 9 = bước vào React: scaffold + JSX + props — danh sách vẫn tĩnh (chưa state).**

---

## 3. Hình ảnh nhớ — UI = f(data)

### 3.1. Dán đầu vở (bắt buộc)

```
┌─────────────────────────────────────────────────────────────┐
│                                                             │
│   UI  =  f ( data )                                         │
│                                                             │
│   “Giao diện là hàm của dữ liệu.”                           │
│                                                             │
│   data đổi  ──►  f chạy lại  ──►  UI khớp data               │
│                                                             │
│   Bạn KHÔNG tự querySelector + textContent từng chỗ.       │
│   Bạn CHỈ khai báo: với data này thì UI trông thế nào.     │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### 3.2. So sánh máy móc: P08 JS thuần vs React

```
   JS thuần (P07–P08):
   ┌──────────┐     bạn tự gọi      ┌──────────┐
   │  data[]  │ ─────────────────►  │   DOM    │
   └──────────┘   render()/stats()  └──────────┘
        ▲  quên 1 hàm = UI lệch data

   React (P09+):
   ┌──────────┐                     ┌──────────┐
   │  data    │ ──► UI = f(data) ─► │   DOM    │
   └──────────┘     React vá tối thiểu
        ▲  data đổi (P10+ state) → f chạy → DOM khớp
```

> Công thức đáng nhớ nhất của cả chuỗi React: **`UI = f(data)`**. Buổi 9 bạn học viết `f` (component + props). Buổi 10+ bạn học đổi `data` (state).

### 3.3. Hình ảnh “kệ hàng CampusMart”

```
CampusMart React Buổi 9 giống cửa hàng campus:

  data.js        = sổ kho (8 SP + 3 DM) — CHƯA sửa lúc chạy
  Component      = kệ / biển hiệu / card từng món
  props          = phiếu gắn trên card: “đây là Keychron K2, giá …”
  map + key      = xếp 8 card theo SKU — React biết card nào là card nào

  Buổi 10        = khách đổi bộ lọc → kệ tự sắp lại (state)
  Buổi 11–12     = nhập / sửa / xóa hàng (CRUD)
```

---

## 4. Vì sao React — sau khi đã làm được bằng JS thuần?

Nhìn lại Dashboard Phiếu 08: sau mỗi lần thêm sản phẩm, bạn phải **tự nhớ** gọi `render()` rồi `renderStats()`. Trang nhỏ thì ổn; cứ thêm trạng thái (filter + search + sort + form) là thêm code đồng bộ DOM dễ sai.

React đề nghị một thỏa thuận:

> **Bạn mô tả UI trông thế nào theo dữ liệu. Việc cập nhật DOM tối thiểu, để React lo.**

| Tiêu chí | P08 JS thuần | P09 React |
|----------|--------------|-----------|
| Sau Create | Tự gọi `render` + `renderStats` | (P11) `setProducts` → UI tự khớp |
| Filter | `innerHTML` xóa grid, vẽ lại | (P10) derived `visible` + map |
| Focus input khi re-render | Dễ mất | React giữ khi `key` đúng |
| Tách UI | Hàm `renderCard()` | Function component + props |
| Học phí | Hiểu DOM thật | Hiểu mô hình khai báo |

> Không “bỏ JS thuần”. P06–P08 dạy bạn **data + DOM thật**. React dạy bạn **không tự vá DOM bằng tay** nữa.

---

## 5. Scaffold bằng Vite

### 5.1. Lệnh chuẩn (exact cho Phiếu 09)

```bash
npm create vite@latest cse391-cm-09 -- --template react
cd cse391-cm-09
npm install
npm run dev        # → http://localhost:5173
```

Yêu cầu máy: **Node.js LTS** (`node -v` ≥ 18 khuyến nghị). Thiếu Node → cài từ nodejs.org trước khi làm phiếu.

### 5.2. Cấu trúc cần quan tâm

```
cse391-cm-09/
├── index.html          ← <div id="root"> + <title> (sửa title ở ĐÂY)
├── src/
│   ├── main.jsx        ← mount <App /> vào #root
│   ├── App.jsx         ← component gốc — bạn làm việc chủ yếu ở đây
│   ├── App.css / index.css
│   └── data.js         ← BẠN tạo: CANONICAL §5 (export)
├── package.json
└── .gitignore          ← đã có node_modules — KHÔNG commit node_modules
```

```
  index.html
      │  <div id="root"></div>
      ▼
  main.jsx  ── createRoot(root).render(<App />)
      │
      ▼
  App.jsx   ── cây component CampusMart
      │
      └── data.js  (products, categories)
```

### 5.3. Sửa title ngay (CANONICAL §8.2)

Trong `index.html`:

```html
<title>CampusMart — React (Buoi 9)</title>
```

Exact — không thêm chữ khác. Autograder / GV đối chiếu chuỗi này.

---

## 6. JSX — HTML “sống” trong JS

### 6.1. Ví dụ đầu tiên

```jsx
const name = "Keychron K2";
const price = 1890000;

const card = (
  <article className="cm-card">
    <h3>{name}</h3>
    <p>{price * 2}</p>      {/* trong {} là JS thật */}
    <img src="/kb01.jpg" alt={name} />
  </article>
);
```

### 6.2. Bảng JSX vs HTML (học thuộc)

| HTML | JSX | Lý do |
|------|-----|-------|
| `class="cm-card"` | `className="cm-card"` | `class` là từ khóa JS |
| `<img ...>` | `<img ... />` | mọi thẻ phải đóng |
| `onclick="..."` | `onClick={fn}` | camelCase + truyền hàm, không chuỗi |
| `for="..."` | `htmlFor="..."` | `for` là từ khóa vòng lặp |
| — | `{bieuThuc}` | nhúng biểu thức JS vào markup |
| nhiều gốc | một gốc hoặc `<>...</>` | Fragment |

Một component phải trả về **một** phần tử gốc (bọc `<div>` hoặc fragment `<>...</>`).

### 6.3. Quy tắc `{}`

```
  JSX bên ngoài {}  = markup giống HTML
  Bên trong {}      = biểu thức JS: biến, toán tử, gọi hàm, &&, ? :

  ĐÚNG:  {product.price}
  ĐÚNG:  {products.length}
  SAI:   {if (x) ...}     ← if là câu lệnh, không phải biểu thức
  ĐÚNG:  {x ? <A/> : <B/>}
```

---

## 7. Component & cây component CampusMart

### 7.1. Function component

```jsx
// Tên PascalCase — bắt buộc, để React phân biệt với thẻ HTML
function Header() {
  return (
    <header className="cm-header">
      <h1 data-testid="cm-brand">CampusMart</h1>
      <nav data-testid="cm-nav">
        <a href="#home">Trang chu</a>
        <a href="#products">San pham</a>
        <a href="#about">Gioi thieu</a>
      </nav>
    </header>
  );
}

// Dùng như thẻ:  <Header />
```

### 7.2. Component tree (ASCII — bắt buộc nhớ)

```
                         App
                          │
      ┌───────────────────┼────────────────────┬────────────┐
      ▼                   ▼                    ▼            ▼
   Header           CategoryList          ProductGrid     Stats
      │                   │                    │            │
      ▼                   ▼            ┌───────┼───────┐    ▼
    (nav)            (3 × li)          ▼       ▼       ▼  So san pham
                                   ProductCard … ×8   Tong gia tri
                                        │
                                  props: product
                                  key: product.sku
```

Mỗi khối bạn từng viết trong HTML giờ là một **hàm** — gọi được, tái sử dụng được, truyền tham số được.

### 7.3. Tách file hay để trong App.jsx?

P09 chấp nhận **tất cả component trong `App.jsx`** (dễ quay video “chỉ cây component”). P10+ có thể tách `components/ProductCard.jsx` nếu muốn — không bắt buộc.

---

## 8. Props — dữ liệu chảy từ cha xuống con

### 8.1. Hình ảnh nhớ props

```
   App (có mảng products)
        │
        │  props (một chiều — chỉ chảy XUỐNG)
        ▼
   ProductCard (chỉ ĐỌC props — không bao giờ sửa)

   Giống phiếu gắn trên kệ: card đọc phiếu, không viết ngược sổ kho.
```

### 8.2. Code mẫu ProductCard

```jsx
function ProductCard({ product }) {
  return (
    <article
      className="cm-card"
      data-testid="cm-product-row"
      data-sku={product.sku}
    >
      <h3>{product.name}</h3>
      <p className="cm-card-price">{product.price}</p>
      <p>So luong: {product.qty}</p>
    </article>
  );
}

// Cha truyền xuống:
<ProductCard product={products[0]} />
```

### 8.3. Quy tắc sắt

| Quy tắc | Chi tiết |
|---------|----------|
| Props read-only | Con không gán `product.price = 0` |
| Muốn đổi data | Đợi state (Buổi 10) — cha đổi rồi truyền props mới |
| Destructuring | `function Card({ product })` gọn hơn `props.product` |
| Đặt tên rõ | `product={p}` — không truyền 10 props rời nếu đã có object |

---

## 9. Render list — `map` + `key`

### 9.1. Từ vòng for P07 sang biểu thức JSX

```jsx
import { products } from "./data.js";

function ProductGrid() {
  return (
    <div className="cm-grid" data-testid="cm-product-table">
      {products.map((p) => (
        <ProductCard key={p.sku} product={p} />
      ))}
    </div>
  );
}
```

### 9.2. `key` là gì — hình ảnh nhớ

```
  Lần render 1:  [KB-01] [KB-02] [KB-03] ...
  Lần render 2:  (sau này P11 thêm MN-03 / P12 xóa MN-02)

  key = SKU ổn định → React biết:
    “KB-01 vẫn là KB-01” → giữ DOM card đó, chỉ vá chỗ đổi
    “MN-03 mới” → tạo card mới
    “MN-02 biến mất” → gỡ card đó

  key = index (0,1,2…) khi thêm/xóa/sort → React vá NHẦM card
  → P11–P12 sẽ đau nếu dùng index hôm nay
```

**Dùng `key={p.sku}`.** Cấm index khi danh sách sẽ CRUD.

### 9.3. CategoryList + helper tên DM

```jsx
import { categories } from "./data.js";

function CategoryList() {
  return (
    <ul data-testid="cm-category-list">
      {categories.map((c) => (
        <li key={c.id}>{c.name}</li>
      ))}
    </ul>
  );
}

// Trong data.js:
export function categoryName(id) {
  return categories.find((c) => c.id === id)?.name ?? "?";
}
```

Trên card: hiện **chữ** danh mục (`Ban phim`), không hiện số `1`.

---

## 10. Stats — tính từ mảng, không hard-code

```jsx
function inventoryValue(list) {
  return list.reduce((sum, p) => sum + p.price * p.qty, 0);
}

function Stats({ products }) {
  return (
    <section data-testid="cm-inventory-total">
      <p>So san pham = {products.length}</p>
      <p>Tong gia tri kho = {inventoryValue(products)}</p>
    </section>
  );
}
```

CORE_8 → `8` và `41380000` (CANONICAL §3). Nếu hard-code `"8"` → P11 Create sẽ lộ ngay (stats không nhảy).

---

## 11. `data.js` — copy CANONICAL §5

Tạo `src/data.js` **nguyên văn** từ [CANONICAL-DATA §5](./worksheets/CANONICAL-DATA.md) (giữ `export`). Có thể thêm:

```js
export function inventoryValue(list) {
  return list.reduce((s, p) => s + p.price * p.qty, 0);
}

export function categoryName(id) {
  return categories.find((c) => c.id === id)?.name ?? "?";
}
```

---

## 12. Ghép App.jsx hoàn chỉnh (khung)

```jsx
import { products, categories, inventoryValue, categoryName } from "./data.js";
import "./index.css";

function Header() { /* ... */ }
function CategoryList() { /* ... */ }
function ProductCard({ product }) { /* ... dùng categoryName */ }
function ProductGrid({ products }) {
  return (
    <div className="cm-grid" data-testid="cm-product-table">
      {products.map((p) => (
        <ProductCard key={p.sku} product={p} />
      ))}
    </div>
  );
}
function Stats({ products }) { /* ... */ }

export default function App() {
  return (
    <main>
      {/* CM_EXPECT product_count=8 inventory_value=41380000 components=Header,CategoryList,ProductGrid,ProductCard,Stats */}
      <Header />
      <CategoryList />
      <Stats products={products} />
      <ProductGrid products={products} />
    </main>
  );
}
```

Copy CSS P05 vào `src/index.css` để card/grid sống lại (không bắt buộc đẹp 100% — bắt buộc testid + số liệu đúng).

---

## 13. Thử ngay trên máy (bắt buộc — dự đoán → chạy → quan sát)

### Chuẩn bị (5 phút)

```bash
npm create vite@latest cse391-cm-09 -- --template react
cd cse391-cm-09 && npm install && npm run dev
```

Mở http://localhost:5173 — thấy trang mẫu Vite.

### Thử 1 — HMR (Hot Module Replacement)

| Bước | Dự đoán | Quan sát |
|------|---------|----------|
| Sửa chữ trong `App.jsx` → Save | Trang đổi **không F5** | Network không reload full |
| Đổi `h1` thành `CampusMart` | Hiện ngay | HMR |

### Thử 2 — Props

| Bước | Dự đoán | Quan sát |
|------|---------|----------|
| Render `<ProductCard product={products[0]} />` | Keychron K2 | Card 1 |
| Đổi thành `products[5]` | Xiaomi Silent | Card đổi, không F5 |

### Thử 3 — JSX sai cố ý

| Bước | Dự đoán | Quan sát |
|------|---------|----------|
| Dùng `class=` thay `className` | Warning / style lệch | Console |
| Hai thẻ gốc không Fragment | Lỗi compile | Overlay Vite đỏ |
| Quên `key` trong map | Warning unique key | Console vàng |

### Thử 4 — Title + testid

| Bước | Dự đoán | Quan sát |
|------|---------|----------|
| Tab trình duyệt | `CampusMart — React (Buoi 9)` | Title bar |
| Inspect `h1` | `data-testid="cm-brand"` | Elements |

**Ghi dự đoán vào vở trước mỗi thử** — đúng phong cách CSE485: dự đoán → chạy → đối chiếu.

---

## 14. So sánh sâu — cùng bài “8 card” P07 vs P09

| Việc | P07 JS thuần | P09 React |
|------|--------------|-----------|
| Nguồn data | `products` trong `data.js` | Giống — cùng CANONICAL |
| Vẽ card | `createElement` / template + `innerHTML` | `<ProductCard />` trong `map` |
| Sau khi đổi data | Gọi lại `render(list)` | (P10+) `setState` — Buổi 9 data còn const |
| Định danh phần tử | Bạn tự gắn `data-sku` | `key={sku}` + `data-sku` |
| Tái sử dụng UI | Copy hàm / copy HTML | Component + props |
| Stats | Hàm `renderStats()` riêng | `<Stats products={...} />` |

```
  Cùng một dataset CORE_8 (41380000)
       │
       ├─ P07/P08: bạn là “thợ vá DOM”
       └─ P09:     bạn là “người khai báo UI”
```

---

## 15. Câu hỏi tự kiểm tra

1. `UI = f(data)` nghĩa là gì bằng một câu?
2. Vì sao sau Create ở P08 dễ quên `renderStats`?
3. Ba khác biệt JSX vs HTML?
4. Vì sao component phải PascalCase?
5. Props chảy hướng nào? Con có được sửa props không?
6. `key` dùng SKU chứ không dùng index — vì sao (nhìn trước P11–P12)?
7. Title P09 exact là gì?
8. Stats hard-code `"8"` sẽ hỏng ở buổi nào?

*(Tự trả lời trước khi xem gợi ý mục 16.)*

---

## 16. Gợi ý đáp án câu tự kiểm tra

1. UI là kết quả tính từ dữ liệu; data đổi thì UI khớp theo.  
2. Phải tự gọi nhiều hàm đồng bộ DOM — dễ sót.  
3. `className`, `{}`, thẻ tự đóng / `onClick` / một gốc.  
4. Phân biệt với thẻ HTML (`<header>` vs `<Header />`).  
5. Cha → con; không — props read-only.  
6. CRUD làm index đổi chỗ → React vá nhầm.  
7. `CampusMart — React (Buoi 9)`.  
8. Buổi 11 Create (stats phải tự thành 9 / 50380000).

---

## 17. Lỗi thường gặp (chi tiết)

| # | Triệu chứng | Nguyên nhân | Cách xử lý |
|---|-------------|-------------|------------|
| 1 | Trang trắng + overlay đỏ | JSX sai cú pháp | Đọc dòng Vite chỉ |
| 2 | Warning `unique "key"` | Quên key trong map | `key={p.sku}` |
| 3 | Component không hiện | Tên viết thường | PascalCase |
| 4 | Style không ăn | Dùng `class` | `className` |
| 5 | `npm run dev` lỗi | Thiếu install / Node cũ | LTS + `npm install` |
| 6 | Title sai | Quên sửa `index.html` | CANONICAL §8.2 |
| 7 | Commit nặng | Commit `node_modules` | Giữ `.gitignore` |
| 8 | DM hiện số `1` | Không map `categoryName` | Helper find theo id |
| 9 | Stats = 0 | Quên truyền props / sai reduce | Kiểm `price * qty` |
| 10 | Import data fail | Thiếu `export` / sai path | `./data.js` có đuôi |

---

## 18. Checklist trước khi sang Buổi 10

- [ ] Tạo + chạy được Vite React (`npm run dev`)
- [ ] Title exact P09
- [ ] Nêu được `UI = f(data)` + 3 khác biệt JSX
- [ ] ≥ 5 component: Header, CategoryList, ProductGrid, ProductCard, Stats
- [ ] Props + destructuring; `map` + `key={sku}`
- [ ] Stats tính: 8 và 41380000
- [ ] Xong [Phiếu 09](./worksheets/phieu-09-react-props.md)

---

## 19. Bài tập trên lớp & về nhà (chuỗi CampusMart UI)

| Phần | Làm gì | Chi tiết |
|------|--------|----------|
| **Trên lớp (~20')** | Scaffold + Header + 1 ProductCard props | [Phiếu 09 — mục 2](./worksheets/phieu-09-react-props.md) |
| **Về nhà (~25–40')** | Đủ cây + 8 card + stats | [Phiếu 09 — mục 3](./worksheets/phieu-09-react-props.md) |

Nộp repo riêng `cse391-cm-09` + video OBS theo box cuối phiếu.

---

## 20. Cầu nối sang Buổi 10

```
Buổi 9: CampusMart React TĨNH — đẹp nhưng chưa bấm lọc được
Buổi 10: useState + controlled + derived — filter/search tự cập nhật DOM
```

→ Tiếp: [Buổi 10](./10-buoi-10-react-state-filter.md) · [Phiếu 10](./worksheets/phieu-10-react-state-filter.md)

---

*CSE391 – Ebook Frontend CampusMart UI | Buổi 9/12 — bản viết lại CSE485*
