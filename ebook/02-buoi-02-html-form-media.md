# BUỔI 2: HTML Form & Media — a11y cơ bản

> **Thời lượng gợi ý:** 3–4 giờ tự học  
> **Tiên quyết:** Hoàn thành Buổi 1 + Phiếu 01 (skeleton CampusMart, bảng 8 SP)  
> **Kết quả đầu ra:** Viết được form có label đúng chuẩn, nhúng ảnh/media có ngữ nghĩa, hiểu a11y cơ bản (label, alt, landmark, focus)  
> **Phiếu học tập liên quan:** [Phiếu 02](./worksheets/phieu-02-form-media.md) — chuỗi **CampusMart UI**

---

## 1. Mục tiêu buổi học

Sau Buổi 2, bạn có thể:

1. Dựng form với `form`, `label`, `input`, `select`, `textarea`, `button` đúng ngữ nghĩa.
2. Chọn đúng `type` input (`text`, `email`, `number`, `password`…) và thuộc tính `required`, `min`, `placeholder`.
3. Nhúng ảnh/media với `figure` + `figcaption`; hiểu vì sao `alt` bắt buộc.
4. Giải thích 4 nguyên tắc a11y cơ bản: label gắn input, alt cho ảnh, landmark, thứ tự heading.
5. Bổ sung form "Đăng ký nhận tin" vào trang CampusMart mà **không phá** cấu trúc Phiếu 01.

---

## 2. Vì sao cần Form đúng chuẩn?

Form là **cửa ngõ dữ liệu** của mọi ứng dụng web: đăng ký, tìm kiếm, thêm sản phẩm… Ở Backend (CSE485) bạn sẽ nhận dữ liệu form bằng `$_POST`; ở Frontend, form đúng chuẩn quyết định:

- Người dùng bàn phím / trình đọc màn hình có dùng được không (a11y).
- Trình duyệt có tự validate được không (`type="email"`, `required`).
- JavaScript (Buổi 8) và React (Buổi 11) có lấy dữ liệu sạch không.

> Một form không có `label` giống một cửa hàng không có biển chỉ dẫn: ai quen mới dùng được.

---

## 3. Giải phẫu một Form (ASCII)

```
┌───────────────────────────────────────────────────────────┐
│ <form action="..." method="...">                          │
│                                                           │
│   <label for="email">  ◄──── liên kết bằng for/id ────┐   │
│   <input id="email" type="email" required>  ◄─────────┘   │
│                                                           │
│   <select> ── <option value="1">Ban phim</option> …       │
│                                                           │
│   <button type="submit">  ── kích hoạt submit             │
│ </form>                                                   │
│                                                           │
│  Submit ──► (HTML thuần) trình duyệt gửi request          │
│         ──► (Buổi 8/11)  JS chặn lại, tự xử lý            │
└───────────────────────────────────────────────────────────┘
```

### 3.1. Khung form tối thiểu

```html
<form data-testid="cm-subscribe-form" action="#" method="get">
  <fieldset>
    <legend>Dang ky nhan tin</legend>

    <p>
      <label for="sub-name">Ho ten</label>
      <input id="sub-name" name="name" type="text" required minlength="2" />
    </p>

    <p>
      <label for="sub-email">Email</label>
      <input id="sub-email" name="email" type="email" required />
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
```

| Chi tiết | Vì sao |
|----------|--------|
| `label for` = `input id` | Click label → focus input; screen reader đọc đúng |
| `name` | Khóa dữ liệu khi submit (Backend/JS đọc theo `name`) |
| `type="email"` | Trình duyệt tự validate định dạng |
| `required` | Chặn submit rỗng — validate "tầng 0" |
| `fieldset`/`legend` | Nhóm field có tiêu đề, tốt cho a11y |

### 3.2. Các `type` input hay dùng

| type | Dùng cho | Ghi chú |
|------|----------|---------|
| `text` | Tên, SKU | `minlength`, `maxlength`, `pattern` |
| `email` | Email | Tự check dạng `a@b` |
| `number` | Giá, số lượng | `min`, `max`, `step` |
| `password` | Mật khẩu | Che ký tự |
| `checkbox` / `radio` | Chọn nhiều / chọn một | Mỗi radio cùng `name` |
| `search` | Ô tìm kiếm | Dùng ở Buổi 10 (React) |

> **Nhìn xa:** Buổi 11 bạn sẽ làm form thêm sản phẩm bằng React — các `type` và `required` học hôm nay dùng lại nguyên vẹn.

---

## 4. Media có ngữ nghĩa

### 4.1. `figure` + `figcaption`

```html
<figure>
  <img
    src="https://placehold.co/240x160"
    alt="Ban phim co Keychron K2 mau den"
    width="240"
    height="160"
  />
  <figcaption>Keychron K2 — best-seller danh muc Ban phim</figcaption>
</figure>
```

- `alt` mô tả **nội dung ảnh** (không phải "ảnh 1", "image").
- `width`/`height` giúp trình duyệt giữ chỗ, tránh giật layout (CLS).

### 4.2. Video / audio (biết để dùng)

```html
<video controls width="320">
  <source src="demo.mp4" type="video/mp4" />
  Trinh duyet khong ho tro video.
</video>
```

> Trong phạm vi CampusMart, media bắt buộc chỉ là **ảnh sản phẩm + figcaption**; video là tùy chọn.

---

## 5. A11y cơ bản — 4 quy tắc tối thiểu

```
1. Mọi <input> có <label>        ──►  form dùng được bằng screen reader
2. Mọi <img> có alt có nghĩa     ──►  ảnh "đọc" được
3. Landmark đúng (nav/main/...)  ──►  nhảy vùng nhanh bằng phím
4. Heading không nhảy cấp        ──►  mục lục trang hợp lệ
```

Kiểm tra nhanh bằng DevTools: tab **Lighthouse** → Accessibility (mục tiêu ≥ 90 điểm cho trang CampusMart tĩnh).

Thêm hai thói quen tốt:

- `aria-label` cho vùng không có text nhìn thấy (ví dụ `nav`).
- Không bỏ outline focus (`:focus`) — người dùng bàn phím cần thấy đang ở đâu.

---

## 6. Bài thực hành minh họa trong buổi (không thay phiếu)

Thêm vào trang thực hành Buổi 1 một form liên hệ rút gọn (2 field) rồi thử:

1. Bỏ trống → nhấn **Dang ky** → trình duyệt chặn (`required`).
2. Gõ `abc` vào ô email → trình duyệt báo sai định dạng.
3. Click vào chữ label → con trỏ nhảy vào input (chứng minh `for`/`id` đúng).
4. Nhấn `Tab` liên tục → thứ tự focus đi từ trên xuống hợp lý.

```html
<form>
  <label for="q-email">Email</label>
  <input id="q-email" name="email" type="email" required />
  <button type="submit">Dang ky</button>
</form>
```

---

## 7. Lỗi thường gặp

| Triệu chứng | Nguyên nhân | Cách xử lý |
|-------------|-------------|------------|
| Click label không focus input | `for` ≠ `id` | Đồng bộ hai giá trị |
| Submit không mang dữ liệu | Thiếu `name` trên input | Thêm `name` cho mọi field |
| Select chấp nhận option rỗng | Option đầu thiếu `value=""` + `required` | Đặt `value=""` cho placeholder |
| Ảnh không có ý nghĩa với máy | `alt` rỗng / vô nghĩa | Viết alt mô tả nội dung |
| 2 radio chọn được cả hai | Khác `name` | Radio cùng nhóm phải cùng `name` |

---

## 8. Checklist trước khi sang buổi sau

- [ ] Form có `fieldset`/`legend`, mọi input có `label` + `name`
- [ ] Dùng đúng `type` + `required` (email, number…)
- [ ] Ảnh nằm trong `figure` + `figcaption`, `alt` có nghĩa
- [ ] Trang qua Lighthouse Accessibility ≥ 90
- [ ] Đã làm xong [Phiếu 02](./worksheets/phieu-02-form-media.md) (trên lớp + về nhà)

---

## 9. Bài tập trên lớp & về nhà (chuỗi CampusMart UI)

| Phần | Làm gì | Chi tiết |
|------|--------|----------|
| **Trên lớp (~20')** | Form "Dang ky nhan tin" 3 field + select 3 DM | [Phiếu 02 — mục 2](./worksheets/phieu-02-form-media.md) |
| **Về nhà (~25–40')** | Ảnh + figcaption cho 8 SP, a11y pass, EXPECT | [Phiếu 02 — mục 3](./worksheets/phieu-02-form-media.md) |

Nộp repo riêng `cse391-cm-02` + video OBS (camera mặt) theo box cuối phiếu.

---

## 10. Cầu nối sang Buổi 3

Trang CampusMart giờ **đủ nội dung nhưng chưa có "quần áo"**. Buổi 3 bắt đầu CSS: selector, cascade (vì sao style này thắng style kia), box model — nền tảng để Buổi 4–5 dựng layout thật.

→ Tiếp: [Buổi 3](./03-buoi-03-css-co-ban.md) · [Phiếu 03](./worksheets/phieu-03-css-co-ban.md)

---

*CSE391 – Ebook Frontend CampusMart UI | Buổi 2/12*
