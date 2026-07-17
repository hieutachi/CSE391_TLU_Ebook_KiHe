# Phần 2 — 12 Phiếu học tập (chuỗi xuyên suốt CampusMart UI)

Mỗi phiếu gắn **1 buổi** (~45–60 phút). Bài **trên lớp** và **về nhà** nối tiếp.

> **Đích cuối (Phiếu 12):** React + 2 thực thể liên kết (Category ↔ Product) + **CRUD đủ trên products** (và quản lý rõ 2 thực thể). Không bắt buộc backend thật.

## Tài liệu khoá số liệu & chấm bài

| File | Vai trò |
|------|---------|
| **[CANONICAL-DATA.md](./CANONICAL-DATA.md)** | Single source of truth: 8 SP, tổng kho **41380000**, UI exact, testid, thông báo, kịch bản P12 |
| **[GRADING-CONTRACT.md](./GRADING-CONTRACT.md)** | CLO + EXPECT ổn định P01–P12 — sẵn cho Autograder |

```
P01–02  HTML: ngữ nghĩa → Form / Media / a11y
   ↓
P03–05  CSS: cascade/box → Flex/Grid → Responsive CampusMart
   ↓
P06–08  JS: nền → DOM filter → Module + localStorage
   ↓
P09–12  React: props → state/filter → Create → ★ CRUD 2 thực thể
```

| Phiếu | Buổi | Repo | Kết quả mang sang |
|-------|------|------|-------------------|
| [01](./phieu-01-html-ngu-nghia.md) | 1 | `cse391-cm-01` | Skeleton ngữ nghĩa + bảng 8 SP + testid |
| [02](./phieu-02-form-media.md) | 2 | `cse391-cm-02` | Form subscribe + 8 figure + a11y ≥90 |
| [03](./phieu-03-css-co-ban.md) | 3 | `cse391-cm-03` | `styles.css` tokens + bảng styled |
| [04](./phieu-04-flexbox-grid.md) | 4 | `cse391-cm-04` | Header flex + `.cm-grid` 8 card |
| [05](./phieu-05-responsive-campusmart.md) | 5 | `cse391-cm-05` | Landing responsive 640/1024 + hero |
| [06](./phieu-06-js-nen.md) | 6 | `cse391-cm-06` | `data.js` + 5 hàm helper + stats sống |
| [07](./phieu-07-dom-render-filter.md) | 7 | `cse391-cm-07` | `render(list)` + filter 8/3/3/2 + sort |
| [08](./phieu-08-module-localstorage.md) | 8 | `cse391-cm-08` | Module + persist filter + validate + login giả |
| [09](./phieu-09-react-props.md) | 9 | `cse391-cm-09` | Cây component React 8 card + stats |
| [10](./phieu-10-react-state-filter.md) | 10 | `cse391-cm-10` | State filter/search/sort + FilterBar |
| [11](./phieu-11-react-create.md) | 11 | `cse391-cm-11` | Form controlled + Create MN-03 (9/50380000) |
| [12](./phieu-12-react-crud.md) | 12 | `cse391-cm-12` | ★ **CRUD 2 thực thể** — kịch bản 5 bước |

## Bảng mang sang (carry-forward)

| Từ → Đến | Artifact mang theo |
|----------|--------------------|
| P01 → P02 | `index.html` ngữ nghĩa, bảng SP, `data-testid` |
| P02 → P03 | Form + figure + a11y — sẵn hook để style |
| P03 → P04 | `styles.css` tokens + `.cm-table` styled |
| P04 → P05 | Header flex + `.cm-grid` card |
| P05 → P06 | Landing tĩnh hoàn chỉnh |
| P06 → P07 | `data.js` + 5 hàm helper |
| P07 → P08 | `render(list)` + filter + sort |
| P08 → P09 | Tư duy module/validate → chuyển sang React |
| P09 → P10 | Cây component tĩnh |
| P10 → P11 | State + controlled input + FilterBar |
| P11 → P12 | products state + ProductForm Create |
| P12 → Project | SPA CRUD 2 thực thể làm nền project nhóm |

## Quy định nộp bài (mọi phiếu)

1. **Mã nguồn:** GitHub Repository riêng (`cse391-cm-{NN}`); React không commit `node_modules`.  
2. **Video OBS:** camera rõ mặt + chạy đúng kịch bản EXPECT của phiếu.  
3. Bám [CANONICAL-DATA](./CANONICAL-DATA.md) — không tự đổi giá/SKU core.

## Ghi chú nhất quán quan trọng

| Chủ đề | Quy ước |
|--------|---------|
| Tổng kho 8 SP CORE | **41380000** |
| Sau Create MN-03 (P11) | 9 SP · tổng **50380000** |
| Kịch bản P12 (từ CORE_8) | xóa MN-02 → **32880000**; sửa KB-01 qty 4 → **34770000** |
| Filter | all=8 · Ban phim=3 · Chuot=3 · Man hinh=2 |
| Search `ra` (P10) | 2 SKU: MS-02, MN-02 |
| Prefix EXPECT | `CM_EXPECT` (không dùng `MS_EXPECT`) |
| Credential login giả | `admin` / `CampusMart@01` |
| Repo | `cse391-cm-01` … `cse391-cm-12` |
