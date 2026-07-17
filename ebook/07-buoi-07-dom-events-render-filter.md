# BUỔI 7: DOM Events — render list từ mảng, filter danh mục

> **Thời lượng gợi ý:** 4 giờ tự học  
> **Tiên quyết:** Buổi 6 (`data.js` + các hàm helper)  
> **Kết quả đầu ra:** JS tự sinh card từ mảng, bắt sự kiện `change`/`click`, lọc sản phẩm theo danh mục, sort theo giá  
> **Phiếu học tập liên quan:** [Phiếu 07](./worksheets/phieu-07-dom-render-filter.md) — chuỗi **CampusMart UI**

---

## 1. Mục tiêu buổi học

Sau Buổi 7, bạn có thể:

1. Tạo phần tử DOM từ dữ liệu (`createElement` / template literal + `innerHTML` có kiểm soát).
2. Viết hàm `render(list)` — vẽ lại toàn bộ lưới card từ một mảng bất kỳ.
3. Bắt sự kiện với `addEventListener` (`change`, `click`, `input`).
4. Làm bộ lọc danh mục + hiển thị `Hien thi: N san pham`.
5. Hiểu Event loop ở mức "một luồng, chờ sự kiện" và event delegation cơ bản.

---

## 2. Vì sao "render từ mảng" là bước ngoặt?

Đến Phiếu 06, card vẫn là HTML gõ tay — JS chỉ gắn thêm badge. Hôm nay đảo ngược hoàn toàn: **HTML chỉ chừa một khung rỗng**, JS đọc mảng và sinh toàn bộ card. Khi đó "lọc" chỉ là *render lại với mảng con* — đơn giản đến bất ngờ:

```
        products (8)
             │ filter(category_id === 2)
             ▼
        mảng con (3)
             │ render()
             ▼
   .cm-grid chỉ còn 3 card
```

Toàn bộ React sau này cũng chỉ là ý tưởng này được làm tự động.

---

## 3. Event — trang web "lắng nghe"

### 3.1. Mô hình event loop (đơn giản hóa)

```
┌────────────────────────────────────────────────────┐
│  JS chỉ có MỘT luồng                               │
│                                                    │
│   ┌──────────────┐    xong việc     ┌───────────┐  │
│   │  Call stack  │ ◄───────────────│ Hàng đợi   │  │
│   │ (đang chạy)  │    lấy tiếp      │ sự kiện    │  │
│   └──────────────┘                  └───────────┘  │
│         ▲                                ▲         │
│         │                                │         │
│    render(...)                 click! change! ...  │
└────────────────────────────────────────────────────┘
 Người dùng bấm → sự kiện xếp hàng → JS rảnh thì xử lý
```

Hệ quả thực tế: hàm xử lý sự kiện phải **nhanh**; đừng lặp vô hạn — cả trang đứng hình.

### 3.2. `addEventListener`

```js
const select = document.querySelector('[data-testid="cm-filter-category"]');

select.addEventListener("change", (event) => {
  console.log("Gia tri moi:", event.target.value); // "all" | "1" | "2" | "3"
});
```

| Sự kiện | Phát khi | Dùng cho |
|---------|----------|----------|
| `click` | bấm chuột/enter | nút sort, nút xóa (P12) |
| `change` | select/input đổi xong | bộ lọc danh mục |
| `input` | gõ từng phím | ô tìm kiếm (Buổi 10) |
| `submit` | gửi form | validate (Buổi 8) |

---

## 4. Render list từ mảng

### 4.1. HTML chỉ còn khung

```html
<label for="filter-cat">Loc theo danh muc</label>
<select id="filter-cat" data-testid="cm-filter-category">
  <option value="all">Tat ca</option>
  <option value="1">Ban phim</option>
  <option value="2">Chuot</option>
  <option value="3">Man hinh</option>
</select>

<p data-testid="cm-visible-count"></p>
<div class="cm-grid" data-testid="cm-product-table"></div>
```

### 4.2. Hàm render — trái tim của phiếu

```js
const grid = document.querySelector('[data-testid="cm-product-table"]');
const countEl = document.querySelector('[data-testid="cm-visible-count"]');

function categoryName(id) {
  const c = categories.find((c) => c.id === id);
  return c ? c.name : "?";
}

function render(list) {
  grid.innerHTML = ""; // xóa sạch rồi vẽ lại — đơn giản, đủ dùng

  for (const p of list) {
    const card = document.createElement("article");
    card.className = "cm-card";
    card.dataset.testid = "cm-product-row";
    card.dataset.sku = p.sku;

    // Dựng con bằng createElement + textContent (an toàn XSS)
    const h3 = document.createElement("h3");
    h3.textContent = p.name;

    const cat = document.createElement("p");
    cat.className = "cm-card-cat";
    cat.textContent = categoryName(p.category_id);

    const price = document.createElement("p");
    price.className = "cm-card-price";
    price.textContent = String(p.price);

    const stock = document.createElement("p");
    stock.className = "cm-stock";
    stock.textContent = stockLevel(p.qty);

    card.append(h3, cat, price, stock);
    grid.appendChild(card);
  }

  countEl.textContent = `Hien thi: ${list.length} san pham`;
}

render(products); // lần đầu: đủ 8
```

> Vì sao `textContent` từng mảnh thay vì ghép chuỗi `innerHTML`? Tên sản phẩm sau này do **người dùng nhập** (P11) — `textContent` tự escape, miễn nhiễm XSS. Nếu dùng `innerHTML`, chỉ nhét dữ liệu mình kiểm soát.

### 4.3. Nối filter vào render

```js
select.addEventListener("change", () => {
  const v = select.value;
  const list =
    v === "all" ? products : products.filter((p) => p.category_id === Number(v));
  render(list);
});
```

Chú ý `Number(v)`: value của select là **chuỗi** `"2"`, còn `category_id` là **số** `2` — so sánh `===` sẽ trượt nếu quên ép kiểu (bug kinh điển số 1 của phiếu này).

---

## 5. Sort theo giá — bất biến dữ liệu gốc

```js
const sortBtn = document.querySelector("#sort-price");

sortBtn.addEventListener("click", () => {
  // [...products] tạo BẢN SAO — không phá thứ tự mảng gốc
  const sorted = [...products].sort((a, b) => a.price - b.price);
  render(sorted);
});
```

Nguyên tắc "không phá dữ liệu gốc" (immutability) sẽ thành **bắt buộc** khi sang React state.

---

## 6. Event delegation (biết trước cho P12)

Card do JS sinh ra **sau** khi `addEventListener` chạy → gắn listener từng card dễ sót. Giải pháp: nghe trên **cha** rồi hỏi sự kiện xuất phát từ đâu:

```js
grid.addEventListener("click", (e) => {
  const card = e.target.closest(".cm-card");
  if (!card) return;
  console.log("Ban vua bam card:", card.dataset.sku);
});
```

Một listener duy nhất phục vụ mọi card — kể cả card sinh sau này.

---

## 7. Bài thực hành minh họa trong buổi (không thay phiếu)

Console trên trang Phiếu 06, không sửa file:

```js
// 1. Render nhanh 3 tên sản phẩm đầu ra một <ul> tạm
const ul = document.createElement("ul");
products.slice(0, 3).forEach((p) => {
  const li = document.createElement("li");
  li.textContent = p.name;
  ul.appendChild(li);
});
document.body.appendChild(ul);

// 2. Đếm card đang có
document.querySelectorAll(".cm-card").length;
```

Thấy `ul` hiện cuối trang → bạn đã "render từ mảng" lần đầu tiên.

---

## 8. Lỗi thường gặp

| Triệu chứng | Nguyên nhân | Cách xử lý |
|-------------|-------------|------------|
| Filter không ra gì | `"2" === 2` là false | `Number(select.value)` |
| Card mất testid | Quên `dataset.sku` khi render | Gắn trong hàm render |
| Listener card mới không chạy | Gắn listener trước khi render | Event delegation (mục 6) |
| Render nhân đôi card | Quên xóa `grid.innerHTML = ""` | Xóa trước khi vẽ |
| Sort xong filter loạn | `sort` phá mảng gốc | `[...products].sort(...)` |

---

## 9. Checklist trước khi sang buổi sau

- [ ] `render(list)` vẽ lại lưới từ mảng bất kỳ
- [ ] Filter 4 mốc: all=8, DM1=3, DM2=3, DM3=2
- [ ] `Hien thi: N san pham` cập nhật đúng
- [ ] Sort giá tăng dần không phá mảng gốc
- [ ] Giải thích được event loop 1 câu + delegation 1 câu
- [ ] Đã làm xong [Phiếu 07](./worksheets/phieu-07-dom-render-filter.md)

---

## 10. Bài tập trên lớp & về nhà (chuỗi CampusMart UI)

| Phần | Làm gì | Chi tiết |
|------|--------|----------|
| **Trên lớp (~20')** | `render(products)` thay card tĩnh | [Phiếu 07 — mục 2](./worksheets/phieu-07-dom-render-filter.md) |
| **Về nhà (~25–40')** | Filter select + visible count + sort giá | [Phiếu 07 — mục 3](./worksheets/phieu-07-dom-render-filter.md) |

Nộp repo riêng `cse391-cm-07` + video OBS theo box cuối phiếu.

---

## 11. Cầu nối sang Buổi 8

Trang đã tương tác — nhưng code dồn cả vào `app.js` và dữ liệu mất sạch mỗi lần F5. Buổi 8: tách **ES Module**, lưu **localStorage** (nhớ filter, danh sách đăng ký), và **validate form** bằng JS thay vì chỉ dựa `required`.

→ Tiếp: [Buổi 8](./08-buoi-08-module-localstorage-validate.md) · [Phiếu 08](./worksheets/phieu-08-module-localstorage.md)

---

*CSE391 – Ebook Frontend CampusMart UI | Buổi 7/12*
