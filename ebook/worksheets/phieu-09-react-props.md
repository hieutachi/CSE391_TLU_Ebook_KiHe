# Phiếu 09 — React khởi đầu: CampusMart bằng Component + Props

| | |
|---|---|
| **Buổi** | 9 |
| **Thời lượng** | ≥ 45 phút (trên lớp ~20' + về nhà ~25–40') |
| **Repo** | `cse391-cm-09` (project Vite) |
| **Nhận từ Phiếu 08** | Dashboard JS thuần có Form Thêm (mốc 1); CSS tái dùng từ P05 |
| **Mang sang Phiếu 10** | Cây component CampusMart tĩnh |
| **Dataset** | [`CANONICAL-DATA.md`](./CANONICAL-DATA.md) — §5 (bản có `export`), §8.2 title |

### Chuẩn đầu ra (CLO)

1. Scaffold Vite React chạy được `npm run dev`; sửa `<title>` đúng canonical.  
2. Tách tối thiểu 5 component: `Header`, `CategoryList`, `ProductGrid`, `ProductCard`, `Stats`.  
3. `ProductCard` nhận props `product`, hiển thị tên/DM chữ/giá/qty + `data-sku`.  
4. Render 8 card bằng `map` với `key={p.sku}` — không warning key.  
5. Stats `So san pham = 8`, `Tong gia tri kho = 41380000` tính từ mảng.

---

## 1. Tóm tắt lý thuyết (đọc lại trước khi code)

- **`UI = f(data)`** — bạn khai báo UI theo data; React vá DOM (so P08 tự gọi `render`).
- `npm create vite@latest ... --template react`; làm việc trong `src/`; title sửa ở `index.html`.
- JSX: `className`, `{}` nhúng JS, thẻ tự đóng, 1 phần tử gốc / Fragment.
- Component PascalCase; props read-only, chảy xuống.
- List: `arr.map(p => <Card key={p.sku} ... />)` — key = SKU, không dùng index.

Chi tiết: [Buổi 9](../09-buoi-09-react-khoi-dau.md).

---

## 2. Bài trên lớp (~20 phút) — Scaffold + card đầu tiên

### Yêu cầu

1. Scaffold:

```bash
npm create vite@latest cse391-cm-09 -- --template react
cd cse391-cm-09 && npm install && npm run dev
```

2. `index.html`: đổi `<title>` exact: **`CampusMart — React (Buoi 9)`**.
3. Tạo `src/data.js` = [CANONICAL-DATA §5](./CANONICAL-DATA.md) **nguyên văn** (giữ `export`).
4. Xóa nội dung mẫu của `App.jsx`; viết:
   - `Header`: `h1` text `CampusMart` + `data-testid="cm-brand"`, nav 3 link (`Trang chu`, `San pham`, `Gioi thieu`).
   - `ProductCard({ product })`: `article.cm-card` + `data-sku`, hiện `name`, `price`, `So luong: {qty}`.
   - `App`: render `<Header />` + 1 thẻ `<ProductCard product={products[0]} />`.

### Cách thử trên lớp (OBS — làm ngay sau khi gõ)

| # | Thử | Dự đoán | Cách quan sát |
|---|-----|---------|---------------|
| T1 | `npm run dev` | Mở được localhost:5173 | Terminal + trình duyệt |
| T2 | Tab title | Exact `CampusMart — React (Buoi 9)` | Title bar |
| T3 | Inspect `h1` | `data-testid="cm-brand"` text `CampusMart` | Elements |
| T4 | Card hiện | Keychron K2 + giá 1890000 | UI |
| T5 | Đổi `products[0]` → `products[5]` → Save | Card thành Xiaomi Silent **không F5** | HMR |
| T6 | Sửa tạm `class=` thay `className` | Style lệch / warning | Console |

**Nếu T5 fail (phải F5):** kiểm tra đã Save file chưa; HMR Vite phải đổi không reload full.

### Output kỳ vọng (trên lớp)

Trang hiện header CampusMart + đúng 1 card Keychron K2. Đổi index props → card đổi không F5.

**Checkpoint giảng viên:** dev server chạy; card nhận props (đổi index là đổi card).

---

## 3. Bài về nhà (~25–40 phút) — Đủ cây component + stats

Tiếp tục **cùng project**.

### Nhiệm vụ A — CategoryList

Component `CategoryList` render `ul` với `data-testid="cm-category-list"`, `map` 3 categories (key = `c.id`), mỗi `li` hiện `c.name`.

#### Cách thử Nhiệm vụ A

| # | Thử | Kỳ vọng |
|---|-----|---------|
| A1 | Inspect `cm-category-list` | Đúng **3** `li` |
| A2 | Đọc text | `Ban phim`, `Chuot`, `Man hinh` |
| A3 | Console | Không warning key |

### Nhiệm vụ B — ProductGrid 8 card

1. `ProductGrid`: `div.cm-grid` + `data-testid="cm-product-table"`, `map` toàn bộ `products` → `ProductCard`, `key={p.sku}`.
2. `ProductCard` bổ sung dòng danh mục **chữ** — helper:

```js
export function categoryName(id) {
  return categories.find((c) => c.id === id)?.name ?? "?";
}
```

3. Console trình duyệt **không** có warning key.

#### Cách thử Nhiệm vụ B

| # | Thử | Kỳ vọng |
|---|-----|---------|
| B1 | Đếm card / `data-sku` | **8** SKU CORE |
| B2 | Card KB-01 | DM chữ `Ban phim` (không hiện `1`) |
| B3 | Quên `key` thử 1 lần | Warning → sửa lại `key={p.sku}` |
| B4 | View Source / bundle | Không hard-code 8 article tay |

### Nhiệm vụ C — Stats component

`Stats`: `section` + `data-testid="cm-inventory-total"`, hai dòng `p`:

```
So san pham = {products.length}
Tong gia tri kho = {tổng reduce}
```

Số phải **tính**, không hard-code. Export `inventoryValue` từ `data.js` (kiến thức P06).

#### Cách thử Nhiệm vụ C

| # | Thử | Kỳ vọng |
|---|-----|---------|
| C1 | Đọc Stats | `So san pham = 8` |
| C2 | Đọc tổng | `Tong gia tri kho = 41380000` |
| C3 | Đổi tạm `products` bỏ 1 SP trong data.js | Stats thành 7 + tổng giảm — chứng minh không hard-code |

### Nhiệm vụ D — CSS + marker

1. Copy `styles.css` của P05 vào `src/index.css` (hoặc import) — layout card/grid sống lại.
2. Trong `App.jsx`, comment JSX đầu `main`:

```jsx
{/* CM_EXPECT product_count=8 inventory_value=41380000 components=Header,CategoryList,ProductGrid,ProductCard,Stats */}
```

#### Cách thử Nhiệm vụ D

| # | Thử | Kỳ vọng |
|---|-----|---------|
| D1 | Search source `CM_EXPECT` | Đủ key như trên |
| D2 | Repo `git status` | **Không** track `node_modules` |

---

## 4. OUTPUT / EXPECT

| Key | Value bắt buộc |
|-----|----------------|
| `page_title` | `CampusMart — React (Buoi 9)` |
| `components` | ≥ 5: Header, CategoryList, ProductGrid, ProductCard, Stats |
| `testid.cm-brand` | text `CampusMart` |
| `testid.cm-category-list` | 3 li |
| `testid.cm-product-table` | 8 card con |
| `card.data-sku` | đủ 8 SKU CORE |
| `card.category_text` | chữ, không số |
| `key` | `p.sku` — console không warning |
| `stats` | `So san pham = 8`, `Tong gia tri kho = 41380000` |
| repo | không commit `node_modules` |

### Checklist tự chấm trước nộp

```
[ ] title exact P09
[ ] 5 component + cây App
[ ] 8 card key=sku, DM chữ
[ ] Stats tính = 8 / 41380000
[ ] HMR demo được trên video
[ ] CM_EXPECT comment
[ ] không node_modules
```

---

## 5. Box nộp bài

```
╔══════════════════════════════════════════════════════════════╗
║  NỘP PHIẾU 09                                                ║
╠══════════════════════════════════════════════════════════════╣
║  1. GitHub repo RIÊNG: cse391-cm-09 (KHÔNG node_modules)     ║
║  2. Video OBS + camera mặt — kịch bản:                       ║
║     - npm run dev từ đầu → trang lên                         ║
║     - Sửa 1 giá trong data.js → HMR đổi ngay không F5        ║
║     - Console sạch warning key                               ║
║     - Chỉ cây component trong file App.jsx (hoặc tương đương)║
║  3. Thuyết trình 30–60s: props là gì, vì sao read-only?      ║
║     (nhắc UI = f(data) nếu được)                             ║
╚══════════════════════════════════════════════════════════════╝
```

---

## 6. Lỗi thường gặp khi nộp (tránh)

| Lỗi | Hậu quả | Cách tránh |
|-----|---------|------------|
| Title vẫn Vite mặc định | `page_title` fail | Sửa `index.html` §8.2 |
| `key={index}` | Trừ điểm + đau P11 | `key={p.sku}` |
| Stats hard-code | P11 Create lộ | `reduce` từ mảng |
| Commit `node_modules` | Repo nặng / trừ `R-REPO` | `.gitignore` |
| DM hiện `category_id` số | EXPECT category_text | `categoryName(id)` |

---

*CSE391 – Phiếu 09 | CampusMart UI — bản viết lại CSE485*
