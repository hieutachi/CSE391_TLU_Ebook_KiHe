# BUỔI 10: State & Sự kiện — List/Filter CampusMart bằng React

> **Thời lượng gợi ý:** 3–4 giờ tự học (lý thuyết + thử máy + Phiếu 10)  
> **Tiên quyết:** Buổi 9 (cây component CampusMart tĩnh)  
> **Kết quả đầu ra:** `useState`; controlled select/input; derived filter+search; lifting state up (`FilterBar`)  
> **Phiếu học tập:** [Phiếu 10](./worksheets/phieu-10-react-state-filter.md) — chuỗi **CampusMart UI**  
> **Dataset:** [CANONICAL §3 filter, §8.6 search](./worksheets/CANONICAL-DATA.md) — title P10

---

## 0. Gợi ý lịch trình giảng (cho GV)

| Phút | Nội dung | Gợi ý thao tác |
|------|----------|----------------|
| 0–10 | Ôn P09 + hỏi “Bấm lọc danh mục — DOM ai cập nhật?” | Chiếu 8 card tĩnh |
| 10–25 | Hình ảnh nhớ: vòng lặp state; so biến thường vs state | Live: gán `n=n+1` hỏng |
| 25–40 | `useState` + Counter 2 phút | SV cùng gõ |
| 40–55 | Controlled select filter 8/3/3/2 | Đọc to count |
| 55–70 | Derived `visible` — **không** state mảng lọc | So P07 `render()` |
| 70–85 | Search `ra`=2; AND với filter | CANONICAL §8.6 |
| 85–100 | Lifting state — tách FilterBar | Vẽ ASCII props xuống / callback lên |
| 100–120 | **Phiếu 10 mục 2** | Checkpoint filter + count |

> Ưu tiên lớp: controlled filter + derived + visible count. Search/sort/FilterBar có thể về nhà.

---

## 1. Mục tiêu buổi học

Sau Buổi 10, bạn có thể:

1. Khai báo state với `useState`; giải thích vì sao không sửa biến thường.
2. Bắt sự kiện React: `onChange`, `onClick`; đọc `e.target.value`.
3. Viết **controlled** select/input (`value` + `onChange`).
4. Tính **derived data**: lọc/tìm từ state ngay trong render — không state danh sách thừa.
5. Kết hợp filter + search (AND); sort giá bằng state.
6. **Lifting state up**: tách `FilterBar`, state ở `App`, con nhận props + callback.

---

## 2. Nhắc lại — từ props tĩnh sang UI sống

```
Buổi 9:  products = const  →  UI tĩnh (đẹp nhưng chưa lọc)
Buổi 10: categoryId, keyword = state  →  UI đổi khi bấm/gõ
         products vẫn const (seed) — đổi mảng chờ Buổi 11
```

**Hình ảnh nhớ chuyển pha:**

```
  P09:  UI = f(data_cố_định)
  P10:  UI = f(data_cố_định, state_tương_tác)
  P11:  UI = f(state_products, state_form, ...)
```

---

## 3. Hình ảnh nhớ — vòng lặp sống của React

### 3.1. Dán đầu vở

```
┌────────────────────────────────────────────────────────┐
│           VÒNG LẶP SỐNG CỦA REACT                      │
│                                                        │
│     render UI  ◄──────────────────┐                    │
│        │                          │                    │
│        ▼                          │                    │
│   người dùng bấm / gõ        setState(mới)             │
│        │                          ▲                    │
│        └── event handler ─────────┘                    │
│                                                        │
│   UI = f(state)  — state đổi thì f chạy lại            │
└────────────────────────────────────────────────────────┘
```

### 3.2. Biến thường vs state

```
   biến thường:  thay đổi ──► React không biết ──► UI đứng im
   state:        setX(...) ──► React render lại ──► UI khớp
```

Thử phá (bắt buộc cảm nhận):

```jsx
// SAI — bấm không nhúc nhích
let n = 0;
<button onClick={() => { n = n + 1; }}>Bam</button>

// ĐÚNG
const [n, setN] = useState(0);
<button onClick={() => setN(n + 1)}>Bam</button>
```

---

## 4. So sánh P07 JS thuần vs P10 React (cùng bài filter)

| Việc | P07 DOM | P10 React |
|------|---------|-----------|
| Lưu filter đang chọn | Biến / `select.value` | `useState("all")` |
| Khi đổi select | Listener → `render(filtered)` | `setCategoryId` → tự render |
| Xóa grid cũ | `innerHTML = ""` hoặc replaceChildren | Không cần — `map` lại `visible` |
| Đếm “Hien thi: N” | Tự cập nhật textContent | `{visible.length}` trong JSX |
| Search | Thêm listener + gọi lại render | Thêm state `keyword` + derived |

```
  P07:  data đổi ──► BẠN gọi render()
  P10:  setState  ──► React gọi f(state) ──► vá DOM
```

> Đây là “trả công” cho việc học JS thuần trước: bạn **hiểu** DOM đang xảy ra gì; React chỉ **giấu** phần vá tay.

---

## 5. `useState` — kho trạng thái

```jsx
import { useState } from "react";

function App() {
  //        giá trị hiện tại ──┐   ┌── hàm ĐỔI (set + tên)
  const [categoryId, setCategoryId] = useState("all");
}
```

Ba quy tắc:

| Quy tắc | Lý do |
|---------|-------|
| Chỉ đổi qua setter | Kênh duy nhất React lắng nghe |
| Không gọi hook trong if/vòng lặp | React đếm hook theo thứ tự |
| State bất biến — mảng/object tạo **bản mới** | Nền P11–P12 (`[...arr]`, `map`, `filter`) |

---

## 6. Controlled select — nguồn sự thật duy nhất

```jsx
function App() {
  const [categoryId, setCategoryId] = useState("all");

  return (
    <>
      <label htmlFor="filter-cat">Loc theo danh muc</label>
      <select
        id="filter-cat"
        data-testid="cm-filter-category"
        value={categoryId}
        onChange={(e) => setCategoryId(e.target.value)}
      >
        <option value="all">Tat ca</option>
        <option value="1">Ban phim</option>
        <option value="2">Chuot</option>
        <option value="3">Man hinh</option>
      </select>
    </>
  );
}
```

```
  state ──value──► UI (select hiển thị đúng option)
    ▲
    └── onChange (gõ/bấm ghi lại state)

  = CONTROLLED COMPONENT
  (P11 form nhiều field cũng cùng pattern)
```

Nhớ: `for` → `htmlFor` trong JSX.

---

## 7. Derived data — lọc trong render, không state thứ hai

### 7.1. Sai lầm phổ biến

```jsx
// SAI hướng: state filteredProducts + useEffect đồng bộ — thừa & dễ lệch
const [filtered, setFiltered] = useState(products);
```

### 7.2. Đúng — tính mỗi lần render

```jsx
function App() {
  const [categoryId, setCategoryId] = useState("all");
  const [keyword, setKeyword] = useState("");

  const visible = products
    .filter((p) => categoryId === "all" || p.category_id === Number(categoryId))
    .filter((p) => p.name.toLowerCase().includes(keyword.toLowerCase()));

  return (
    <main>
      <input
        type="search"
        data-testid="cm-search"
        placeholder="Tim theo ten..."
        value={keyword}
        onChange={(e) => setKeyword(e.target.value)}
      />
      <p data-testid="cm-visible-count">
        Hien thi: {visible.length} san pham
      </p>
      <ProductGrid products={visible} />
    </main>
  );
}
```

### 7.3. Hình ảnh nhớ derived

```
  state:    categoryId="2"     keyword="ra"
                 │                 │
                 └────── AND ──────┘
                          │
                          ▼
  visible = products.filter(DM).filter(tên)   ← derived, tự đúng
                          │
                          ▼
              map → card  +  "Hien thi: N san pham"
```

### 7.4. Số liệu CANONICAL (tự kiểm)

**Filter (§3):**

| Filter | visible_count |
|--------|--------------:|
| Tat ca / `all` | **8** |
| Ban phim / `1` | **3** |
| Chuot / `2` | **3** |
| Man hinh / `3` | **2** |

**Search (§8.6)** — case-insensitive `includes`:

| Query | SKU | count |
|-------|-----|------:|
| `ra` | MS-02, MN-02 | **2** |
| `keychron` | KB-01 | **1** |
| `xyz` | — | **0** |

**Kết hợp:** Chuot + `ra` → **1** (MS-02).

---

## 8. Sort giá (state boolean)

```jsx
const [sortAsc, setSortAsc] = useState(false);

const shown = sortAsc
  ? [...visible].sort((a, b) => a.price - b.price)
  : visible;

// Nút:
<button
  data-testid="cm-sort-price"
  onClick={() => setSortAsc((v) => !v)}
>
  Gia tang dan
</button>
```

Tat ca + sort bật → card đầu **MS-03** (giá 250000 thấp nhất CORE_8).

> Spread `[...visible]` trước `sort` — `sort` mutate mảng; đừng mutate `visible` trực tiếp.

---

## 9. Lifting state up — FilterBar

### 9.1. Hình ảnh nhớ

```
        App  ─── state: categoryId, keyword, sortAsc ───┐
         │ props xuống (giá trị)                        │
         ▼                                              │
     FilterBar                                          │
         │ onCategoryChange / onKeywordChange           │
         └──────── callback gọi ngược ──────────────────┘
```

Dữ liệu xuống bằng props, tín hiệu lên bằng callback — mô hình một chiều của React.

### 9.2. Code khung

```jsx
function FilterBar({ categoryId, keyword, onCategoryChange, onKeywordChange }) {
  return (
    <div className="cm-filter-bar">
      <select
        data-testid="cm-filter-category"
        value={categoryId}
        onChange={(e) => onCategoryChange(e.target.value)}
      >
        {/* 4 option */}
      </select>
      <input
        type="search"
        data-testid="cm-search"
        placeholder="Tim theo ten..."
        value={keyword}
        onChange={(e) => onKeywordChange(e.target.value)}
      />
    </div>
  );
}

// App:
<FilterBar
  categoryId={categoryId}
  keyword={keyword}
  onCategoryChange={setCategoryId}
  onKeywordChange={setKeyword}
/>
```

### 9.3. Cây component sau P10

```
App (state: categoryId, keyword, sortAsc)
├── Header
├── FilterBar          ← props + callbacks
├── Stats
├── p cm-visible-count
└── ProductGrid (products={shown})
```

---

## 10. Thử ngay trên máy

Copy project P09 → `cse391-cm-10`, `npm install`, đổi title:

`CampusMart — React State (Buoi 10)`

### Thử 1 — Counter phá / đúng

| Bước | Dự đoán | Quan sát |
|------|---------|----------|
| `n = n + 1` trong onClick | UI không đổi | Đứng im |
| `setN(n + 1)` | Tăng | Đổi số |

### Thử 2 — Filter 8/3/3/2

| Option | Dự đoán count | Quan sát |
|--------|--------------:|----------|
| Tat ca | 8 | `cm-visible-count` |
| Ban phim | 3 | 3 card |
| Chuot | 3 | 3 card |
| Man hinh | 2 | 2 card |

### Thử 3 — Search §8.6

| Gõ | Dự đoán | Quan sát |
|----|---------|----------|
| `ra` | 2 card | MS-02, MN-02 |
| `keychron` | 1 | KB-01 |
| `xyz` | 0 + `Hien thi: 0 san pham` | Count |

### Thử 4 — AND

Chuot + `ra` → đúng 1 card MS-02.

### Thử 5 — Controlled thiếu onChange

Có `value={keyword}` bỏ `onChange` → không gõ được. Sửa lại đủ cặp.

---

## 11. Câu hỏi tự kiểm tra

1. Vì sao gán `categoryId = "2"` không làm UI đổi?
2. Controlled component là gì (một câu)?
3. Derived data khác state chỗ nào?
4. Vì sao cần `Number(categoryId)`?
5. Search quên `toLowerCase` một vế → hậu quả?
6. Lifting state up dùng khi nào?
7. Title P10 exact?
8. Sort tại sao `[...visible].sort` chứ không `visible.sort`?

---

## 12. Gợi ý đáp án

1. React chỉ lắng nghe setter.  
2. UI đọc từ state (`value`) và ghi bằng event (`onChange`).  
3. Derived tính được từ state/data có sẵn — không cần lưu riêng.  
4. Tránh `"2" === 2` so sánh trượt.  
5. Lệch hoa/thường → miss kết quả (vd `Ra` vs `ra`).  
6. Hai (nhiều) component cần chung một state → đặt state ở cha chung.  
7. `CampusMart — React State (Buoi 10)`.  
8. `sort` mutate; spread tạo bản sao.

---

## 13. Lỗi thường gặp

| # | Triệu chứng | Nguyên nhân | Cách xử lý |
|---|-------------|-------------|------------|
| 1 | UI không cập nhật | Gán thẳng biến state | Luôn qua setter |
| 2 | Filter trượt | `"2" === 2` | `Number(categoryId)` |
| 3 | Search lệch case | Quên `toLowerCase` | Chuẩn hóa 2 vế |
| 4 | Select đóng băng | Có value thiếu onChange | Đủ cặp controlled |
| 5 | Render vô hạn | Setter trong thân render | Chỉ trong handler |
| 6 | `for` warning | Attribute HTML | `htmlFor` |
| 7 | Count ≠ số card | Hard-code count | `{visible.length}` |
| 8 | Sort phá mảng gốc | `visible.sort` | `[...visible].sort` |
| 9 | Title sai | Quên đổi P10 | §8.2 |
| 10 | State ở FilterBar cô lập | Quên lift | State ở App |

---

## 14. Checklist trước Buổi 11

- [ ] Giải thích vòng: event → setState → render
- [ ] Controlled select + search
- [ ] `visible` derived — không state `filteredProducts`
- [ ] Filter 8/3/3/2 + search `ra`=2
- [ ] FilterBar lifting state up
- [ ] Xong [Phiếu 10](./worksheets/phieu-10-react-state-filter.md)

---

## 15. Bài tập (chuỗi CampusMart UI)

| Phần | Làm gì | Chi tiết |
|------|--------|----------|
| **Trên lớp (~20')** | State filter + visible count | [Phiếu 10 — mục 2](./worksheets/phieu-10-react-state-filter.md) |
| **Về nhà (~25–40')** | Search + sort + FilterBar | [Phiếu 10 — mục 3](./worksheets/phieu-10-react-state-filter.md) |

Repo: `cse391-cm-10` + video OBS.

---

## 16. Cầu nối sang Buổi 11

```
Buổi 10: lọc/tìm được — products vẫn const
Buổi 11: products → state + form Create + validate (cùng ca MN-03 với P08)
```

→ [Buổi 11](./11-buoi-11-react-form-create.md) · [Phiếu 11](./worksheets/phieu-11-react-create.md)

---

*CSE391 – Ebook Frontend CampusMart UI | Buổi 10/12 — bản viết lại CSE485*


---

## 17. Ví dụ App tối thiểu (tham khảo — tự gõ lại)

```jsx
import { useState } from "react";
import { products as seed } from "./data.js";

export default function App() {
  const [categoryId, setCategoryId] = useState("all");
  const [keyword, setKeyword] = useState("");
  const [sortAsc, setSortAsc] = useState(false);

  let visible = seed
    .filter((p) => categoryId === "all" || p.category_id === Number(categoryId))
    .filter((p) => p.name.toLowerCase().includes(keyword.toLowerCase()));
  if (sortAsc) visible = [...visible].sort((a, b) => a.price - b.price);

  return (
    <main>
      {/* CM_EXPECT filter_counts=8,3,3,2 search_ra=2 first_sorted_sku=MS-03 */}
      <select
        data-testid="cm-filter-category"
        value={categoryId}
        onChange={(e) => setCategoryId(e.target.value)}
      >
        <option value="all">Tat ca</option>
        <option value="1">Ban phim</option>
        <option value="2">Chuot</option>
        <option value="3">Man hinh</option>
      </select>
      <input
        data-testid="cm-search"
        placeholder="Tim theo ten..."
        value={keyword}
        onChange={(e) => setKeyword(e.target.value)}
      />
      <button data-testid="cm-sort-price" onClick={() => setSortAsc((v) => !v)}>
        Gia tang dan
      </button>
      <p data-testid="cm-visible-count">Hien thi: {visible.length} san pham</p>
      {/* ProductGrid products={visible} — mang từ P09 */}
    </main>
  );
}
```

> Đây là **khung tư duy**, không phải đáp án copy nguyên xi. Phiếu 10 bắt buộc tách `FilterBar` + giữ Header/Stats/ProductCard.

---

## 18. So sánh trang — trước / sau Buổi 10

| Tiêu chí | P09 | P10 |
|----------|-----|-----|
| `<title>` | React (Buoi 9) | React State (Buoi 10) |
| Filter | Không | Controlled 8/3/3/2 |
| Search | Không | `cm-search`, §8.6 |
| `cm-visible-count` | Không bắt buộc | Luôn khớp số card |
| FilterBar | — | Component riêng, state ở App |
| products | const import | Vẫn const (P11 mới thành state) |


---

## 19. Walkthrough từng phím — search `ra`

Ghi vào vở khi demo:

```
1. categoryId = "all", keyword = ""
   visible = 8 card

2. Gõ "r"
   filter tên includes "r"
   → nhiều card (Razer, UltraFine, Keychron? "keychron" có "r"? có — KeychRon)
   (không khóa EXPECT ở bước giữa — chỉ khóa chuỗi đủ "ra")

3. Gõ thêm "a" → keyword = "ra"
   MS-02 Razer Viper  ✓
   MN-02 LG UltraFine ✓  (chữ "ra" trong Ultra)
   → count = 2  ← EXPECT §8.6

4. Đổi select → Chuot (value "2")
   visible = trong 2 card trên, chỉ còn category_id === 2
   → MS-02 alone, count = 1
```

---

## 20. Debug checklist 5 phút (khi filter “sai số”)

```
[ ] select value có đúng "all"|"1"|"2"|"3"?
[ ] đã Number(categoryId) khi so với p.category_id?
[ ] keyword đã toLowerCase cả 2 vế?
[ ] ProductGrid nhận products={visible} chứ không import cứng seed?
[ ] cm-visible-count dùng visible.length cùng mảng đang map?
[ ] React DevTools: categoryId / keyword có đổi khi thao tác?
```

---

## 21. Liên hệ CSE485 (nhìn trước)

| CSE391 P10 | CSE485 sau này |
|------------|----------------|
| Filter client trên mảng | Filter query `?category=` trên server |
| Derived `visible` | Collection đã filter từ Controller |
| Search `includes` | `LIKE %keyword%` / Scout |
| State ở App | “Source of truth” gần với session/request |

> Học derived + controlled hôm nay = đỡ bỡ ngỡ khi form/filter Laravel render từ biến PHP.

---

## 22. Bài tập tư duy (không nộp)

1. Nếu để `filteredProducts` là state và cập nhật trong `onChange` — nêu 1 cách UI lệch data.  
2. Vì sao P09 cố ý chưa dạy state?  
3. Vẽ lại cây App sau khi thêm `sortAsc`.  
4. Viết bằng lời: `UI = f(seedProducts, categoryId, keyword, sortAsc)`.

---

## 23. Tóm tắt một trang (ôn nhanh)

```
useState          → khai báo state + setter
controlled        → value + onChange
derived visible   → filter/filter trong render
AND               → DM ∩ tên
lifting           → state App, FilterBar callback
CANONICAL         → 8/3/3/2 · ra=2 · sort MS-03
title             → CampusMart — React State (Buoi 10)
```

---

## 24. Marker & EXPECT máy (nhắc)

```jsx
{/* CM_EXPECT filter_counts=8,3,3,2 search_ra=2 first_sorted_sku=MS-03 */}
```

| Key | Value |
|-----|-------|
| filter | 8 / 3 / 3 / 2 |
| search.ra | 2 |
| sort first | MS-03 |
| placeholder | `Tim theo ten...` |
| derived | không state mảng lọc trùng |

---

*Hết phần mở rộng ôn tập Buổi 10.*


---

## 25. Mini-lab: dự đoán trước khi chạy (in phiếu nháp)

Với CORE_8, điền cột “Dự đoán” trước, rồi chạy app đối chiếu:

| # | categoryId | keyword | sortAsc | Dự đoán count | Dự đoán SKU đầu (nếu sort) | Thực tế |
|---|------------|---------|---------|---------------|------------------------------|---------|
| 1 | all | (rỗng) | false | | — | |
| 2 | 1 | (rỗng) | false | | — | |
| 3 | all | ra | false | | — | |
| 4 | 2 | ra | false | | — | |
| 5 | all | (rỗng) | true | | MS-03? | |
| 6 | 3 | ultra | false | | MN-02? | |

> Lab này rèn thói quen CSE485: **dự đoán → chạy → quan sát** — không bấm thử mò.

---

## 26. FAQ nhanh

**Q: Có cần `useEffect` để lọc không?**  
A: Không cho bài P10. Lọc là derived trong render.

**Q: Đặt `products` vào state từ P10 luôn được không?**  
A: Được kỹ thuật, nhưng phiếu cố ý để P11 mới chuyển — để bạn thấy rõ “đọc data” vs “đổi data”.

**Q: FilterBar có được chứa nút sort không?**  
A: Được nếu vẫn lift state `sortAsc` lên App (hoặc truyền callback). Đừng để sortAsc chỉ sống trong FilterBar nếu ProductGrid ở ngoài cần biết.


---

## 27. Nhắc title & repo

| | Exact |
|--|-------|
| Title | `CampusMart — React State (Buoi 10)` |
| Repo | `cse391-cm-10` |
| Placeholder search | `Tim theo ten...` |
| Count format | `Hien thi: {N} san pham` |

*Đủ độ dài mục tiêu Buổi 10 (650–800 dòng khi ghép toàn file).*
