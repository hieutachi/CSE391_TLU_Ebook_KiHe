# Phiếu 08 — Module, localStorage & Dashboard có Form Thêm ★ mốc JS thuần

| | |
|---|---|
| **Buổi** | 8 |
| **Thời lượng** | ≥ 45 phút (trên lớp ~20' + về nhà ~25–40') |
| **Repo** | `cse391-cm-08` |
| **Nhận từ Phiếu 07** | `render(list)` + filter 8/3/3/2 + sort MS-03 + visible count |
| **Mang sang Phiếu 09** | **Dashboard JS thuần có Form Thêm** — nền React CRUD |
| **Dataset** | [`CANONICAL-DATA.md`](../worksheets/CANONICAL-DATA.md) — §8.4 thông báo, §8.5 keys, §8.7 ca MN-03 |

### Chuẩn đầu ra (CLO)

1. Tách **3 module** `data.js` / `helpers.js` / `app.js` với `export`/`import`; một entry `<script type="module">`.  
2. Filter ghi nhớ qua **F5** bằng key `cm_filter` (Application tab kiểm được).  
3. Validate form đăng ký: **2 quy tắc**, chuỗi lỗi exact; hợp lệ → lưu `cm_subscribers`.  
4. **Form Thêm** (`data-testid="cm-product-form"`): validate **3 quy tắc** → `products.push` → `render` + `renderStats`. Ca **MN-03** → **9 card / Man hinh 3 / 50380000**.  
5. Giữ toàn bộ P07 sau khi tách module; hiểu **push vs immutable** (React P11 dùng spread).  
6. *(Điểm cộng)* Login giả `admin` / `CampusMart@01` → `cm_auth`.

---

## 1. Tóm tắt lý thuyết (đọc lại trước khi code)

- **ES Module:** `export` / `import { x } from "./file.js"` — đường dẫn có `./` và đuôi `.js`.
- HTML chỉ **một** script entry: `<script type="module" src="app.js"></script>`.
- Chạy qua **Live Server** — `file://` chặn CORS module.
- **localStorage:** chỉ lưu chuỗi → `JSON.stringify` / `JSON.parse`; phòng `null` bằng `?? "[]"` hoặc `?? "all"`.
- Form: `submit` → **`e.preventDefault()`** → validate → lỗi exact §8.4 → xử lý → `reset()`.
- **Dashboard** = stats + filter + list + **Form Thêm**; sau Create phải gọi lại `render()` + `renderStats()`.
- **JS thuần Create:** `products.push(item)` — **mutate** mảng. React (P11): `[...products, item]` — **immutable**. Cùng ca MN-03, hai cách render lại khác nhau.

Chi tiết: [Buổi 8](../08-buoi-08-module-localstorage-validate.md).

---

## 2. Bài trên lớp (~20 phút) — Tách module + nhớ filter qua F5

### Yêu cầu

Copy repo Phiếu 07 sang `cse391-cm-08`, rồi:

1. Đổi `<title>` exact: **`CampusMart — Module & Storage (Buoi 8)`**.
2. Tách code:
   - **`data.js`:** `export const categories = …` · `export const products = …`
   - **`helpers.js`:** chuyển 5 hàm P06 + `export` từng hàm (`lineTotal`, `inventoryValue`, `stockLevel`, `findProductBySku`, `countByCategory`)
   - **`app.js`:** `import` từ 2 file; giữ render/filter/sort/delegation; bọc stats P06 thành **`renderStats()`** gọi lại được
3. HTML — xóa mọi `<script src="data.js">` cũ; chỉ còn:

```html
<script type="module" src="app.js"></script>
```

4. **Nhớ filter:**

```js
// Khi đổi filter
select.addEventListener("change", () => {
  localStorage.setItem("cm_filter", select.value);
  applyFilter(select.value);
});

// Khi load trang
const saved = localStorage.getItem("cm_filter") ?? "all";
select.value = saved;
applyFilter(saved);
```

(`applyFilter` cập nhật `currentList` + gọi `render`.)

### Cách thử trên lớp (DevTools)

| # | Thử | Thao tác | Kỳ vọng |
|---|-----|----------|---------|
| T1 | Load module | Live Server → reload | Console **không** `Cannot use import` |
| T2 | Filter | Chọn **Chuot** | **3** card |
| T3 | F5 | Reload trang | Select vẫn **Chuot**, vẫn **3** card |
| T4 | Application | F12 → **Application** → Local Storage | Key **`cm_filter`** = `"2"` |
| T5 | Files | Tab Sources | Thấy `data.js`, `helpers.js`, `app.js` load |

### Output kỳ vọng (trên lớp)

Chọn Chuot → F5 → select vẫn Chuot, lưới vẫn 3 card MS-01/02/03.

**Checkpoint giảng viên:** 3 file module + Application tab có `cm_filter`; SV giải thích vì sao cần Live Server.

---

## 3. Bài về nhà (~25–40 phút) — Validate + Form Thêm + subscriber list

Tiếp tục **cùng repo** `cse391-cm-08`.

### Nhiệm vụ A — Validate form đăng ký (form P02)

Thêm `<p id="form-msg" aria-live="polite"></p>` dưới `[data-testid="cm-subscribe-form"]`.

Handler `submit`:

| Quy tắc | Chuỗi lỗi exact |
|---------|------------------|
| `name.trim().length < 2` | `Ten toi thieu 2 ky tu` |
| email không khớp `/^\S+@\S+\.\S+$/` | `Email khong hop le` |

- Có lỗi → nối bằng `. ` → `#form-msg`, class `cm-error`, **không** lưu.
- Hợp lệ → push `{name, email, category_id}` vào array `cm_subscribers` → `Dang ky thanh cong` (class `cm-success`) → `form.reset()`.

#### Cách thử chi tiết — Nhiệm vụ A

1. Reload → Application → Local Storage → xóa key `cm_subscribers` (nếu có) để test sạch.
2. Submit **rỗng** hoặc tên `"A"` + email `"bad"`:

```
Ten toi thieu 2 ky tu. Email khong hop le
```

(Thứ tự 2 lỗi có thể gộp một chuỗi — autograder so exact từng msg.)

3. Submit hợp lệ: `An` / `an@tlu.edu.vn` / chọn danh mục bất kỳ:

```
Dang ky thanh cong
```

4. Application tab → **`cm_subscribers`** → Value là JSON array có 1 phần tử.
5. Console:

```js
JSON.parse(localStorage.getItem("cm_subscribers"))
// [{ name: "An", email: "an@tlu.edu.vn", category_id: ... }]
```

6. F5 → đăng ký thêm 1 người → array **2 phần tử** (persist qua reload).

---

### Nhiệm vụ B — Form Thêm sản phẩm ★ trọng tâm phiếu

```html
<section id="add-product">
  <h2>Them san pham</h2>
  <form data-testid="cm-product-form">
    <label>SKU <input name="sku" required></label>
    <label>Ten <input name="name" required></label>
    <label>Danh muc
      <select name="category_id" required>
        <option value="">-- Chon danh muc --</option>
        <option value="1">Ban phim</option>
        <option value="2">Chuot</option>
        <option value="3">Man hinh</option>
      </select>
    </label>
    <label>Gia <input name="price" type="number" required></label>
    <label>So luong <input name="qty" type="number" value="0"></label>
    <button type="submit">Them san pham</button>
  </form>
  <p id="product-form-msg" aria-live="polite"></p>
</section>
```

Validate — chuỗi exact §8.4:

| # | Điều kiện | Chuỗi lỗi |
|---|-----------|-----------|
| 1 | sku/name/category_id rỗng | `Thieu thong tin bat buoc` |
| 2 | `Number(price) <= 0` | `Gia phai lon hon 0` |
| 3 | SKU trùng | `SKU da ton tai` |

Hợp lệ:

```js
products.push(item);   // JS thuần — mutate mảng import
render(products);
renderStats();
addForm.reset();
```

> **Ghi chú push vs immutable:** Ở P11 React bạn sẽ viết `setProducts([...products, item])` — không `push` trực tiếp state. Cùng ca MN-03, hai paradigm; video P08 giải thích vì sao sau `push` phải **tự** gọi `render()`.

#### Cách thử chi tiết — Nhiệm vụ B (3 lỗi)

1. Submit form **trống** → `#product-form-msg`:

```
Thieu thong tin bat buoc
```

2. Điền sku+name+category, **price = 0** →:

```
Gia phai lon hon 0
```

3. Nhập **KB-01** (SKU có sẵn) + field còn lại hợp lệ →:

```
SKU da ton tai
```

4. Sau mỗi lỗi: vẫn **8** card (`document.querySelectorAll(".cm-card").length`).

#### Cách thử chi tiết — Ca kiểm MN-03 (§8.7)

Nhập exact:

| Field | Value |
|-------|-------|
| sku | `MN-03` |
| name | `AOC 27 inch` |
| category_id | `3` (Man hinh) |
| price | `4500000` |
| qty | `2` |

Sau submit thành công:

1. **9 card** @ Tat ca:

```js
document.querySelectorAll(".cm-card").length
// 9
```

2. `#stats` / `renderStats` output chứa:

```
Tong gia tri kho = 50380000
```

(41380000 + 4500000×2 = **50380000**)

3. Filter **Man hinh** → **3** card, SKU gồm MN-01, MN-02, **MN-03**:

```js
[...document.querySelectorAll(".cm-card")].map(c => c.dataset.sku).sort().join(",")
// "MN-01,MN-02,MN-03"
```

4. Visible count: `Hien thi: 3 san pham`.

5. **F5** → về **8** card (dữ liệu SP mới chỉ trong RAM — **chấp nhận** ở mốc JS thuần này; filter `cm_filter` vẫn persist).

6. Video: đọc to **3 số** — **9 / 3 / 50380000**.

---

### Nhiệm vụ C — Danh sách subscriber

```html
<ul data-testid="cm-subscriber-list"></ul>
```

Hàm `renderSubscribers()` — đọc `cm_subscribers`, mỗi `li` = `name — email` bằng `textContent`. Gọi khi load + sau đăng ký thành công.

#### Cách thử chi tiết — Nhiệm vụ C

1. Xóa `cm_subscribers` → reload → list **rỗng**.
2. Đăng ký `Binh` / `binh@tlu.edu.vn` → 1 `<li>`.
3. F5 → vẫn 1 `<li>` (đọc từ localStorage).
4. Elements → không có HTML thô `<script>` inject — mọi `li` do JS tạo.

---

### Nhiệm vụ D — (Điểm cộng) Login giả

Form `data-testid="cm-login-form"`: username / password.

| Input | Kết quả |
|-------|---------|
| `admin` / `CampusMart@01` | `cm_auth = "true"` · msg **`Dang nhap thanh cong`** |
| Sai | **`Sai tai khoan hoac mat khau`** |

Ghi README: đây là UI demo — mật khẩu nằm trong JS, **không** bảo mật thật (CSE485).

#### Cách thử chi tiết — Nhiệm vụ D (nếu làm)

1. Login đúng → Application → `cm_auth` = `"true"`.
2. F5 → vẫn `"true"` (hoặc theo logic bạn chọn).
3. Logout (nếu có) → `removeItem("cm_auth")`.

---

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
| `files` | `data.js`, `helpers.js`, `app.js` export/import |
| `filter.persist` | chọn DM → F5 → giữ select + card |
| `localStorage.cm_filter` | có khi đổi filter |
| `validate.name` / `.email` | `Ten toi thieu 2 ky tu` / `Email khong hop le` |
| `validate.ok` | `Dang ky thanh cong` + append `cm_subscribers` |
| `testid.cm-product-form` | 5 field + nút `Them san pham` |
| `create.empty` / `.price` / `.dup` | 3 chuỗi lỗi exact §8.4 |
| `create.MN-03` | **9** card · Man hinh **3** · `Tong gia tri kho = 50380000` |
| `create.immutable_note` | push + manual render (≠ React spread P11) |
| Giữ từ P07 | render 8 card ban đầu, filter 8/3/3/2, sort MS-03 |
| Điểm cộng | login `cm_auth` + 2 msg exact |
| `CM_EXPECT` | như Nhiệm vụ E |

### Checklist tự chấm trước nộp

```
[ ] title exact P08
[ ] 3 module + type=module + Live Server
[ ] filter F5 + cm_filter trong Application
[ ] subscribe: 2 lỗi + thành công + cm_subscribers
[ ] form thêm: 3 lỗi exact
[ ] MN-03 → 9 / Man hinh 3 / 50380000
[ ] renderStats() sau push
[ ] subscriber list (Nhiệm vụ C)
[ ] CM_EXPECT comment
[ ] Video đủ kịch bản box nộp
```

---

## 5. Box nộp bài

```
╔══════════════════════════════════════════════════════════════╗
║  NỘP PHIẾU 08 — MỐC JS THUẦN: DASHBOARD CÓ FORM THÊM         ║
╠══════════════════════════════════════════════════════════════╣
║  1. GitHub repo RIÊNG: cse391-cm-08                          ║
║  2. Video OBS + camera mặt — kịch bản:                       ║
║     - Chọn Chuot → F5 → vẫn Chuot (mở Application tab kèm)   ║
║     - Form đăng ký: 2 lỗi exact rồi đăng ký hợp lệ           ║
║     - FORM THÊM: submit rỗng / giá 0 / KB-01 → 3 lỗi exact   ║
║     - Thêm MN-03 → đọc to 3 số: 9 / 3 / 50380000             ║
║  3. Thuyết trình 30–60s: vì sao push xong phải gọi render()? ║
║     (gợi mở sang React immutable state P11)                  ║
╚══════════════════════════════════════════════════════════════╝
```

> **Mốc quan trọng:** Dashboard JS thuần **phục vụ CRUD có Form Thêm** đã xong. P09–P12 chuyển sang React để hoàn chỉnh Update/Delete + 2 thực thể.

---

## 6. Lỗi thường gặp khi nộp (tránh)

| Lỗi | Triệu chứng | Cách xử lý |
|-----|-------------|------------|
| `Cannot use import` | Module không load | `type="module"` + Live Server |
| CORS / file:// | Trang trắng | Không mở trực tiếp HTML |
| F5 mất filter | Quên đọc `cm_filter` lúc load | Set select + applyFilter(saved) |
| Stats không đổi sau Create | Stats ghi 1 lần P06 | Bọc `renderStats()` gọi sau push |
| `JSON.parse` nổ | Key chưa tồn tại | `?? "[]"` trước parse |
| Form reload trang | Quên `preventDefault` | Dòng đầu handler |
| Import mutate không render | Quên `render(products)` | Gọi cả render + renderStats |
| MN-03 sai tổng | price/qty nhầm | 4500000 × 2 = 9000000 cộng vào 41380000 |

---

## 7. Hướng dẫn Application tab (localStorage)

1. F12 → tab **Application** (Chrome) / **Storage** (Firefox).
2. Sidebar → **Local Storage** → chọn origin Live Server (`http://127.0.0.1:5500` …).
3. Sau filter Chuot: thấy **`cm_filter`** = `"2"`.
4. Sau đăng ký: **`cm_subscribers`** = chuỗi JSON `[{...}]`.
5. *(Bonus login)* **`cm_auth`** = `"true"`.
6. Chuột phải key → Delete → reload để reset test.

| Key | Khi nào có | Giá trị mẫu |
|-----|------------|-------------|
| `cm_filter` | Sau đổi select | `"all"` · `"1"` · `"2"` · `"3"` |
| `cm_subscribers` | Sau đăng ký OK | `'[{"name":"An",...}]'` |
| `cm_auth` | Login bonus | `"true"` |

---

*CSE391 – Phiếu 08 | CampusMart UI — Module & Storage ★ mốc JS thuần*
