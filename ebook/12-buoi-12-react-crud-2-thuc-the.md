# BUỔI 12: Update & Delete — CRUD 2 thực thể Category ↔ Product ★ ĐÍCH CUỐI

> **Thời lượng gợi ý:** 4–5 giờ tự học  
> **Tiên quyết:** Buổi 11 (products là state + Create)  
> **Kết quả đầu ra:** CRUD đủ trên products (sửa/xóa bằng `map`/`filter` bất biến); categories thành thực thể quản lý được (thêm/sửa, chặn xóa khi còn hàng)  
> **Phiếu học tập liên quan:** [Phiếu 12](./worksheets/phieu-12-react-crud.md) — **đích cuối chuỗi CampusMart**

---

## 1. Mục tiêu buổi học

Sau Buổi 12, bạn có thể:

1. Update phần tử trong state bằng `map` (bản mới, không mutate).
2. Delete bằng `filter` + `confirm`.
3. Dùng chung một form cho Create/Edit (state `editingSku`).
4. Nâng `categories` thành state; thêm/sửa danh mục.
5. Cài **quy tắc nghiệp vụ liên thực thể**: chặn xóa category còn product.
6. Ghép tất cả thành CampusMart Admin SPA hoàn chỉnh.

---

## 2. Vì sao "2 thực thể" mới là bài toán thật?

Một mảng products đứng riêng chỉ là danh sách. Ứng dụng thật luôn có **quan hệ**: sản phẩm *thuộc về* danh mục — xóa danh mục thì sản phẩm mồ côi. Đây chính là `belongsTo`/`hasMany` mà bạn sẽ gặp lại nguyên vẹn ở Laravel (CSE485, Phiếu 10–12 bên đó). Học cách bảo vệ quan hệ ở UI hôm nay = hiểu vì sao backend cũng phải chặn ở tầng dữ liệu.

```
      categories (state)            products (state)
     ┌──────────────────┐         ┌─────────────────────┐
     │ 1 Ban phim   ◄───┼─────────│ KB-01 category_id=1 │
     │ 2 Chuot      ◄───┼─────────│ MS-01 category_id=2 │
     │ 3 Man hinh   ◄───┼─────────│ MN-01 category_id=3 │
     │ 4 Phu kien (mới) │         │ ...                 │
     └──────────────────┘         └─────────────────────┘
        XÓA "Chuot"?  ──►  còn 3 SP trỏ vào ──►  CHẶN!
        "Khong the xoa danh muc con san pham"
```

---

## 3. Update — `map` tạo mảng mới

```jsx
function handleUpdate(sku, patch) {
  setProducts(
    products.map((p) =>
      p.sku === sku ? { ...p, ...patch } : p   // đúng SKU: object MỚI; khác: giữ nguyên
    )
  );
}

// Ví dụ: KB-01 đổi qty 3 → 4
handleUpdate("KB-01", { qty: 4 });
```

```
 products cũ ──map──► products mới
 [KB-01 qty3] ──►  {…KB-01, qty:4}   ← object mới
 [KB-02     ] ──►  giữ nguyên tham chiếu
 [  ...     ] ──►  giữ nguyên
      React so tham chiếu → chỉ vá card KB-01
```

### Form dùng chung Create/Edit

Thêm state `editingSku` (null = đang Create):

```jsx
const [editingSku, setEditingSku] = useState(null);

// Bấm "Sua" trên card:
function startEdit(p) {
  setEditingSku(p.sku);
  setForm({ ...p, category_id: String(p.category_id), price: String(p.price), qty: String(p.qty) });
  // đổ ngược số → chuỗi vì input controlled làm việc với chuỗi
}

// Trong handleSubmit:
if (editingSku) {
  onUpdate(editingSku, itemDaEpKieu);   // giữ nguyên sku khi edit
  setEditingSku(null);
} else {
  onCreate(itemDaEpKieu);               // validate trùng SKU chỉ áp cho Create
}
```

Nút submit đổi nhãn theo ngữ cảnh: `Them san pham` ↔ `Cap nhat san pham`.

---

## 4. Delete — `filter` + confirm

```jsx
function handleDelete(sku) {
  const p = products.find((x) => x.sku === sku);
  if (!window.confirm(`Xoa san pham ${p.name}?`)) return;
  setProducts(products.filter((x) => x.sku !== sku));
}
```

Nút đặt trong `ProductCard` (dùng callback props — đúng mô hình lifting state up của Buổi 10):

```jsx
function ProductCard({ product, onEdit, onDelete }) {
  return (
    <article className="cm-card" data-sku={product.sku} data-testid="cm-product-row">
      {/* ...nội dung như cũ... */}
      <div className="cm-card-actions">
        <button onClick={() => onEdit(product)}>Sua</button>
        <button onClick={() => onDelete(product.sku)}>Xoa</button>
      </div>
    </article>
  );
}
```

---

## 5. Categories thành thực thể thứ hai

### 5.1. State + form danh mục

```jsx
const [categories, setCategories] = useState(seedCategories);

function handleCreateCategory(name) {
  const trimmed = name.trim();
  if (trimmed === "") return "Thieu thong tin bat buoc";
  if (categories.some((c) => c.name === trimmed)) return "Danh muc da ton tai";

  const nextId = Math.max(...categories.map((c) => c.id)) + 1; // 3+1 = 4
  setCategories([...categories, { id: nextId, name: trimmed, description: "" }]);
  return "";
}
```

Form `data-testid="cm-category-form"`: một input tên + nút `Them danh muc`. Danh mục mới (ví dụ **Phu kien**, id 4) phải **tự xuất hiện** trong: `CategoryList`, select filter, select của ProductForm — cả ba nơi đều `map` từ state nên tự đúng.

### 5.2. Quy tắc liên thực thể — chặn xóa

```jsx
function handleDeleteCategory(id) {
  const count = products.filter((p) => p.category_id === id).length;
  if (count > 0) {
    setCatMsg("Khong the xoa danh muc con san pham");   // exact §8.4
    return;
  }
  if (!window.confirm("Xoa danh muc?")) return;
  setCategories(categories.filter((c) => c.id !== id));
  setCatMsg("");
}
```

Quy tắc này **giống hệt** ràng buộc bạn sẽ viết ở Laravel P12 (chặn destroy khi `products()->count() > 0`) — một bài toán, hai tầng bảo vệ.

---

## 6. Kịch bản chuẩn toàn màn (CANONICAL §8.8)

Từ nền CORE_8, làm **đúng thứ tự** — đây là kịch bản video và Autograder:

| Bước | Thao tác | Kết quả bắt buộc |
|------|----------|-------------------|
| 1 | Delete `MN-02` (OK confirm) | 7 card · `Tong gia tri kho = 32880000` |
| 2 | Edit `KB-01`: qty 3 → 4 | `Tong gia tri kho = 34770000` |
| 3 | Thêm danh mục `Phu kien` | select lọc + select form đều 4 danh mục |
| 4 | Thử xóa danh mục `Chuot` | chặn: `Khong the xoa danh muc con san pham` |
| 5 | Xóa danh mục `Phu kien` (0 SP) | xóa được — quay về 3 danh mục |

Kiểm số tay: `41380000 − 8500000 = 32880000`; `+1890000 = 34770000`.

---

## 7. Bài thực hành minh họa trong buổi (không thay phiếu)

Nháp 5 phút trên console tư duy `map`/`filter` bất biến (không cần React):

```js
let arr = [{ id: 1, qty: 3 }, { id: 2, qty: 5 }];

// update id=1 → qty 9, KHÔNG mutate:
const updated = arr.map((x) => (x.id === 1 ? { ...x, qty: 9 } : x));
console.log(arr[0].qty, updated[0].qty);       // 3 9  ← gốc còn nguyên

// delete id=2:
const deleted = arr.filter((x) => x.id !== 2);
console.log(arr.length, deleted.length);       // 2 1
```

Hai dòng `map`/`filter` này chính là **toàn bộ** Update/Delete của SPA.

---

## 8. Lỗi thường gặp

| Triệu chứng | Nguyên nhân | Cách xử lý |
|-------------|-------------|------------|
| Edit không ăn | Mutate object trong map | `{ ...p, ...patch }` object mới |
| Edit xong sinh SP trùng | Nhánh edit lại gọi onCreate | Rẽ nhánh theo `editingSku` |
| Edit báo `SKU da ton tai` chính nó | Validate trùng áp cả khi edit | Chỉ check trùng khi Create |
| Xóa nhầm không hỏi | Thiếu confirm | `window.confirm` trước filter |
| DM mới không vào select form | Select hard-code option | `map` từ state categories |
| Xóa DM còn hàng vẫn được | Thiếu guard đếm products | Kiểm count trước khi filter |
| Số qty thành chuỗi sau edit | Quên ép kiểu khi submit | `Number()` trong handleSubmit |

---

## 9. Checklist ĐÍCH CUỐI

- [ ] Products: **C**reate (P11) + **R**ead/filter/search (P10) + **U**pdate + **D**elete
- [ ] Form dùng chung Create/Edit, nhãn nút đổi đúng ngữ cảnh
- [ ] Categories: thêm + xóa (khi rỗng) — thực thể thứ hai đúng nghĩa
- [ ] Chặn xóa danh mục còn sản phẩm, thông báo exact
- [ ] Kịch bản 5 bước mục 6 chạy đúng từng số
- [ ] Đã làm xong [Phiếu 12](./worksheets/phieu-12-react-crud.md)

---

## 10. Bài tập trên lớp & về nhà (chuỗi CampusMart UI)

| Phần | Làm gì | Chi tiết |
|------|--------|----------|
| **Trên lớp (~20')** | Delete + Update qty (map/filter) | [Phiếu 12 — mục 2](./worksheets/phieu-12-react-crud.md) |
| **Về nhà (~25–40')** | Form Edit chung + quản lý categories + kịch bản 5 bước | [Phiếu 12 — mục 3](./worksheets/phieu-12-react-crud.md) |

Nộp repo riêng `cse391-cm-12` + video OBS theo box cuối phiếu.

---

## 11. Cầu nối sang Project cuối môn

Bạn vừa hoàn thành **đích cuối chuỗi phiếu**: CampusMart React CRUD 2 thực thể. Buổi 13–15 là project nhóm: chọn đề tài riêng, thêm routing / fetch API giả / deploy để lấy điểm Cộng — và mang code này làm portfolio thật.

→ Tiếp: [Project cuối môn](./13-project-cuoi-mon.md)

---

*CSE391 – Ebook Frontend CampusMart UI | Buổi 12/12 ★*
