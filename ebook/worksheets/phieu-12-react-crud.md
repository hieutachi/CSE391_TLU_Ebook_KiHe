# Phiếu 12 — React CRUD 2 thực thể ★ ĐÍCH CUỐI CHUỖI

| | |
|---|---|
| **Buổi** | 12 |
| **Thời lượng** | ≥ 45 phút (trên lớp ~20' + về nhà ~25–40') |
| **Repo** | `cse391-cm-12` (project Vite — copy từ P11) |
| **Nhận từ Phiếu 11** | products state + ProductForm Create |
| **Mang sang** | Project cuối môn (nhóm) — nền SPA hoàn chỉnh |
| **Dataset** | [`CANONICAL-DATA.md`](./CANONICAL-DATA.md) — kịch bản §8.8, thông báo §8.4 |

### Chuẩn đầu ra (CLO)

1. Delete product: nút `Xoa` + confirm + `filter` bất biến.  
2. Update product: nút `Sua` đổ dữ liệu vào form chung, submit bằng `map` bất biến; nhãn nút đổi `Them san pham` ↔ `Cap nhat san pham`.  
3. `categories` thành state: thêm danh mục qua `cm-category-form`; DM mới tự vào cả 2 select.  
4. Chặn xóa danh mục còn sản phẩm với thông báo exact; xóa được danh mục rỗng.  
5. Chạy đúng **kịch bản 5 bước** §8.8 với từng con số khớp (**32880000**, **34770000**).

---

## 1. Tóm tắt lý thuyết (đọc lại trước khi code)

- Update: `products.map(p => p.sku === sku ? { ...p, ...patch } : p)`.
- Delete: `window.confirm` → `products.filter(p => p.sku !== sku)`.
- Form chung: state `editingSku` (null = Create); check trùng SKU **chỉ khi Create**.
- Liên thực thể: đếm products theo `category_id` trước khi cho xóa category.
- P12 = **ứng dụng React CRUD hoàn chỉnh** (mốc 2).

Chi tiết: [Buổi 12](../12-buoi-12-react-crud-2-thuc-the.md).

---

## 2. Bài trên lớp (~20 phút) — Delete + Update qty

### Yêu cầu

Copy project P11 sang `cse391-cm-12`, rồi:

1. `index.html`: `<title>` exact: **`CampusMart — React CRUD (Buoi 12)`**.
2. `ProductCard` thêm 2 nút: `Sua` và `Xoa` (nhận `onEdit`, `onDelete` qua props).
3. `Xoa`: confirm (`Xoa san pham {name}?`) → `setProducts(products.filter(...))`.
4. `Sua` (bản tối giản trên lớp): `prompt` số lượng mới → `handleUpdate(sku, { qty: Number(...) })` bằng `map`. (Về nhà thay bằng form tử tế.)

### Cách thử trên lớp (OBS)

| # | Thử | Dự đoán | Quan sát |
|---|-----|---------|----------|
| T1 | Title | `CampusMart — React CRUD (Buoi 12)` | Tab |
| T2 | Xoa MS-03 → Cancel | Vẫn đủ card | confirm |
| T3 | Xoa MS-03 → OK | −1 card, stats giảm | UI |
| T4 | Sua KB-02 qty 5→1 | Tổng đổi; stockLevel đổi nếu có | UI |
| T5 | Không splice | Code review | Source |

**Checkpoint giảng viên:** xóa + sửa chạy; không mutate bằng splice.

---

## 3. Bài về nhà (~25–40 phút) — Form Edit + categories + kịch bản

**Reset data về CORE_8** trước khi quay video (không mang MN-03).

### Nhiệm vụ A — Form dùng chung Create/Edit

1. State `editingSku` (mặc định `null`).
2. `Sua` → đổ product vào form, set `editingSku`.
3. Submit: nếu `editingSku` → update `map` (giữ SKU, không check trùng); else Create như P11.
4. Nhãn: `Cap nhat san pham` khi edit; nút `Huy`.

#### Cách thử A

| # | Thử | Kỳ vọng |
|---|-----|---------|
| A1 | Bấm Sua KB-01 | Form đầy dữ liệu, nút Cap nhat |
| A2 | Đổi qty → submit | Card + stats đổi, không thêm card |
| A3 | Huy | Form reset, về Them san pham |
| A4 | Edit không báo dup chính SKU | Không `SKU da ton tai` |

### Nhiệm vụ B — Quản lý categories

1. `categories` thành state.
2. `CategoryManager` + `data-testid="cm-category-form"`: input + `Them danh muc`.
   - Rỗng → `Thieu thong tin bat buoc`; trùng → `Danh muc da ton tai`.
   - Hợp lệ → `{ id: maxId+1, name, description: "" }`.
3. Nút `Xoa` từng DM:
   - Còn SP → `Khong the xoa danh muc con san pham`.
   - Rỗng → confirm → xóa.
4. Lan tỏa: CategoryList + 2 select đều `map` state.

#### Cách thử B

| # | Thử | Kỳ vọng |
|---|-----|---------|
| B1 | Them `Phu kien` | 4 option ở filter + form |
| B2 | Xoa Chuot | Msg exact, vẫn còn Chuot |
| B3 | Xoa Phu kien | Về 3 DM |

### Nhiệm vụ C — Kịch bản 5 bước (OBS — đúng thứ tự)

| Bước | Thao tác | Số phải khớp |
|------|----------|--------------|
| 1 | Xoa `MN-02` | 7 card · tổng **32880000** |
| 2 | Sua `KB-01` qty 3 → 4 | tổng **34770000** |
| 3 | Them danh muc `Phu kien` | 2 select đều 4 DM |
| 4 | Xoa danh muc `Chuot` | chặn + msg exact |
| 5 | Xoa danh muc `Phu kien` | xóa được, về 3 DM |

#### Cách thử C (đọc to trên video)

Đọc to **32880000** sau bước 1; **34770000** sau bước 2; đọc msg chặn bước 4.

### Nhiệm vụ D — Marker

```jsx
{/* CM_EXPECT after_delete=7,32880000 after_edit=34770000 new_category=Phu kien block_msg=Khong the xoa danh muc con san pham */}
```

---

## 4. OUTPUT / EXPECT

| Key | Value bắt buộc |
|-----|----------------|
| `page_title` | `CampusMart — React CRUD (Buoi 12)` |
| `card.buttons` | `Sua`, `Xoa` trên mỗi card |
| `delete.MN-02` | 7 card · `Tong gia tri kho = 32880000` |
| `delete.confirm_cancel` | Cancel → không xóa |
| `edit.KB-01.qty4` | `Tong gia tri kho = 34770000` |
| `edit.button_label` | `Cap nhat san pham` khi đang edit |
| `edit.no_dup_check` | edit không tự báo `SKU da ton tai` |
| `category.create` | `Phu kien` id 4, vào cả 2 select |
| `category.block` | `Khong the xoa danh muc con san pham` (exact) |
| `category.delete_empty` | xóa `Phu kien` khi 0 SP → OK |
| `immutable` | không `push`/`splice` trên state |

### Checklist tự chấm

```
[ ] title P12
[ ] Sua/Xoa + confirm
[ ] form Edit chung + Huy
[ ] cm-category-form + Phu kien
[ ] chặn xóa Chuot — msg exact
[ ] 32880000 → 34770000
[ ] CM_EXPECT
[ ] reset CORE_8 trước video
```

---

## 5. Box nộp bài

```
╔══════════════════════════════════════════════════════════════╗
║  NỘP PHIẾU 12 — ĐÍCH CUỐI                                    ║
╠══════════════════════════════════════════════════════════════╣
║  1. GitHub repo RIÊNG: cse391-cm-12 (KHÔNG node_modules)     ║
║  2. Video OBS + camera mặt — chạy ĐÚNG kịch bản 5 bước:      ║
║     xóa MN-02 → sửa KB-01 → thêm Phu kien →                  ║
║     xóa Chuot (bị chặn) → xóa Phu kien (OK)                  ║
║     Đọc to 2 con số: 32880000 và 34770000                    ║
║  3. Thuyết trình 60–90s: quan hệ Category–Product là gì,     ║
║     vì sao phải chặn xóa ở cả UI lẫn backend?                ║
╚══════════════════════════════════════════════════════════════╝
```

> **Chúc mừng!** Hoàn thành phiếu này = CampusMart UI React 2 thực thể + CRUD — đích cuối chuỗi. Sẵn sàng Project nhóm (Buổi 13–15).

---

## 6. Lỗi thường gặp khi nộp

| Lỗi | Hậu quả | Cách tránh |
|-----|---------|------------|
| Video còn MN-03 | Sai 32880000 | Reset CORE_8 |
| Msg tự viết | EXPECT fail | Exact §8.4 |
| splice/push | Trừ immutable | map/filter/spread |
| Quên Huy / nhãn Cap nhat | CLO form chung | Checklist A |
| Title Buoi 11 | page_title fail | §8.2 P12 |

---

*CSE391 – Phiếu 12 ★ | CampusMart UI — bản viết lại CSE485*
