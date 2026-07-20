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

- Form = **cửa dữ liệu**; giải phẫu ASCII: `form` → `fieldset` → `label`/`input`/`select`/`button`.
- `label for` = `input id`; mọi field có `name` (khóa submit).
- `type="email"` + `required` + `minlength="2"` = validate tầng 0 trình duyệt.
- `fieldset`/`legend` nhóm form; select placeholder **`value=""`**.
- `figure`/`figcaption`/`alt`; 4 quy tắc a11y: label, alt, landmark, heading.
- Chuỗi `err_name`, `err_email`… dùng từ **P08** — P02 chưa bắt buộc hiển thị JS.

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

### Cách thử trên lớp (làm ngay sau khi gõ)

| # | Thử | Dự đoán | Cách quan sát |
|---|-----|---------|---------------|
| T1 | Click label **Ho ten** | Focus vào input | Con trỏ / viền focus |
| T2 | Submit form rỗng | Bị chặn | Tooltip trình duyệt |
| T3 | Email = `abc` → submit | Bị chặn | Tooltip email invalid |
| T4 | Giữ placeholder select → submit | Bị chặn | Không đổi URL |
| T5 | F12 → Elements | Có `data-testid="cm-subscribe-form"` | Inspect form |

**Nếu T1 fail:** so `for` và `id` từng cặp — lỗi #1 phổ biến nhất.

### Output kỳ vọng (trên lớp)

- Bỏ trống → submit bị trình duyệt chặn.
- Email `abc` → trình duyệt báo lỗi định dạng.
- Click từng label → focus nhảy đúng input.
- Tab title chứa `Form & Media (Buoi 2)`.

**Checkpoint giảng viên:** form đủ 3 field + select 4 option, click label focus đúng.

---

## 3. Bài về nhà (~25–40 phút) — Media + a11y hoàn thiện

Tiếp tục **cùng repo** `cse391-cm-02`.

### Nhiệm vụ A — Ảnh cho 8 sản phẩm

Trong bảng sản phẩm (hoặc cột mới `Anh`), mỗi dòng thêm:

```html
<figure>
  <img src="https://placehold.co/120x80" alt="Ban phim Keychron K2" width="120" height="80" />
  <figcaption>KB-01</figcaption>
</figure>
```

- `alt` = mô tả sản phẩm (tên + loại), **không** để rỗng, không để "image".
- `figcaption` = SKU exact (KB-01 … MN-02).
- Placeholder URL được chấp nhận; ảnh thật càng tốt.

#### Cách thử Nhiệm vụ A

| # | Thử | Kỳ vọng |
|---|-----|---------|
| A1 | View Source / Elements — đếm `<figure>` | **8** |
| A2 | Mỗi `figcaption` | Khớp `data-sku` cùng dòng |
| A3 | Search `alt=""` hoặc `alt="image"` | **0** kết quả |
| A4 | Tắt ảnh (DevTools → disable cache + block image) | Vẫn hiểu SP qua alt (đọc thử) |

**Gợi ý alt nhanh (không copy y nguyên — tự diễn đạt):**

| SKU | Gợi ý alt |
|-----|-----------|
| KB-01 | Ban phim co Keychron K2 |
| MS-02 | Chuot gaming Razer Viper |
| MN-02 | Man hinh LG UltraFine |

### Nhiệm vụ B — Form liên hệ thứ hai (tự luyện, không cần testid)

Section `#about` thêm form gồm: `Ho ten` (text), `Noi dung` (`textarea`, `rows="4"`, required), nút `Gui`. Đây là phần luyện tay — chấm qua video.

#### Cách thử Nhiệm vụ B

| # | Thử | Kỳ vọng |
|---|-----|---------|
| B1 | Submit textarea rỗng | Bị chặn (`required`) |
| B2 | Label **Noi dung** click | Focus textarea |
| B3 | Đếm `<form>` trên trang | **2** (subscribe + liên hệ) |

### Nhiệm vụ C — A11y audit

1. Thêm `aria-label="Menu chinh"` cho `nav` (nếu Phiếu 01 chưa có).
2. Rà heading: một `h1`, các section dùng `h2` — không nhảy cấp.
3. Chạy Lighthouse (Chrome DevTools → Lighthouse → Accessibility) → chụp/quay kết quả **≥ 90**.

#### Cách thử Nhiệm vụ C (Lighthouse)

```
Bước 1: Mở http://localhost:... (Live Server — KHÔNG file://)
Bước 2: F12 → Lighthouse → chỉ tick Accessibility
Bước 3: Analyze → ghi điểm
Bước 4: Mở mục "Failed audits" (nếu có) → sửa từng lỗi → chạy lại
```

| Audit hay fail | Sửa |
|----------------|-----|
| Form elements lack labels | Thêm `<label for>` |
| Images missing alt | Bổ sung alt mô tả |
| Heading order | Thêm/sửa h2 |

**Mục tiêu:** ≥ **90**. Nếu 88–89: sửa 1–2 audit rồi chạy lại.

#### Cách thử focus (bàn phím)

| # | Thử | Kỳ vọng |
|---|-----|---------|
| C4 | Tab từ đầu trang qua form | Thứ tự: nav → field → submit hợp lý |
| C5 | Không dùng chuột — điền form + Enter | Submit hoặc validate chặn |

### Nhiệm vụ D — Marker chấm máy

Cập nhật comment cuối trang:

```html
<!-- CM_EXPECT product_count=8 category_count=3 inventory_value=41380000 form_count=2 figure_count=8 -->
```

#### Cách thử Nhiệm vụ D

View Source → tìm `CM_EXPECT` → đủ **5** key: `product_count`, `category_count`, `inventory_value`, `form_count`, `figure_count`.

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

### Checklist tự chấm trước nộp

```
[ ] title exact P02
[ ] 8 dòng SP + Tong = 41380000 (không đụng nhầm bảng)
[ ] cm-subscribe-form + legend + 3 field + 4 option
[ ] 8 figure + alt + figcaption SKU
[ ] 2 form (subscribe + liên hệ)
[ ] Lighthouse ≥ 90 (ảnh chụp / quay video)
[ ] CM_EXPECT 5 key
```

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
║     - Scroll 8 figure + đọc 1 alt mẫu                        ║
║     - Kết quả Lighthouse Accessibility ≥ 90                  ║
║  3. Thuyết trình 30–60s: vì sao label/for quan trọng?       ║
╚══════════════════════════════════════════════════════════════╝
```

> **Nhắc:** Tự gõ code. Không copy form mẫu trong ebook nguyên xi — tối thiểu tự đặt lại id/cấu trúc phần Nhiệm vụ B.

---

## 6. Lỗi thường gặp khi nộp (tránh)

| Lỗi | Hậu quả | Cách tránh |
|-----|---------|------------|
| Quên `value=""` placeholder | Select gửi DM mặc định | Copy đúng 4 option |
| Xóa nhầm dòng bảng khi thêm form | Sai product_count | Chỉ **thêm** section |
| alt = "image" | Lighthouse / autograder trừ | Mô tả SP |
| title vẫn Buoi 1 | page_title fail | CANONICAL §8.2 |
| Mở file:// thay Live Server | Lighthouse không tin cậy | Live Server |

---

*CSE391 – Phiếu 02 | CampusMart UI*
