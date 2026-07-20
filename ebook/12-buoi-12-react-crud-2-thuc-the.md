# BUỔI 12: Update & Delete — CRUD 2 thực thể Category ↔ Product ★ ĐÍCH CUỐI

> **Thời lượng gợi ý:** 4–5 giờ tự học (lý thuyết + thử máy + Phiếu 12)  
> **Tiên quyết:** Buổi 11 (products state + Create)  
> **Kết quả đầu ra:** **Ứng dụng React CRUD hoàn chỉnh** (mốc 2): C/R/U/D products; categories là thực thể thứ hai; chặn xóa DM còn hàng  
> **Phiếu học tập:** [Phiếu 12](./worksheets/phieu-12-react-crud.md) — **đích cuối chuỗi CampusMart**  
> **Dataset:** [CANONICAL §8.4, §8.8](./worksheets/CANONICAL-DATA.md) — 32880000 / 34770000; title P12

---

## 0. Gợi ý lịch trình giảng (cho GV)

| Phút | Nội dung | Gợi ý thao tác |
|------|----------|----------------|
| 0–10 | Ôn P11 + tuyên bố “hôm nay khép CRUD + 2 thực thể” | Chiếu mốc 1 P08 vs mốc 2 P12 |
| 10–25 | Hình ảnh nhớ quan hệ Category↔Product | Vẽ mũi tên category_id |
| 25–40 | Delete = filter + confirm | Xóa MS-03 demo |
| 40–55 | Update = map + form chung editingSku | Sửa qty live |
| 55–70 | Categories state + form `cm-category-form` | Thêm Phu kien |
| 70–85 | Chặn xóa DM còn SP — msg exact | Thử xóa Chuot |
| 85–100 | **Kịch bản §8.8** đọc to 32880000 / 34770000 | Cả lớp cùng chạy |
| 100–120 | **Phiếu 12 mục 2** + giao về nhà kịch bản 5 bước | Checkpoint Delete+Update |

> Ưu tiên lớp: Delete + Update qty + hiểu quan hệ. Categories + kịch bản 5 bước về nhà / lab dài.

---

## 1. Mục tiêu buổi học

Sau Buổi 12, bạn có thể:

1. Update phần tử trong state bằng `map` (bản mới, không mutate).
2. Delete bằng `filter` + `window.confirm`.
3. Dùng chung một form Create/Edit (`editingSku`).
4. Nâng `categories` thành state; thêm/sửa danh mục; lan tỏa 2 select.
5. Cài **quy tắc nghiệp vụ liên thực thể**: chặn xóa category còn product — msg exact.
6. Chạy kịch bản §8.8 khớp **32880000** và **34770000**.
7. Nhận diện P12 = **ứng dụng CRUD hoàn chỉnh** (mốc 2), đối xứng mốc 1 P08.

---

## 2. Nhắc lại — từ Create tới CRUD đủ chữ

```
P09  Read (tĩnh)          UI = f(data)
P10  Read + Filter        UI = f(data, state UI)
P11  Create               products thành state
P12  Update + Delete      + categories state
     = CRUD 2 thực thể    ★ ĐÍCH CUỐI CHUỖI PHIẾU
```

**Mốc đầu ra môn (CANONICAL / GRADING):**

| Mốc | Phiếu | Ý nghĩa |
|-----|-------|---------|
| 1 | P08 | Dashboard CRUD có Form Thêm (**JS thuần**) |
| 2 | P12 | **Ứng dụng React CRUD hoàn chỉnh 2 thực thể** |

---

## 3. Hình ảnh nhớ — 2 thực thể liên kết

```
      categories (state)            products (state)
     ┌──────────────────┐         ┌─────────────────────┐
     │ 1 Ban phim   ◄───┼─────────│ KB-01 category_id=1 │
     │ 2 Chuot      ◄───┼─────────│ MS-01 category_id=2 │
     │ 3 Man hinh   ◄───┼─────────│ MN-01 category_id=3 │
     │ 4 Phu kien (mới) │         │ ...                 │
     └──────────────────┘         └─────────────────────┘
        XÓA "Chuot"?  ──►  còn 3 SP trỏ vào ──►  CHẶN!
        msg exact: "Khong the xoa danh muc con san pham"
```

> Đây chính là `belongsTo` / `hasMany` bạn sẽ gặp lại ở Laravel CSE485. Bảo vệ quan hệ ở UI hôm nay = hiểu vì sao backend cũng phải chặn.

### 3.1. UI = f(data) với hai mảng

```
  UI = f(products, categories, editingSku, filterState, form...)

  Đổi products  → grid/stats đổi
  Đổi categories → CategoryList + 2 select đổi
  Không tự “gọi renderCategories()” riêng — khai báo map từ state
```

---

## 4. So sánh P08 / P11 / P12

| Khả năng | P08 JS | P11 | P12 |
|----------|--------|-----|-----|
| List + filter | ✓ | ✓ | ✓ |
| Create SP | ✓ | ✓ | ✓ |
| Update SP | — | — | ✓ map |
| Delete SP | — | — | ✓ filter |
| Quản lý Category | — | — | ✓ thực thể 2 |
| Chặn xóa DM còn hàng | — | — | ✓ |
| Kịch bản 32880000→34770000 | — | — | ✓ |

---

## 5. Update — `map` tạo mảng mới

### 5.1. Hình ảnh nhớ

```
 products cũ ──map──► products mới
 [KB-01 qty3] ──►  {…KB-01, qty:4}   ← object mới
 [KB-02     ] ──►  giữ nguyên tham chiếu
 [  ...     ] ──►  giữ nguyên
      React so tham chiếu → vá card KB-01
```

### 5.2. Code

```jsx
function handleUpdate(sku, patch) {
  setProducts(
    products.map((p) =>
      p.sku === sku ? { ...p, ...patch } : p
    )
  );
}

// KB-01 đổi qty 3 → 4
handleUpdate("KB-01", { qty: 4 });
```

### 5.3. Form dùng chung Create/Edit

```jsx
const [editingSku, setEditingSku] = useState(null);

function startEdit(p) {
  setEditingSku(p.sku);
  setForm({
    ...p,
    category_id: String(p.category_id),
    price: String(p.price),
    qty: String(p.qty),
  });
}

// Trong handleSubmit:
if (editingSku) {
  onUpdate(editingSku, itemDaEpKieu); // giữ sku; KHÔNG check trùng SKU
  setEditingSku(null);
} else {
  onCreate(itemDaEpKieu); // validate trùng như P11
}
```

Nhãn nút: `Them san pham` ↔ `Cap nhat san pham`. Có nút `Huy` (reset form + `editingSku=null`).

---

## 6. Delete — `filter` + confirm

```jsx
function handleDelete(sku) {
  const p = products.find((x) => x.sku === sku);
  if (!window.confirm(`Xoa san pham ${p.name}?`)) return;
  setProducts(products.filter((x) => x.sku !== sku));
}
```

```jsx
function ProductCard({ product, onEdit, onDelete }) {
  return (
    <article className="cm-card" data-sku={product.sku} data-testid="cm-product-row">
      {/* nội dung */}
      <div className="cm-card-actions">
        <button type="button" onClick={() => onEdit(product)}>Sua</button>
        <button type="button" onClick={() => onDelete(product.sku)}>Xoa</button>
      </div>
    </article>
  );
}
```

Callback props = lifting state up (P10) — card không tự `setProducts`.

---

## 7. Categories thành thực thể thứ hai

### 7.1. State + Create danh mục

```jsx
const [categories, setCategories] = useState(seedCategories);

function handleCreateCategory(name) {
  const trimmed = name.trim();
  if (trimmed === "") return "Thieu thong tin bat buoc";
  if (categories.some((c) => c.name === trimmed)) return "Danh muc da ton tai";

  const nextId = Math.max(...categories.map((c) => c.id)) + 1;
  setCategories([...categories, { id: nextId, name: trimmed, description: "" }]);
  return "";
}
```

Form `data-testid="cm-category-form"`: input tên + nút `Them danh muc`.

DM mới (**Phu kien**, id 4) phải tự xuất hiện ở:

1. `CategoryList`  
2. Select filter  
3. Select ProductForm  

→ cả ba `map` từ **state** categories.

### 7.2. Chặn xóa — quy tắc liên thực thể

```jsx
function handleDeleteCategory(id) {
  const count = products.filter((p) => p.category_id === id).length;
  if (count > 0) {
    setCatMsg("Khong the xoa danh muc con san pham"); // exact §8.4
    return;
  }
  if (!window.confirm("Xoa danh muc?")) return;
  setCategories(categories.filter((c) => c.id !== id));
  setCatMsg("");
}
```

| Key §8.4 | Chuỗi exact |
|----------|-------------|
| `err_delete_category` | `Khong the xoa danh muc con san pham` |
| `err_cat_dup` | `Danh muc da ton tai` |
| `err_required` | `Thieu thong tin bat buoc` |

---

## 8. Kịch bản chuẩn toàn màn (CANONICAL §8.8) ★

Từ nền **CORE_8** (không mang MN-03 từ P11 sang — reset seed), làm **đúng thứ tự**:

| Bước | Thao tác | Kết quả bắt buộc |
|------|----------|------------------|
| 1 | Delete `MN-02` (OK confirm) | 7 card · `Tong gia tri kho = 32880000` |
| 2 | Edit `KB-01`: qty 3 → 4 | `Tong gia tri kho = 34770000` |
| 3 | Thêm danh mục `Phu kien` | select lọc + select form đều **4** DM |
| 4 | Thử xóa danh mục `Chuot` | chặn: `Khong the xoa danh muc con san pham` |
| 5 | Xóa danh mục `Phu kien` (0 SP) | xóa được — về 3 DM |

**Kiểm số tay (thuộc lòng):**

```
41380000 − 8500000 = 32880000     (xóa MN-02 giá 8500000 × qty 1)
32880000 + 1890000 = 34770000     (KB-01 thêm 1 đơn vị × 1890000)
```

Đây là kịch bản **video OBS + Autograder**. Sai thứ tự → số lệch.

---

## 9. Component tree đích cuối

```
App
├── state: products, categories, editingSku, categoryId, keyword, ...
├── Header
├── FilterBar                 (map categories state)
├── CategoryManager           (cm-category-form + list + Xoa)
├── ProductForm               (Create/Edit chung)
├── Stats
├── cm-visible-count
└── ProductGrid
     └── ProductCard × N      (Sua / Xoa)
```

---

## 10. Thử ngay trên máy

### Chuẩn bị

Copy P11 → `cse391-cm-12`, title:

`CampusMart — React CRUD (Buoi 12)`

Đảm bảo `products` seed lại CORE_8 (8 SP, không MN-03).

### Thử 1 — Delete + Cancel

| Bước | Dự đoán | Quan sát |
|------|---------|----------|
| Xoa MS-03 → Cancel | Vẫn 8 card | confirm |
| Xoa MS-03 → OK | 7 card, tổng giảm | stats |

### Thử 2 — Update map

Sửa KB-02 qty 5→1: badge stockLevel `Du`→`Can nhap` (nếu có); tổng đổi.

### Thử 3 — Kịch bản §8.8 bước 1–2

| Sau bước | Tổng |
|----------|-----:|
| Xóa MN-02 | **32880000** |
| KB-01 qty 4 | **34770000** |

Đọc to hai số trên video.

### Thử 4 — Category

Thêm `Phu kien` → đếm option = 4 ở cả 2 select. Xóa `Chuot` → msg exact. Xóa `Phu kien` → OK.

### Thử 5 — Immutable phá

Thử `products.splice` / gán `p.qty = 4` trong map thiếu spread → hành vi sai → sửa lại.

---

## 11. Nháp console 5 phút (map/filter)

```js
let arr = [{ id: 1, qty: 3 }, { id: 2, qty: 5 }];
const updated = arr.map((x) => (x.id === 1 ? { ...x, qty: 9 } : x));
console.log(arr[0].qty, updated[0].qty); // 3 9
const deleted = arr.filter((x) => x.id !== 2);
console.log(arr.length, deleted.length); // 2 1
```

Hai dòng này = toàn bộ Update/Delete SPA.

---

## 12. Câu hỏi tự kiểm tra

1. P12 là mốc nào của môn? Khác P08 chỗ nào?
2. Update dùng `map` thế nào (một dòng ý)?
3. Vì sao edit không check `SKU da ton tai`?
4. Delete bắt buộc có gì trước `filter`?
5. Vì sao không xóa được `Chuot` khi còn SP?
6. Hai số khóa kịch bản §8.8?
7. Title P12 exact?
8. DM mới phải xuất hiện ở những đâu?

---

## 13. Gợi ý đáp án

1. Mốc 2 — React CRUD 2 thực thể; P08 là mốc 1 JS thuần Form Thêm.  
2. `map`: đúng sku → object mới `{...p,...patch}`, khác → giữ.  
3. SKU đang là chính nó — trùng với chính mình sẽ false positive.  
4. `window.confirm`.  
5. Quy tắc liên thực thể — còn product trỏ `category_id`.  
6. 32880000 và 34770000.  
7. `CampusMart — React CRUD (Buoi 12)`.  
8. CategoryList + filter select + ProductForm select.

---

## 14. Lỗi thường gặp

| # | Triệu chứng | Nguyên nhân | Cách xử lý |
|---|-------------|-------------|------------|
| 1 | Edit không ăn | Mutate trong map | `{ ...p, ...patch }` |
| 2 | Edit tạo SP trùng | Nhánh edit gọi onCreate | Rẽ `editingSku` |
| 3 | Edit báo dup chính nó | Check trùng cả khi edit | Chỉ Create |
| 4 | Xóa không hỏi | Thiếu confirm | `window.confirm` |
| 5 | DM mới không vào select | Option hard-code | `map` state |
| 6 | Xóa Chuot vẫn được | Thiếu đếm products | Guard count |
| 7 | Số qty thành chuỗi | Quên Number | Ép ở submit |
| 8 | Sai 32880000 | Còn MN-03 / sai thứ tự | Reset CORE_8 |
| 9 | Sai 34770000 | Không +1 qty KB-01 | qty 3→4 |
| 10 | Msg lệch ký tự | Tự diễn đạt | Exact §8.4 |
| 11 | Title sai | Quên P12 | §8.2 |
| 12 | splice state | Mutate | filter |

---

## 15. Checklist ĐÍCH CUỐI

- [ ] Products: Create (P11) + Read/filter (P10) + Update + Delete
- [ ] Form chung Create/Edit, nhãn đúng, có Huy
- [ ] Categories: thêm + xóa khi rỗng
- [ ] Chặn xóa DM còn SP — msg exact
- [ ] Kịch bản 5 bước §8.8 đúng từng số
- [ ] Xong [Phiếu 12](./worksheets/phieu-12-react-crud.md)

---

## 16. Bài tập (chuỗi CampusMart UI)

| Phần | Làm gì | Chi tiết |
|------|--------|----------|
| **Trên lớp (~20')** | Delete + Update qty | [Phiếu 12 — mục 2](./worksheets/phieu-12-react-crud.md) |
| **Về nhà (~25–40')** | Form Edit + categories + kịch bản 5 bước | [Phiếu 12 — mục 3](./worksheets/phieu-12-react-crud.md) |

Repo: `cse391-cm-12` + video OBS.

---

## 17. Cầu nối sang Project cuối môn

```
Chuỗi phiếu P01–P12 = xong CampusMart UI
Buổi 13–15: project nhóm — routing / fetch giả / deploy / portfolio
```

→ [Project cuối môn](./13-project-cuoi-mon.md)

---

## 18. So sánh trang P11 → P12

| Tiêu chí | P11 | P12 |
|----------|-----|-----|
| title | React Create (Buoi 11) | React CRUD (Buoi 12) |
| Update/Delete SP | — | Sua / Xoa |
| categories | const | state + form |
| Kịch bản §8.8 | — | 32880000 → 34770000 |
| Mốc | Create React | ★ CRUD 2 thực thể |

---

## 19. Walkthrough số liệu §8.8 chi tiết

```
CORE_8 inventory = 41380000
MN-02: price 8500000 × qty 1 = 8500000

Bước 1 Delete MN-02:
  41380000 − 8500000 = 32880000
  product_count = 7

Bước 2 Edit KB-01 qty 3 → 4:
  thêm 1 × 1890000 = 1890000
  32880000 + 1890000 = 34770000
  (KB-01 line_total: 5670000 → 7560000)

Bước 3 Phu kien:
  categories.length = 4
  max id = 4

Bước 4 Xóa Chuot:
  products với category_id=2: MS-01, MS-02, MS-03 → count 3 > 0
  → msg: Khong the xoa danh muc con san pham
  categories vẫn 4

Bước 5 Xóa Phu kien:
  products category_id=4: 0
  → cho xóa → categories.length = 3
```

---

## 20. FAQ

**Q: Video có được thêm MN-03 trước kịch bản không?**  
A: Không — §8.8 từ CORE_8. F5 hoặc reset state trước khi quay.

**Q: Confirm Cancel có EXPECT không?**  
A: Có — Cancel không xóa (GRADING / phiếu).

**Q: Có bắt buộc sửa tên DM không?**  
A: P12 lõi: thêm + xóa có điều kiện. Sửa tên = mở rộng OK.

**Q: localStorage?**  
A: Điểm cộng — không thay kịch bản số liệu.

---

## 21. Immutable cheat-sheet P12

| Việc | Code |
|------|------|
| Update | `products.map(p => p.sku===sku ? {...p,...patch} : p)` |
| Delete | `products.filter(p => p.sku!==sku)` |
| Add cat | `[...categories, {id: next, name, description:""}]` |
| Del cat | `categories.filter(c => c.id!==id)` sau khi count===0 |

---

## 22. Liên hệ CSE485 (chốt)

| CSE391 P12 | CSE485 |
|------------|--------|
| Chặn xóa DM còn SP | `destroy` Policy / `products()->exists()` |
| category_id trên product | FK + `belongsTo` |
| Form Create/Edit chung | resource Controller `store`/`update` |
| Confirm trước xóa | UX + (server) form DELETE |

Một bài toán — hai tầng bảo vệ (UI + server).

---

## 23. Tóm tắt một trang ★

```
Update = map bất biến
Delete = confirm + filter
Form chung = editingSku
Categories = state thực thể 2
Chặn xóa = đếm products theo category_id
Msg = Khong the xoa danh muc con san pham
Kịch bản = 32880000 → 34770000
Mốc 2 = ứng dụng React CRUD hoàn chỉnh
title = CampusMart — React CRUD (Buoi 12)
```

---

## 24. Marker EXPECT

```jsx
{/* CM_EXPECT after_delete=7,32880000 after_edit=34770000 new_category=Phu kien block_msg=Khong the xoa danh muc con san pham */}
```

---

## 25. Bài tập tư duy (không nộp)

1. Nếu xóa DM vẫn để product `category_id` cũ — UI lệch chỗ nào?  
2. Vì sao P12 cố ý không dùng MN-03 từ P11 trong kịch bản video?  
3. Vẽ lại cây App đủ CategoryManager.  
4. Viết 3 câu thuyết trình 60–90s theo box nộp phiếu.

---

## 26. Checklist code review bạn bè

```
[ ] Sua / Xoa trên mọi card
[ ] confirm trước xóa SP
[ ] editingSku + nhãn Cap nhat san pham + Huy
[ ] cm-category-form
[ ] Phu kien vào cả 2 select
[ ] Xóa Chuot → msg exact
[ ] Không splice/push
[ ] Kịch bản số đúng khi reset CORE_8
```

---

*CSE391 – Ebook Frontend CampusMart UI | Buổi 12/12 ★ — bản viết lại CSE485*


---

## 27. Kịch bản sai thường gặp (tránh)

| Sai | Hậu quả số | Cách tránh |
|-----|------------|------------|
| Xóa MN-01 thay MN-02 | Tổng ≠ 32880000 | Đúng SKU MN-02 |
| Sửa giá KB-01 thay qty | Tổng lệch | Chỉ qty 3→4 |
| Thêm Phu kien trước khi xóa MN-02 | Vẫn OK số kho nhưng lệch thứ tự video | Đúng 5 bước |
| Mang MN-03 từ P11 | Tổng nền ≠ 41380000 | Reset seed |
| Gõ msg tự diễn đạt | EXPECT chuỗi fail | Copy exact |

---

## 28. DevTools khi chấm

```
1. Components → App → products.length
2. Sau bước 1: length 7; sau bước 2: KB-01.qty === 4
3. categories: tìm name "Phu kien" / id 4
4. Thử xóa Chuot → catMsg state Exact
```

---

## 29. Kết — bạn đã đi hết chuỗi

```
HTML → CSS → Responsive → JS → DOM → Module
  → React props → State → Create → CRUD 2 thực thể
```

Chúc mừng nếu kịch bản §8.8 chạy đúng hai số **32880000** và **34770000**.


---

## 30. App.jsx khung đích (tham khảo tư duy — tự gõ)

```jsx
import { useState } from "react";
import { products as seedP, categories as seedC } from "./data.js";

export default function App() {
  const [products, setProducts] = useState(seedP);
  const [categories, setCategories] = useState(seedC);
  const [editingSku, setEditingSku] = useState(null);
  const [categoryId, setCategoryId] = useState("all");
  const [keyword, setKeyword] = useState("");
  // ... form state có thể lift lên App hoặc giữ trong ProductForm qua props editingSku

  function handleCreate(item) {
    setProducts((prev) => [...prev, item]);
  }
  function handleUpdate(sku, patch) {
    setProducts((prev) =>
      prev.map((p) => (p.sku === sku ? { ...p, ...patch } : p))
    );
  }
  function handleDelete(sku) {
    const p = products.find((x) => x.sku === sku);
    if (!window.confirm(`Xoa san pham ${p.name}?`)) return;
    setProducts((prev) => prev.filter((x) => x.sku !== sku));
  }

  // derived visible như P10, nhưng từ state products
  const visible = products
    .filter((p) => categoryId === "all" || p.category_id === Number(categoryId))
    .filter((p) => p.name.toLowerCase().includes(keyword.toLowerCase()));

  return (
    <main>
      {/* CM_EXPECT after_delete=7,32880000 after_edit=34770000 new_category=Phu kien block_msg=Khong the xoa danh muc con san pham */}
      {/* Header, FilterBar(categories), CategoryManager, ProductForm, Stats, Grid */}
    </main>
  );
}
```

> Dùng functional update `setProducts(prev => ...)` tránh stale closure khi handler phức tạp — khuyến nghị P12.

---

## 31. Functional update — vì sao P12 nên dùng

```
  Click Xoa nhanh 2 card:
  - setProducts(products.filter(...))  dùng products lúc closure
    → có thể cả 2 lần dựa cùng mảng cũ → chỉ xóa 1
  - setProducts(prev => prev.filter(...))
    → mỗi lần dựa mảng mới nhất
```

Hình ảnh nhớ: **setter nhận hàm** = “cho tôi bản mới nhất rồi tính”.

---

## 32. stockLevel sau Update (ôn P06)

| qty sau sửa | stockLevel |
|------------:|------------|
| ≥ 5 | `Du` |
| ≥ 2 | `Sap het` |
| else | `Can nhap` |

Ví dụ lớp: KB-02 qty 5→1 → `Du` → `Can nhap`. Không khóa EXPECT P12 nhưng demo UI = f(data) đẹp.

---

## 33. Bảng tự chấm kịch bản §8.8 (in mang theo)

| Bước | Đã làm? | Số quan sát | Khớp? |
|------|---------|-------------|-------|
| Reset CORE_8 | | count=8, tổng=41380000 | |
| 1 Xóa MN-02 | | 7 / 32880000 | |
| 2 KB-01 qty4 | | 34770000 | |
| 3 Phu kien | | 4 DM × 2 select | |
| 4 Xóa Chuot | | msg exact | |
| 5 Xóa Phu kien | | 3 DM | |

---

## 34. Pseudo-code Autograder (hiểu máy chấm)

```
reset to CORE_8
delete MN-02 confirm OK
  assert count==7 and inventory==32880000
edit KB-01 qty=4
  assert inventory==34770000
create category name=Phu kien
  assert both selects option count >= 4 and has Phu kien
try delete category Chuot
  assert message == "Khong the xoa danh muc con san pham"
delete category Phu kien confirm OK
  assert category count == 3
```

---

## 35. Phân vai trong video thuyết trình 60–90s (gợi ý)

1. “Category và Product liên kết bằng `category_id`.”  
2. “Xóa danh mục khi còn sản phẩm sẽ làm SP mồ côi — UI chặn bằng đếm.”  
3. “Backend CSE485 cũng phải chặn — không tin mỗi UI.”  
4. Chỉ vào msg exact trên màn hình.

---

## 36. So sánh Delete SP vs Delete Category

| | Xóa Product | Xóa Category |
|--|-------------|--------------|
| Cấu trúc | 1 mảng | 1 mảng |
| Ràng buộc | confirm UX | confirm + **count products** |
| Thuật toán | `filter` sku | `filter` id **sau** guard |
| Msg lỗi nghiệp vụ | — | `Khong the xoa danh muc con san pham` |

---

## 37. Lỗi editingSku thường gặp — sơ đồ quyết định

```
Submit form
   │
   ├─ editingSku != null ──► Update map ──► clear editingSku
   │                         (không check dup SKU)
   │
   └─ editingSku == null ──► Validate + Create
                             (có check dup SKU)
```

Nếu nhầm nhánh → hoặc nhân đôi SP, hoặc báo dup khi đang sửa chính mình.

---

## 38. Reset về CORE_8 trước video — 3 cách

1. F5 (nếu không persist localStorage).  
2. Nút dev “Reset demo” gọi `setProducts(seedP); setCategories(seedC)`.  
3. Xóa `cm_products` trong Application tab nếu có điểm cộng storage.

---

## 39. Kiểm lan tỏa categories (checklist mắt)

Sau khi thêm `Phu kien`:

```
[ ] CategoryList có dòng Phu kien
[ ] Filter select có option Phu kien
[ ] ProductForm select có option Phu kien
[ ] value option = "4" (hoặc id số 4)
```

Thiếu 1 chỗ → chỗ đó đang hard-code 3 option P09.

---

## 40. Tổng kết hành trình CampusMart UI (để ôn)

| Buổi | Năng lực |
|------|----------|
| 1–2 | HTML ngữ nghĩa + form |
| 3–5 | CSS + layout + responsive |
| 6–8 | JS + DOM + module + Create JS |
| 9 | React props + UI=f(data) |
| 10 | State + derived filter |
| 11 | Create React + validate |
| 12 | CRUD 2 thực thể ★ |

---

## 41. Câu hỏi mở cho giờ thảo luận (GV)

1. Nếu cho xóa Chuot và set `category_id` SP về `null` — UX tốt hơn hay xấu hơn?  
2. Optimistic UI (xóa trước, hoàn tác sau) có cần ở P12 không?  
3. Vì sao khóa số 32880000 thay vì “khoảng đúng”?

---

## 42. Definition of Done P12

```
Done khi:
  ✓ Kịch bản 5 bước quay được một take sạch
  ✓ Hai số đọc to khớp
  ✓ Msg chặn exact
  ✓ Repo không node_modules
  ✓ Thuyết trình nêu được quan hệ 2 thực thể
```

---

*Hết phần mở rộng Buổi 12 — đủ độ dài sư phạm đích 750–950.*
