# BUỔI 5: Responsive & UI Components tĩnh — cắt layout CampusMart

> **Thời lượng gợi ý:** 3–4 giờ tự học  
> **Tiên quyết:** Buổi 4 (header flex + `.cm-grid` card)  
> **Kết quả đầu ra:** Trang CampusMart hiển thị tốt ở 3 khung: mobile / tablet / desktop; bộ component tĩnh hoàn chỉnh (nav, hero, card, bảng)  
> **Phiếu học tập liên quan:** [Phiếu 05](./worksheets/phieu-05-responsive-campusmart.md) — chuỗi **CampusMart UI**

---

## 1. Mục tiêu buổi học

Sau Buổi 5, bạn có thể:

1. Giải thích mobile-first và vai trò của meta viewport.
2. Viết media query đúng breakpoint môn học: **640px** và **1024px**.
3. Điều khiển số cột `.cm-grid` theo khung nhìn (1 → 2 → 4).
4. Làm nav co giãn: dọc trên mobile, ngang từ tablet.
5. Hoàn thiện trang landing CampusMart tĩnh: hero, section danh mục, lưới card, footer.

---

## 2. Vì sao Responsive không phải "tùy chọn"?

Hơn 60% lượt truy cập web tại Việt Nam đến từ điện thoại. Một trang chỉ đẹp trên desktop nghĩa là hỏng với đa số người dùng. Responsive không phải viết **hai trang**, mà viết **một trang biết thích nghi** — đúng triết lý "one source of truth" bạn đã quen từ dataset.

---

## 3. Mobile-first & viewport

### 3.1. Meta viewport (đã có từ Buổi 1 — giờ hiểu tại sao)

```html
<meta name="viewport" content="width=device-width, initial-scale=1.0" />
```

Thiếu dòng này, điện thoại giả lập màn 980px rồi thu nhỏ — chữ li ti, media query vô dụng.

### 3.2. Chiến lược mobile-first

```
Viết CSS mặc định cho MOBILE (hẹp nhất, 1 cột)
        │
        ▼  min-width: 640px  ── tablet: nới 2 cột, nav ngang
        │
        ▼  min-width: 1024px ── desktop: 4 cột, container tối đa
```

```css
/* ===== Mặc định: MOBILE ===== */
.cm-grid { grid-template-columns: 1fr; }

/* ===== TABLET ===== */
@media (min-width: 640px) {
  .cm-grid { grid-template-columns: repeat(2, 1fr); }
}

/* ===== DESKTOP ===== */
@media (min-width: 1024px) {
  .cm-grid { grid-template-columns: repeat(4, 1fr); }
  main { max-width: 1120px; margin-inline: auto; }
}
```

### 3.3. Sơ đồ khung nhìn (ASCII bắt buộc nhớ)

```
 MOBILE (<640px)     TABLET (640–1023px)    DESKTOP (>=1024px)
┌───────────┐       ┌──────────┬──────────┐  ┌────┬────┬────┬────┐
│  KB-01    │       │  KB-01   │  KB-02   │  │KB01│KB02│KB03│MS01│
├───────────┤       ├──────────┼──────────┤  ├────┼────┼────┼────┤
│  KB-02    │       │  KB-03   │  MS-01   │  │MS02│MS03│MN01│MN02│
├───────────┤       ├──────────┼──────────┤  └────┴────┴────┴────┘
│  ...      │       │  ...     │  ...     │       main ≤ 1120px
└───────────┘       └──────────┴──────────┘
  nav: DỌC             nav: NGANG              nav: NGANG
```

---

## 4. Nav responsive (không cần JS)

Mobile: brand trên, menu xếp dọc dưới. Tablet trở lên: một hàng như Buổi 4.

```css
/* Mobile mặc định */
.cm-header { display: flex; flex-direction: column; gap: 0.75rem; }
.cm-nav ul { display: flex; flex-direction: column; gap: 0.5rem; }

@media (min-width: 640px) {
  .cm-header { flex-direction: row; justify-content: space-between; align-items: center; }
  .cm-nav ul { flex-direction: row; gap: 1.5rem; }
}
```

> Hamburger menu (nút ☰ đóng/mở) cần JavaScript — hẹn Buổi 7. Buổi này chấp nhận menu dọc hiện đầy đủ trên mobile.

---

## 5. Bộ component tĩnh CampusMart

### 5.1. Hero section

```html
<section class="cm-hero">
  <h2>Do choi cong nghe cho sinh vien</h2>
  <p>Ban phim, chuot, man hinh — gia sinh vien.</p>
  <a class="cm-btn" href="#catalog">Xem catalog</a>
</section>
```

```css
.cm-hero {
  background: var(--cm-primary);
  color: white;
  padding: 3rem 1.5rem;
  text-align: center;
}
.cm-btn {
  display: inline-block;
  background: white;
  color: var(--cm-primary);
  padding: 0.75rem 1.5rem;
  border-radius: 6px;
  font-weight: 700;
  text-decoration: none;
}
```

### 5.2. Bảng responsive (phụ lục dữ liệu)

Bảng rộng hơn màn mobile → cho phép cuộn ngang thay vì vỡ:

```html
<div class="cm-table-wrap">
  <table class="cm-table">…</table>
</div>
```

```css
.cm-table-wrap { overflow-x: auto; }
```

### 5.3. Ảnh co giãn

```css
img { max-width: 100%; height: auto; }
```

---

## 6. Kiểm thử responsive bằng DevTools

1. F12 → biểu tượng điện thoại (Device toolbar) hoặc `Cmd/Ctrl+Shift+M`.
2. Chọn "iPhone SE" (375px) → grid 1 cột, nav dọc.
3. Kéo lên 700px → 2 cột, nav ngang.
4. 1200px → 4 cột, main căn giữa max-width.

Ba mốc này chính là **kịch bản video OBS** của Phiếu 05.

---

## 7. Bài thực hành minh họa trong buổi (không thay phiếu)

Không cần trang mới — mở trang Phiếu 04 của bạn, chỉ thêm 1 media query và quan sát trước/sau:

```css
@media (min-width: 640px) {
  body { background: #ecfeff; } /* đổi màu nền để THẤY query bắt đúng mốc */
}
```

Kéo qua lại quanh 640px thấy nền đổi màu → hiểu breakpoint bằng mắt. Xóa dòng test đi trước khi làm phiếu.

---

## 8. Lỗi thường gặp

| Triệu chứng | Nguyên nhân | Cách xử lý |
|-------------|-------------|------------|
| Media query không ăn trên điện thoại | Thiếu meta viewport | Thêm lại meta (mục 3.1) |
| Query desktop đè mobile lộn xộn | Viết `max-width` lẫn `min-width` | Thống nhất mobile-first + `min-width` |
| Grid vẫn 4 cột trên mobile | Query đặt **trước** rule mặc định | Rule mặc định trước, query sau |
| Chữ hero to quá trên mobile | Font cố định px lớn | Dùng `rem` + giảm trong mặc định |
| Bảng tràn màn | Không có wrapper cuộn | `.cm-table-wrap { overflow-x: auto; }` |

---

## 9. Checklist trước khi sang buổi sau

- [ ] Trang đạt 3 khung: 1 cột / 2 cột / 4 cột đúng mốc 640/1024
- [ ] Nav dọc mobile → ngang tablet
- [ ] Hero + nút `.cm-btn` hoạt động
- [ ] Bảng phụ lục cuộn ngang được trên mobile
- [ ] Đã làm xong [Phiếu 05](./worksheets/phieu-05-responsive-campusmart.md)

---

## 10. Bài tập trên lớp & về nhà (chuỗi CampusMart UI)

| Phần | Làm gì | Chi tiết |
|------|--------|----------|
| **Trên lớp (~20')** | 2 media query 640/1024 cho grid + nav | [Phiếu 05 — mục 2](./worksheets/phieu-05-responsive-campusmart.md) |
| **Về nhà (~25–40')** | Hero + hoàn thiện landing + quay 3 khung | [Phiếu 05 — mục 3](./worksheets/phieu-05-responsive-campusmart.md) |

Nộp repo riêng `cse391-cm-05` + video OBS theo box cuối phiếu.

---

## 11. Cầu nối sang Buổi 6

Giao diện CampusMart tĩnh đã **hoàn chỉnh** — nhưng dữ liệu đang "chết cứng" trong HTML: thêm 1 sản phẩm phải sửa tay 3 chỗ. Buổi 6 đưa dữ liệu vào **JavaScript** (mảng object như CANONICAL §5) và học nền tảng ngôn ngữ để Buổi 7 render tự động.

→ Tiếp: [Buổi 6](./06-buoi-06-javascript-nen.md) · [Phiếu 06](./worksheets/phieu-06-js-nen.md)

---

*CSE391 – Ebook Frontend CampusMart UI | Buổi 5/12*
