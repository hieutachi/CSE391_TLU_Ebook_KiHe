# BUỔI 11: Form Controlled — Thêm sản phẩm (Create) + Validate UI

> **Thời lượng gợi ý:** 4 giờ tự học  
> **Tiên quyết:** Buổi 10 (state, controlled input, derived data)  
> **Kết quả đầu ra:** Form controlled nhiều field, validate với thông báo chuẩn, thêm sản phẩm vào state bất biến — card mới mọc ra tức thì  
> **Phiếu học tập liên quan:** [Phiếu 11](./worksheets/phieu-11-react-create.md) — chuỗi **CampusMart UI**

---

## 1. Mục tiêu buổi học

Sau Buổi 11, bạn có thể:

1. Chuyển `products` từ hằng import thành **state** (`useState(seed)`).
2. Quản lý form nhiều field bằng một object state.
3. Validate: required, số dương, SKU không trùng — thông báo exact.
4. Thêm phần tử **bất biến**: `setProducts([...products, item])`.
5. Giải thích luồng: submit → validate → setState → React vá DOM.

---

## 2. Vì sao Create là bước ngoặt của SPA?

Đến giờ `products` là **hằng số**: mọi state (filter, search) chỉ *nhìn* dữ liệu. Hôm nay dữ liệu **tự thay đổi trong lúc chạy** — đó là ranh giới giữa "trang có tương tác" và "ứng dụng". Khi products thành state, mọi thứ dựa vào nó (grid, stats, filter) tự đúng theo — không sửa thêm dòng nào. Đó là phần thưởng của kiến trúc `UI = f(data)`.

```
   TRƯỚC (P10):  products = const  ──►  filter/search chỉ ĐỌC
   SAU  (P11):   [products, setProducts] = useState(seed)
                        │
        submit form ────┘ setProducts([...products, sp_moi])
                        │
                        ▼
     grid +1 card · stats tự cộng · filter tự gom — MIỄN PHÍ
```

---

## 3. `products` thành state

```jsx
import { products as seedProducts, categories } from "./data.js";

function App() {
  // seed từ CANONICAL — nhưng từ giờ SỐNG trong state
  const [products, setProducts] = useState(seedProducts);
  // ... categoryId, keyword như P10 — KHÔNG phải sửa gì thêm
}
```

> Dữ liệu chỉ sống trong RAM: F5 là về lại CORE_8. Chấp nhận ở phiếu này — persist bằng localStorage là **điểm cộng** (bạn đã biết cách từ P08), backend thật là chuyện của CSE485.

---

## 4. Form nhiều field — một object state

Thay vì 5 `useState` lẻ tẻ, gom một object:

```jsx
const emptyForm = { sku: "", name: "", category_id: "", price: "", qty: "" };

function ProductForm({ products, onCreate }) {
  const [form, setForm] = useState(emptyForm);
  const [msg, setMsg] = useState("");

  // MỘT handler cho mọi field — nhờ thuộc tính name trùng key
  function handleChange(e) {
    const { name, value } = e.target;
    setForm({ ...form, [name]: value }); // copy object + đè 1 key (bất biến!)
  }

  return (
    <form data-testid="cm-product-form" onSubmit={handleSubmit}>
      <label htmlFor="f-sku">SKU</label>
      <input id="f-sku" name="sku" value={form.sku} onChange={handleChange} />

      <label htmlFor="f-name">Ten</label>
      <input id="f-name" name="name" value={form.name} onChange={handleChange} />

      <label htmlFor="f-cat">Danh muc</label>
      <select id="f-cat" name="category_id" value={form.category_id} onChange={handleChange}>
        <option value="">-- Chon danh muc --</option>
        {categories.map((c) => (
          <option key={c.id} value={c.id}>{c.name}</option>
        ))}
      </select>

      <label htmlFor="f-price">Gia</label>
      <input id="f-price" name="price" type="number" value={form.price} onChange={handleChange} />

      <label htmlFor="f-qty">So luong</label>
      <input id="f-qty" name="qty" type="number" value={form.qty} onChange={handleChange} />

      <button type="submit">Them san pham</button>
      <p aria-live="polite">{msg}</p>
    </form>
  );
}
```

```
   gõ phím ──► onChange ──► setForm({...form, [name]: value})
                                │
                                ▼
                        input hiển thị lại từ state
        (state là nguồn sự thật — input chỉ là màn chiếu)
```

---

## 5. Validate + Create bất biến

```jsx
function handleSubmit(e) {
  e.preventDefault();

  const errors = [];
  if (form.sku.trim() === "" || form.name.trim() === "" || form.category_id === "")
    errors.push("Thieu thong tin bat buoc");
  if (Number(form.price) <= 0) errors.push("Gia phai lon hon 0");
  if (products.some((p) => p.sku === form.sku.trim()))
    errors.push("SKU da ton tai");                     // exact §8.4

  if (errors.length > 0) {
    setMsg(errors.join(". "));
    return;
  }

  onCreate({
    sku: form.sku.trim(),
    name: form.name.trim(),
    category_id: Number(form.category_id),  // ép số — bài học P07!
    price: Number(form.price),
    qty: Number(form.qty || 0),
  });
  setForm(emptyForm);   // dọn form
  setMsg("");
}
```

Bên `App`:

```jsx
function handleCreate(item) {
  setProducts([...products, item]);  // KHÔNG push! Tạo mảng MỚI
}

<ProductForm products={products} onCreate={handleCreate} />
```

### Vì sao không `products.push(item)`?

React so sánh **tham chiếu** mảng cũ/mới để biết có gì đổi. `push` giữ nguyên tham chiếu → React tưởng không có gì mới → không render. `[...products, item]` tạo mảng mới → render. Quy tắc này áp cho mọi update P12 (`map` để sửa, `filter` để xóa).

### Ca kiểm chuẩn (CANONICAL §8.7) — cùng ca với P08

Đây chính là ca kiểm bạn đã chạy ở Phiếu 08 bằng JS thuần (`push` + tự gọi `render`/`renderStats`). Lần này React lo phần render — hãy so hai cách làm để thấy mình được gì. Thêm **MN-03 / AOC 27 inch / Man hinh / 4500000 / 2**:

| Sau Create | Giá trị |
|------------|--------:|
| Tổng card (Tat ca) | **9** |
| Filter Man hinh | **3** |
| Tong gia tri kho | **50380000** |

Stats phải tự nhảy — nếu bạn phải sửa Stats mới đúng, nghĩa là Stats đang hard-code (sai từ P09).

---

## 6. Bài thực hành minh họa trong buổi (không thay phiếu)

Form một field, 5 phút, nắm trọn pattern:

```jsx
function TodoMini() {
  const [items, setItems] = useState(["Hoc bai"]);
  const [text, setText] = useState("");

  return (
    <div>
      <form onSubmit={(e) => {
        e.preventDefault();
        if (text.trim() === "") return;
        setItems([...items, text.trim()]);
        setText("");
      }}>
        <input value={text} onChange={(e) => setText(e.target.value)} />
        <button>Them</button>
      </form>
      <ul>{items.map((t, i) => <li key={`${t}-${i}`}>{t}</li>)}</ul>
    </div>
  );
}
```

Chạy được cái này là bạn đã hiểu 80% Phiếu 11 — phần còn lại chỉ là nhiều field hơn và validate chặt hơn.

---

## 7. Lỗi thường gặp

| Triệu chứng | Nguyên nhân | Cách xử lý |
|-------------|-------------|------------|
| Thêm xong không thấy card | `push` thay vì mảng mới | `[...products, item]` |
| Gõ 1 ký tự mất focus/không gõ được | `value` mà thiếu `onChange` | Đủ cặp controlled |
| Field ghi đè nhau | `setForm({ [name]: value })` thiếu spread | `{ ...form, [name]: value }` |
| Giá thành chuỗi `"4500000"` | Quên `Number()` khi submit | Ép kiểu tại handleSubmit |
| Filter không gom SP mới | `category_id` là chuỗi | `Number(form.category_id)` |
| Trang reload khi submit | Quên `preventDefault` | Dòng đầu handler |

---

## 8. Checklist trước khi sang buổi sau

- [ ] `products` là state, seed từ data.js
- [ ] Form object state + một `handleChange` chung
- [ ] 3 quy tắc validate, chuỗi exact `Gia phai lon hon 0` / `SKU da ton tai`
- [ ] Thêm MN-03 → 9 card / Man hinh 3 / tổng 50380000 tự nhảy
- [ ] Giải thích được vì sao không dùng `push`
- [ ] Đã làm xong [Phiếu 11](./worksheets/phieu-11-react-create.md)

---

## 9. Bài tập trên lớp & về nhà (chuỗi CampusMart UI)

| Phần | Làm gì | Chi tiết |
|------|--------|----------|
| **Trên lớp (~20')** | products → state + form 5 field controlled | [Phiếu 11 — mục 2](./worksheets/phieu-11-react-create.md) |
| **Về nhà (~25–40')** | Validate + Create MN-03 + kiểm 3 số | [Phiếu 11 — mục 3](./worksheets/phieu-11-react-create.md) |

Nộp repo riêng `cse391-cm-11` + video OBS theo box cuối phiếu.

---

## 10. Cầu nối sang Buổi 12

Create xong — còn **Update** và **Delete**, cộng thêm quản lý **danh mục** như một thực thể thứ hai đúng nghĩa (thêm danh mục mới, chặn xóa danh mục còn hàng). Buổi 12 khép vòng **CRUD 2 thực thể** — đích cuối của chuỗi phiếu.

→ Tiếp: [Buổi 12](./12-buoi-12-react-crud-2-thuc-the.md) · [Phiếu 12](./worksheets/phieu-12-react-crud.md)

---

*CSE391 – Ebook Frontend CampusMart UI | Buổi 11/12*
