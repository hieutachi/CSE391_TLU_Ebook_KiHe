# Phiếu 02 — Form & Media CampusMart, a11y cơ bản

| | |
|---|---|
| **Buổi** | 2 |
| **Thời lượng** | ≥ 45 phút (trên lớp ~20' + về nhà ~25–40') |
| **Repo** | `cse391-cm-02` |
| **Nhận từ Phiếu 01** | `index.html` ngữ nghĩa + bảng 8 SP + `data-testid` |
| **Mang sang Phiếu 03** | Trang đủ form + media, sẵn hook để style |
| **Dataset** | [`CANONICAL-DATA.md`](./CANONICAL-DATA.md) — §8.4 chuỗi thông báo, §8.2 title |

### Chuẩn đầu ra (CLO)

1. Dựng form `cm-subscribe-form` đủ 3 field có `label`/`name`/`required` đúng chuẩn.  
2. Select danh mục đúng **3 + 1** option (placeholder + 3 DM canonical).  
3. Mỗi sản phẩm có ảnh trong `figure`/`figcaption`, `alt` có nghĩa.  
4. Trang đạt Lighthouse Accessibility ≥ 90.  
5. Giữ nguyên toàn bộ EXPECT của Phiếu 01 (8 SP, tổng 41380000, testid).

---

## 1. Tóm tắt lý thuyết (đọc lại trước khi code)

- `label for` = `input id`; mọi field có `name`.
- `type="email"` + `required` = validate tầng 0 của trình duyệt.
- `fieldset`/`legend` nhóm form; `figure`/`figcaption` bọc ảnh.
- 4 quy tắc a11y: label, alt, landmark, heading không nhảy cấp.

Chi tiết: [Buổi 2](../02-buoi-02-html-form-media.md).

---

## 2. Bài trên lớp (~20 phút) — Form "Dang ky nhan tin"

### Yêu cầu

Copy `index.html` từ repo Phiếu 01 sang repo mới `cse391-cm-02` (đây là "mang sang", không làm lại từ đầu). Sau đó:

1. Đổi `<title>` thành exact: **`CampusMart — Form & Media (Buoi 2)`**.
2. Thêm vào cuối `<main>` một `section id="subscribe"` chứa form:
   - `data-testid="cm-subscribe-form"`
   - `legend` exact: `Dang ky nhan tin`
   - Field 1: `label` `Ho ten` → `input type="text" name="name" required minlength="2"`
   - Field 2: `label` `Email` → `input type="email" name="email" required`
   - Field 3: `label` `Danh muc quan tam` → `select name="category_id" required` với 4 option:

     | value | text |
     |-------|------|
     | *(rỗng)* | `-- Chon danh muc --` |
     | `1` | `Ban phim` |
     | `2` | `Chuot` |
     | `3` | `Man hinh` |

   - Nút submit text exact: `Dang ky`
3. Thêm link `Dang ky` vào `nav` trỏ `#subscribe`.

### Output kỳ vọng (trên lớp)

- Bỏ trống → submit bị trình duyệt chặn.
- Email `abc` → trình duyệt báo lỗi định dạng.
- Click từng label → focus nhảy đúng input.

**Checkpoint giảng viên:** form đủ 3 field + select 4 option, click label focus đúng.

---

## 3. Bài về nhà (~25–40 phút) — Media + a11y hoàn thiện

Tiếp tục **cùng repo**.

### Nhiệm vụ A — Ảnh cho 8 sản phẩm

Trong bảng sản phẩm (hoặc cột mới `Anh`), mỗi dòng thêm:

```html
<figure>
  <img src="https://placehold.co/120x80" alt="Ban phim Keychron K2" width="120" height="80" />
  <figcaption>KB-01</figcaption>
</figure>
```

- `alt` = mô tả sản phẩm (tên + loại), **không** để rỗng, không để "image".
- `figcaption` = SKU (giúp máy chấm đối chiếu).
- Placeholder URL được chấp nhận; ảnh thật càng tốt.

### Nhiệm vụ B — Form liên hệ thứ hai (tự luyện, không cần testid)

Section `#about` thêm form gồm: `Ho ten` (text), `Noi dung` (`textarea`, `rows="4"`, required), nút `Gui`. Đây là phần luyện tay — chấm qua video.

### Nhiệm vụ C — A11y audit

1. Thêm `aria-label="Menu chinh"` cho `nav` (nếu Phiếu 01 chưa có).
2. Rà heading: một `h1`, các section dùng `h2` — không nhảy cấp.
3. Chạy Lighthouse (Chrome DevTools → Lighthouse → Accessibility) → chụp/quay kết quả **≥ 90**.

### Nhiệm vụ D — Marker chấm máy

Cập nhật comment cuối trang:

```html
<!-- CM_EXPECT product_count=8 category_count=3 inventory_value=41380000 form_count=2 figure_count=8 -->
```

---

## 4. OUTPUT / EXPECT

| Key | Value bắt buộc |
|-----|----------------|
| `page_title` | `CampusMart — Form & Media (Buoi 2)` |
| `testid.cm-subscribe-form` | tồn tại |
| `legend` | `Dang ky nhan tin` |
| `select.option_count` | 4 (1 placeholder + 3 DM) |
| `select.options` | `Ban phim`, `Chuot`, `Man hinh` (value 1/2/3) |
| `submit_text` | `Dang ky` |
| `figure_count` | 8 (mỗi SP một figure, figcaption = SKU) |
| `img.alt` | không rỗng, không trùng "image" |
| Giữ từ P01 | 8 SKU, `cm-product-table`, tổng 41380000, landmark |
| `CM_EXPECT` comment | đủ 5 key như Nhiệm vụ D |

---

## 5. Box nộp bài

```
╔══════════════════════════════════════════════════════════════╗
║  NỘP PHIẾU 02                                                ║
╠══════════════════════════════════════════════════════════════╣
║  1. GitHub repo RIÊNG: cse391-cm-02                          ║
║  2. Video OBS + camera mặt:                                  ║
║     - Submit rỗng bị chặn + email sai bị báo                 ║
║     - Click label → focus đúng input                         ║
║     - Kết quả Lighthouse Accessibility ≥ 90                  ║
║  3. Thuyết trình 30–60s: vì sao label/for quan trọng?        ║
╚══════════════════════════════════════════════════════════════╝
```

> **Nhắc:** Tự gõ code. Không copy form mẫu trong ebook nguyên xi — tối thiểu tự đặt lại id/cấu trúc phần Nhiệm vụ B.
