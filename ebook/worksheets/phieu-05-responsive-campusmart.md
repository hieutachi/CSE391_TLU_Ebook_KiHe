# Phiếu 05 — Responsive CampusMart: dashboard tĩnh hoàn chỉnh

| | |
|---|---|
| **Buổi** | 5 |
| **Thời lượng** | ≥ 45 phút (trên lớp ~20' + về nhà ~25–40') |
| **Repo** | `cse391-cm-05` |
| **Nhận từ Phiếu 04** | Header flex + `.cm-grid` 8 card |
| **Mang sang Phiếu 06** | Dashboard tĩnh hoàn chỉnh — nền cho JS render |
| **Dataset** | [`CANONICAL-DATA.md`](./CANONICAL-DATA.md) — §8.1 breakpoints 640/1024 |

### Chuẩn đầu ra (CLO)

1. Viết media query mobile-first đúng 2 mốc **640px**, **1024px**.  
2. `.cm-grid`: 1 cột mobile → 2 cột tablet → 4 cột desktop.  
3. Nav dọc mobile → ngang từ 640px.  
4. Hero section + `.cm-btn` + `main max-width: 1120px` desktop.  
5. Quay được video 3 khung nhìn chuẩn kịch bản.

---

## 1. Tóm tắt lý thuyết (đọc lại trước khi code)

- Mobile-first: CSS mặc định = mobile; nới dần bằng `min-width`.
- Breakpoints môn học: `640px` (tablet), `1024px` (desktop).
- `overflow-x: auto` cứu bảng rộng; `img { max-width: 100%; }`.
- DevTools Device toolbar để kiểm 375 / 700 / 1200px.

Chi tiết: [Buổi 5](../05-buoi-05-responsive-components.md).

---

## 2. Bài trên lớp (~20 phút) — 2 media query cốt lõi

### Yêu cầu

Copy từ repo Phiếu 04 sang `cse391-cm-05`, rồi:

1. Đổi `<title>` exact: **`CampusMart — Responsive (Buoi 5)`**.
2. Sửa `.cm-grid` theo mobile-first:

```css
.cm-grid { display: grid; grid-template-columns: 1fr; gap: 1rem; }

@media (min-width: 640px) {
  .cm-grid { grid-template-columns: repeat(2, 1fr); }
}

@media (min-width: 1024px) {
  .cm-grid { grid-template-columns: repeat(4, 1fr); }
}
```

3. Nav: mặc định cột; từ `640px` thành hàng (xem Buổi 5 mục 4).

### Output kỳ vọng (trên lớp)

DevTools ở 375px → 1 cột + nav dọc; 700px → 2 cột + nav ngang; 1200px → 4 cột.

**Checkpoint giảng viên:** kéo Device toolbar qua 3 mốc, đúng số cột từng mốc.

---

## 3. Bài về nhà (~25–40 phút) — Dashboard tĩnh hoàn chỉnh

Tiếp tục **cùng repo**.

### Nhiệm vụ A — Hero

Thêm ngay dưới header, trước catalog:

- `section.cm-hero`: heading exact `Do choi cong nghe cho sinh vien`, một câu mô tả tự viết, nút `a.cm-btn` text `Xem catalog` trỏ `#catalog`.
- Style: nền `var(--cm-primary)`, chữ trắng, `text-align: center`, padding thoáng.

### Nhiệm vụ B — Container desktop + bảng cuộn

1. `@media (min-width: 1024px) { main { max-width: 1120px; margin-inline: auto; } }`
2. Bọc bảng phụ lục trong `.cm-table-wrap { overflow-x: auto; }` — mobile cuộn ngang, không vỡ.
3. `img { max-width: 100%; height: auto; }` toàn cục.

### Nhiệm vụ C — Rà soát 3 khung

Tự kiểm từng khung (chính là kịch bản OBS):

| Khung | Kiểm tra |
|-------|----------|
| 375px | 1 cột card, nav dọc, hero không tràn, bảng cuộn ngang |
| 700px | 2 cột card, nav ngang |
| 1200px | 4 cột card, main căn giữa ≤1120px |

### Nhiệm vụ D — Marker

```html
<!-- CM_EXPECT product_count=8 card_count=8 breakpoints=640,1024 inventory_value=41380000 -->
```

---

## 4. OUTPUT / EXPECT

| Key | Value bắt buộc |
|-----|----------------|
| `page_title` | `CampusMart — Responsive (Buoi 5)` |
| `css_contains.@media640` | `@media (min-width: 640px)` |
| `css_contains.@media1024` | `@media (min-width: 1024px)` |
| `grid.mobile` | 1 cột (rule mặc định `1fr`) |
| `grid.desktop` | `repeat(4, 1fr)` |
| `hero.heading` | `Do choi cong nghe cho sinh vien` |
| `hero.btn_text` | `Xem catalog` |
| `card_count` | 8 (đủ SKU CORE) |
| `main.max-width` | `1120px` trong query 1024 |
| `CM_EXPECT` comment | như Nhiệm vụ D |

---

## 5. Box nộp bài

```
╔══════════════════════════════════════════════════════════════╗
║  NỘP PHIẾU 05                                                ║
╠══════════════════════════════════════════════════════════════╣
║  1. GitHub repo RIÊNG: cse391-cm-05                          ║
║  2. Video OBS + camera mặt — đúng kịch bản 3 khung:          ║
║     - 375px: 1 cột + nav dọc + bảng cuộn ngang               ║
║     - 700px: 2 cột + nav ngang                               ║
║     - 1200px: 4 cột + main căn giữa                          ║
║  3. Thuyết trình 30–60s: mobile-first nghĩa là gì?           ║
╚══════════════════════════════════════════════════════════════╝
```

> **Mốc quan trọng:** Đây là bản **Dashboard CampusMart tĩnh** hoàn chỉnh (khung stats + catalog + form sẽ "sống" dần) — từ Phiếu 06, dữ liệu chuyển vào JavaScript, đến Phiếu 08 dashboard có Form Thêm.
