# Ebook Tự Học: Nền tảng phát triển Web (Frontend)

> **Môn học:** CSE391 – Nền tảng phát triển Web  
> **Đối tượng:** Sinh viên ngành Công nghệ thông tin  
> **Mức độ:** Từ trang HTML tĩnh đến React SPA CRUD 2 thực thể  
> **Đề tài xuyên suốt:** CampusMart UI (`categories` + `products`)

---

## Lời mở đầu

Chào các bạn sinh viên,

Mỗi lần bạn mở Shopee, xem lịch học online, hay nhấn nút **Thêm vào giỏ** trên một trang bán hàng — bạn đang tương tác với **Frontend**: phần giao diện chạy ngay trong trình duyệt. HTML dựng khung, CSS tô màu và bố cục, JavaScript làm trang “sống”, React giúp bạn tổ chức UI thành các mảnh ghép tái sử dụng.

Cuốn Ebook này đi theo lộ trình:

> **HTML ngữ nghĩa → CSS layout → JavaScript thuần → React SPA**

Lý do rất đơn giản:

> Framework là công cụ. Nếu bạn hiểu DOM, cascade và sự kiện, React sẽ là bạn đồng hành.  
> Nếu bạn chỉ “copy component từ tutorial”, React sẽ là hộp đen.

Song song 12 buổi là **12 phiếu học tập** (≥ 45 phút/phiếu). Mỗi phiếu có phần **trên lớp** và **về nhà** nối tiếp trên cùng chuỗi dữ liệu **CampusMart** — cửa hàng / catalog sản phẩm campus (bàn phím, chuột, màn hình… cùng miền số liệu quen thuộc với MiniShop Backend).

Chuỗi có **hai mốc đầu ra** rõ ràng:

> **Mốc 1 (hết Phiếu 08 — JS thuần):** một **Dashboard CampusMart phục vụ công tác CRUD, có Form Thêm** — stats, lọc danh mục, danh sách sản phẩm, thêm sản phẩm mới.  
> **Mốc 2 (Phiếu 12 — đích cuối):** **ứng dụng React CRUD hoàn chỉnh** — 2 thực thể liên kết (Category → Product), chọn danh mục → lọc/hiển thị, đủ Thêm/Sửa/Xóa trên products + quản lý danh mục. Không bắt buộc backend thật.

### Mục tiêu xuyên suốt 12 buổi + 12 phiếu

| Giai đoạn | Buổi / Phiếu | Bạn sẽ đạt được |
|-----------|--------------|-----------------|
| HTML | 1–2 / P01–P02 | Trang ngữ nghĩa + Form/Media + a11y cơ bản |
| CSS | 3–5 / P03–P05 | Cascade, box model → Flex/Grid → Dashboard tĩnh responsive |
| JavaScript | 6–8 / P06–P08 | DOM, events, filter → ★ **Dashboard CRUD có Form Thêm** (mốc 1) |
| React | 9–12 / P09–P12 | Props → State/filter → Create → ★ **Ứng dụng CRUD hoàn chỉnh 2 thực thể** (mốc 2) |
| Project nhóm | 13–15 | Routing, fetch API giả, deploy (điểm Cộng) |

### Cách dùng hiệu quả

1. Đọc buổi trên lớp → làm phần **Trên lớp** của phiếu cùng số.
2. Về nhà hoàn thành phần **Về nhà** (≥ 25–40 phút) — giữ nguyên dữ liệu chuỗi CampusMart.
3. Nộp GitHub repo riêng từng phiếu + video OBS (camera mặt).
4. Không nhảy cóc: phiếu sau dựa kết quả phiếu trước.
5. Mọi SKU / giá / tiêu đề UI lấy từ [`worksheets/CANONICAL-DATA.md`](./worksheets/CANONICAL-DATA.md) — **không tự bịa bộ số khác**.

### Công cụ bạn cần chuẩn bị

- Máy tính (Windows / macOS / Linux)
- Trình duyệt hiện đại (Chrome / Edge / Firefox) + DevTools (F12)
- [VS Code](https://code.visualstudio.com/) hoặc [Cursor](https://cursor.com/)
- [Node.js LTS](https://nodejs.org/) (từ Buổi 9 — Vite + React)
- Tài khoản [GitHub](https://github.com/)
- Phần mềm [OBS Studio](https://obsproject.com/) (quay minh chứng)
- Tùy chọn: Live Server (extension VS Code) để mở HTML cục bộ

### Liên thông với Backend (CSE485)

Dataset CORE 8 sản phẩm + 3 danh mục **trùng số liệu MiniShop** (tổng kho **41380000**). Khi học Backend, bạn sẽ lưu cùng miền dữ liệu vào PHP/Laravel; ở Frontend bạn học **hiển thị, lọc, CRUD trên UI**.

---

**Sẵn sàng chưa?** Hãy bắt đầu Buổi 1 — nơi bạn dựng bộ xương HTML ngữ nghĩa cho CampusMart.
