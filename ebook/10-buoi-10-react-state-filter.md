# BUỔI 10: State & Sự kiện — List/Filter CampusMart bằng React

> **Thời lượng gợi ý:** 4 giờ tự học  
> **Tiên quyết:** Buổi 9 (cây component CampusMart tĩnh)  
> **Kết quả đầu ra:** Dùng `useState`, bắt sự kiện React, filter danh mục + search theo tên chạy bằng state, hiểu luồng "state đổi → render lại"  
> **Phiếu học tập liên quan:** [Phiếu 10](./worksheets/phieu-10-react-state-filter.md) — chuỗi **CampusMart UI**

---

## 1. Mục tiêu buổi học

Sau Buổi 10, bạn có thể:

1. Khai báo state với `useState`; giải thích vì sao không sửa biến thường.
2. Bắt sự kiện React: `onChange`, `onClick`; đọc `e.target.value`.
3. Tính **dữ liệu dẫn xuất** (derived data): lọc/tìm từ state ngay trong render.
4. Kết hợp 2 state (filter + search) trên một danh sách.
5. Nâng state lên cha (lifting state up) khi 2 component cần chung dữ liệu.

---

## 2. Vì sao cần State?

Thử làm filter kiểu Buổi 7 trong React: sửa một biến thường rồi… không có gì xảy ra. React chỉ render lại khi bạn báo cho nó biết **qua kênh chính thức** — state:

```
   biến thường:  thay đổi ──► React không biết ──► UI đứng im
   state:        setX(...) ──► React render lại ──► UI khớp dữ liệu

        ┌────────────────────────────────────────┐
        │      VÒNG LẶP SỐNG CỦA REACT           │
        │                                        │
        │   render UI  ◄──────────┐              │
        │      │                  │              │
        │      ▼                  │              │
        │   người dùng bấm    setState(mới)      │
        │      │                  ▲              │
        │      └── event handler ─┘              │
        └────────────────────────────────────────┘
```

---

## 3. `useState` — kho trạng thái của component

```jsx
import { useState } from "react";

function App() {
  //        giá trị hiện tại ──┐   ┌── hàm ĐỔI giá trị (tên set + PascalCase)
  const [categoryId, setCategoryId] = useState("all"); // "all" là giá trị đầu

  // ĐÚNG: đổi qua setter → React render lại
  // setCategoryId("2");

  // SAI: categoryId = "2";  ← React không hay biết
}
```

Ba quy tắc:

| Quy tắc | Lý do |
|---------|-------|
| Chỉ đổi qua setter | Kênh duy nhất React lắng nghe |
| Không gọi hook trong if/vòng lặp | React đếm hook theo thứ tự |
| State là bất biến — mảng/object phải tạo **bản mới** | Nền tảng cho P11–P12 (`[...arr, item]`, `arr.filter(...)`) |

---

## 4. Sự kiện React + controlled select

```jsx
function App() {
  const [categoryId, setCategoryId] = useState("all");

  return (
    <>
      <label htmlFor="filter-cat">Loc theo danh muc</label>  {/* for → htmlFor */}
      <select
        id="filter-cat"
        data-testid="cm-filter-category"
        value={categoryId}                              /* UI đọc TỪ state */
        onChange={(e) => setCategoryId(e.target.value)} /* event ghi VÀO state */
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

`value` + `onChange` = **controlled component**: state là nguồn sự thật duy nhất, UI chỉ phản chiếu. Đây là pattern sống còn của form Buổi 11.

---

## 5. Derived data — lọc ngay trong render, không cần state thứ hai

Sai lầm phổ biến: tạo state `filteredProducts` rồi cố đồng bộ nó với filter. **Không cần.** Danh sách đã lọc là thứ *suy ra được* — tính thẳng mỗi lần render:

```jsx
function App() {
  const [categoryId, setCategoryId] = useState("all");
  const [keyword, setKeyword] = useState("");

  // DERIVED — tính lại mỗi render, không phải state
  const visible = products
    .filter((p) => categoryId === "all" || p.category_id === Number(categoryId))
    .filter((p) => p.name.toLowerCase().includes(keyword.toLowerCase()));

  return (
    <main>
      {/* select như mục 4 + ô search: */}
      <input
        type="search"
        placeholder="Tim theo ten..."
        value={keyword}
        onChange={(e) => setKeyword(e.target.value)}
      />

      <p data-testid="cm-visible-count">Hien thi: {visible.length} san pham</p>

      <div className="cm-grid" data-testid="cm-product-table">
        {visible.map((p) => (
          <ProductCard key={p.sku} product={p} />
        ))}
      </div>
    </main>
  );
}
```

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

Số liệu chuẩn để tự kiểm ([CANONICAL §8.6](./worksheets/CANONICAL-DATA.md)): search `ra` toàn cục → **2** (Razer Viper, LG Ult**ra**Fine); `keychron` → 1; `xyz` → 0.

> **So sánh sướng tay:** Buổi 7 bạn viết `render()`, tự xóa grid, tự gọi lại đúng lúc. Hôm nay chỉ *khai báo* `visible` — React lo phần còn lại. Đó là trả công cho việc học JS thuần trước.

---

## 6. Lifting state up — khi con cần nói chuyện với cha

Muốn tách `FilterBar` thành component riêng nhưng `App` cần biết giá trị filter? Đặt state ở **cha**, truyền xuống *giá trị* + *hàm setter*:

```jsx
function FilterBar({ categoryId, onChange }) {
  return (
    <select value={categoryId} onChange={(e) => onChange(e.target.value)}>
      {/* options */}
    </select>
  );
}

// Trong App:
<FilterBar categoryId={categoryId} onChange={setCategoryId} />
```

```
        App  ─── state: categoryId ───┐
         │ props xuống                │ hàm gọi ngược lên
         ▼                            │
     FilterBar ── onChange ───────────┘
```

Dữ liệu xuống bằng props, tín hiệu lên bằng callback — mô hình một chiều của toàn bộ React.

---

## 7. Bài thực hành minh họa trong buổi (không thay phiếu)

Counter 2 phút — cảm nhận vòng lặp state:

```jsx
function Counter() {
  const [n, setN] = useState(0);
  return (
    <div>
      <p>Da bam: {n} lan</p>
      <button onClick={() => setN(n + 1)}>Bam</button>
      <button onClick={() => setN(0)}>Reset</button>
    </div>
  );
}
```

Thử phá: đổi `setN(n + 1)` thành `n = n + 1` → bấm không nhúc nhích. Bài học nằm ở chỗ hỏng đó.

---

## 8. Lỗi thường gặp

| Triệu chứng | Nguyên nhân | Cách xử lý |
|-------------|-------------|------------|
| UI không cập nhật | Gán thẳng biến state | Luôn qua setter |
| Filter so sánh trượt | `"2" === 2` | `Number(categoryId)` |
| Search phân biệt hoa thường | Quên `toLowerCase` cả 2 vế | Chuẩn hóa trước khi `includes` |
| Select không đổi được | Có `value` mà quên `onChange` | Controlled = đủ cặp |
| Vòng lặp render vô hạn | Gọi setter ngay trong thân render | Setter chỉ nằm trong handler/effect |
| `for` warning trong JSX | Attribute HTML | `htmlFor` |

---

## 9. Checklist trước khi sang buổi sau

- [ ] Giải thích vòng lặp: event → setState → render
- [ ] Controlled select + input hoạt động
- [ ] `visible` là derived — không có state danh sách thừa
- [ ] Filter 8/3/3/2 + search `ra`=2 đúng chuẩn
- [ ] Biết lifting state up khi tách FilterBar
- [ ] Đã làm xong [Phiếu 10](./worksheets/phieu-10-react-state-filter.md)

---

## 10. Bài tập trên lớp & về nhà (chuỗi CampusMart UI)

| Phần | Làm gì | Chi tiết |
|------|--------|----------|
| **Trên lớp (~20')** | State filter danh mục + visible count | [Phiếu 10 — mục 2](./worksheets/phieu-10-react-state-filter.md) |
| **Về nhà (~25–40')** | Search kết hợp + sort + FilterBar tách riêng | [Phiếu 10 — mục 3](./worksheets/phieu-10-react-state-filter.md) |

Nộp repo riêng `cse391-cm-10` + video OBS theo box cuối phiếu.

---

## 11. Cầu nối sang Buổi 11

Danh sách đã lọc/tìm được — nhưng **chưa thêm** được sản phẩm nào. Buổi 11: form controlled đầy đủ + validate + **Create** — sản phẩm mới nhảy vào state, card tự mọc thêm. Chữ C đầu tiên của CRUD.

→ Tiếp: [Buổi 11](./11-buoi-11-react-form-create.md) · [Phiếu 11](./worksheets/phieu-11-react-create.md)

---

*CSE391 – Ebook Frontend CampusMart UI | Buổi 10/12*
