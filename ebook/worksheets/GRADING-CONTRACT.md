# Hợp đồng chấm bài (chuẩn bị Autograder Online)

Tài liệu này khóa **chuẩn đầu ra + OUTPUT ổn định** cho từng phiếu.  
Dataset: [`CANONICAL-DATA.md`](./CANONICAL-DATA.md). **Đã khóa đủ P01–P12.**

## Quy ước chung (mọi phiếu)

| Rule ID | Quy ước |
|---------|---------|
| `R-REPO` | Repo GitHub riêng; tên khuyến nghị `cse391-cm-{NN}` (`01`…`12`); React không commit `node_modules` |
| `R-VIDEO` | Video OBS + camera mặt, chạy đúng kịch bản EXPECT của phiếu |
| `R-NO-SOLUTION` | Không chấp nhận nộp nguyên solution mẫu / clone ebook |
| `R-CORE-DATA` | Đúng 8 SKU CORE + `inventory_value_core=41380000` khi phiếu ở nền CORE_8 |
| `R-TESTID` | Giữ `data-testid` canonical (§4, §8.3) — không đổi tên |
| `R-EXPECT-COMMENT` | HTML: `<!-- CM_EXPECT k=v -->`; React: comment JSX `{/* CM_EXPECT ... */}` |
| `R-TITLE` | `<title>` exact theo §8.2 |
| `R-ESCAPE` | Dữ liệu người dùng ghi bằng `textContent` / JSX text (không innerHTML thô) |
| `R-IMMUTABLE` | React state: cấm `push`/`splice`/gán thẳng — dùng spread/`map`/`filter` |

### Ma trận "Autograder giai đoạn 1" vs "chấm tay"

| Phần | Máy chấm được ngay | Chấm tay (video) |
|------|--------------------|-------------------|
| Đếm phần tử theo testid / data-sku | ✅ | — |
| Chuỗi exact (title, msg lỗi, count text) | ✅ | — |
| Grep CSS (tokens, breakpoints, rule) | ✅ | — |
| Giá trị hàm JS (chạy headless / import) | ✅ | — |
| Kịch bản CRUD React (Playwright click theo testid) | ✅ giai đoạn 2 | ✅ giai đoạn 1 |
| A11y cảm quan, thẩm mỹ, giải thích khái niệm | — | ✅ |
| Camera mặt + thao tác thật | — | ✅ |

---

## P01 — HTML ngữ nghĩa & Catalog tĩnh

### CLO

1. Trang HTML5 đúng DOCTYPE / `lang="vi"` / UTF-8 / viewport.  
2. Landmark: `header`, `nav`, `main`, `footer`.  
3. 3 categories + 4 SP (lớp) → **8 SP** (về nhà), map tên DM chữ.  
4. `data-testid` / `data-sku` + comment `CM_EXPECT`.

### EXPECT

| Key | Value |
|-----|-------|
| `page_title` | `CampusMart — Catalog (Buoi 1)` |
| `brand_text` | `CampusMart` (`cm-brand`) |
| `category_count` / `product_count` | 3 / 8 |
| `skus` | KB-01,KB-02,KB-03,MS-01,MS-02,MS-03,MN-01,MN-02 |
| `inventory_value` | 41380000 (`cm-inventory-total`) |
| `caption` | `Danh sach san pham CORE` |
| `landmarks` | header, nav, main, footer |

| | Hành vi chấm |
|--|--------------|
| Trên lớp | 4 SKU (KB-01,KB-02,MS-01,MS-02) + 3 DM + landmark |
| Về nhà | đủ 8 SKU + tổng + comment EXPECT |

---

## P02 — Form & Media, a11y

### CLO

1. Form `cm-subscribe-form` 3 field đủ label/name/required.  
2. Select 4 option (placeholder + 3 DM).  
3. 8 figure + alt có nghĩa, figcaption = SKU.  
4. Lighthouse Accessibility ≥ 90.

### EXPECT

| Key | Value |
|-----|-------|
| `page_title` | `CampusMart — Form & Media (Buoi 2)` |
| `legend` | `Dang ky nhan tin` |
| `select.option_count` | 4 (value ``/1/2/3) |
| `submit_text` | `Dang ky` |
| `figure_count` | 8, figcaption = SKU |
| `a11y` | ≥ 90 (video) |
| Giữ P01 | 8 SKU + 41380000 + landmark |

---

## P03 — CSS cơ bản

### CLO

1. `styles.css` external; không inline style.  
2. Tokens `--cm-primary: #0f766e`, `--cm-bg: #f8fafc`.  
3. `box-sizing: border-box` toàn cục.  
4. `.cm-table` styled; focus nhìn thấy.

### EXPECT (grep CSS)

| Key | Value |
|-----|-------|
| `page_title` | `CampusMart — CSS (Buoi 3)` |
| `--cm-primary` / `--cm-bg` | `#0f766e` / `#f8fafc` |
| `box-sizing` | `border-box` |
| `.cm-table` | `border-collapse` + zebra `nth-child(even)` |
| `.cm-header` | dùng `var(--cm-primary)` |
| `:focus` | outline hiện hữu (không `outline: none` chay) |

---

## P04 — Flexbox & Grid

### CLO

1. `.cm-header` flex `space-between`; `.cm-nav` flex + gap.  
2. `.cm-grid` = `repeat(auto-fill, minmax(220px, 1fr))`.  
3. 8 `.cm-card` đủ data + `data-sku`; testid `cm-product-table` chuyển lên container lưới (duy nhất).

### EXPECT

| Key | Value |
|-----|-------|
| `page_title` | `CampusMart — Layout (Buoi 4)` |
| `css..cm-header` | `display: flex` + `space-between` |
| `css..cm-grid` | `repeat(auto-fill, minmax(` |
| `card_count` | 8 (`.cm-card`, đủ SKU) |
| `card.category` | chữ, không số thô |
| `testid.cm-product-table` | đúng 1 (trên `.cm-grid`) |

---

## P05 — Responsive & components

### CLO

1. Mobile-first + 2 query `min-width: 640px` / `1024px`.  
2. Grid 1 → 2 → 4 cột; nav dọc → ngang.  
3. Hero + `.cm-btn`; main `max-width: 1120px` desktop; bảng cuộn ngang.

### EXPECT

| Key | Value |
|-----|-------|
| `page_title` | `CampusMart — Responsive (Buoi 5)` |
| `css.@media` | `(min-width: 640px)` và `(min-width: 1024px)` |
| `grid.cols` | mặc định `1fr`; 640→`repeat(2, 1fr)`; 1024→`repeat(4, 1fr)` |
| `hero.heading` | `Do choi cong nghe cho sinh vien` |
| `hero.btn` | `Xem catalog` |
| `main.max-width` | `1120px` |
| Video | 3 khung 375 / 700 / 1200px |

---

## P06 — JS nền: dữ liệu & chỉ số

### CLO

1. `data.js` (canonical §5, không export) + `app.js`.  
2. 5 hàm: `lineTotal`, `inventoryValue`, `stockLevel`, `findProductBySku`, `countByCategory`.  
3. Stats sống trên DOM; báo cáo 3 DM Console; badge stockLevel trên card.

### EXPECT

| Key | Value |
|-----|-------|
| `page_title` | `CampusMart — JS (Buoi 6)` |
| `inventoryValue(products)` | 41380000 |
| `stockLevel(10)/(3)/(1)` | `Du` / `Sap het` / `Can nhap` |
| `findProductBySku("MN-02").name` | `LG UltraFine` |
| `countByCategory(2)` | 3 |
| `report` | Ban phim 3/17620000 · Chuot 3/8860000 · Man hinh 2/14900000 |
| `stats.text` | `So san pham = 8` + `Tong gia tri kho = 41380000` |
| `badge_count` | 8, đúng bảng stockLevel §3 |

---

## P07 — Render từ mảng + Filter

### CLO

1. Grid rỗng trong HTML nguồn; `render(list)` sinh card (createElement + textContent).  
2. Filter select `cm-filter-category`; `cm-visible-count`.  
3. Sort giá bằng bản sao; delegation click log SKU.

### EXPECT

| Key | Value |
|-----|-------|
| `page_title` | `CampusMart — Filter (Buoi 7)` |
| `filter_counts` | all=8, 1=3, 2=3, 3=2 (đúng SKU nhóm) |
| `visible_count.text` | `Hien thi: N san pham` |
| `sort.first_sku` | `MS-03` (Tat ca + Gia tang dan) |
| `sort.immutable` | mảng gốc giữ thứ tự |
| `card.badge` | stockLevel exact |

---

## P08 — Module, localStorage, Dashboard có Form Thêm ★ mốc JS thuần

### CLO

1. 3 module export/import; 1 entry `type="module"`.  
2. `cm_filter` persist qua F5.  
3. Validate form đăng ký 2 quy tắc + lưu `cm_subscribers`.  
4. **Form Thêm sản phẩm** (`cm-product-form`): validate 3 quy tắc → thêm → render + stats tự cộng (ca MN-03).

### EXPECT

| Key | Value |
|-----|-------|
| `page_title` | `CampusMart — Module & Storage (Buoi 8)` |
| `modules` | data.js, helpers.js, app.js |
| `persist` | chọn DM → F5 → giữ select + card |
| `validate.name` | `Ten toi thieu 2 ky tu` |
| `validate.email` | `Email khong hop le` |
| `validate.ok` | `Dang ky thanh cong` + append `cm_subscribers` |
| `create.empty` | `Thieu thong tin bat buoc` |
| `create.price` | `Gia phai lon hon 0` |
| `create.dup` | `SKU da ton tai` (thử KB-01) |
| `create.MN-03` | 9 card · Man hinh=3 · `Tong gia tri kho = 50380000` |
| Giữ P07 | render/filter/sort nguyên vẹn |
| Điểm cộng | login giả: admin/`CampusMart@01` → `cm_auth="true"` + `Dang nhap thanh cong`; sai → `Sai tai khoan hoac mat khau` |

---

## P09 — React: Component + Props

### CLO

1. Vite React chạy; title đúng.  
2. ≥ 5 component: Header, CategoryList, ProductGrid, ProductCard, Stats.  
3. `map` + `key={p.sku}` không warning; stats tính từ mảng.

### EXPECT

| Key | Value |
|-----|-------|
| `page_title` | `CampusMart — React (Buoi 9)` |
| `testid.cm-brand` | `CampusMart` |
| `testid.cm-category-list` | 3 li |
| `card_count` | 8, đủ `data-sku`, DM chữ |
| `key` | sku — console sạch warning |
| `stats` | `So san pham = 8` + `Tong gia tri kho = 41380000` |
| `repo` | không có node_modules |

---

## P10 — React State: Filter + Search

### CLO

1. Controlled select/input; state ở App; FilterBar tách riêng.  
2. `visible` là derived (không state mảng lọc).  
3. Filter + search AND; sort toggle.

### EXPECT

| Key | Value |
|-----|-------|
| `page_title` | `CampusMart — React State (Buoi 10)` |
| `filter` | 8 / 3 / 3 / 2 |
| `search.ra` | 2 (MS-02, MN-02) |
| `search.keychron` / `.xyz` | 1 / 0 |
| `combine.chuot+ra` | 1 (MS-02) |
| `sort.first_sku` | `MS-03` |
| `search.placeholder` | `Tim theo ten...` (`cm-search`) |
| `visible_count` | luôn khớp số card |

---

## P11 — React Create + Validate

### CLO

1. `products` = state (seed CORE_8); form object state + 1 handleChange.  
2. Validate 3 quy tắc chuỗi exact.  
3. Create bất biến; ca MN-03 khớp 3 số; form reset.

### EXPECT

| Key | Value |
|-----|-------|
| `page_title` | `CampusMart — React Create (Buoi 11)` |
| `testid.cm-product-form` | 5 field controlled; nút `Them san pham` |
| `validate.empty` | `Thieu thong tin bat buoc` |
| `validate.price` | `Gia phai lon hon 0` |
| `validate.dup` | `SKU da ton tai` (thử KB-01) |
| `create.MN-03` | count=9 · Man hinh=3 · inventory=**50380000** |
| `immutable` | `[...products, item]` — không push |

---

## P12 — React CRUD 2 thực thể ★ ĐÍCH

### CLO

1. Delete (confirm + filter) và Update (map, form chung, nhãn `Cap nhat san pham`).  
2. `categories` state: thêm DM (`cm-category-form`), lan tỏa 2 select.  
3. Chặn xóa DM còn SP — thông báo exact; xóa được DM rỗng.  
4. Kịch bản 5 bước §8.8 khớp từng số.

### EXPECT (kịch bản tuần tự từ CORE_8)

| Step | Thao tác | Result |
|------|----------|--------|
| 1 | Delete MN-02 | 7 card · `Tong gia tri kho = 32880000` |
| 2 | Edit KB-01 qty 3→4 | `Tong gia tri kho = 34770000` |
| 3 | Create category `Phu kien` | 2 select đều 4 DM |
| 4 | Delete category `Chuot` | chặn: `Khong the xoa danh muc con san pham` |
| 5 | Delete category `Phu kien` | OK → 3 DM |

| Key phụ | Value |
|---------|-------|
| `page_title` | `CampusMart — React CRUD (Buoi 12)` |
| `card.buttons` | `Sua` / `Xoa` |
| `edit.label` | `Cap nhat san pham` + nút `Huy` |
| `category.dup` | `Danh muc da ton tai` |
| `immutable` | map/filter — không splice |

---

## Ghi chú nhất quán chốt

| Chủ đề | Quy ước |
|--------|---------|
| Nền CORE_8 | P01–P07 + đầu P08/P11/P12: 8 SKU, tổng **41380000** |
| Sau Create MN-03 (P08 JS thuần & P11 React — cùng ca kiểm) | 9 SP, tổng **50380000** |
| Kịch bản P12 | từ CORE_8 (không mang MN-03 sang): 7 SP / 32880000 → 34770000 |
| Mốc đầu ra | P08 = **Dashboard CRUD có Form Thêm (JS thuần)** · P12 = **ứng dụng React CRUD hoàn chỉnh 2 thực thể** |
| Prefix marker | `CM_EXPECT` |
| Credential | `admin` / `CampusMart@01` |
| stockLevel | `Du` (≥5) / `Sap het` (≥2) / `Can nhap` |
