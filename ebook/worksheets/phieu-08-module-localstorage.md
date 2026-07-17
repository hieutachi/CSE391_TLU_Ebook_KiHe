# Phiếu 08 — Module, localStorage & Validate CampusMart

| | |
|---|---|
| **Buổi** | 8 |
| **Thời lượng** | ≥ 45 phút (trên lớp ~20' + về nhà ~25–40') |
| **Repo** | `cse391-cm-08` |
| **Nhận từ Phiếu 07** | `render(list)` + filter + sort |
| **Mang sang Phiếu 09** | Tư duy module + validate — chuyển sang React |
| **Dataset** | [`CANONICAL-DATA.md`](./CANONICAL-DATA.md) — §8.4 thông báo, §8.5 keys, §6 credential |

### Chuẩn đầu ra (CLO)

1. Tách 3 module `data.js` / `helpers.js` / `app.js` với `export`/`import`; một entry `type="module"`.  
2. Filter được ghi nhớ qua F5 bằng key `cm_filter`.  
3. Validate form đăng ký: 2 quy tắc, chuỗi lỗi exact; hợp lệ → lưu `cm_subscribers`.  
4. Login giả với `admin` / `CampusMart@01` → `cm_auth="true"` + thông báo exact.  
5. Toàn bộ chức năng Phiếu 07 (render/filter/sort) vẫn chạy sau khi tách module.

---

## 1. Tóm tắt lý thuyết (đọc lại trước khi code)

- `export const/function` → `import { x } from "./file.js"`; chạy qua Live Server.
- localStorage: chuỗi only → `JSON.stringify`/`parse`; phòng `null` bằng `?? "[]"`.
- `submit` + `preventDefault` → kiểm tra → lỗi exact §8.4 → lưu → `form.reset()`.

Chi tiết: [Buổi 8](../08-buoi-08-module-localstorage-validate.md).

---

## 2. Bài trên lớp (~20 phút) — Tách module + nhớ filter

### Yêu cầu

Copy từ repo Phiếu 07 sang `cse391-cm-08`, rồi:

1. Đổi `<title>` exact: **`CampusMart — Module & Storage (Buoi 8)`**.
2. Tách code:
   - `data.js`: thêm `export` trước `categories`, `products`.
   - `helpers.js`: chuyển 5 hàm Phiếu 06 vào + `export` từng hàm.
   - `app.js`: `import` từ 2 file trên; giữ render/filter/sort.
3. HTML chỉ còn: `<script type="module" src="app.js"></script>`.
4. Nhớ filter:
   - `change` → `localStorage.setItem("cm_filter", value)` rồi render.
   - Khi load → đọc `cm_filter` (mặc định `"all"`), set lại select, render đúng danh sách.

### Output kỳ vọng (trên lớp)

Chọn `Chuot` → F5 → select vẫn `Chuot`, lưới vẫn 3 card.

**Checkpoint giảng viên:** 3 file module; Application tab có `cm_filter`; F5 giữ trạng thái.

---

## 3. Bài về nhà (~25–40 phút) — Validate + subscriber + login giả

Tiếp tục **cùng repo**.

### Nhiệm vụ A — Validate form đăng ký (form Phiếu 02)

Thêm `<p id="form-msg" aria-live="polite"></p>` dưới form. Handler `submit`:

| Quy tắc | Chuỗi lỗi exact |
|---------|------------------|
| `name.trim().length < 2` | `Ten toi thieu 2 ky tu` |
| email không khớp `/^\S+@\S+\.\S+$/` | `Email khong hop le` |

- Có lỗi → ghi các lỗi (nối bằng `. `) vào `#form-msg`, class `cm-error`, **không** lưu.
- Hợp lệ → push `{name, email, category_id}` vào `cm_subscribers` (JSON array), ghi `Dang ky thanh cong` (class `cm-success`), `form.reset()`.

### Nhiệm vụ B — Danh sách subscriber sống

Dưới form, thêm `<ul data-testid="cm-subscriber-list"></ul>`. Mỗi lần load trang + mỗi lần đăng ký thành công → render lại danh sách từ localStorage (mỗi `li`: `name — email`). Dùng `textContent` (dữ liệu người dùng!).

### Nhiệm vụ C — Login giả

Section mới `#login` với form `data-testid="cm-login-form"`: username, password, nút `Dang nhap`, `<p id="login-msg">`.

| Input | Kết quả |
|-------|---------|
| `admin` / `CampusMart@01` | `cm_auth="true"` + msg `Dang nhap thanh cong` + hiện thêm nút `Dang xuat` |
| khác | msg `Sai tai khoan hoac mat khau`, không set key |

`Dang xuat`: xóa `cm_auth`, ẩn nút, xóa msg. Khi load trang, nếu `cm_auth==="true"` → hiện trạng thái đã đăng nhập.

### Nhiệm vụ D — Marker

```html
<!-- CM_EXPECT product_count=8 storage_keys=cm_filter,cm_subscribers,cm_auth modules=data.js,helpers.js,app.js -->
```

---

## 4. OUTPUT / EXPECT

| Key | Value bắt buộc |
|-----|----------------|
| `page_title` | `CampusMart — Module & Storage (Buoi 8)` |
| `script` | duy nhất 1 entry `type="module"` |
| `files` | `data.js`, `helpers.js`, `app.js` có export/import |
| `filter.persist` | chọn DM → F5 → giữ nguyên select + card |
| `validate.name` | `Ten toi thieu 2 ky tu` (exact) |
| `validate.email` | `Email khong hop le` (exact) |
| `validate.ok` | `Dang ky thanh cong` + item mới trong `cm_subscribers` |
| `login.ok` | admin/CampusMart@01 → `cm_auth="true"` + `Dang nhap thanh cong` |
| `login.fail` | `Sai tai khoan hoac mat khau`, không set key |
| Giữ từ P07 | render 8 card, filter 8/3/3/2, sort MS-03 đầu |
| `CM_EXPECT` comment | như Nhiệm vụ D |

---

## 5. Box nộp bài

```
╔══════════════════════════════════════════════════════════════╗
║  NỘP PHIẾU 08                                                ║
╠══════════════════════════════════════════════════════════════╣
║  1. GitHub repo RIÊNG: cse391-cm-08                          ║
║  2. Video OBS + camera mặt — kịch bản:                       ║
║     - Chọn Chuot → F5 → vẫn Chuot (Application tab mở kèm)   ║
║     - Submit tên 1 ký tự + email sai → đọc to 2 lỗi          ║
║     - Đăng ký hợp lệ → item vào list + localStorage          ║
║     - Login sai rồi đúng → 2 thông báo + cm_auth             ║
║  3. Thuyết trình 30–60s: vì sao localStorage không phải DB?  ║
╚══════════════════════════════════════════════════════════════╝
```

> **Mốc quan trọng:** Kết thúc giai đoạn JS thuần. Phiếu 09 chuyển sang React — mang theo toàn bộ tư duy render/validate của P06–P08.
