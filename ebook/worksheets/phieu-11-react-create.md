# Phiếu 11 — React Create: Form Controlled + Validate

| | |
|---|---|
| **Buổi** | 11 |
| **Thời lượng** | ≥ 45 phút (trên lớp ~20' + về nhà ~25–40') |
| **Repo** | `cse391-cm-11` (project Vite — copy từ P10) |
| **Nhận từ Phiếu 10** | State filter/search + FilterBar |
| **Mang sang Phiếu 12** | products trong state + ProductForm — nền Update/Delete |
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
- Cùng ca MN-03 với P08 — cùng số 9 / 3 / 50380000.

Chi tiết: [Buổi 11](../11-buoi-11-react-form-create.md).

---

## 2. Bài trên lớp (~20 phút) — products thành state + form hiện hình

### Yêu cầu

Copy project P10 sang `cse391-cm-11`, rồi:

1. `index.html`: `<title>` exact: **`CampusMart — React Create (Buoi 11)`**.
2. Đổi `products` thành state trong `App` (seed từ `data.js`). Chạy lại — filter/search/stats **phải y nguyên**.
3. Dựng `ProductForm` với 5 field controlled: `sku`, `name`, `category_id` (select từ `categories.map`, option đầu `-- Chon danh muc --`), `price`, `qty`. Nút: `Them san pham`.
4. Submit tạm `console.log(form)` — kiểm object đủ 5 key.

### Cách thử trên lớp (OBS)

| # | Thử | Dự đoán | Quan sát |
|---|-----|---------|----------|
| T1 | Title | `CampusMart — React Create (Buoi 11)` | Tab |
| T2 | Filter vẫn 8/3/3/2 | Như P10 | UI |
| T3 | Gõ từng field | Không mất focus | Controlled |
| T4 | Submit → console | Object đủ 5 key | DevTools / Console |
| T5 | React DevTools | `form` đổi theo phím | Components |

**Checkpoint giảng viên:** form controlled + products đã là state.

---

## 3. Bài về nhà (~25–40 phút) — Validate + Create thật

### Nhiệm vụ A — Validate

| # | Điều kiện lỗi | Chuỗi exact |
|---|---------------|-------------|
| 1 | sku/name/category_id rỗng | `Thieu thong tin bat buoc` |
| 2 | `Number(price) <= 0` | `Gia phai lon hon 0` |
| 3 | SKU đã có | `SKU da ton tai` |

Hiện trong `<p aria-live="polite" data-testid="cm-form-msg">`.

#### Cách thử A (OBS)

| # | Thử | Kỳ vọng |
|---|-----|---------|
| A1 | Submit rỗng | Msg exact required |
| A2 | Giá 0 | `Gia phai lon hon 0` |
| A3 | SKU KB-01 | `SKU da ton tai` |
| A4 | Có lỗi | **Không** tăng số card |

### Nhiệm vụ B — Create bất biến

`setProducts([...products, item])` + reset form + xóa msg. Item đã `Number()` + `trim()`.

#### Cách thử B

| # | Thử | Kỳ vọng |
|---|-----|---------|
| B1 | Không dùng push | Đọc code / demo push hỏng |
| B2 | Sau thêm thành công | Field rỗng |

### Nhiệm vụ C — Ca MN-03 (cùng P08)

Nhập: `MN-03` / `AOC 27 inch` / `Man hinh` / `4500000` / `2`

| Kiểm tra | Giá trị |
|----------|--------:|
| Card Tat ca | **9** |
| Filter Man hinh | **3** |
| Tong gia tri kho | **50380000** |

#### Cách thử C (OBS — đọc to)

Quay video: đọc to **9**, **3**, **50380000**. Thử lại KB-01 → dup, vẫn 9.

### Nhiệm vụ D — Điểm cộng

Persist `cm_products` localStorage (P08). Ghi README nếu làm.

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
| `form.reset` | field rỗng sau thêm thành công |
| `immutable` | không `push` trên state |

### Checklist tự chấm

```
[ ] title P11
[ ] products state
[ ] 3 msg exact
[ ] MN-03 → 9 / 3 / 50380000
[ ] form reset
[ ] CM_EXPECT
```

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

---

## 6. Lỗi thường gặp khi nộp

| Lỗi | Hậu quả | Cách tránh |
|-----|---------|------------|
| push | Không render / trừ immutable | spread |
| Sai chuỗi lỗi (dấu, hoa thường) | EXPECT fail | Copy §8.4 |
| Quên Number category_id | Filter không thấy MN-03 | Ép số |
| Stats hard-code | Tổng không 50380000 | reduce từ state |
| Title Buoi 10 | page_title fail | §8.2 P11 |

---

*CSE391 – Phiếu 11 | CampusMart UI — bản viết lại CSE485*
