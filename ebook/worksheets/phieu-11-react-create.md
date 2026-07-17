# Phiếu 11 — React Create: Form Controlled + Validate

| | |
|---|---|
| **Buổi** | 11 |
| **Thời lượng** | ≥ 45 phút (trên lớp ~20' + về nhà ~25–40') |
| **Repo** | `cse391-cm-11` (project Vite — copy từ P10) |
| **Nhận từ Phiếu 10** | State filter/search + FilterBar |
| **Mang sang Phiếu 12** | products trong state + ProductForm — nền cho Update/Delete |
| **Dataset** | [`CANONICAL-DATA.md`](./CANONICAL-DATA.md) — §8.7 MN-03, §8.4 thông báo |

### Chuẩn đầu ra (CLO)

1. `products` chuyển thành state (`useState(seedProducts)`); filter/search/stats vẫn đúng.  
2. `ProductForm` (`data-testid="cm-product-form"`) 5 field controlled qua một object state + một `handleChange`.  
3. Validate 3 quy tắc với chuỗi exact: thiếu thông tin, `Gia phai lon hon 0`, `SKU da ton tai`.  
4. Create bất biến (`[...products, item]`); thêm MN-03 → 9 card / Man hinh 3 / tổng **50380000**.  
5. Form reset sau khi thêm thành công.

---

## 1. Tóm tắt lý thuyết (đọc lại trước khi code)

- `const [products, setProducts] = useState(seedProducts)`.
- Object form state: `setForm({ ...form, [name]: value })`.
- Submit: `preventDefault` → validate → `onCreate(item)` → reset.
- Bất biến: **không** `push`; ép `Number()` cho price/qty/category_id.

Chi tiết: [Buổi 11](../11-buoi-11-react-form-create.md).

---

## 2. Bài trên lớp (~20 phút) — products thành state + form hiện hình

### Yêu cầu

Copy project P10 sang `cse391-cm-11`, rồi:

1. `index.html`: `<title>` exact: **`CampusMart — React Create (Buoi 11)`**.
2. Đổi `products` thành state trong `App` (seed từ `data.js`). Chạy lại — filter/search/stats **phải y nguyên** (nếu vỡ tức là chỗ nào đó import cứng mảng thay vì nhận props).
3. Dựng `ProductForm` với 5 field controlled: `sku`, `name`, `category_id` (select build từ `categories.map`, option đầu `-- Chon danh muc --`), `price` (number), `qty` (number). Nút submit text: `Them san pham`.
4. Submit tạm thời chỉ `console.log(form)` — kiểm object đủ 5 key.

### Output kỳ vọng (trên lớp)

Gõ vào từng field → console log ra object cập nhật đúng key; không mất focus, không reload.

**Checkpoint giảng viên:** React DevTools thấy state `form` đổi theo từng phím gõ.

---

## 3. Bài về nhà (~25–40 phút) — Validate + Create thật

Tiếp tục **cùng project**.

### Nhiệm vụ A — Validate

Trong `handleSubmit`, thứ tự kiểm:

| # | Điều kiện lỗi | Chuỗi exact |
|---|---------------|--------------|
| 1 | sku/name/category_id rỗng | `Thieu thong tin bat buoc` |
| 2 | `Number(price) <= 0` | `Gia phai lon hon 0` |
| 3 | SKU đã có trong products | `SKU da ton tai` |

Lỗi hiện trong `<p aria-live="polite" data-testid="cm-form-msg">` (nối `. ` nếu nhiều lỗi); có lỗi thì **không** thêm.

### Nhiệm vụ B — Create bất biến

Hợp lệ → `onCreate` đẩy lên `App`: `setProducts([...products, item])` (item đã `Number()` hóa price/qty/category_id, `trim()` chuỗi). Sau đó reset form + xóa msg.

### Nhiệm vụ C — Ca kiểm chuẩn MN-03

Nhập đúng: `MN-03` / `AOC 27 inch` / `Man hinh` / `4500000` / `2` → kiểm:

| Kiểm tra | Giá trị |
|----------|--------:|
| Card ở Tat ca | **9** |
| Filter Man hinh | **3** (MN-01, MN-02, MN-03) |
| `Tong gia tri kho` trong Stats | **50380000** |
| `Hien thi: 9 san pham` | đúng count |

Thử tiếp ca lỗi: nhập lại `KB-01` → `SKU da ton tai`, vẫn 9 card.

### Nhiệm vụ D — Điểm cộng (không bắt buộc)

Persist `products` vào localStorage key `cm_products` (đọc khi khởi tạo state, ghi mỗi lần đổi) — kiến thức P08. Ghi rõ trong README repo nếu làm.

### Nhiệm vụ E — Marker

```jsx
{/* CM_EXPECT create_sku=MN-03 count_after=9 inventory_after=50380000 err_dup=SKU da ton tai */}
```

---

## 4. OUTPUT / EXPECT

| Key | Value bắt buộc |
|-----|----------------|
| `page_title` | `CampusMart — React Create (Buoi 11)` |
| `testid.cm-product-form` | form 5 field controlled |
| `submit_text` | `Them san pham` |
| `validate.empty` | `Thieu thong tin bat buoc` |
| `validate.price` | `Gia phai lon hon 0` |
| `validate.dup` | `SKU da ton tai` (nhập KB-01) |
| `create.MN-03.count` | 9 card |
| `create.MN-03.manhinh` | 3 card khi filter Man hinh |
| `create.MN-03.inventory` | 50380000 trong Stats |
| `form.reset` | các field rỗng sau thêm thành công |
| `immutable` | không dùng `push` trên state |

---

## 5. Box nộp bài

```
╔══════════════════════════════════════════════════════════════╗
║  NỘP PHIẾU 11                                                ║
╠══════════════════════════════════════════════════════════════╣
║  1. GitHub repo RIÊNG: cse391-cm-11 (KHÔNG node_modules)     ║
║  2. Video OBS + camera mặt — kịch bản:                       ║
║     - Submit rỗng → "Thieu thong tin bat buoc"               ║
║     - Gia 0 → "Gia phai lon hon 0"; KB-01 → "SKU da ton tai" ║
║     - Thêm MN-03 → đọc to 3 số: 9 / 3 / 50380000             ║
║     - F5 → về 8 (hoặc demo điểm cộng localStorage)           ║
║  3. Thuyết trình 30–60s: vì sao [...products, item] mà       ║
║     không phải push?                                         ║
╚══════════════════════════════════════════════════════════════╝
```
