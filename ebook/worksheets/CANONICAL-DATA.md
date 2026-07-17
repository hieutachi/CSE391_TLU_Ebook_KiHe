# CampusMart UI — Dữ liệu chuẩn (Single Source of Truth)

> Mọi phiếu **P01–P12** phải dùng đúng dataset này (phần CORE).  
> Autograder / giảng viên lấy EXPECT từ đây — **không đổi số liệu tùy tiện**.  
> Số liệu CORE **khớp MiniShop Backend CSE485** để liên thông môn.

## 1. Categories (3)

| id | name | description |
|----|------|-------------|
| 1 | Ban phim | Danh muc ban phim co / membrane |
| 2 | Chuot | Danh muc chuot may tinh |
| 3 | Man hinh | Danh muc man hinh |

> Khi sang React (P09+), `id` có thể là số trong state/JSON. **Map theo `name` hoặc `sku`** khi chấm — không phụ thuộc thứ tự DOM ngẫu nhiên.

## 2. Products gốc (8) — bắt buộc đủ SKU

| sku | name | category name | category_id | price | qty | line_total |
|-----|------|---------------|------------:|------:|----:|----------:|
| KB-01 | Keychron K2 | Ban phim | 1 | 1890000 | 3 | 5670000 |
| KB-02 | Akko 3087 | Ban phim | 1 | 1290000 | 5 | 6450000 |
| KB-03 | Leopold FC660M | Ban phim | 1 | 2750000 | 2 | 5500000 |
| MS-01 | Logitech M331 | Chuot | 2 | 290000 | 10 | 2900000 |
| MS-02 | Razer Viper | Chuot | 2 | 990000 | 4 | 3960000 |
| MS-03 | Xiaomi Silent | Chuot | 2 | 250000 | 8 | 2000000 |
| MN-01 | Dell 24 inch | Man hinh | 3 | 3200000 | 2 | 6400000 |
| MN-02 | LG UltraFine | Man hinh | 3 | 8500000 | 1 | 8500000 |

## 3. Hằng số chấm ổn định (CORE_8)

| Key | Value |
|-----|------:|
| `product_count_core` | **8** |
| `category_count_core` | **3** |
| `inventory_value_core` | **41380000** |
| Ban phim — count / value | **3** / **17620000** |
| Chuot — count / value | **3** / **8860000** |
| Man hinh — count / value | **2** / **14900000** |

### Quy tắc `stockLevel(qty)` (dùng từ P06/P07 JS trở đi)

| Điều kiện | Output đúng (chuỗi exact) |
|-----------|---------------------------|
| `qty >= 5` | `Du` |
| `qty >= 2` | `Sap het` |
| else | `Can nhap` |

| sku | qty | stockLevel |
|-----|----:|------------|
| KB-01 | 3 | Sap het |
| KB-02 | 5 | Du |
| KB-03 | 2 | Sap het |
| MS-01 | 10 | Du |
| MS-02 | 4 | Sap het |
| MS-03 | 8 | Du |
| MN-01 | 2 | Sap het |
| MN-02 | 1 | Can nhap |

### Quy tắc format giá VND (UI)

| Rule | Ví dụ |
|------|--------|
| Số nguyên, không dấu chấm thập phân trong data | `1890000` |
| Hiển thị gợi ý | `1.890.000 ₫` hoặc `1890000 VND` — **EXPECT máy** ưu tiên số thô trong `data-price` / text chứa `1890000` |
| Không đổi giá CORE | Cấm làm tròn / đổi đơn vị |

### Quy tắc filter theo category (P07, P10+)

| Filter | SKU hiển thị | `visible_count` |
|--------|--------------|----------------:|
| Tất cả / `all` | 8 SKU | **8** |
| Ban phim / `category_id=1` | KB-01, KB-02, KB-03 | **3** |
| Chuot / `category_id=2` | MS-01, MS-02, MS-03 | **3** |
| Man hinh / `category_id=3` | MN-01, MN-02 | **2** |

## 4. Chuỗi UI exact & marker chấm máy

| Key | Value exact |
|-----|-------------|
| `page_title_p01` | `CampusMart — Catalog (Buoi 1)` |
| `brand_name` | `CampusMart` |
| `heading_h1_p01` | `CampusMart` |
| `nav_home_label` | `Trang chu` |
| `nav_products_label` | `San pham` |
| `nav_about_label` | `Gioi thieu` |
| `table_caption_p01` | `Danh sach san pham CORE` |

### `data-testid` khuyến nghị (ổn định từ P01)

| testid | Ý nghĩa |
|--------|---------|
| `cm-brand` | Logo / tên thương hiệu |
| `cm-nav` | Thanh điều hướng |
| `cm-product-table` | Bảng / list sản phẩm |
| `cm-product-row` | Mỗi dòng/card sản phẩm (có thể kèm `data-sku`) |
| `cm-category-list` | Danh sách danh mục |
| `cm-inventory-total` | Chỗ hiện tổng giá trị kho (khi có) |
| `cm-filter-category` | Select/nút lọc danh mục (P07+) |

### Comment EXPECT (HTML)

```html
<!-- CM_EXPECT product_count=8 inventory_value=41380000 -->
```

Prefix: **`CM_EXPECT`** (CampusMart) — khác `MS_EXPECT` của Backend MiniShop.

## 5. JSON / JS object mẫu (copy vào `data.js` hoặc nhúng script)

```js
// CampusMart — CORE_8 (khóa số liệu)
export const categories = [
  { id: 1, name: "Ban phim", description: "Danh muc ban phim co / membrane" },
  { id: 2, name: "Chuot", description: "Danh muc chuot may tinh" },
  { id: 3, name: "Man hinh", description: "Danh muc man hinh" },
];

export const products = [
  { sku: "KB-01", name: "Keychron K2", category_id: 1, price: 1890000, qty: 3 },
  { sku: "KB-02", name: "Akko 3087", category_id: 1, price: 1290000, qty: 5 },
  { sku: "KB-03", name: "Leopold FC660M", category_id: 1, price: 2750000, qty: 2 },
  { sku: "MS-01", name: "Logitech M331", category_id: 2, price: 290000, qty: 10 },
  { sku: "MS-02", name: "Razer Viper", category_id: 2, price: 990000, qty: 4 },
  { sku: "MS-03", name: "Xiaomi Silent", category_id: 2, price: 250000, qty: 8 },
  { sku: "MN-01", name: "Dell 24 inch", category_id: 3, price: 3200000, qty: 2 },
  { sku: "MN-02", name: "LG UltraFine", category_id: 3, price: 8500000, qty: 1 },
];
```

> P01–P05: có thể **hard-code HTML** đúng 8 dòng / 3 danh mục — không bắt buộc file JS.  
> Từ P06: chuyển sang mảng JS như trên.

## 6. Credential demo (UI login giả — nếu phiếu yêu cầu)

| Field | Value |
|-------|-------|
| username | `admin` |
| password | `CampusMart@01` |
| ghi chú | Chỉ UI / localStorage — **không** phải auth server thật |

## 7. Class CSS bắt buộc (khi phiếu yêu cầu layout)

| Class | Dùng từ | Ý nghĩa |
|-------|---------|---------|
| `.cm-header` | P05 | Header trang |
| `.cm-nav` | P05 | Nav ngang |
| `.cm-card` | P05 | Card sản phẩm |
| `.cm-grid` | P04/P05 | Lưới sản phẩm |
| `.cm-table` | P01+ | Bảng catalog (nếu dùng table) |

## 8. Hằng số phát sinh theo phiếu (P03–P12) — khóa sớm

### 8.1. CSS tokens (P03+)

| Token | Value exact |
|-------|-------------|
| `--cm-primary` | `#0f766e` |
| `--cm-bg` | `#f8fafc` |
| Breakpoint mobile | `640px` |
| Breakpoint desktop | `1024px` |

### 8.2. Title từng phiếu (exact)

| Phiếu | `<title>` |
|-------|-----------|
| P01 | `CampusMart — Catalog (Buoi 1)` |
| P02 | `CampusMart — Form & Media (Buoi 2)` |
| P03 | `CampusMart — CSS (Buoi 3)` |
| P04 | `CampusMart — Layout (Buoi 4)` |
| P05 | `CampusMart — Responsive (Buoi 5)` |
| P06 | `CampusMart — JS (Buoi 6)` |
| P07 | `CampusMart — Filter (Buoi 7)` |
| P08 | `CampusMart — Module & Storage (Buoi 8)` |
| P09 | `CampusMart — React (Buoi 9)` |
| P10 | `CampusMart — React State (Buoi 10)` |
| P11 | `CampusMart — React Create (Buoi 11)` |
| P12 | `CampusMart — React CRUD (Buoi 12)` |

### 8.3. `data-testid` bổ sung (P07+)

| testid | Ý nghĩa | Từ phiếu |
|--------|---------|----------|
| `cm-visible-count` | Đoạn text `Hien thi: N san pham` | P07 |
| `cm-subscribe-form` | Form đăng ký nhận tin | P02 |
| `cm-login-form` | Form login giả | P08 |
| `cm-product-form` | Form thêm/sửa product (React) | P11 |
| `cm-category-form` | Form thêm/sửa category (React) | P12 |

### 8.4. Chuỗi thông báo exact (validate / login / CRUD)

| Key | Chuỗi exact |
|-----|-------------|
| `err_name` | `Ten toi thieu 2 ky tu` |
| `err_email` | `Email khong hop le` |
| `msg_subscribed` | `Dang ky thanh cong` |
| `err_sku_dup` | `SKU da ton tai` |
| `err_price` | `Gia phai lon hon 0` |
| `msg_login_ok` | `Dang nhap thanh cong` |
| `err_login` | `Sai tai khoan hoac mat khau` |
| `err_delete_category` | `Khong the xoa danh muc con san pham` |

### 8.5. localStorage keys (P08)

| Key | Value |
|-----|-------|
| `cm_subscribers` | JSON array subscriber `{name, email, category_id}` |
| `cm_auth` | `"true"` sau login đúng |
| `cm_filter` | category_id đang chọn (`"all"`, `"1"`, `"2"`, `"3"`) |

### 8.6. Search theo tên (P10) — case-insensitive `includes`

| Query | SKU khớp | count |
|-------|----------|------:|
| `ra` | MS-02 (Razer Viper), MN-02 (LG Ult**ra**Fine) | **2** |
| `keychron` | KB-01 | **1** |
| `xyz` | — | **0** |

### 8.7. Product mới của P11 (Create)

| sku | name | category | price | qty | line_total |
|-----|------|----------|------:|----:|----------:|
| MN-03 | AOC 27 inch | Man hinh (3) | 4500000 | 2 | 9000000 |

Sau khi Create thành công (từ nền CORE_8):

| Key | Value |
|-----|------:|
| `product_count_after_create` | **9** |
| `manhinh_count_after_create` | **3** |
| `inventory_value_after_create` | **50380000** |

### 8.8. Kịch bản P12 (từ nền CORE_8, làm đúng thứ tự)

| Bước | Thao tác | Kết quả khóa |
|------|----------|--------------|
| 1 | Delete `MN-02` (confirm) | `product_count=7`, `inventory_value=32880000` |
| 2 | Edit `KB-01`: qty `3 → 4` | `inventory_value=34770000` |
| 3 | Create category `Phu kien` (id 4) | select danh mục có **4** option |
| 4 | Thử delete category `Chuot` | bị chặn: `Khong the xoa danh muc con san pham` |

Kiểm tra: `41380000 − 8500000 = 32880000`; `32880000 + 1890000 = 34770000`.

## 9. Kiểm tra nhanh tổng kho

```
KB-01: 1890000*3 = 5670000
KB-02: 1290000*5 = 6450000
KB-03: 2750000*2 = 5500000
MS-01:  290000*10 = 2900000
MS-02:  990000*4  = 3960000
MS-03:  250000*8  = 2000000
MN-01: 3200000*2  = 6400000
MN-02: 8500000*1  = 8500000
TỔNG = 41380000
```
