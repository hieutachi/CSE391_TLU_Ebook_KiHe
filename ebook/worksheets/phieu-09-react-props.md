# Phiếu 09 — React khởi đầu: CampusMart bằng Component + Props

| | |
|---|---|
| **Buổi** | 9 |
| **Thời lượng** | ≥ 45 phút (trên lớp ~20' + về nhà ~25–40') |
| **Repo** | `cse391-cm-09` (project Vite) |
| **Nhận từ Phiếu 08** | Dashboard JS thuần có Form Thêm (mốc 1); CSS tái dùng từ P05 |
| **Mang sang Phiếu 10** | Cây component CampusMart tĩnh |
| **Dataset** | [`CANONICAL-DATA.md`](./CANONICAL-DATA.md) — §5 (bản có `export`) |

### Chuẩn đầu ra (CLO)

1. Scaffold Vite React chạy được `npm run dev`; sửa `<title>` đúng canonical.  
2. Tách tối thiểu 4 component: `Header`, `CategoryList`, `ProductGrid`, `ProductCard`.  
3. `ProductCard` nhận props `product`, hiển thị tên/DM chữ/giá/qty + `data-sku`.  
4. Render 8 card bằng `map` với `key={p.sku}` — không warning key.  
5. Stats `So san pham = 8`, `Tong gia tri kho = 41380000` tính từ mảng.

---

## 1. Tóm tắt lý thuyết (đọc lại trước khi code)

- `npm create vite@latest ... --template react`; làm việc trong `src/`.
- JSX: `className`, `{}` nhúng JS, thẻ tự đóng, 1 phần tử gốc.
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

### Output kỳ vọng (trên lớp)

Trang hiện header CampusMart + đúng 1 card Keychron K2. Sửa `products[0]` → `products[5]` → card đổi thành Xiaomi Silent không cần F5.

**Checkpoint giảng viên:** dev server chạy; card nhận props (đổi index là đổi card).

---

## 3. Bài về nhà (~25–40 phút) — Đủ cây component + stats

Tiếp tục **cùng project**.

### Nhiệm vụ A — CategoryList

Component `CategoryList` render `ul` với `data-testid="cm-category-list"`, `map` 3 categories (key = `c.id`), mỗi `li` hiện `c.name`.

### Nhiệm vụ B — ProductGrid 8 card

1. `ProductGrid`: `div.cm-grid` + `data-testid="cm-product-table"`, `map` toàn bộ `products` → `ProductCard`, `key={p.sku}`.
2. `ProductCard` bổ sung dòng danh mục **chữ**: viết helper trong `data.js` hoặc component:

```js
export function categoryName(id) {
  return categories.find((c) => c.id === id)?.name ?? "?";
}
```

3. Console trình duyệt **không** có warning key.

### Nhiệm vụ C — Stats component

`Stats`: `section` + `data-testid="cm-inventory-total"`, hai dòng `p`:

```
So san pham = {products.length}
Tong gia tri kho = {tổng reduce}
```

Số phải **tính**, không hard-code. Có thể copy `inventoryValue` từ P06 vào `data.js` (export).

### Nhiệm vụ D — CSS + marker

1. Copy `styles.css` của P05 vào `src/index.css` (hoặc import) — layout card/grid sống lại.
2. Trong `App.jsx`, phần tử gốc thêm comment JSX đầu `main`:

```jsx
{/* CM_EXPECT product_count=8 inventory_value=41380000 components=Header,CategoryList,ProductGrid,ProductCard,Stats */}
```

(Autograder React đọc marker qua source; DOM check bằng testid.)

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
║     - Chỉ cây component trong file App.jsx                   ║
║  3. Thuyết trình 30–60s: props là gì, vì sao read-only?      ║
╚══════════════════════════════════════════════════════════════╝
```
