# Phiếu 07 — Render từ mảng + Filter danh mục CampusMart

| | |
|---|---|
| **Buổi** | 7 |
| **Thời lượng** | ≥ 45 phút (trên lớp ~20' + về nhà ~25–40') |
| **Repo** | `cse391-cm-07` |
| **Nhận từ Phiếu 06** | `data.js` + helpers (`stockLevel`, `inventoryValue`…) |
| **Mang sang Phiếu 08** | `render(list)` + filter hoạt động |
| **Dataset** | [`CANONICAL-DATA.md`](./CANONICAL-DATA.md) — quy tắc filter §3, testid §8.3 |

### Chuẩn đầu ra (CLO)

1. Xóa card tĩnh; `render(list)` sinh toàn bộ card từ mảng.  
2. Card render giữ `data-testid="cm-product-row"` + `data-sku` + tên DM chữ + badge stockLevel.  
3. Filter select 4 option: all=8, Ban phim=3, Chuot=3, Man hinh=2.  
4. `cm-visible-count` hiển thị `Hien thi: N san pham` đúng N.  
5. Sort giá tăng dần bằng bản sao mảng (không phá gốc).

---

## 1. Tóm tắt lý thuyết (đọc lại trước khi code)

- Render = xóa khung (`innerHTML=""`) → tạo phần tử từ mảng → append.
- `textContent` cho mọi dữ liệu; `Number()` khi so value select với id.
- `addEventListener("change")` cho select; `[...arr].sort()` giữ bất biến.
- Delegation: nghe trên `.cm-grid`, `closest(".cm-card")`.

Chi tiết: [Buổi 7](../07-buoi-07-dom-events-render-filter.md).

---

## 2. Bài trên lớp (~20 phút) — Thay card tĩnh bằng render

### Yêu cầu

Copy từ repo Phiếu 06 sang `cse391-cm-07`, rồi:

1. Đổi `<title>` exact: **`CampusMart — Filter (Buoi 7)`**.
2. Xóa 8 card tĩnh trong HTML — `.cm-grid` để **rỗng** (giữ `data-testid="cm-product-table"`).
3. Viết `render(list)` trong `app.js` (theo Buổi 7 mục 4.2): mỗi card gồm tên (`h3`), danh mục chữ, giá số thô, badge `stockLevel` — tất cả bằng `createElement` + `textContent`.
4. Gọi `render(products)` → trang trông **y hệt** Phiếu 06 nhưng card do JS sinh.

### Output kỳ vọng (trên lớp)

- View-source (Ctrl+U): `.cm-grid` rỗng. DevTools Elements: 8 card đầy đủ.
- Sửa `data.js` thêm tạm 1 sản phẩm → reload → 9 card (xóa lại sau khi thử).

**Checkpoint giảng viên:** grid rỗng trong source nhưng 8 card trên trang; `data-sku` đủ.

---

## 3. Bài về nhà (~25–40 phút) — Filter + count + sort

Tiếp tục **cùng repo**.

### Nhiệm vụ A — Bộ lọc danh mục

Thêm trên lưới:

```html
<label for="filter-cat">Loc theo danh muc</label>
<select id="filter-cat" data-testid="cm-filter-category">
  <option value="all">Tat ca</option>
  <option value="1">Ban phim</option>
  <option value="2">Chuot</option>
  <option value="3">Man hinh</option>
</select>
<p data-testid="cm-visible-count"></p>
```

JS: sự kiện `change` → lọc (`Number(value)`) → `render(list)`.

### Nhiệm vụ B — Visible count

Cuối `render(list)`: ghi `Hien thi: ${list.length} san pham` vào `cm-visible-count`. Bảng chuẩn:

| Chọn | N |
|------|--:|
| Tat ca | 8 |
| Ban phim | 3 |
| Chuot | 3 |
| Man hinh | 2 |

### Nhiệm vụ C — Sort giá tăng dần

Nút `<button id="sort-price">Gia tang dan</button>`: click → `render([...danhSachDangHien].sort((a,b) => a.price - b.price))`.

Kiểm tra: ở chế độ `Tat ca`, card đầu sau sort là **MS-03 (250000)**, card cuối **MN-02 (8500000)**. Sort phải tôn trọng filter đang chọn (sort trong phạm vi list đang hiển thị).

Gợi ý tổ chức: giữ biến trạng thái `let currentList = products;` — filter cập nhật nó, sort đọc nó.

### Nhiệm vụ D — Delegation demo (chuẩn bị P12)

Click vào card bất kỳ → `console.log` SKU của card (một listener trên `.cm-grid`, dùng `closest`).

### Nhiệm vụ E — Marker

```html
<!-- CM_EXPECT product_count=8 filter_counts=8,3,3,2 first_sorted_sku=MS-03 -->
```

---

## 4. OUTPUT / EXPECT

| Key | Value bắt buộc |
|-----|----------------|
| `page_title` | `CampusMart — Filter (Buoi 7)` |
| `grid.source` | rỗng trong HTML nguồn; card sinh bằng JS |
| `render.card_count` | 8 (mặc định) |
| `filter.1 / .2 / .3` | 3 / 3 / 2 card, đúng SKU nhóm |
| `visible_count.text` | `Hien thi: 8 san pham` (và 3/3/2 khi lọc) |
| `sort.first_sku` | `MS-03` (250000) khi Tat ca + sort |
| `sort.immutable` | mảng `products` gốc không đổi thứ tự |
| `card.badge` | stockLevel exact theo CANONICAL §3 |
| `CM_EXPECT` comment | như Nhiệm vụ E |

---

## 5. Box nộp bài

```
╔══════════════════════════════════════════════════════════════╗
║  NỘP PHIẾU 07                                                ║
╠══════════════════════════════════════════════════════════════╣
║  1. GitHub repo RIÊNG: cse391-cm-07                          ║
║  2. Video OBS + camera mặt — kịch bản:                       ║
║     - View-source: grid rỗng → trang vẫn 8 card              ║
║     - Lọc lần lượt 4 option, đọc to visible count            ║
║     - Bấm "Gia tang dan" → card đầu MS-03                    ║
║     - Click 1 card → console in SKU                          ║
║  3. Thuyết trình 30–60s: vì sao phải Number(select.value)?   ║
╚══════════════════════════════════════════════════════════════╝
```
