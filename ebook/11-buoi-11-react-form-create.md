# BUỔI 11: Form Controlled — Thêm sản phẩm (Create) + Validate UI

> **Thời lượng gợi ý:** 3–4 giờ tự học (lý thuyết + thử máy + Phiếu 11)  
> **Tiên quyết:** Buổi 10 (state, controlled input, derived data)  
> **Kết quả đầu ra:** Form controlled nhiều field; validate chuỗi exact; Create bất biến — card mới mọc tức thì  
> **Phiếu học tập:** [Phiếu 11](./worksheets/phieu-11-react-create.md) — chuỗi **CampusMart UI**  
> **Dataset:** [CANONICAL §8.4, §8.7](./worksheets/CANONICAL-DATA.md) — MN-03, title P11; **cùng ca kiểm với P08**

---

## 0. Gợi ý lịch trình giảng (cho GV)

| Phút | Nội dung | Gợi ý thao tác |
|------|----------|----------------|
| 0–10 | Ôn P10 + hỏi “Làm sao thêm SP mà stats tự đúng?” | Nhắc UI = f(data) |
| 10–25 | So sánh Create P08 (push+render) vs P11 (setState) | Hai cột bảng |
| 25–40 | `products` → `useState(seed)` | Filter vẫn chạy |
| 40–55 | Object form state + 1 `handleChange` | DevTools xem từng phím |
| 55–70 | Validate 3 chuỗi exact §8.4 | Submit rỗng / giá 0 / KB-01 |
| 70–85 | `[...products, item]` — cấm push | Demo push không render |
| 85–100 | Ca MN-03 → 9 / 3 / 50380000 | Đọc to 3 số |
| 100–120 | **Phiếu 11 mục 2** | Checkpoint form controlled |

> Ưu tiên lớp: products state + form hiện hình + hiểu bất biến. Validate + MN-03 về nhà OK.

---

## 1. Mục tiêu buổi học

Sau Buổi 11, bạn có thể:

1. Chuyển `products` từ hằng import thành **state** (`useState(seed)`).
2. Quản lý form nhiều field bằng một object state + một `handleChange`.
3. Validate: required, số dương, SKU không trùng — thông báo **exact** §8.4.
4. Thêm phần tử **bất biến**: `setProducts([...products, item])`.
5. Chạy **cùng ca MN-03 với P08** và giải thích React lo render giúp gì.
6. Giải thích luồng: submit → validate → setState → React vá DOM.

---

## 2. Nhắc lại điểm đứng — Create là bước ngoặt SPA

```
TRƯỚC (P10):  products = const  ──►  filter/search chỉ ĐỌC
SAU  (P11):   [products, setProducts] = useState(seed)
                     │
     submit form ────┘ setProducts([...products, sp_moi])
                     │
                     ▼
  grid +1 card · stats tự cộng · filter tự gom — “miễn phí” nhờ UI = f(data)
```

**Hình ảnh nhớ:**

```
  P08 JS thuần:  push vào mảng  →  BẠN gọi render() + renderStats()
  P11 React:     setProducts(mảng mới)  →  React gọi lại f  →  mọi chỗ dùng products tự đúng
```

---

## 3. Hình ảnh nhớ — form controlled nhiều field

```
   gõ phím ──► onChange ──► setForm({ ...form, [name]: value })
                                  │
                                  ▼
                          input hiển thị lại từ state
          (state = nguồn sự thật — input chỉ là màn chiếu)
```

```
┌─────────────────────────────────────────────────┐
│  ProductForm                                    │
│   state form: { sku, name, category_id,         │
│                 price, qty }                    │
│   state msg: "" | lỗi exact                     │
│                                                 │
│   submit → preventDefault → validate → onCreate │
└─────────────────────────────────────────────────┘
         │ callback
         ▼
┌─────────────────────────────────────────────────┐
│  App                                            │
│   state products ← setProducts([...p, item])    │
│   FilterBar / Grid / Stats đọc products         │
└─────────────────────────────────────────────────┘
```

---

## 4. So sánh sâu — P08 Form Thêm vs P11 React Create

| Bước | P08 JS thuần | P11 React |
|------|--------------|-----------|
| Lưu danh sách | Biến module / mảng | `useState(seedProducts)` |
| Lấy giá trị field | `input.value` lúc submit | Đã có trong `form` state |
| Chặn reload | `preventDefault` | Giống |
| Validate | if + `textContent` lỗi | if + `setMsg` exact |
| Thêm phần tử | `products.push(item)` | `[...products, item]` |
| Cập nhật UI | `render()` + `renderStats()` | Tự động |
| Ca MN-03 | 9 / 3 / 50380000 | **Cùng số** §8.7 |
| Chuỗi lỗi | §8.4 exact | **Cùng chuỗi** |

> Điểm chấm **số liệu và chuỗi** giống P08 — khác **cách** cập nhật UI. Đây là cầu nối sư phạm cố ý.

---

## 5. `products` thành state

```jsx
import { products as seedProducts, categories } from "./data.js";
import { useState } from "react";

function App() {
  const [products, setProducts] = useState(seedProducts);
  // categoryId, keyword từ P10 — KHÔNG phải sửa logic filter
  // chỉ nhớ: filter trên state products, không import cứng seed
}
```

> F5 → về CORE_8 (RAM). Persist `cm_products` = điểm cộng (kiến thức P08). Backend thật = CSE485.

---

## 6. Form object state + một handleChange

```jsx
const emptyForm = { sku: "", name: "", category_id: "", price: "", qty: "" };

function ProductForm({ products, onCreate }) {
  const [form, setForm] = useState(emptyForm);
  const [msg, setMsg] = useState("");

  function handleChange(e) {
    const { name, value } = e.target;
    setForm({ ...form, [name]: value }); // bất biến object
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
      <p aria-live="polite" data-testid="cm-form-msg">{msg}</p>
    </form>
  );
}
```

---

## 7. Validate + Create bất biến

### 7.1. Chuỗi exact (CANONICAL §8.4) — thuộc lòng

| Key | Chuỗi exact |
|-----|-------------|
| `err_required` | `Thieu thong tin bat buoc` |
| `err_price` | `Gia phai lon hon 0` |
| `err_sku_dup` | `SKU da ton tai` |

### 7.2. handleSubmit

```jsx
function handleSubmit(e) {
  e.preventDefault();

  const errors = [];
  if (form.sku.trim() === "" || form.name.trim() === "" || form.category_id === "")
    errors.push("Thieu thong tin bat buoc");
  if (Number(form.price) <= 0) errors.push("Gia phai lon hon 0");
  if (products.some((p) => p.sku === form.sku.trim()))
    errors.push("SKU da ton tai");

  if (errors.length > 0) {
    setMsg(errors.join(". "));
    return;
  }

  onCreate({
    sku: form.sku.trim(),
    name: form.name.trim(),
    category_id: Number(form.category_id),
    price: Number(form.price),
    qty: Number(form.qty || 0),
  });
  setForm(emptyForm);
  setMsg("");
}
```

### 7.3. App nhận Create

```jsx
function handleCreate(item) {
  setProducts([...products, item]); // KHÔNG push!
}

<ProductForm products={products} onCreate={handleCreate} />
```

### 7.4. Vì sao không `push`? — hình ảnh nhớ

```
 products cũ ──ref A──► React nhớ A
 products.push(x)     ──► vẫn ref A ──► React: "không đổi" ──► không render
 [...products, x]     ──► ref B mới ──► React render lại
```

Quy tắc này mang sang P12: `map` (sửa), `filter` (xóa).

---

## 8. Ca kiểm chuẩn MN-03 — cùng P08 (§8.7)

Thêm: **MN-03** / **AOC 27 inch** / **Man hinh** / **4500000** / **2**

| Sau Create (từ CORE_8) | Giá trị |
|------------------------|--------:|
| Tổng card (Tat ca) | **9** |
| Filter Man hinh | **3** |
| Tong gia tri kho | **50380000** |

Kiểm tay: `41380000 + 4500000*2 = 41380000 + 9000000 = 50380000`.

Stats phải **tự** nhảy. Nếu phải sửa Stats → đang hard-code (sai từ P09).

Thử lỗi: nhập lại `KB-01` → `SKU da ton tai`, vẫn 9 card.

---

## 9. Component tree sau P11

```
App (state: products, categoryId, keyword, sortAsc)
├── Header
├── FilterBar
├── ProductForm     ← form state nội bộ + onCreate ↑
├── Stats           ← đọc products.length + inventoryValue(products)
├── cm-visible-count
└── ProductGrid → ProductCard × N
```

---

## 10. Thử ngay trên máy

### Thử 1 — Demo push hỏng

Tạm viết `products.push(item); setProducts(products)` → thêm xong **không** thấy card (hoặc hành vi lạ). Sửa lại spread → thấy ngay.

### Thử 2 — Controlled

| Bước | Dự đoán | Quan sát |
|------|---------|----------|
| Gõ SKU từng chữ | Input không mất focus | DevTools `form.sku` đổi |
| Bỏ `onChange` giữ `value` | Không gõ được | Đóng băng |

### Thử 3 — Validate exact

| Submit | Msg exact |
|--------|-----------|
| Rỗng | `Thieu thong tin bat buoc` |
| Giá 0 | `Gia phai lon hon 0` |
| SKU `KB-01` | `SKU da ton tai` |

### Thử 4 — MN-03

Đọc to: **9** / Man hinh **3** / **50380000**.

### Thử 5 — So với P08 (nếu còn repo)

Chạy cùng ca trên `cse391-cm-08` → cùng 3 số. Khác: P08 phải gọi render tay.

---

## 11. TodoMini (5 phút nắm pattern)

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

Chạy được cái này ≈ hiểu 80% Phiếu 11.

---

## 12. Câu hỏi tự kiểm tra

1. Vì sao Create là bước ngoặt so với P10?
2. Object form: vì sao `{ ...form, [name]: value }` chứ không `{ [name]: value }`?
3. Ba chuỗi validate exact?
4. Vì sao `Number(form.category_id)`?
5. `push` vs spread — React so gì?
6. MN-03 sau Create: 3 số khóa?
7. Title P11?
8. Giống P08 chỗ nào, khác chỗ nào?

---

## 13. Gợi ý đáp án

1. Data danh sách bắt đầu **đổi lúc chạy** — thành ứng dụng.  
2. Thiếu spread → mất các field khác.  
3. `Thieu thong tin bat buoc` / `Gia phai lon hon 0` / `SKU da ton tai`.  
4. Tránh filter so chuỗi/số lệch (bài học P07/P10).  
5. So tham chiếu mảng cũ/mới.  
6. 9 · 3 · 50380000.  
7. `CampusMart — React Create (Buoi 11)`.  
8. Giống ca + chuỗi; khác cơ chế cập nhật UI.

---

## 14. Lỗi thường gặp

| # | Triệu chứng | Nguyên nhân | Cách xử lý |
|---|-------------|-------------|------------|
| 1 | Thêm không thấy card | `push` | `[...products, item]` |
| 2 | Mất focus khi gõ | Thiếu onChange | Đủ controlled |
| 3 | Field đè nhau | Thiếu spread form | `{ ...form, [name]: value }` |
| 4 | Giá thành chuỗi | Quên Number | Ép ở submit |
| 5 | Filter không gom SP mới | category_id chuỗi | `Number(...)` |
| 6 | Reload khi submit | Quên preventDefault | Dòng đầu handler |
| 7 | Stats không nhảy | Hard-code | Tính từ state |
| 8 | Msg sai chính tả | Tự viết lại chuỗi | Copy exact §8.4 |
| 9 | Title sai | Quên P11 | §8.2 |
| 10 | Dup không bắt | So sánh khác trim | `trim()` trước some |

---

## 15. Checklist trước Buổi 12

- [ ] `products` là state, seed từ data.js
- [ ] Form object + một handleChange
- [ ] 3 chuỗi validate exact
- [ ] MN-03 → 9 / 3 / 50380000
- [ ] Giải thích được vì sao không push
- [ ] Xong [Phiếu 11](./worksheets/phieu-11-react-create.md)

---

## 16. Bài tập (chuỗi CampusMart UI)

| Phần | Làm gì | Chi tiết |
|------|--------|----------|
| **Trên lớp (~20')** | products state + form 5 field | [Phiếu 11 — mục 2](./worksheets/phieu-11-react-create.md) |
| **Về nhà (~25–40')** | Validate + MN-03 | [Phiếu 11 — mục 3](./worksheets/phieu-11-react-create.md) |

Repo: `cse391-cm-11` + video OBS.

---

## 17. Cầu nối sang Buổi 12

```
Buổi 11: Create (chữ C)
Buổi 12: Update + Delete + categories (CRUD 2 thực thể) = mốc 2 ★
         Kịch bản: 32880000 → 34770000
```

→ [Buổi 12](./12-buoi-12-react-crud-2-thuc-the.md) · [Phiếu 12](./worksheets/phieu-12-react-crud.md)

---

## 18. So sánh trang P10 → P11

| Tiêu chí | P10 | P11 |
|----------|-----|-----|
| title | React State (Buoi 10) | React Create (Buoi 11) |
| products | const | state |
| Form thêm | Không | `cm-product-form` |
| Ca MN-03 | — | 9 / 3 / 50380000 |
| Validate §8.4 | — | 3 chuỗi exact |

---

## 19. Liên hệ P08 Dashboard (ôn)

Ở P08 bạn đã có Form Thêm + validate. P11 **không đổi nghiệp vụ** — đổi kiến trúc UI:

```
  P08:  data ──push──► data ──render()──► DOM
  P11:  data ──setState(new)──► UI = f(data) ──► DOM
```

Khi sang CSE485 (Laravel), Create lại là `POST` + redirect/re-render Blade — nhưng **validate + SKU unique** vẫn cùng tư duy.

---

## 20. FAQ

**Q: Có được nhiều `useState` từng field không?**  
A: Được, nhưng phiếu bắt object form + 1 handleChange (scale hơn).

**Q: `qty` để trống?**  
A: `Number(form.qty || 0)` — chấp nhận 0; không thay ca MN-03 (qty=2).

**Q: localStorage bắt buộc?**  
A: Không — điểm cộng. F5 về 8 là chấp nhận P11.

---

*CSE391 – Ebook Frontend CampusMart UI | Buổi 11/12 — bản viết lại CSE485*


---

## 21. Walkthrough submit MN-03 từng bước

```
1. Mở form cm-product-form (đang 8 card, tổng 41380000)
2. sku = MN-03
3. name = AOC 27 inch
4. category = Man hinh (value 3)
5. price = 4500000
6. qty = 2
7. Submit

Kiểm ngay:
  - card data-sku=MN-03 xuất hiện
  - Tat ca: Hien thi: 9 san pham
  - Filter Man hinh: 3 card (MN-01, MN-02, MN-03)
  - Stats: Tong gia tri kho = 50380000
  - form các field rỗng (reset)
  - msg trống
```

---

## 22. Bảng tự chấm validate (in mang theo)

| # | Input | Kỳ vọng msg | Kỳ vọng count |
|---|-------|-------------|---------------|
| 1 | Tất cả rỗng | Thieu thong tin bat buoc | 8 |
| 2 | Đủ field, price=0 | Gia phai lon hon 0 | 8 |
| 3 | sku=KB-01, còn lại hợp lệ | SKU da ton tai | 8 |
| 4 | MN-03 hợp lệ | (rỗng) | 9 |
| 5 | MN-03 lần 2 | SKU da ton tai | 9 |

---

## 23. Debug: “Đã setProducts mà Stats sai”

```
[ ] Stats nhận props products từ App state?
[ ] inventoryValue(products) chứ không inventoryValue(seed)?
[ ] Sau Create, React DevTools: products.length === 9?
[ ] Có nhầm 2 biến products (shadowing import)?
```

---

## 24. Immutable cheat-sheet (mang sang P12)

| Mục tiêu | Sai | Đúng |
|----------|-----|------|
| Thêm | `arr.push(x)` | `[...arr, x]` |
| Sửa 1 phần tử | `arr[i].qty = 4` | `arr.map(...)` |
| Xóa | `arr.splice(i,1)` | `arr.filter(...)` |
| Sửa object | `obj.price = 1` | `{ ...obj, price: 1 }` |
| Sửa form field | `setForm({ sku: v })` | `setForm({ ...form, sku: v })` |

---

## 25. Mini-lab dự đoán (giống P10)

| Thao tác | Dự đoán tổng kho | Thực tế |
|----------|----------------:|---------|
| CORE_8 | 41380000 | |
| + MN-03 | 50380000 | |
| (giả sử) xóa MN-03 ngay sau | 41380000 | (P12 mới học Delete) |

---

## 26. Tóm tắt một trang Buổi 11

```
products = useState(seed)
form object + handleChange
preventDefault → validate exact → onCreate
setProducts([...products, item])
MN-03 → 9 / 3 / 50380000
cùng ca P08 — khác cơ chế UI
title: CampusMart — React Create (Buoi 11)
```

---

## 27. Marker EXPECT

```jsx
{/* CM_EXPECT create_sku=MN-03 count_after=9 inventory_after=50380000 err_dup=SKU da ton tai */}
```

---

## 28. Bài tập tư duy (không nộp)

1. Nếu quên `preventDefault`, chuyện gì xảy ra với state?
2. Vì sao phiếu bắt `aria-live="polite"` trên msg?
3. So sánh 1 câu: `renderStats()` P08 vs `<Stats products={products} />` P11.
4. Liệt kê mọi chỗ trong App sẽ “tự đúng” sau Create nhờ UI = f(data).

---

## 29. An toàn kiểu dữ liệu khi Create

```
Input HTML luôn cho chuỗi:
  form.price = "4500000"  (string)

Ép trước khi vào state products:
  price: Number(form.price)     → 4500000
  qty: Number(form.qty || 0)
  category_id: Number(form.category_id)

Nếu quên Number(price):
  inventoryValue có thể nối chuỗi hoặc NaN — tổng kho sai
```

---

## 30. Luồng dữ liệu ASCII đầy đủ

```
[User] gõ form
   │
   ▼
ProductForm.state.form  ←── controlled
   │ submit
   ▼
validate (exact strings)
   │ ok
   ▼
onCreate(item) ──► App.setProducts([...products, item])
   │
   ├─► ProductGrid map thêm card
   ├─► Stats reduce lại tổng
   └─► Filter Man hinh thấy category_id=3 mới
```


---

## 31. So sánh tay hai repo (khuyến nghị GV)

Nếu SV còn `cse391-cm-08`:

1. Chạy P08 → thêm MN-03 → ghi 3 số.  
2. Chạy P11 → thêm MN-03 → cùng 3 số.  
3. Hỏi: “Lần nào bạn phải gọi `render`?”

Câu trả lời đúng là cầu nối cả môn Frontend → tư duy khai báo.

---

## 32. Checklist code review nhanh (bạn bè chấm)

```
[ ] Không products.push
[ ] Không setForm thiếu spread
[ ] Có preventDefault
[ ] Ba chuỗi lỗi khớp ký tự (không thêm dấu chấm cuối bừa)
[ ] category_id / price / qty đã Number
[ ] ProductGrid/Stats dùng state products
[ ] Title P11 exact
```

---

## 33. Mở rộng tùy chọn (không bắt buộc P11)

- Disable nút submit khi đang lỗi.  
- Clear msg khi `handleChange`.  
- `stockLevel` badge trên card mới (hàm P06).  

Không thay EXPECT lõi.

---

*CSE391 – Buổi 11 đủ độ dài mục tiêu 650–800.*


---

## 34. Pseudo-code chấm máy (hiểu EXPECT)

```
open page title contains "React Create (Buoi 11)"
find [data-testid=cm-product-form]
submit empty → text includes "Thieu thong tin bat buoc"
fill price=0 → includes "Gia phai lon hon 0"
fill sku=KB-01 valid else → includes "SKU da ton tai"
fill MN-03 / AOC 27 inch / cat=3 / 4500000 / 2 → submit
assert cards == 9
assert filter Man hinh cards == 3
assert inventory text includes 50380000
```

---

## 35. Phân biệt lỗi nghiệp vụ vs lỗi kỹ thuật

| Loại | Ví dụ | Sinh viên làm gì |
|------|-------|------------------|
| Nghiệp vụ | SKU trùng | Hiện msg exact, không thêm |
| Kỹ thuật | Quên spread form | Field mất — sửa code |
| Kỹ thuật | push | UI không cập nhật |
| Nghiệp vụ | Giá ≤ 0 | Msg `Gia phai lon hon 0` |

---

## 36. Gợi ý tổ chức file (tùy chọn)

```
src/
  data.js
  App.jsx
  components/
    ProductForm.jsx
    ProductGrid.jsx
    ...
```

P11 không bắt tách file — nhưng tách Form giúp P12 đọc `editingSku` rõ hơn.
