# Phiếu 05 — Responsive CampusMart: dashboard tĩnh hoàn chỉnh

| | |
|---|---|
| **Buổi** | 5 |
| **Thời lượng** | ≥ 45 phút (trên lớp ~20' + về nhà ~25–40') |
| **Repo** | `cse391-cm-05` |
| **Nhận từ Phiếu 04** | Header flex, `.cm-grid`, 8 `.cm-card`, testid trên lưới |
| **Mang sang Phiếu 06** | Dashboard tĩnh hoàn chỉnh — nền cho JS render |
| **Dataset** | [`CANONICAL-DATA.md`](./CANONICAL-DATA.md) — §8.1 breakpoint 640/1024, hero exact |

### Chuẩn đầu ra (CLO)

1. CSS **mobile-first** + 2 query: `min-width: 640px`, `min-width: 1024px`.  
2. `.cm-grid`: **1 → 2 → 4** cột theo khung nhìn.  
3. Nav **dọc** mobile → **ngang** từ tablet.  
4. Hero: heading + nút **exact** CANONICAL.  
5. `main` / `.cm-main` `max-width: 1120px` desktop.  
6. Bảng trong `.table-wrap` — cuộn ngang, không vỡ body.  
7. Video quay **375 / 700 / 1200** px.

---

## 1. Tóm tắt lý thuyết (đọc lại trước khi code)

- Mobile-first: base mobile, `min-width` thêm style lớn hơn.
- Breakpoint exact: **640px**, **1024px** — không 768/1280.
- ASCII 3 khung: 375 / 700 / 1200 (Buổi 5 mục 4).
- Hero: `Do choi cong nghe cho sinh vien` + `.cm-btn` `Xem catalog`.
- `.table-wrap { overflow-x: auto }` + bảng `min-width`.
- Thuật ngữ: **dashboard tĩnh** (không “landing”).

Chi tiết: [Buổi 5](../05-buoi-05-responsive-components.md).

---

## 2. Bài trên lớp (~20 phút) — Mobile base + query 640px

### Yêu cầu

Copy repo Phiếu 04 sang `cse391-cm-05`, rồi:

1. Đổi `<title>` exact: **`CampusMart — Responsive (Buoi 5)`**.
2. **Base mobile** — thêm/sửa trong `styles.css`:

```css
.cm-header {
  display: flex;
  flex-direction: column;
  align-items: flex-start;
  gap: 1rem;
}

.cm-nav ul {
  display: flex;
  flex-direction: column;
  gap: 0.75rem;
}

.cm-grid {
  display: grid;
  grid-template-columns: 1fr;
  gap: 1rem;
}
```

3. Query tablet:

```css
@media (min-width: 640px) {
  .cm-header {
    flex-direction: row;
    justify-content: space-between;
    align-items: center;
  }

  .cm-nav ul {
    flex-direction: row;
    flex-wrap: wrap;
    gap: 1.5rem;
  }

  .cm-grid {
    grid-template-columns: repeat(2, 1fr);
  }
}
```

4. Bọc `<main>` (hoặc thêm class):

```html
<main class="cm-main" id="main-content">
  ...
</main>
```

### Cách thử trên lớp (DevTools)

| # | Thử | Width | Dự đoán | Quan sát |
|---|-----|-------|---------|----------|
| T1 | Device toolbar | **375** | Nav dọc, grid 1 cột | Elements + layout |
| T2 | Device toolbar | **700** | Nav ngang, grid 2 cột | ≥640 query |
| T3 | Search CSS | — | Có `(min-width: 640px)` | View `styles.css` |
| T4 | Inspect header @375 | — | `flex-direction: column` | Computed |
| T5 | Inspect header @700 | — | `flex-direction: row` | Computed |

**Nếu T1/T2 giống nhau:** thiếu base mobile hoặc query sai `max-width`.

### Output kỳ vọng (trên lớp)

375px: menu xếp dọc, 1 card/cột. 700px: header một hàng, 2 card/cột.

**Checkpoint giảng viên:** SV chỉ ra sự khác biệt 375 vs 700 trên màn chiếu.

---

## 3. Bài về nhà (~25–40 phút) — Hero + desktop + table-wrap + video

Tiếp tục **cùng repo** `cse391-cm-05`.

### Nhiệm vụ A — Query desktop 1024px

```css
@media (min-width: 1024px) {
  .cm-grid {
    grid-template-columns: repeat(4, 1fr);
  }

  .cm-main {
    width: 100%;
    max-width: 1120px;
    margin: 0 auto;
    padding: 1rem;
  }
}
```

#### Cách thử Nhiệm vụ A

| # | Thử | Width | Kỳ vọng |
|---|-----|-------|---------|
| A1 | DevTools | **1200** | Grid **4** cột |
| A2 | Inspect `.cm-main` | 1200 | `max-width: 1120px`, margin auto |
| A3 | Search CSS | — | Có `(min-width: 1024px)` | |

### Nhiệm vụ B — Hero section

```html
<section class="cm-hero" id="hero">
  <h2>Do choi cong nghe cho sinh vien</h2>
  <a href="#products" class="cm-btn">Xem catalog</a>
</section>
```

Style `.cm-hero`, `.cm-btn` — xem Buổi 5 mục 7.

#### Cách thử Nhiệm vụ B

| # | Thử | Kỳ vọng |
|---|-----|---------|
| B1 | View Source `<h2>` | Exact: `Do choi cong nghe cho sinh vien` |
| B2 | Text nút | Exact: `Xem catalog` |
| B3 | Click nút | Scroll/jump tới `#products` |

### Nhiệm vụ C — `.table-wrap` cho bảng inventory

```html
<div class="table-wrap">
  <table class="cm-table">
    <!-- 8 dòng CORE — giữ từ P01 -->
  </table>
</div>
```

```css
.table-wrap {
  width: 100%;
  overflow-x: auto;
  -webkit-overflow-scrolling: touch;
}

.cm-table {
  min-width: 640px;
}
```

#### Cách thử Nhiệm vụ C

| # | Thử | Width | Kỳ vọng |
|---|-----|-------|---------|
| C1 | DevTools | **375** | Cuộn ngang **trong** `.table-wrap` |
| C2 | Quan sát body | 375 | Không (hoặc ít) scroll ngang toàn trang |

### Nhiệm vụ D — Dashboard tĩnh checklist

Đảm bảo trang có đủ:

- [ ] `.cm-header` + `.cm-nav` responsive  
- [ ] `.cm-hero` exact  
- [ ] `.cm-grid` 8 card + `data-testid="cm-product-table"`  
- [ ] `.table-wrap` + `.cm-table`  
- [ ] `#subscribe` + `cm-subscribe-form`  
- [ ] footer  

### Nhiệm vụ E — Marker

```html
<!-- CM_EXPECT product_count=8 card_count=8 inventory_value=41380000 responsive=640,1024 -->
```

### Nhiệm vụ F — Video OBS (bắt buộc)

Quay liên tiếp 3 khung (resize DevTools hoặc preset):

| Khung | Width | Phải thấy |
|-------|-------|-----------|
| Mobile | **375** | Nav dọc, 1 cột, bảng cuộn |
| Tablet | **700** | Nav ngang, 2 cột |
| Desktop | **1200** | 4 cột, main ≤1120px giữa |

---

## 4. OUTPUT / EXPECT

| Key | Value bắt buộc |
|-----|----------------|
| `page_title` | `CampusMart — Responsive (Buoi 5)` |
| `css.@media` | `(min-width: 640px)` **và** `(min-width: 1024px)` |
| `grid.cols` | default `1fr`; 640→`repeat(2, 1fr)`; 1024→`repeat(4, 1fr)` |
| `hero.heading` | `Do choi cong nghe cho sinh vien` |
| `hero.btn` | `Xem catalog` |
| `main.max-width` | `1120px` |
| `table.wrap` | `.table-wrap` + overflow-x |
| Video | 3 khung **375 / 700 / 1200** |
| Giữ P04 | 8 card, testid, CORE 41380000 |
| `CM_EXPECT` | như Nhiệm vụ E |

### Checklist tự chấm trước nộp

```
[ ] title exact P05
[ ] mobile-first base (nav column, grid 1fr)
[ ] @media 640 + 1024
[ ] grid 1 → 2 → 4 @375/700/1200
[ ] hero heading + btn exact
[ ] cm-main max-width 1120px
[ ] table-wrap overflow
[ ] dashboard đủ section
[ ] video 3 khung
[ ] CM_EXPECT responsive=640,1024
```

---

## 5. Box nộp bài

```
╔══════════════════════════════════════════════════════════════╗
║  NỘP PHIẾU 05                                                ║
╠══════════════════════════════════════════════════════════════╣
║  1. GitHub repo RIÊNG: cse391-cm-05                          ║
║  2. Video OBS + camera mặt — BẮT BUỘC 3 khung:               ║
║     - 375px: nav dọc, 1 cột grid, table cuộn ngang           ║
║     - 700px: nav ngang, 2 cột grid                           ║
║     - 1200px: 4 cột, main max 1120px                         ║
║  3. Hero + nút Xem catalog hiển thị rõ                       ║
╚══════════════════════════════════════════════════════════════╝
```

---

## 6. Cách mở DevTools đúng 375 / 700 / 1200

1. F12 → **Toggle device toolbar** (Ctrl+Shift+M / Cmd+Shift+M).  
2. Dropdown device → **Responsive**.  
3. Nhập width ô trên cùng: `375` → Enter → quay clip 1.  
4. Đổi `700` → clip 2.  
5. Đổi `1200` → clip 3.  
6. (Tùy chọn) Reload mỗi lần đổi để chắc CSS không phụ thuộc cache.

| Lỗi khi thử | Cách xử lý |
|-------------|------------|
| Không thấy khác biệt | Hard reload Ctrl+F5 |
| 700 vẫn 1 cột | Kiểm tra `@media (min-width: 640px)` |
| 1200 vẫn 2 cột | Thiếu query 1024 |
| Zoom browser ≠ 100% | Reset zoom 100% |

---

## 7. Lỗi thường gặp khi nộp (tránh)

| Lỗi | Hậu quả | Cách tránh |
|-----|---------|------------|
| Dùng `max-width` query | mobile-first fail | Chỉ `min-width` |
| Hero sai chữ | hero.heading fail | Copy CANONICAL |
| Bỏ 8 card P04 | card_count fail | Giữ nguyên lưới |
| Body scroll ngang | thiếu table-wrap | Bọc bảng + overflow-x |
| Video thiếu 1 khung | trừ video | Đủ 375/700/1200 |
| Gọi “landing” trong báo cáo | không trừ điểm nhưng sai thuật ngữ | Nói **dashboard tĩnh** |

---

*CSE391 – Phiếu 05 | CampusMart UI*
