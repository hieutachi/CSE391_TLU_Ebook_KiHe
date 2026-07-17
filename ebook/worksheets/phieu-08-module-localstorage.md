# Phiếu 08 — Module, localStorage & Dashboard có Form Thêm ★ mốc JS thuần

| | |
|---|---|
| **Buổi** | 8 |
| **Thời lượng** | ≥ 45 phút (trên lớp ~20' + về nhà ~25–40') |
| **Repo** | `cse391-cm-08` |
| **Nhận từ Phiếu 07** | `render(list)` + filter + sort |
| **Mang sang Phiếu 09** | **Dashboard JS thuần có Form Thêm** — nền để React hoàn chỉnh CRUD |
| **Dataset** | [`CANONICAL-DATA.md`](./CANONICAL-DATA.md) — §8.4 thông báo, §8.5 keys, §8.7 ca MN-03 |

### Chuẩn đầu ra (CLO)

1. Tách 3 module `data.js` / `helpers.js` / `app.js` với `export`/`import`; một entry `type="module"`.  
2. Filter được ghi nhớ qua F5 bằng key `cm_filter`.  
3. Validate form đăng ký: 2 quy tắc, chuỗi lỗi exact; hợp lệ → lưu `cm_subscribers`.  
4. **Form Thêm sản phẩm (`cm-product-form`) hoạt động:** validate 3 quy tắc → thêm vào mảng → render lại → stats tự cộng. Ca MN-03: 9 card / Man hinh 3 / tổng **50380000**.  
5. Toàn bộ chức năng Phiếu 07 (render/filter/sort) vẫn chạy sau khi tách module.

---

## 1. Tóm tắt lý thuyết (đọc lại trước khi code)

- `export const/function` → `import { x } from "./file.js"`; chạy qua Live Server.
- localStorage: chuỗi only → `JSON.stringify`/`parse`; phòng `null` bằng `?? "[]"`.
- `submit` + `preventDefault` → kiểm tra → lỗi exact §8.4 → xử lý → `form.reset()`.
- Dashboard = stats + filter + list + **Form Thêm**; sau khi thêm phải gọi lại `render` + `renderStats`.

Chi tiết: [Buổi 8](../08-buoi-08-module-localstorage-validate.md).

---

## 2. Bài trên lớp (~20 phút) — Tách module + nhớ filter

### Yêu cầu

Copy từ repo Phiếu 07 sang `cse391-cm-08`, rồi:

1. Đổi `<title>` exact: **`CampusMart — Module & Storage (Buoi 8)`**.
2. Tách code:
   - `data.js`: thêm `export` trước `categories`, `products`.
   - `helpers.js`: chuyển 5 hàm Phiếu 06 vào + `export` từng hàm.
   - `app.js`: `import` từ 2 file trên; giữ render/filter/sort. Bọc phần ghi stats (P06) thành hàm `renderStats()` gọi lại được.
3. HTML chỉ còn: `<script type="module" src="app.js"></script>`.
4. Nhớ filter:
   - `change` → `localStorage.setItem("cm_filter", value)` rồi render.
   - Khi load → đọc `cm_filter` (mặc định `"all"`), set lại select, render đúng danh sách.

### Output kỳ vọng (trên lớp)

Chọn `Chuot` → F5 → select vẫn `Chuot`, lưới vẫn 3 card.

**Checkpoint giảng viên:** 3 file module; Application tab có `cm_filter`; F5 giữ trạng thái.

---

## 3. Bài về nhà (~25–40 phút) — Validate + Form Thêm sản phẩm

Tiếp tục **cùng repo**.

### Nhiệm vụ A — Validate form đăng ký (form Phiếu 02)

Thêm `<p id="form-msg" aria-live="polite"></p>` dưới form. Handler `submit`:

| Quy tắc | Chuỗi lỗi exact |
|---------|------------------|
| `name.trim().length < 2` | `Ten toi thieu 2 ky tu` |
| email không khớp `/^\S+@\S+\.\S+$/` | `Email khong hop le` |

- Có lỗi → ghi các lỗi (nối bằng `. `) vào `#form-msg`, class `cm-error`, **không** lưu.
- Hợp lệ → push `{name, email, category_id}` vào `cm_subscribers` (JSON array), ghi `Dang ky thanh cong` (class `cm-success`), `form.reset()`.

### Nhiệm vụ B — Form Thêm sản phẩm ★ trọng tâm phiếu

Thêm section trên/dưới lưới card:

```html
<section id="add-product">
  <h2>Them san pham</h2>
  <form data-testid="cm-product-form">
    <!-- 5 field: sku (text) · name (text) · category_id (select 3 DM,
         option đầu "-- Chon danh muc --") · price (number) · qty (number) -->
    <button type="submit">Them san pham</button>
  </form>
  <p id="product-form-msg" aria-live="polite"></p>
</section>
```

Handler theo Buổi 8 mục 6 — thứ tự validate:

| # | Điều kiện lỗi | Chuỗi exact |
|---|---------------|--------------|
| 1 | sku/name/category_id rỗng | `Thieu thong tin bat buoc` |
| 2 | `Number(price) <= 0` | `Gia phai lon hon 0` |
| 3 | SKU đã tồn tại | `SKU da ton tai` |

Hợp lệ → `products.push(item)` (đã `Number()` hóa) → `render(products)` + `renderStats()` → reset form.

**Ca kiểm chuẩn (§8.7):** thêm `MN-03` / `AOC 27 inch` / `Man hinh` / `4500000` / `2`:

| Kiểm tra | Giá trị |
|----------|--------:|
| Card ở Tat ca | **9** |
| Filter Man hinh | **3** |
| Stats `Tong gia tri kho` | **50380000** |

Ca lỗi: nhập lại `KB-01` → `SKU da ton tai`, vẫn 9 card. (F5 → về 8 — dữ liệu sống trong RAM, chấp nhận ở mốc này.)

### Nhiệm vụ C — Danh sách subscriber sống (nhanh)

Dưới form đăng ký: `<ul data-testid="cm-subscriber-list"></ul>` — render từ `cm_subscribers` khi load + sau mỗi đăng ký thành công (mỗi `li`: `name — email`, dùng `textContent`).

### Nhiệm vụ D — (Điểm cộng, không bắt buộc) Login giả

Form `cm-login-form`: `admin` / `CampusMart@01` → `cm_auth="true"` + `Dang nhap thanh cong`; sai → `Sai tai khoan hoac mat khau`. Ghi vào README repo nếu làm.

### Nhiệm vụ E — Marker

```html
<!-- CM_EXPECT product_count=8 create_sku=MN-03 count_after=9 inventory_after=50380000 modules=data.js,helpers.js,app.js -->
```

---

## 4. OUTPUT / EXPECT

| Key | Value bắt buộc |
|-----|----------------|
| `page_title` | `CampusMart — Module & Storage (Buoi 8)` |
| `script` | duy nhất 1 entry `type="module"` |
| `files` | `data.js`, `helpers.js`, `app.js` có export/import |
| `filter.persist` | chọn DM → F5 → giữ nguyên select + card |
| `validate.name` / `.email` | `Ten toi thieu 2 ky tu` / `Email khong hop le` |
| `validate.ok` | `Dang ky thanh cong` + append `cm_subscribers` |
| `testid.cm-product-form` | 5 field + nút `Them san pham` |
| `create.empty` / `.price` / `.dup` | `Thieu thong tin bat buoc` / `Gia phai lon hon 0` / `SKU da ton tai` |
| `create.MN-03` | 9 card · Man hinh=3 · `Tong gia tri kho = 50380000` |
| Giữ từ P07 | render 8 card, filter 8/3/3/2, sort MS-03 đầu |
| Điểm cộng | login giả (`cm_auth`, 2 msg exact) — không bắt buộc |
| `CM_EXPECT` comment | như Nhiệm vụ E |

---

## 5. Box nộp bài

```
╔══════════════════════════════════════════════════════════════╗
║  NỘP PHIẾU 08 — MỐC JS THUẦN: DASHBOARD CÓ FORM THÊM         ║
╠══════════════════════════════════════════════════════════════╣
║  1. GitHub repo RIÊNG: cse391-cm-08                          ║
║  2. Video OBS + camera mặt — kịch bản:                       ║
║     - Chọn Chuot → F5 → vẫn Chuot (Application tab mở kèm)   ║
║     - Form đăng ký: 2 lỗi exact rồi đăng ký hợp lệ           ║
║     - FORM THÊM: submit rỗng/giá 0/KB-01 → 3 lỗi exact       ║
║     - Thêm MN-03 → đọc to 3 số: 9 / 3 / 50380000             ║
║  3. Thuyết trình 30–60s: vì sao sau khi push phải tự gọi     ║
║     render()? (gợi mở sang React)                            ║
╚══════════════════════════════════════════════════════════════╝
```

> **Mốc quan trọng:** Dashboard JS thuần **phục vụ CRUD có Form Thêm** đã xong. P09–P12 chuyển sang React để hoàn chỉnh ứng dụng CRUD (thêm Update/Delete + 2 thực thể).
