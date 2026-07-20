# Phiếu 10 — React State: Filter + Search CampusMart

| | |
|---|---|
| **Buổi** | 10 |
| **Thời lượng** | ≥ 45 phút (trên lớp ~20' + về nhà ~25–40') |
| **Repo** | `cse391-cm-10` (project Vite — copy từ P09) |
| **Nhận từ Phiếu 09** | Cây component tĩnh 8 card |
| **Mang sang Phiếu 11** | State + controlled input — nền cho form Create |
| **Dataset** | [`CANONICAL-DATA.md`](./CANONICAL-DATA.md) — filter §3, search §8.6 |

### Chuẩn đầu ra (CLO)

1. `useState` cho filter danh mục; select controlled (`value` + `onChange`).  
2. Danh sách hiển thị là **derived data** — không có state danh sách trùng lặp.  
3. Filter đúng 8/3/3/2; search đúng bảng §8.6; hai điều kiện kết hợp AND.  
4. `cm-visible-count` luôn khớp số card đang hiện.  
5. Tách `FilterBar` — lifting state up (state ở `App`, con nhận props + callback).

---

## 1. Tóm tắt lý thuyết (đọc lại trước khi code)

- `const [x, setX] = useState(banDau)`; chỉ đổi qua setter.
- Controlled: `value={state}` + `onChange={(e) => setState(e.target.value)}`.
- Derived: `visible = products.filter(...).filter(...)` tính trong render.
- `Number()` khi so id; `toLowerCase()` hai vế khi search.
- Lifting: state cha + callback xuống FilterBar.

Chi tiết: [Buổi 10](../10-buoi-10-react-state-filter.md).

---

## 2. Bài trên lớp (~20 phút) — Filter danh mục bằng state

### Yêu cầu

Copy project P09 sang `cse391-cm-10` (`npm install` lại), rồi:

1. `index.html`: đổi `<title>` exact: **`CampusMart — React State (Buoi 10)`**.
2. Trong `App`: state `categoryId` (khởi tạo `"all"`).
3. Select controlled `data-testid="cm-filter-category"` — 4 option đúng nhãn: `Tat ca`, `Ban phim`, `Chuot`, `Man hinh` (value `all/1/2/3`).
4. `visible` = derived filter; `ProductGrid` nhận `products={visible}` qua props.
5. `<p data-testid="cm-visible-count">Hien thi: {visible.length} san pham</p>`.

### Cách thử trên lớp (OBS)

| # | Thử | Dự đoán | Quan sát |
|---|-----|---------|----------|
| T1 | Title | `CampusMart — React State (Buoi 10)` | Tab |
| T2 | Tat ca | 8 card + `Hien thi: 8 san pham` | UI + count |
| T3 | Ban phim | 3 card / count 3 | |
| T4 | Chuot | 3 / 3 | |
| T5 | Man hinh | 2 / 2 | |
| T6 | Không F5 khi đổi select | SPA | Network / mắt nhìn |

**Checkpoint giảng viên:** đổi select → card + count đổi, không reload.

---

## 3. Bài về nhà (~25–40 phút) — Search + sort + FilterBar

Tiếp tục **cùng project**.

### Nhiệm vụ A — Ô tìm kiếm theo tên

1. State `keyword` (chuỗi rỗng); `input type="search"` controlled, `placeholder` exact: `Tim theo ten...`, `data-testid="cm-search"`.
2. `visible` kết hợp **AND** (danh mục + `name.toLowerCase().includes(keyword.toLowerCase())`).
3. Tự kiểm theo [§8.6](./CANONICAL-DATA.md):

| Thao tác | Kết quả |
|----------|--------|
| Tat ca + gõ `ra` | 2 card (MS-02, MN-02) |
| Tat ca + `keychron` | 1 card (KB-01) |
| Chuot + `ra` | 1 card (MS-02) |
| `xyz` | 0 card + count `Hien thi: 0 san pham` |

#### Cách thử Nhiệm vụ A (OBS)

| # | Thử | Kỳ vọng |
|---|-----|---------|
| A1 | Gõ `ra` chậm từng chữ | Count về 2 khi đủ `ra` |
| A2 | Xóa keyword | Về 8 (nếu Tat ca) |
| A3 | Chuot + `ra` | Đúng 1 card MS-02 |
| A4 | `xyz` | 0 card, đọc to count 0 |

### Nhiệm vụ B — Sort giá bằng state

1. State `sortAsc` (boolean, mặc định `false`); nút `Gia tang dan` (`data-testid="cm-sort-price"`).
2. Click → toggle `sortAsc`; nếu true thì `[...visible].sort((a,b) => a.price - b.price)`.
3. Tat ca + sort → card đầu **MS-03**.

#### Cách thử Nhiệm vụ B

| # | Thử | Kỳ vọng |
|---|-----|---------|
| B1 | Tat ca + bật sort | Card đầu `data-sku="MS-03"` |
| B2 | Tắt sort | Thứ tự seed trở lại |
| B3 | Không dùng `visible.sort` trực tiếp | Mảng gốc không bị mutate |

### Nhiệm vụ C — Tách FilterBar (lifting state up)

Component `FilterBar({ categoryId, keyword, onCategoryChange, onKeywordChange })` chứa select + search. State vẫn ở `App`.

```
App (state: categoryId, keyword, sortAsc)
├── Header
├── FilterBar   ← props + callbacks
├── Stats
└── ProductGrid (products=visible đã sort)
```

#### Cách thử Nhiệm vụ C

| # | Thử | Kỳ vọng |
|---|-----|---------|
| C1 | Đổi filter trong FilterBar | App re-render, grid đổi |
| C2 | React DevTools | State nằm ở App, không ở FilterBar |
| C3 | Đọc code | FilterBar không `useState` cho category/keyword |

### Nhiệm vụ D — Marker

```jsx
{/* CM_EXPECT filter_counts=8,3,3,2 search_ra=2 first_sorted_sku=MS-03 */}
```

#### Cách thử D

Search source → đủ 3 nhóm key trong comment.

---

## 4. OUTPUT / EXPECT

| Key | Value bắt buộc |
|-----|----------------|
| `page_title` | `CampusMart — React State (Buoi 10)` |
| `filter.all/1/2/3` | 8 / 3 / 3 / 2 card |
| `search.ra` | 2 card (MS-02, MN-02) |
| `search.keychron` | 1 card |
| `search.xyz` | 0 card + `Hien thi: 0 san pham` |
| `combine.chuot+ra` | 1 card (MS-02) |
| `sort.first_sku` | `MS-03` khi Tat ca + sort |
| `derived` | không có state mảng `filteredProducts` |
| `FilterBar` | component riêng, state ở App |
| `visible_count` | luôn = số card render |

### Checklist tự chấm

```
[ ] title P10
[ ] filter 8/3/3/2
[ ] search ra/keychron/xyz
[ ] Chuot+ra = 1
[ ] sort → MS-03
[ ] FilterBar lift
[ ] CM_EXPECT
```

---

## 5. Box nộp bài

```
╔══════════════════════════════════════════════════════════════╗
║  NỘP PHIẾU 10                                                ║
╠══════════════════════════════════════════════════════════════╣
║  1. GitHub repo RIÊNG: cse391-cm-10 (KHÔNG node_modules)     ║
║  2. Video OBS + camera mặt — kịch bản:                       ║
║     - 4 option filter → đọc to count 8/3/3/2                 ║
║     - Gõ "ra" → 2 card; Chuot + "ra" → 1 card                ║
║     - "xyz" → 0 card; sort → MS-03 đứng đầu                  ║
║  3. Thuyết trình 30–60s: derived data là gì, sao không       ║
║     cần state cho danh sách đã lọc?                          ║
╚══════════════════════════════════════════════════════════════╝
```

---

## 6. Lỗi thường gặp khi nộp

| Lỗi | Hậu quả | Cách tránh |
|-----|---------|------------|
| `"2" === 2` | Sai count DM | `Number(categoryId)` |
| State `filteredProducts` | Trừ derived | Chỉ tính trong render |
| Sort mutate | Bug lạ | `[...visible].sort` |
| Title vẫn Buoi 9 | page_title fail | §8.2 P10 |
| Search phân biệt hoa thường | Sai §8.6 | `toLowerCase` 2 vế |

---

*CSE391 – Phiếu 10 | CampusMart UI — bản viết lại CSE485*
