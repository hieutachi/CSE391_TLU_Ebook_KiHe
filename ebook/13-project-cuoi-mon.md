# BUỔI 13–15: Project cuối môn (nhóm) — Frontend SPA

> **Thời lượng:** 3 buổi trên lớp + làm việc nhóm ngoài giờ  
> **Tiên quyết:** Hoàn thành chuỗi Phiếu 01–12 (đặc biệt P12 — CRUD 2 thực thể)  
> **Hình thức:** Nhóm 3–4 sinh viên; một repo GitHub chung; chấm demo + vấn đáp cá nhân

---

## 1. Mục tiêu

Project là nơi bạn chứng minh có thể **tự thiết kế** một SPA hoàn chỉnh — không còn bám phiếu từng bước. Yêu cầu cốt lõi giữ nguyên tinh thần P12, nhưng trên **miền dữ liệu do nhóm chọn**.

## 2. Đề tài

Chọn miền dữ liệu có quan hệ **1–nhiều** rõ ràng (không dùng lại nguyên xi CampusMart):

| Gợi ý đề tài | 2 thực thể |
|--------------|------------|
| Thư viện mini | TheLoai → Sach |
| Quản lý CLB | CLB → ThanhVien |
| Menu quán cafe | NhomMon → MonAn |
| Lịch tập gym | NhomBaiTap → BaiTap |
| Cửa hàng khác miền | DanhMuc → SanPham (miền khác: sách vở, thời trang…) |

Đăng ký đề tài với giảng viên **cuối Buổi 12** — hai nhóm không trùng miền trong cùng lớp.

## 3. Yêu cầu bắt buộc (mức Đạt — trần điểm B)

Phản chiếu đúng chuẩn P09–P12:

1. React (Vite) + function component + hooks; cây component tách hợp lý (≥ 6 component).
2. **2 thực thể liên kết** (1–nhiều) trong state; seed ≥ 3 + ≥ 8 bản ghi, khai báo trong `src/data.js` kiểu CANONICAL riêng của nhóm (SKU/mã + số liệu khóa trong README).
3. **CRUD đủ trên thực thể "nhiều"** (list, filter theo thực thể "một", search, create + validate, update, delete + confirm).
4. Quản lý thực thể "một": thêm + chặn xóa khi còn bản ghi con (thông báo rõ).
5. Derived data cho filter/search; state bất biến (không push/splice).
6. Responsive 2 mốc 640/1024; a11y cơ bản (label, alt, landmark).
7. `data-testid` cho: brand, list container, item, form, visible-count (đặt tên theo tiền tố nhóm, khai báo trong README).
8. README: thành viên + phân công, dataset khóa, hướng dẫn chạy, bảng EXPECT tự định nghĩa (≥ 6 dòng).

## 4. Điểm Cộng (mức A — chọn ≥ 2 mục, làm thật)

| Mục | Nội dung | Gợi ý |
|-----|----------|--------|
| Routing | `react-router-dom`: trang list / trang chi tiết / trang quản trị | ≥ 3 route |
| Persist | localStorage (hoặc IndexedDB) — F5 không mất dữ liệu | pattern P08 |
| Fetch API giả | `json-server` hoặc `fetch` từ file JSON tĩnh + trạng thái loading/error | `useEffect` |
| Deploy | Vercel / Netlify / GitHub Pages — link chạy công khai | đưa link vào README |
| UI framework | Tailwind hoặc Bootstrap — sau khi đã chứng minh Flex/Grid tay ở P04–05 | nhất quán toàn app |
| Test | ≥ 5 test Vitest + React Testing Library cho helper/filter | chạy trong CI càng tốt |

## 5. Cấm & trừ điểm

- ❌ Clone template admin có sẵn rồi đổi chữ.
- ❌ Copy CampusMart đổi tên biến (chấm chéo diff với ebook).
- ❌ Thành viên không nắm code phần mình (vấn đáp cá nhân — điểm cá nhân có thể khác điểm nhóm).
- ❌ Commit dồn 1 cục cuối kỳ — lịch sử commit phải trải theo tuần, đủ tên các thành viên.

## 6. Mốc nộp theo buổi

| Buổi | Mốc | Nộp gì |
|------|-----|--------|
| 13 | Kick-off | Đề tài + wireframe giấy/Figma + dataset khóa (bảng canonical của nhóm) + phân công |
| 14 | Giữa kỳ project | Repo: xong Read + filter + Create; demo 5' trước lớp |
| 15 | Demo cuối | SPA đủ mục 3 (+ điểm Cộng); video OBS 5–8' cả nhóm (đủ mặt); vấn đáp |

## 7. Rubric chấm (100đ project)

| Tiêu chí | Điểm |
|----------|-----:|
| CRUD 2 thực thể đúng + bất biến | 30 |
| Kiến trúc component + derived state | 15 |
| Validate + thông báo + confirm | 10 |
| Responsive + a11y | 10 |
| Dataset khóa + testid + README EXPECT | 10 |
| Lịch sử Git + teamwork đều tay | 10 |
| Vấn đáp cá nhân | 15 |
| **Điểm Cộng (mục 4)** | tối đa +15 |

## 8. Lời khuyên từ giảng viên

1. **Dataset khóa trước, code sau** — đúng bài học CANONICAL-DATA: cả nhóm cãi nhau về số liệu ngay Buổi 13, đừng để đến demo.
2. Chia module theo **tính năng** (mỗi người 1 nhánh: form / list / category / layout), merge sớm và thường xuyên.
3. Làm chạy được mức Đạt **trước**, mới thêm điểm Cộng — routing đẹp không cứu được CRUD hỏng.
4. Video demo: chạy kịch bản EXPECT của chính nhóm mình, như đã quen 12 phiếu.

---

*CSE391 – Ebook Frontend CampusMart UI | Project cuối môn*
