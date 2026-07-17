# BUỔI 4: CSS Layout — Flexbox (và Grid tối thiểu)

> **Thời lượng gợi ý:** 3–4 giờ tự học  
> **Tiên quyết:** Buổi 3 (styles.css + tokens + box model)  
> **Kết quả đầu ra:** Xếp nav ngang bằng Flexbox, dựng lưới card sản phẩm bằng Grid, hiểu trục chính/trục phụ  
> **Phiếu học tập liên quan:** [Phiếu 04](./worksheets/phieu-04-flexbox-grid.md) — chuỗi **CampusMart UI**

---

## 1. Mục tiêu buổi học

Sau Buổi 4, bạn có thể:

1. Bật Flexbox và điều khiển trục: `flex-direction`, `justify-content`, `align-items`, `gap`.
2. Phân biệt khi nào dùng Flexbox (1 chiều) vs Grid (2 chiều).
3. Dựng `.cm-grid` bằng `grid-template-columns` + `repeat`/`minmax`.
4. Chuyển catalog CampusMart từ bảng sang **card lưới** (giữ bảng làm phụ lục).
5. Debug layout bằng DevTools (badge `flex` / `grid`).

---

## 2. Vì sao cần Flexbox/Grid?

Trước 2015, dân frontend xếp cột bằng `float` và `table` — mong manh, khó canh giữa. Flexbox và Grid là **hệ thống layout thật sự** của CSS: khai báo ý đồ ("xếp ngang, cách nhau 1rem, canh giữa dọc") thay vì hack từng pixel. Toàn bộ giao diện CampusMart (nav, card, footer) sẽ đứng trên hai công cụ này.

---

## 3. Flexbox — layout một chiều

### 3.1. Mô hình trục (ASCII bắt buộc nhớ)

```
flex-direction: row  (mặc định)

 main axis ────────────────────────────►
┌───────────────────────────────────────┐
│  ┌─────┐  ┌─────┐  ┌─────┐            │   ▲
│  │  A  │  │  B  │  │  C  │            │   │ cross axis
│  └─────┘  └─────┘  └─────┘            │   ▼
└───────────────────────────────────────┘
   justify-content → canh theo TRỤC CHÍNH (ngang)
   align-items     → canh theo TRỤC PHỤ  (dọc)

flex-direction: column → hai trục ĐỔI CHỖ:
   justify-content thành canh DỌC, align-items thành canh NGANG
```

### 3.2. Thuộc tính container hay dùng

```css
.cm-nav ul {
  display: flex;            /* bật flexbox cho các <li> con */
  flex-direction: row;      /* xếp ngang (mặc định) */
  gap: 1.5rem;              /* khoảng cách đều — thay margin từng item */
  justify-content: flex-end;/* dồn menu về phải */
  align-items: center;      /* canh giữa theo chiều dọc */
  list-style: none;
  margin: 0;
  padding: 0;
}
```

| Giá trị `justify-content` | Hình dung |
|---------------------------|-----------|
| `flex-start` | `[A][B][C]........` |
| `center` | `....[A][B][C]....` |
| `flex-end` | `........[A][B][C]` |
| `space-between` | `[A]....[B]....[C]` |

### 3.3. Header hai đầu — pattern kinh điển

```css
.cm-header {
  display: flex;
  justify-content: space-between; /* brand trái — nav phải */
  align-items: center;
  padding: 1rem 1.5rem;
}
```

```
┌────────────────────────────────────────────────┐
│ CampusMart              Trang chu San pham ... │
└────────────────────────────────────────────────┘
```

---

## 4. Grid — layout hai chiều

### 4.1. Khi nào Grid thay vì Flex?

```
FLEX  = 1 chiều: một hàng nav, một cột form
GRID  = 2 chiều: LƯỚI card sản phẩm (hàng × cột)

┌──────┬──────┬──────┬──────┐
│ KB-01│ KB-02│ KB-03│ MS-01│
├──────┼──────┼──────┼──────┤
│ MS-02│ MS-03│ MN-01│ MN-02│
└──────┴──────┴──────┴──────┘
        .cm-grid (4 cột × 2 hàng)
```

### 4.2. `.cm-grid` chuẩn môn

```css
.cm-grid {
  display: grid;
  /* auto-fill: tự nhét vừa bao nhiêu cột thì nhét,
     mỗi cột tối thiểu 220px, tối đa chia đều (1fr) */
  grid-template-columns: repeat(auto-fill, minmax(220px, 1fr));
  gap: 1rem;
}
```

Một dòng khai báo trên đã "responsive sẵn 80%": màn hẹp tự còn 1–2 cột. Buổi 5 tinh chỉnh nốt bằng media query.

### 4.3. Card sản phẩm `.cm-card`

```html
<article class="cm-card" data-testid="cm-product-row" data-sku="KB-01">
  <img src="https://placehold.co/220x140" alt="Ban phim Keychron K2" />
  <h3>Keychron K2</h3>
  <p class="cm-card-cat">Ban phim</p>
  <p class="cm-card-price">1890000</p>
  <p class="cm-card-qty">So luong: 3</p>
</article>
```

```css
.cm-card {
  background: white;
  border: 1px solid #e2e8f0;
  border-radius: 8px;
  padding: 1rem;
  display: flex;             /* flex TRONG card: xếp dọc nội dung */
  flex-direction: column;
  gap: 0.5rem;
}
.cm-card img { width: 100%; height: auto; border-radius: 4px; }
.cm-card-price { color: var(--cm-primary); font-weight: 700; }
```

> Lưu ý quy ước: card mang `data-testid="cm-product-row"` + `data-sku` — **giống dòng bảng cũ**, để máy chấm đếm được dù bạn đổi hình thức hiển thị.

---

## 5. Bài thực hành minh họa trong buổi (không thay phiếu)

Tạo `demo-flex.html`, thử từng giá trị và quan sát:

```html
<div class="row">
  <div class="box">A</div>
  <div class="box">B</div>
  <div class="box">C</div>
</div>
<style>
  .row { display: flex; gap: 1rem; border: 2px dashed #999; height: 120px;
         justify-content: center;   /* đổi lần lượt 5 giá trị */
         align-items: center; }     /* đổi: flex-start / flex-end / stretch */
  .box { background: #0f766e; color: white; padding: 1rem 2rem; }
</style>
```

Bài kiểm tra nhanh: làm chữ "C" tự dồn về phải trong khi A, B ở trái? (gợi ý: `margin-left: auto` trên `.box:last-child` — trick hay dùng cho nút logout trên nav).

---

## 6. Lỗi thường gặp

| Triệu chứng | Nguyên nhân | Cách xử lý |
|-------------|-------------|------------|
| `justify-content` không tác dụng | Quên `display: flex` trên **cha** | Flex property đặt ở container |
| Item không canh giữa dọc | Container không có chiều cao | Đặt height/padding hoặc `align-items` đúng trục |
| Card cột lệch nhau | Nội dung dài ngắn khác nhau | `display: flex; flex-direction: column` trong card |
| Grid 1 cột mãi | `minmax` min quá lớn so với khung | Giảm 220px hoặc kiểm tra width cha |
| Khoảng cách items lởm chởm | Margin từng item | Dùng `gap` trên container |

---

## 7. Checklist trước khi sang buổi sau

- [ ] Vẽ được 2 trục flex và giải thích justify/align
- [ ] Header brand-trái nav-phải bằng `space-between`
- [ ] `.cm-grid` với `repeat(auto-fill, minmax(...))` chạy được
- [ ] 8 card giữ `data-sku` + `data-testid`
- [ ] Biết bật badge flex/grid trong DevTools
- [ ] Đã làm xong [Phiếu 04](./worksheets/phieu-04-flexbox-grid.md)

---

## 8. Bài tập trên lớp & về nhà (chuỗi CampusMart UI)

| Phần | Làm gì | Chi tiết |
|------|--------|----------|
| **Trên lớp (~20')** | Nav ngang flex + header space-between | [Phiếu 04 — mục 2](./worksheets/phieu-04-flexbox-grid.md) |
| **Về nhà (~25–40')** | `.cm-grid` 8 card đủ data + footer flex | [Phiếu 04 — mục 3](./worksheets/phieu-04-flexbox-grid.md) |

Nộp repo riêng `cse391-cm-04` + video OBS theo box cuối phiếu.

---

## 9. Cầu nối sang Buổi 5

Layout đẹp trên màn máy bạn — nhưng sinh viên xem CampusMart bằng **điện thoại**. Buổi 5: media query, mobile-first, hoàn thiện bộ component tĩnh (nav, card, bảng) đúng breakpoint 640/1024.

→ Tiếp: [Buổi 5](./05-buoi-05-responsive-components.md) · [Phiếu 05](./worksheets/phieu-05-responsive-campusmart.md)

---

*CSE391 – Ebook Frontend CampusMart UI | Buổi 4/12*
