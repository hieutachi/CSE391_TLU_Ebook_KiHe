# Ebook Tự Học: Nền tảng phát triển Web (Frontend)

> **Môn học:** CSE391 – Nền tảng phát triển Web  
> **Đối tượng:** Sinh viên ngành CNTT — **kể cả khi chưa từng viết HTML**  
> **Mức độ:** Từ trang tĩnh đến React SPA CRUD 2 thực thể  
> **Đề tài xuyên suốt (single source of truth):** CampusMart UI (`categories` + `products`)  
> **Dataset khóa:** [`worksheets/CANONICAL-DATA.md`](./worksheets/CANONICAL-DATA.md) — 8 SP, tổng kho **41380000**

---

## Lời mở đầu

Chào các bạn sinh viên,

Mỗi lần bạn mở Shopee, xem lịch học online, hay nhấn **Thêm vào giỏ** — bạn đang dùng **Frontend**: phần giao diện chạy trong trình duyệt. HTML dựng khung, CSS tô màu và bố cục, JavaScript làm trang “sống”, React giúp bạn tổ chức UI thành các mảnh tái sử dụng.

Cuốn Ebook này **không giả định** bạn đã biết Web. Nếu từ khóa *DOM*, *HTTP*, *CSS Flex* còn mơ hồ — đúng chỗ rồi. Mỗi buổi đi từ **hình ảnh đời thường → sơ đồ ASCII → code nhỏ → thử ngay trên máy → phiếu**.

Lộ trình:

> **HTML ngữ nghĩa → CSS layout → JavaScript thuần → React SPA**

> Framework là công cụ. Nếu bạn hiểu DOM, cascade và sự kiện, React sẽ là bạn đồng hành.  
> Nếu bạn chỉ “copy component từ tutorial”, React sẽ là hộp đen.

### Hai mốc đầu ra (nhớ suốt khóa)

| Mốc | Phiếu | Bạn cầm được gì trên tay? |
|-----|-------|---------------------------|
| **1 — JS thuần** | **P08** | **01 Dashboard CampusMart phục vụ CRUD, có Form Thêm** (stats + lọc + danh sách + thêm SP) |
| **2 — React** | **P12** | **Ứng dụng CRUD hoàn chỉnh**: Category ↔ Product, Thêm/Sửa/Xóa + chặn xóa danh mục còn hàng |

Không bắt buộc backend thật. Số liệu CORE trùng MiniShop Backend (CSE485) để hai môn nói cùng một “cửa hàng campus”.

### Mục tiêu xuyên suốt 12 buổi + 12 phiếu

| Giai đoạn | Buổi / Phiếu | Bạn sẽ đạt được |
|-----------|--------------|-----------------|
| HTML | 1–2 / P01–P02 | Trang ngữ nghĩa + Form/Media + a11y cơ bản |
| CSS | 3–5 / P03–P05 | Cascade, box model → Flex/Grid → **Dashboard tĩnh** responsive |
| JavaScript | 6–8 / P06–P08 | DOM, events, filter → ★ **Dashboard CRUD có Form Thêm** (mốc 1) |
| React | 9–12 / P09–P12 | Props → State → Create → ★ **CRUD hoàn chỉnh 2 thực thể** (mốc 2) |
| Project nhóm | 13–15 | Routing, fetch API giả, deploy (điểm Cộng) |

---

## Cách đọc Ebook khi bạn chưa “biết Web” (bắt buộc đọc)

Ebook này là **source of truth**: đọc trước khi xem YouTube / hỏi AI. Làm theo thứ tự:

```
1. Đọc mục tiêu buổi (biết mình sẽ làm được gì)
2. Đọc phần “Vì sao” + sơ đồ ASCII (đừng bỏ qua hình)
3. Gõ LẠI code minh họa (không copy-paste — tay nhớ khác mắt nhớ)
4. Làm mục “Thử ngay trên máy” — dự đoán → chạy → so sánh
5. Trả lời “Câu hỏi tự kiểm tra” (nói to hoặc viết 2–3 câu)
6. Mới sang Phiếu cùng số: trên lớp → về nhà
7. Chỉ khi xong phiếu mới đọc buổi kế
```

### Ba quy tắc vàng

1. **Một bộ số liệu duy nhất** — mọi SKU/giá/title/`data-testid` lấy từ [CANONICAL-DATA](./worksheets/CANONICAL-DATA.md). Đừng tự bịa “cho vui”.
2. **Trên lớp và về nhà nối tiếp** — cùng repo, cùng chuỗi CampusMart; không làm bài “mẫu khác đề tài”.
3. **Hiểu trước, đẹp sau** — buổi đầu chấp nhận trang xấu; đến CSS/React mới tô. Nhảy cóc React buổi 1 = học thuộc lòng.

### Hình ảnh nhớ cả khóa (dán đầu vở)

```
CampusMart giống một cửa hàng campus:

  HTML  = khung kệ + biển hiệu + bảng giá (xương trang)
  CSS   = sơn màu, sắp kệ ngang/dọc, điện thoại/desktop
  JS    = nhân viên: lọc hàng, tính tổng, thêm SP vào sổ
  React = hệ thống quản lý: tự cập nhật bảng khi sổ thay đổi
            + đủ Thêm / Sửa / Xóa + quản lý danh mục

  CANONICAL-DATA = sổ kho chính thức (8 SP, tổng 41380000)
```

### Công cụ chuẩn bị

| Công cụ | Dùng từ |
|---------|---------|
| Máy tính + trình duyệt Chrome/Edge/Firefox | Buổi 1 |
| [VS Code](https://code.visualstudio.com/) hoặc [Cursor](https://cursor.com/) | Buổi 1 |
| Extension **Live Server** (VS Code) | Buổi 1 |
| DevTools (phím **F12**) | Buổi 1 — bạn sẽ sống với nó |
| [Node.js LTS](https://nodejs.org/) | Buổi 9 (Vite + React) |
| Tài khoản [GitHub](https://github.com/) | Mọi phiếu |
| [OBS Studio](https://obsproject.com/) | Quay minh chứng (có camera mặt) |

### Liên thông CSE485 (Backend)

Cùng 3 danh mục + 8 SKU + tổng kho **41380000**. Frontend học **hiển thị / lọc / CRUD trên UI**; Backend học **lưu DB / Session / Eloquent**. Hai mặt của một MiniShop–CampusMart.

---

**Sẵn sàng chưa?** Mở [Buổi 1](./01-buoi-01-web-html-ngu-nghia.md) — đặt viên gạch đầu: Web là gì, HTML là xương, và dựng skeleton CampusMart đầu tiên.
