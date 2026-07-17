# BUỔI 8: Module JS, localStorage, Form validate phía client

> **Thời lượng gợi ý:** 4 giờ tự học  
> **Tiên quyết:** Buổi 7 (render + filter hoạt động)  
> **Kết quả đầu ra:** Tách code thành ES Module, lưu/đọc localStorage, validate form bằng JS với thông báo lỗi chuẩn  
> **Phiếu học tập liên quan:** [Phiếu 08](./worksheets/phieu-08-module-localstorage.md) — chuỗi **CampusMart UI**

---

## 1. Mục tiêu buổi học

Sau Buổi 8, bạn có thể:

1. Dùng `export` / `import` với `<script type="module">`.
2. Lưu, đọc, xóa dữ liệu localStorage (`JSON.stringify` / `JSON.parse`).
3. Chặn submit mặc định (`preventDefault`), validate từng field, hiện lỗi cạnh input.
4. Ghi nhớ trạng thái filter qua các lần F5.
5. Làm login UI giả (localStorage) với credential canonical.

---

## 2. Vì sao cần Module + Storage?

Hai cơn đau sau Buổi 7:

1. **`app.js` phình to** — render, filter, sort, helpers trộn một file; biến toàn cục dẫm nhau.
2. **F5 là mất hết** — filter đang chọn, form vừa nhập… bay sạch.

ES Module giải quyết (1): mỗi file một nhiệm vụ, chỉ "xuất" thứ cần chia sẻ. localStorage giải quyết (2): kho key-value nhỏ nằm ngay trong trình duyệt.

```
   BEFORE                        AFTER
┌────────────┐          ┌────────────┐  export
│  app.js    │          │  data.js   │────────┐
│ (mọi thứ,  │          ├────────────┤        │ import
│  toàn cục) │   ───►   │ helpers.js │────────┤
└────────────┘          ├────────────┤        ▼
                        │  app.js    │  (điểm vào duy nhất
                        └────────────┘   trong <script>)
```

---

## 3. ES Module

### 3.1. Cú pháp

```js
// data.js
export const categories = [ /* ... */ ];
export const products = [ /* ... */ ];
```

```js
// helpers.js
import { products } from "./data.js"; // nếu cần

export function stockLevel(qty) { /* ... */ }
export function inventoryValue(list) { /* ... */ }
```

```js
// app.js — điểm vào
import { categories, products } from "./data.js";
import { stockLevel, inventoryValue } from "./helpers.js";
```

```html
<!-- CHỈ nhúng điểm vào, kèm type="module" -->
<script type="module" src="app.js"></script>
```

### 3.2. Ba điều phải nhớ

| Điều | Chi tiết |
|------|----------|
| Bắt buộc chạy qua server | `file://` chặn module → dùng **Live Server** |
| Đường dẫn có `./` và `.js` | `import ... from "./data.js"` — không được thiếu |
| Biến module không rơi ra `window` | Console không gọi trực tiếp được — muốn debug tạm: `window.debugProducts = products` |

---

## 4. localStorage — bộ nhớ sống qua F5

```js
// Ghi: chỉ nhận CHUỖI → object phải stringify
localStorage.setItem("cm_filter", "2");
localStorage.setItem("cm_subscribers", JSON.stringify([{ name: "An", email: "an@tlu.edu.vn", category_id: 1 }]));

// Đọc: parse ngược lại, nhớ phòng null lần đầu
const filter = localStorage.getItem("cm_filter") ?? "all";
const subs = JSON.parse(localStorage.getItem("cm_subscribers") ?? "[]");

// Xóa
localStorage.removeItem("cm_auth");
```

```
┌───────────────────────────────────────────────┐
│  localStorage (theo origin, sống qua F5)      │
│                                               │
│  "cm_filter"      → "2"                       │
│  "cm_subscribers" → '[{"name":"An",...}]'     │
│  "cm_auth"        → "true"                    │
└───────────────────────────────────────────────┘
   DevTools → Application → Local Storage: xem/xóa tay
```

Ba key của môn (khóa trong [CANONICAL §8.5](./worksheets/CANONICAL-DATA.md)): `cm_filter`, `cm_subscribers`, `cm_auth`.

### Pattern "nhớ filter"

```js
// Khi đổi filter
select.addEventListener("change", () => {
  localStorage.setItem("cm_filter", select.value);
  applyFilter(select.value);
});

// Khi tải trang
const saved = localStorage.getItem("cm_filter") ?? "all";
select.value = saved;
applyFilter(saved);
```

> **Giới hạn cần biết:** localStorage là chuỗi, ~5MB, theo trình duyệt của máy — **không phải database**. CRUD thật cần backend (CSE485); môn này dùng nó làm "DB giả" đủ cho UI.

---

## 5. Form validate phía client

`required` của HTML là tầng 0. Tầng 1 là JS: quy tắc riêng + thông báo tiếng Việt + không rời trang.

### 5.1. Luồng chuẩn

```
 submit ──► e.preventDefault()  (chặn reload/gửi đi)
        ──► kiểm tra từng field
        │      sai  → hiện lỗi cạnh input (chuỗi exact §8.4)
        │      đúng → xóa lỗi
        ──► tất cả đúng → xử lý (lưu localStorage) → "Dang ky thanh cong"
```

### 5.2. Code khung

```js
const form = document.querySelector('[data-testid="cm-subscribe-form"]');

form.addEventListener("submit", (e) => {
  e.preventDefault(); // giành quyền xử lý từ trình duyệt

  const name = form.elements.name.value.trim();
  const email = form.elements.email.value.trim();

  const errors = [];
  if (name.length < 2) errors.push("Ten toi thieu 2 ky tu");
  // Regex email tối thiểu đủ dùng cho môn học:
  if (!/^\S+@\S+\.\S+$/.test(email)) errors.push("Email khong hop le");

  const msgEl = document.querySelector("#form-msg");
  if (errors.length > 0) {
    msgEl.textContent = errors.join(". ");
    msgEl.className = "cm-error";
    return;
  }

  // Hợp lệ → lưu subscriber
  const subs = JSON.parse(localStorage.getItem("cm_subscribers") ?? "[]");
  subs.push({ name, email, category_id: Number(form.elements.category_id.value) });
  localStorage.setItem("cm_subscribers", JSON.stringify(subs));

  msgEl.textContent = "Dang ky thanh cong";
  msgEl.className = "cm-success";
  form.reset();
});
```

Chuỗi lỗi/thành công **exact** theo CANONICAL §8.4 — Autograder so sánh nguyên văn.

---

## 6. Login UI giả (chuẩn bị pattern auth)

Credential canonical (§6): `admin` / `CampusMart@01`.

```js
loginForm.addEventListener("submit", (e) => {
  e.preventDefault();
  const u = loginForm.elements.username.value;
  const p = loginForm.elements.password.value;

  if (u === "admin" && p === "CampusMart@01") {
    localStorage.setItem("cm_auth", "true");
    msg.textContent = "Dang nhap thanh cong";
  } else {
    msg.textContent = "Sai tai khoan hoac mat khau";
  }
});
```

> Nói thẳng cho đúng nghề: đây là **UI demo**. Mật khẩu nằm trong JS là ai cũng đọc được — auth thật phải ở server (bạn sẽ làm ở CSE485). Mục tiêu ở đây là luyện luồng form → kiểm tra → đổi trạng thái UI.

---

## 7. Bài thực hành minh họa trong buổi (không thay phiếu)

Console trên trang bất kỳ:

```js
localStorage.setItem("demo", JSON.stringify({ a: 1 }));
JSON.parse(localStorage.getItem("demo"));      // {a: 1}
// F5 trang → chạy lại dòng dưới → vẫn còn!
JSON.parse(localStorage.getItem("demo"));      // {a: 1}
localStorage.removeItem("demo");
```

Mở DevTools → **Application** → Local Storage: nhìn thấy key `demo` xuất hiện rồi biến mất.

---

## 8. Lỗi thường gặp

| Triệu chứng | Nguyên nhân | Cách xử lý |
|-------------|-------------|------------|
| `Cannot use import statement…` | Thiếu `type="module"` | Sửa thẻ script |
| CORS / module không load | Mở bằng `file://` | Dùng Live Server |
| `JSON.parse` nổ lần đầu | Key chưa tồn tại → `null` | `?? "[]"` trước khi parse |
| Form vẫn reload trang | Quên `preventDefault` | Dòng đầu handler |
| Lỗi hiện xong không mất | Không xóa msg khi hợp lệ | Reset textContent/class mỗi lần submit |
| Import không tìm thấy file | Thiếu `./` hoặc `.js` | `"./helpers.js"` đầy đủ |

---

## 9. Checklist trước khi sang buổi sau

- [ ] 3 file module: `data.js` / `helpers.js` / `app.js`, một script entry
- [ ] Filter sống sót qua F5 (cm_filter)
- [ ] Form validate 2 quy tắc, chuỗi lỗi exact
- [ ] Subscriber lưu vào `cm_subscribers` (xem được trong Application tab)
- [ ] Login giả đúng/sai ra đúng 2 thông báo
- [ ] Đã làm xong [Phiếu 08](./worksheets/phieu-08-module-localstorage.md)

---

## 10. Bài tập trên lớp & về nhà (chuỗi CampusMart UI)

| Phần | Làm gì | Chi tiết |
|------|--------|----------|
| **Trên lớp (~20')** | Tách module + nhớ filter qua F5 | [Phiếu 08 — mục 2](./worksheets/phieu-08-module-localstorage.md) |
| **Về nhà (~25–40')** | Validate form + lưu subscriber + login giả | [Phiếu 08 — mục 3](./worksheets/phieu-08-module-localstorage.md) |

Nộp repo riêng `cse391-cm-08` + video OBS theo box cuối phiếu.

---

## 11. Cầu nối sang Buổi 9

Bạn vừa tự tay làm những gì React sinh ra để tự động hóa: render từ dữ liệu, cập nhật khi trạng thái đổi, tách code thành mảnh. Buổi 9 bước vào **React**: Vite, JSX, component, props — và bạn sẽ nhận ra mọi khái niệm đều đã quen.

→ Tiếp: [Buổi 9](./09-buoi-09-react-khoi-dau.md) · [Phiếu 09](./worksheets/phieu-09-react-props.md)

---

*CSE391 – Ebook Frontend CampusMart UI | Buổi 8/12*
