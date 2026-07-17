# BUỔI 9: React khởi đầu — Vite, JSX, Component, Props

> **Thời lượng gợi ý:** 4 giờ tự học  
> **Tiên quyết:** Buổi 6–8 (JS: render từ mảng, module, event). Node.js LTS đã cài  
> **Kết quả đầu ra:** Scaffold project Vite + React, hiểu JSX, tách UI thành component, truyền dữ liệu qua props, render list bằng `map` + `key`  
> **Phiếu học tập liên quan:** [Phiếu 09](./worksheets/phieu-09-react-props.md) — chuỗi **CampusMart UI**

---

## 1. Mục tiêu buổi học

Sau Buổi 9, bạn có thể:

1. Tạo và chạy project React bằng Vite (`npm create vite`).
2. Đọc/viết JSX; nêu 3 khác biệt JSX vs HTML (`className`, `{}`, đóng thẻ).
3. Viết function component; ghép cây component.
4. Truyền props từ cha xuống con; destructuring props.
5. Render danh sách bằng `map` + `key`; hiểu vì sao cần `key`.
6. Dựng lại CampusMart tĩnh (8 card) bằng component.

---

## 2. Vì sao React — sau khi đã làm được bằng JS thuần?

Nhìn lại Dashboard của Phiếu 08: sau mỗi lần thêm sản phẩm, bạn phải **tự nhớ** gọi `render()` rồi `renderStats()` — quên một cái là UI lệch dữ liệu. Trang nhỏ thì ổn; cứ thêm một trạng thái (filter + search + sort + form) là thêm một mớ code "đồng bộ hóa DOM" dễ sai, và cách "xóa sạch grid vẽ lại" làm mất trạng thái (con trỏ trong input, vị trí cuộn).

React đề nghị một thỏa thuận:

> **Bạn mô tả UI trông thế nào theo dữ liệu. Việc cập nhật DOM tối thiểu, để React lo.**

```
   JS thuần (P07):  dữ liệu đổi ──► BẠN tự xóa/vẽ DOM
   React    (P09+): dữ liệu đổi ──► React tính phần thay đổi ──► vá DOM

   UI = f(data)     ← công thức đáng nhớ nhất của cả môn
```

---

## 3. Scaffold bằng Vite

```bash
npm create vite@latest cse391-cm-09 -- --template react
cd cse391-cm-09
npm install
npm run dev        # → http://localhost:5173
```

Cấu trúc cần quan tâm (phần còn lại kệ nó):

```
cse391-cm-09/
├── index.html          ← có <div id="root"> + <title> (sửa title ở đây!)
├── src/
│   ├── main.jsx        ← điểm vào: mount <App /> vào #root
│   ├── App.jsx         ← component gốc — bạn làm việc chủ yếu ở đây
│   ├── App.css / index.css
│   └── data.js         ← BẠN tạo: dataset canonical (§5)
└── package.json
```

> `node_modules/` không bao giờ commit — template Vite đã có `.gitignore` đúng.

---

## 4. JSX — HTML "sống" trong JS

```jsx
const name = "Keychron K2";
const price = 1890000;

const card = (
  <article className="cm-card">
    <h3>{name}</h3>
    <p>{price * 2}</p>      {/* trong {} là JS thật: biểu thức nào cũng được */}
    <img src="/kb01.jpg" alt={name} />
  </article>
);
```

| HTML | JSX | Lý do |
|------|-----|-------|
| `class="cm-card"` | `className="cm-card"` | `class` là từ khóa JS |
| `<img ...>` | `<img ... />` | mọi thẻ phải đóng |
| `onclick="..."` | `onClick={fn}` | camelCase + truyền hàm, không truyền chuỗi |
| — | `{bieuThuc}` | nhúng JS vào markup |

Một component phải trả về **một** phần tử gốc (bọc `<div>` hoặc fragment `<>...</>`).

---

## 5. Component & cây component

### 5.1. Function component

```jsx
// Tên PascalCase — bắt buộc, để React phân biệt với thẻ HTML
function Header() {
  return (
    <header className="cm-header">
      <h1 data-testid="cm-brand">CampusMart</h1>
    </header>
  );
}

// Dùng như thẻ:  <Header />
```

### 5.2. Component tree của CampusMart (ASCII bắt buộc nhớ)

```
                     App
                      │
      ┌───────────────┼──────────────────┐
      ▼               ▼                  ▼
   Header        CategoryList       ProductGrid
      │               │                  │
      ▼               ▼          ┌───────┼───────┐
    (nav)        (3 × li)        ▼       ▼       ▼
                             ProductCard ProductCard … ×8
                                  │
                            props: product
```

Mỗi khối bạn từng viết trong HTML giờ là một **hàm** — gọi được, tái sử dụng được, truyền tham số được.

---

## 6. Props — dữ liệu chảy từ cha xuống con

```jsx
// Con: nhận props (destructuring cho gọn)
function ProductCard({ product }) {
  return (
    <article className="cm-card" data-testid="cm-product-row" data-sku={product.sku}>
      <h3>{product.name}</h3>
      <p className="cm-card-price">{product.price}</p>
      <p>So luong: {product.qty}</p>
    </article>
  );
}

// Cha: truyền xuống như attribute
<ProductCard product={products[0]} />
```

```
   App (có products)
        │  props (một chiều, chỉ chảy XUỐNG)
        ▼
   ProductCard (chỉ ĐỌC props — không bao giờ sửa)
```

Quy tắc sắt: **props là read-only**. Muốn dữ liệu thay đổi được → state (Buổi 10).

---

## 7. Render list — `map` + `key`

Đúng ý tưởng Buổi 7, nhưng thay vòng `for`+`createElement` bằng một biểu thức:

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

**`key` là gì?** Nhãn định danh giúp React biết card nào là card nào giữa hai lần render — nhờ đó chỉ vá đúng chỗ đổi. Dùng **`p.sku`** (ổn định, duy nhất). **Cấm** dùng index mảng khi danh sách sẽ thêm/xóa/sắp xếp (chính là CRUD của P11–P12) — index đổi chỗ làm React vá nhầm phần tử.

---

## 8. Bài thực hành minh họa trong buổi (không thay phiếu)

Trong project Vite mới tạo, sửa `App.jsx` thành:

```jsx
function Hello({ ten }) {
  return <p>Xin chao {ten}!</p>;
}

export default function App() {
  const ds = ["An", "Binh", "Chi"];
  return (
    <main>
      <h1>Demo props</h1>
      {ds.map((t) => (
        <Hello key={t} ten={t} />
      ))}
    </main>
  );
}
```

Lưu file → trình duyệt **tự cập nhật không F5** (HMR). Thêm tên thứ 4 vào mảng → thấy ngay. Đây là vòng lặp dev bạn sẽ sống cùng 4 buổi tới.

---

## 9. Lỗi thường gặp

| Triệu chứng | Nguyên nhân | Cách xử lý |
|-------------|-------------|------------|
| Trang trắng + console đỏ | JSX sai cú pháp (quên đóng thẻ, 2 phần tử gốc) | Đọc lỗi — Vite chỉ đúng dòng |
| Warning `unique "key" prop` | Quên `key` trong `map` | `key={p.sku}` |
| Component không hiện | Tên viết thường (`<header/>` vs `<Header/>`) | PascalCase |
| `class` không ăn style | Dùng `class` thay `className` | Sửa attribute |
| Sửa props trong con | Hiểu nhầm props mutable | Props read-only; chờ state Buổi 10 |
| `npm run dev` lỗi | Thiếu `npm install` / Node cũ | Node LTS + install lại |

---

## 10. Checklist trước khi sang buổi sau

- [ ] Tạo + chạy được project Vite React
- [ ] Nêu được 3 khác biệt JSX vs HTML
- [ ] Tách được ≥4 component: Header, CategoryList, ProductGrid, ProductCard
- [ ] Truyền props + destructuring
- [ ] `map` + `key={sku}` render 8 card
- [ ] Đã làm xong [Phiếu 09](./worksheets/phieu-09-react-props.md)

---

## 11. Bài tập trên lớp & về nhà (chuỗi CampusMart UI)

| Phần | Làm gì | Chi tiết |
|------|--------|----------|
| **Trên lớp (~20')** | Scaffold + Header + 1 ProductCard props | [Phiếu 09 — mục 2](./worksheets/phieu-09-react-props.md) |
| **Về nhà (~25–40')** | Đủ cây component + 8 card + stats | [Phiếu 09 — mục 3](./worksheets/phieu-09-react-props.md) |

Nộp repo riêng `cse391-cm-09` + video OBS theo box cuối phiếu.

---

## 12. Cầu nối sang Buổi 10

CampusMart React hiện **tĩnh** — đẹp nhưng chưa bấm được gì. Buổi 10 thêm linh hồn: **state** (`useState`) và sự kiện — bộ lọc danh mục + ô tìm kiếm sẽ chạy lại, lần này React tự cập nhật DOM cho bạn.

→ Tiếp: [Buổi 10](./10-buoi-10-react-state-filter.md) · [Phiếu 10](./worksheets/phieu-10-react-state-filter.md)

---

*CSE391 – Ebook Frontend CampusMart UI | Buổi 9/12*
