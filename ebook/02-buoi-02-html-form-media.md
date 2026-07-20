# BUỔI 2: HTML Form & Media — a11y cơ bản

> **Thời lượng gợi ý:** 3–4 giờ tự học (lý thuyết + thử máy + Phiếu 02)  
> **Tiên quyết:** Hoàn thành Buổi 1 + [Phiếu 01](./worksheets/phieu-01-html-ngu-nghia.md) (skeleton CampusMart, bảng 8 SP)  
> **Kết quả đầu ra:** Viết được form có label đúng chuẩn; nhúng ảnh/media có ngữ nghĩa; hiểu a11y cơ bản (label, alt, landmark, focus)  
> **Phiếu học tập:** [Phiếu 02](./worksheets/phieu-02-form-media.md) — chuỗi **CampusMart UI**  
> **Dataset:** [CANONICAL-DATA §8.2–8.4](./worksheets/CANONICAL-DATA.md) — title P02, 3 DM, chuỗi thông báo form, `data-testid`

---

## 0. Gợi ý lịch trình giảng (cho GV)

| Phút | Nội dung | Gợi ý thao tác |
|------|----------|----------------|
| 0–10 | Ôn Buổi 1 + hỏi “CampusMart thiếu gì để khách đăng ký nhận tin?” | Chiếu trang P01 → hỏi SV: “Bấm ở đâu để gửi email?” |
| 10–25 | Form = cửa dữ liệu; giải phẫu ASCII; `label`/`for`/`name` | Live-code 1 field; demo click label |
| 25–40 | `type`, `required`, `fieldset`; select 3 DM + `value=""` | Giải thích vì sao placeholder không được submit |
| 40–55 | `figure`/`figcaption`/`alt`; 4 quy tắc a11y | Chiếu ảnh SP KB-01 mẫu |
| 55–70 | **Thử ngay:** submit rỗng, email sai, Tab focus | SV dự đoán trước → chạy → DevTools |
| 70–100 | **Thực hành Phiếu 02 mục 2** | SV copy P01 → `cse391-cm-02`, thêm form |
| 100–120 | Checkpoint + giao về nhà Phiếu 02 mục 3 | Nhắc Lighthouse ≥ 90, 8 figure |

> Trên lớp **ưu tiên xong form 3 field + select 4 option**. Ảnh 8 SP và Lighthouse có thể hoàn thiện về nhà.

---

## 1. Mục tiêu buổi học

Sau Buổi 2, bạn có thể:

1. Giải thích form là **cửa dữ liệu** giữa người dùng và ứng dụng (Frontend ↔ Backend).
2. Dựng form với `form`, `label`, `input`, `select`, `textarea`, `button` đúng ngữ nghĩa.
3. Chọn đúng `type` (`text`, `email`, `number`…); dùng `required`, `minlength`, `name` hợp lý.
4. Nhóm field bằng `fieldset`/`legend`; hiểu vì sao option placeholder cần `value=""`.
5. Nhúng ảnh trong `figure` + `figcaption`; viết `alt` có nghĩa cho 8 sản phẩm CampusMart.
6. Nêu và áp dụng **4 quy tắc a11y tối thiểu**; chạy Lighthouse Accessibility ≥ 90.
7. Bổ sung form “Đăng ký nhận tin” vào trang CampusMart **không phá** EXPECT Phiếu 01.

---

## 2. Nhắc lại điểm đứng trước Buổi 2

Ở Buổi 1, CampusMart đã có:

```
header + nav + main
  ├── section#categories   → 3 DM: Ban phim, Chuot, Man hinh
  └── section#products     → bảng 8 SP (CORE_8, tổng 41380000)
footer
```

**Vấn đề sư phạm (tự hỏi):**

- Khách muốn “nhận tin khi có hàng mới” — **bấm ở đâu?**
- Bảng có tên SP nhưng **chưa có ảnh** — mắt người khó hình dung Keychron K2.
- Trang “đọc được” bằng mắt nhưng **screen reader** có hiểu đúng vùng form không?

```
Buổi 1 (xương + catalog)     Buổi 2 (form + media + a11y)
─────────────────────────     ───────────────────────────────
Chỉ xem, không nhập      →   Form đăng ký nhận tin
Chữ + bảng               →   Ảnh SP trong figure
Landmark cơ bản          →   Label, alt, focus, Lighthouse
```

**Buổi 2 = mở cửa dữ liệu vào CampusMart — vẫn HTML tĩnh, chưa CSS (Buổi 3).**

---

## 3. Form là cửa dữ liệu — hiểu bằng hình ảnh

### 3.1. Hình ảnh nhớ (dán đầu vở)

```
CampusMart giống cửa hàng campus thật:

  Buổi 1 = kệ hàng + bảng giá (bạn CHỈ XEM)
  Buổi 2 = quầy "Đăng ký nhận tin" (bạn GHI TÊN + EMAIL vào phiếu)

  <form> = cái khay đựng phiếu + nút "Gửi"
  <input> = ô trống trên phiếu
  <label> = dòng chữ "Họ tên:" in sẵn — chỉ đúng chỗ cần điền
  <button submit> = hộp thư gửi phiếu đi

  Backend (CSE485) sau này = nhân viên kho MỞ HỘP THƯ, đọc phiếu ($_POST)
  Frontend Buổi 8/11 = nhân viên tại quầy KIỂM TRA phiếu trước khi bỏ vào hộp (JS validate)
```

> Form không chỉ “cho đẹp”. Nó là **hợp đồng cấu trúc** giữa giao diện và dữ liệu: tên field (`name`), kiểu (`type`), bắt buộc (`required`).

### 3.2. Giải phẫu Form (ASCII bắt buộc)

```
┌─────────────────────────────────────────────────────────────────┐
│  <form action="..." method="get|post" data-testid="...">        │
│                                                                 │
│    ┌─ <fieldset> ─────────────────────────────────────────┐     │
│    │  <legend> Dang ky nhan tin </legend>  ← tiêu đề nhóm │     │
│    │                                                       │     │
│    │   <label for="sub-name"> ──for/id──► <input id="sub-name">│
│    │        Ho ten                    name="name" type="text"  │
│    │                                    required minlength="2" │
│    │                                                       │     │
│    │   <label for="sub-email"> ────────► <input id="sub-email">│
│    │        Email                     name="email" type="email"│
│    │                                                       │     │
│    │   <label for="sub-category"> ─────► <select id="sub-category">│
│    │        Danh muc quan tam         name="category_id"       │
│    │              │                      <option value="">…    │
│    │              │                      <option value="1">…   │
│    │              └─ 3 DM CampusMart (CANONICAL)               │
│    │                                                       │     │
│    │   <button type="submit"> Dang ky </button>               │
│    └───────────────────────────────────────────────────────┘     │
│                                                                 │
│  Submit ──► (HTML thuần P02) trình duyệt validate + gửi request │
│          ──► (Buổi 8) JS chặn submit, hiện err_name / err_email │
│          ──► (Buổi 11) React controlled form                    │
└─────────────────────────────────────────────────────────────────┘
```

### 3.3. Luồng dữ liệu khi submit (HTML thuần)

```
Người dùng điền form
        │
        ▼
Trình duyệt chạy validate HTML5 (required, type=email, minlength…)
        │
        ├── FAIL → hiện tooltip lỗi, KHÔNG gửi
        │
        └── PASS → gửi theo method:
                    GET  → dữ liệu dính URL  ?name=...&email=...
                    POST → dữ liệu trong body (chuẩn form nhạy cảm)

P02 dùng action="#" method="get" — chỉ để SV thấy URL đổi khi submit thành công.
Buổi 8 sẽ dùng JS preventDefault() — form không reload trang nữa.
```

### 3.4. Chuỗi thông báo CANONICAL (nhìn trước — dùng từ Buổi 8)

| Key | Chuỗi exact | Buổi áp dụng |
|-----|-------------|--------------|
| `err_name` | `Ten toi thieu 2 ky tu` | P08 JS |
| `err_email` | `Email khong hop le` | P08 JS |
| `msg_subscribed` | `Dang ky thanh cong` | P08 JS |
| `err_required` | `Thieu thong tin bat buoc` | P08 JS |

P02 chỉ dùng **validate HTML5** của trình duyệt — chưa hiển thị các chuỗi trên.

---

## 4. `label` + `for` — liên kết bắt buộc

### 4.1. Vì sao không chỉ viết chữ cạnh input?

| Cách | Click chữ “Email” có focus input? | Screen reader đọc đúng? |
|------|-----------------------------------|-------------------------|
| `<span>Email</span><input>` | Không (trừ khi tự viết JS) | Không chắc |
| `<label for="x">Email</label><input id="x">` | **Có** | **Có** |

```html
<!-- SAI: không có label thật -->
<p>
  Email<br />
  <input type="email" name="email" />
</p>

<!-- ĐÚNG: label for = input id -->
<p>
  <label for="sub-email">Email</label>
  <input id="sub-email" name="email" type="email" required />
</p>
```

**Quy tắc vàng:** `for` trên `<label>` **phải trùng** `id` trên `<input>` hoặc `<select>`.

### 4.2. Hai cách viết label hợp lệ

```html
<!-- Cách 1: for + id (khuyến nghị — layout linh hoạt) -->
<label for="sub-name">Ho ten</label>
<input id="sub-name" name="name" type="text" required />

<!-- Cách 2: bọc input (OK khi layout đơn giản) -->
<label>
  Ho ten
  <input name="name" type="text" required />
</label>
```

Phiếu 02 yêu cầu **cách 1** (for/id rõ ràng) để máy và GV chấm nhất quán.

### 4.3. Bảng so sánh label — sai vs đúng

| Tình huống | SAI | ĐÚNG |
|------------|-----|------|
| Chỉ placeholder | `<input placeholder="Ho ten">` | `<label for="n">Ho ten</label><input id="n">` |
| for/id lệch | `for="email"` + `id="sub-email"` | Cùng cặp `sub-email` |
| Label trống | `<label for="x"></label>` | Text nhãn hiển thị được |

---

## 5. `name` — khóa dữ liệu khi submit

Mỗi field cần `name` — đây là **tên biến** server/JS sẽ đọc:

```
Submit form → cặp name=value:
  name=Nguyen Van A
  email=a@tlu.edu.vn
  category_id=1
```

| Thiếu `name` | Hậu quả |
|--------------|---------|
| Input có `id` nhưng không `name` | Submit **không mang** field đó |
| Hai input cùng `name` (checkbox) | Gửi nhiều giá trị cùng key |
| Radio cùng nhóm | **Phải** cùng `name`, khác `value` |

```html
<!-- CampusMart P02 — name khớp CANONICAL (Buổi 8 dùng lại) -->
<input id="sub-name" name="name" type="text" required minlength="2" />
<input id="sub-email" name="email" type="email" required />
<select id="sub-category" name="category_id" required>...</select>
```

> **Nhìn xa:** Buổi 8 validate `name` → nếu `< 2` ký tự hiện **`Ten toi thieu 2 ky tu`**. Buổi 2 chỉ dựa validate HTML5 (`minlength="2"`).

---

## 6. `type` và `required` — validate tầng 0

### 6.1. Bảng `type` hay dùng ở CampusMart

| type | Dùng cho (CampusMart) | Thuộc tính hay kèm | Validate tự động |
|------|----------------------|--------------------|------------------|
| `text` | Họ tên, SKU (P08+) | `minlength`, `maxlength` | Độ dài |
| `email` | Email đăng ký | `required` | Dạng `a@b` |
| `number` | Giá, số lượng (P08+) | `min`, `max`, `step` | Số |
| `password` | Login giả (P08) | `required` | — |
| `search` | Tìm SP (P10 React) | `placeholder` | — |
| `checkbox` / `radio` | Tùy chọn | cùng `name` (radio) | — |

### 6.2. `required` — chặn submit rỗng

```html
<input id="sub-email" name="email" type="email" required />
```

| Tầng | Buổi | Ai validate? | Thông báo |
|------|------|--------------|-----------|
| 0 | P02 | Trình duyệt (HTML5) | Tooltip mặc định |
| 1 | P08 | JavaScript | `err_name`, `err_email`, `msg_subscribed` |
| 2 | P11 | React + state | Cùng chuỗi CANONICAL |

### 6.3. So sánh trước / sau khi thêm validate

| Trạng thái | Submit email rỗng | Submit `abc` vào ô email |
|------------|-------------------|--------------------------|
| **Trước** (input thường) | Gửi được, dữ liệu rỗng | Gửi được chuỗi vô nghĩa |
| **Sau** (`type="email" required`) | Bị chặn | Bị chặn (sai định dạng) |

### 6.4. Các thuộc tính input hay gặp thêm

| Thuộc tính | Ví dụ | Tác dụng |
|------------|-------|----------|
| `minlength` / `maxlength` | `minlength="2"` trên tên | Giới hạn độ dài |
| `placeholder` | `placeholder="VD: a@tlu.edu.vn"` | Gợi ý — **không thay label** |
| `autocomplete` | `autocomplete="email"` | Trình duyệt gợi ý điền |
| `disabled` | `disabled` | Không submit, không focus |
| `readonly` | `readonly` | Submit được, không sửa |

---

## 7. `fieldset` + `legend` — nhóm form có tiêu đề

```html
<form data-testid="cm-subscribe-form" action="#" method="get">
  <fieldset>
    <legend>Dang ky nhan tin</legend>
    <!-- các field -->
    <p><button type="submit">Dang ky</button></p>
  </fieldset>
</form>
```

```
fieldset = khung viền logic quanh một nhóm field
legend   = tiêu đề nhóm (screen reader đọc trước khi vào field)
```

| Không fieldset | Có fieldset + legend |
|----------------|----------------------|
| Screen reader liệt kê field rời | Nghe “Dang ky nhan tin, Ho ten, …” |
| Khó phân biệt 2 form trên trang | Mỗi form một nhóm rõ |

**Phiếu 02:** `legend` exact = `Dang ky nhan tin`; `data-testid="cm-subscribe-form"`.

---

## 8. `<select>` — 3 danh mục CampusMart + placeholder

### 8.1. Markup chuẩn (CANONICAL-DATA)

```html
<label for="sub-category">Danh muc quan tam</label>
<select id="sub-category" name="category_id" required>
  <option value="">-- Chon danh muc --</option>
  <option value="1">Ban phim</option>
  <option value="2">Chuot</option>
  <option value="3">Man hinh</option>
</select>
```

| id | name (DM) | category_id |
|----|-----------|-------------|
| 1 | Ban phim | 1 |
| 2 | Chuot | 2 |
| 3 | Man hinh | 3 |

→ **4 option** (1 placeholder + 3 DM). Không tự thêm DM thứ 4 — CORE luôn là 3.

### 8.2. Vì sao placeholder phải `value=""`?

```
<option value="">-- Chon danh muc --</option>
         ▲▲
         rỗng = "chưa chọn"
```

| Thiết kế | Submit khi SV chưa chọn | `required` có tác dụng? |
|----------|-------------------------|-------------------------|
| `value=""` trên placeholder | `category_id=` (rỗng) | **Có** — coi là invalid |
| `value="0"` hoặc thiếu value | Gửi `0` hoặc text | **Không** — lách validate |
| Không có placeholder | Mặc định chọn option 1 | SV không ý thức chọn |

**Hình nhớ:** placeholder giống dòng “— Chọn danh mục —” in mờ trên phiếu — **chưa phải lựa chọn thật**, nên `value` phải rỗng.

### 8.3. So sánh sai vs đúng (select)

| | Sai | Đúng |
|---|-----|------|
| Placeholder | `<option>-- Chon --</option>` (không value) | `<option value="">-- Chon danh muc --</option>` |
| Tên DM | Tự bịa "Phụ kiện" | Ban phim / Chuot / Man hinh |
| value | `value="Ban phim"` | `value="1"` (id số, khớp Backend) |
| Số option | 3 (thiếu placeholder) | **4** |

---

## 9. `<button>` — submit vs button thường

```html
<button type="submit">Dang ky</button>   <!-- gửi form -->
<button type="button">Huy</button>       <!-- không gửi — JS sau này -->
<button type="reset">Xoa form</button>  <!-- reset field — ít dùng -->
```

| Lỗi | Hậu quả |
|-----|---------|
| `<button>` không type trong form | Mặc định `submit` — bấm nhầm gửi form |
| Dùng `<a>` giả nút submit | Không mang dữ liệu form |

P02: nút text exact **`Dang ky`**, `type="submit"`.

---

## 10. Form CampusMart hoàn chỉnh (mẫu giảng — tham khảo, tự gõ lại)

```html
<section id="subscribe" aria-labelledby="sub-heading">
  <h2 id="sub-heading">Nhan tin CampusMart</h2>

  <form data-testid="cm-subscribe-form" action="#" method="get">
    <fieldset>
      <legend>Dang ky nhan tin</legend>

      <p>
        <label for="sub-name">Ho ten</label>
        <input
          id="sub-name"
          name="name"
          type="text"
          required
          minlength="2"
          autocomplete="name"
        />
      </p>

      <p>
        <label for="sub-email">Email</label>
        <input
          id="sub-email"
          name="email"
          type="email"
          required
          autocomplete="email"
        />
      </p>

      <p>
        <label for="sub-category">Danh muc quan tam</label>
        <select id="sub-category" name="category_id" required>
          <option value="">-- Chon danh muc --</option>
          <option value="1">Ban phim</option>
          <option value="2">Chuot</option>
          <option value="3">Man hinh</option>
        </select>
      </p>

      <p>
        <button type="submit">Dang ky</button>
      </p>
    </fieldset>
  </form>
</section>
```

Đổi `<title>` trang: **`CampusMart — Form & Media (Buoi 2)`** (CANONICAL §8.2).

Thêm link `Dang ky` trong `nav` trỏ `#subscribe`.

---

## 11. Media có ngữ nghĩa — `figure`, `figcaption`, `alt`

### 11.1. Hình ảnh nhớ

```
<img> một mình     = tấm ảnh dán tường (không chú thích)
<figure>           = khung trưng bày trong cửa hàng
  <img alt="…">    = ảnh + mô tả cho người không nhìn thấy
  <figcaption>     = nhãn kệ: "KB-01" — đối chiếu SKU
```

### 11.2. Mẫu cho sản phẩm CampusMart

```html
<figure>
  <img
    src="https://placehold.co/120x80"
    alt="Ban phim co Keychron K2 mau den"
    width="120"
    height="80"
  />
  <figcaption>KB-01</figcaption>
</figure>
```

| Thuộc tính | Vì sao |
|------------|--------|
| `alt` | Mô tả **nội dung** ảnh — không phải "ảnh 1", "image" |
| `width`/`height` | Giữ chỗ trước khi ảnh load → tránh giật layout (CLS) |
| `figcaption` | SKU exact — autograder đối chiếu KB-01…MN-02 |

### 11.3. Bảng alt — sai vs đúng (8 SKU)

| SKU | alt SAI | alt ĐÚNG (gợi ý) |
|-----|---------|------------------|
| KB-01 | `image` / rỗng | `Ban phim co Keychron K2` |
| KB-02 | `KB-02` | `Ban phim Akko 3087` |
| KB-03 | `anh` | `Ban phim Leopold FC660M` |
| MS-01 | `photo` | `Chuot Logitech M331` |
| MS-02 | `MS-02` | `Chuot gaming Razer Viper` |
| MS-03 | `picture` | `Chuot im lang Xiaomi Silent` |
| MN-01 | `man hinh` | `Man hinh Dell 24 inch` |
| MN-02 | `LG` | `Man hinh LG UltraFine` |

### 11.4. Ảnh trang trí vs ảnh nội dung

| Loại | alt |
|------|-----|
| Icon trang trí (có text thay thế) | `alt=""` (có chủ ý) |
| Ảnh sản phẩm catalog P02 | **Bắt buộc** alt mô tả |

P02: **8 figure** — `figure_count=8`.

### 11.5. Video / audio (biết để dùng — không bắt buộc P02)

```html
<video controls width="320">
  <source src="assets/demo.mp4" type="video/mp4" />
  Trinh duyet khong ho tro video.
</video>
```

---

## 12. A11y cơ bản — 4 quy tắc tối thiểu

### 12.1. Sơ đồ 4 quy tắc (ASCII)

```
┌────────────────────────────────────────────────────────────┐
│  A11y CampusMart — 4 quy tắc tối thiểu (P02)               │
├────────────────────────────────────────────────────────────┤
│  1. Mọi input/select/textarea có <label> (for/id)          │
│  2. Mọi <img> SP có alt có nghĩa                           │
│  3. Landmark: header, nav, main, footer; aria-label nav      │
│  4. Heading không nhảy cấp: h1 → h2                         │
└────────────────────────────────────────────────────────────┘
```

### 12.2. Focus và bàn phím

```
Tab / Shift+Tab  → di chuyển giữa các field
Enter            → submit (khi focus nút submit)
Space            → chọn checkbox / mở select

Buổi 3: style :focus { outline: 2px solid var(--cm-primary) }
         KHÔNG outline: none trên input
```

### 12.3. Lighthouse Accessibility

1. Live Server → F12 → **Lighthouse** → Accessibility → Analyze.  
2. Mục tiêu P02: **≥ 90**.  
3. Chụp/quay kết quả cho video nộp.

---

## 13. Thử ngay trên máy (bắt buộc — dự đoán → chạy → quan sát)

Tạo `thu-form.html`, **gõ tay**:

```html
<!DOCTYPE html>
<html lang="vi">
<head>
  <meta charset="UTF-8" />
  <title>Thu Form — Buoi 2</title>
</head>
<body>
  <form action="#" method="get">
    <p>
      <label for="q-name">Ho ten</label>
      <input id="q-name" name="name" type="text" required minlength="2" />
    </p>
    <p>
      <label for="q-email">Email</label>
      <input id="q-email" name="email" type="email" required />
    </p>
    <p>
      <label for="q-cat">Danh muc</label>
      <select id="q-cat" name="category_id" required>
        <option value="">-- Chon danh muc --</option>
        <option value="1">Ban phim</option>
        <option value="2">Chuot</option>
        <option value="3">Man hinh</option>
      </select>
    </p>
    <button type="submit">Dang ky</button>
  </form>
</body>
</html>
```

**Ghi dự đoán vào vở trước mỗi thử.**

### Thử 1 — Click label

| Bước | Dự đoán | Quan sát |
|------|---------|----------|
| Click **Ho ten** | Focus `#q-name` | Elements → `:focus` |
| Click **Email** | Focus `#q-email` | Focus ring |

### Thử 2 — Submit rỗng

| Bước | Dự đoán | Quan sát |
|------|---------|----------|
| Submit không điền | Bị chặn | Tooltip trên field invalid |
| Network tab | Không request mới | — |

### Thử 3 — Email sai

| Bước | Dự đoán | Quan sát |
|------|---------|----------|
| Gõ `abc` → submit | Invalid email | Tooltip |
| Gõ `abc@tlu.edu.vn` | Pass validate | URL `?email=abc@tlu.edu.vn` |

### Thử 4 — Select placeholder

Giữ placeholder → submit → bị chặn. Chọn Ban phim → URL có `category_id=1`.

### Thử 5 — Phá `for`/`id`

`for="sai"` → click label không focus → sửa lại → OK.

### Thử 6 — Lighthouse (trên trang P02 hoàn chỉnh)

Ghi điểm + 1 mục audit fail (nếu có).

---

## 14. `<textarea>` — form liên hệ (Phiếu 02 mục 3B)

```html
<section id="about">
  <h2>Gioi thieu</h2>
  <form action="#" method="get">
    <p>
      <label for="contact-name">Ho ten</label>
      <input id="contact-name" name="name" type="text" required />
    </p>
    <p>
      <label for="contact-body">Noi dung</label>
      <textarea id="contact-body" name="body" rows="4" required></textarea>
    </p>
    <button type="submit">Gui</button>
  </form>
</section>
```

P02 EXPECT: `form_count=2` (subscribe + liên hệ).

---

## 15. So sánh trang — trước / sau Buổi 2

| Tiêu chí | P01 | P02 |
|----------|-----|-----|
| `<title>` | Catalog (Buoi 1) | Form & Media (Buoi 2) |
| Form | Không | `cm-subscribe-form` |
| Select | Không | 4 option |
| Figure | 0–1 | **8** |
| Lighthouse | — | **≥ 90** |
| CORE_8 | 41380000 | **Giữ nguyên** |

---

## 16. Câu hỏi tự kiểm tra

1. Form là “cửa dữ liệu” nghĩa là gì?
2. Vì sao `label for` = `input id`?
3. Thiếu `name` thì submit ra sao?
4. Vì sao placeholder `value=""`?
5. Khác `alt` vs `figcaption`?
6. 4 quy tắc a11y P02?
7. HTML5 validate vs JS `err_email`?
8. Trang vẫn xấu sau Buổi 2 — bình thường không?

*(Tự trả lời trước khi xem gợi ý mục 17.)*

---

## 17. Gợi ý đáp án câu tự kiểm tra

1. Thu thập input; PHP `$_GET`/`$_POST` theo `name`.  
2. Click label, screen reader, vùng bấm lớn.  
3. Field không có trong request.  
4. Rỗng = chưa chọn → `required` chặn.  
5. alt = mô tả ảnh; figcaption = SKU.  
6. Label, alt, landmark, heading.  
7. Tooltip trình duyệt vs chuỗi CANONICAL tùy chỉnh.  
8. CSS Buổi 3.

---

## 18. Lỗi thường gặp (chi tiết)

| # | Triệu chứng | Nguyên nhân | Cách xử lý |
|---|-------------|-------------|------------|
| 1 | Click label không focus | `for` ≠ `id` | Đồng bộ cặp |
| 2 | Submit thiếu field | Thiếu `name` | Thêm `name` |
| 3 | Select gửi DM mặc định | Thiếu placeholder `value=""` | Thêm option rỗng |
| 4 | Lighthouse thấp | Thiếu label/alt | Sửa từng audit |
| 5 | Mất 8 SP | Xóa nhầm tbody | Chỉ thêm section |
| 6 | title sai | Quên đổi P02 | CANONICAL §8.2 |
| 7 | 2 radio chọn cả hai | Khác `name` | Cùng `name` |
| 8 | Button gửi nhầm | Thiếu `type="button"` | Khai báo type |
| 9 | Ảnh giật layout | Thiếu width/height | 120×80 |
| 10 | Form ngoài main | Paste lệch | `#subscribe` trong `<main>` |

---

## 19. Checklist trước Buổi 3

- [ ] Form `cm-subscribe-form` + legend + 3 field + select 4 option
- [ ] 8 figure, alt + figcaption SKU
- [ ] title P02, Lighthouse ≥ 90
- [ ] CM_EXPECT đủ key; giữ 8 SP + 41380000
- [ ] Xong [Phiếu 02](./worksheets/phieu-02-form-media.md)

---

## 20. Bài tập (chuỗi CampusMart UI)

| Phần | Làm gì | Chi tiết |
|------|--------|----------|
| **Trên lớp (~20')** | Form đăng ký 3 field | [Phiếu 02 — mục 2](./worksheets/phieu-02-form-media.md) |
| **Về nhà (~25–40')** | Media + a11y | [Phiếu 02 — mục 3](./worksheets/phieu-02-form-media.md) |

Repo: **`cse391-cm-02`** + video OBS.

---

## 21. Cầu nối sang Buổi 3 (CSS)

```
Buổi 2: HTML — form + ảnh (nội dung)
Buổi 3: CSS  — màu #0f766e, bảng đẹp, box model, cascade
```

Giữ `data-testid` khi thêm class `.cm-table`, `.cm-header`.

→ [Buổi 3](./03-buoi-03-css-co-ban.md) · [Phiếu 03](./worksheets/phieu-03-css-co-ban.md)

---

*CSE391 – Ebook Frontend CampusMart UI | Buổi 2/12*
