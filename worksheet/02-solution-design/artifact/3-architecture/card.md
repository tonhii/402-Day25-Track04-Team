---
artifact: 3 — Lớp kiến trúc dữ liệu
bai-tap: 2 — Thiết kế giải pháp
demo: ./demo.md
---

# card.md — Lớp kiến trúc dữ liệu

**Tình huống xử lý**: T-03 — Bill có nhiều dòng số lớn, AI lấy dòng to nhất (tiền khách đưa / tiền thối) thay vì dòng "Tổng cộng"  
Xem `../../1-map-and-format.md` Phần A.

---

## 1. Giải pháp là gì?

Hệ thống thêm một lớp **kiểm tra cấu trúc hóa đơn** sau OCR để phân biệt các dòng số như `Tổng cộng`, `Thành tiền`, `Tiền khách đưa`, `Tiền thối lại`, `VAT`, `Subtotal`, `Service charge`. AI không được chọn số tiền chỉ vì số đó lớn hơn hoặc font to hơn; thay vào đó hệ thống phải tạo danh sách các số đọc được kèm nhãn dòng, vị trí trên bill và mức độ chắc chắn.

Sau bước kiểm tra, kết quả được chuyển sang UI theo 4 trạng thái: `DEFAULT`, `UNCERTAIN`, `REFUSE`, `PRESSURE-TRAP`. Nếu không xác định chắc dòng "Tổng cộng", hệ thống không cho lưu thẳng vào database mà yêu cầu người dùng xác nhận, chọn lại dòng đúng, chụp lại ảnh hoặc nhập tay.

---

## 2. Vì sao sửa ở lớp kiến trúc dữ liệu?

- AI đang đọc OCR thô nhưng chưa hiểu cấu trúc hóa đơn, nên dễ lấy nhầm số lớn nhất hoặc dòng in đậm nhất.
- Cần kiểm tra dữ liệu trước khi câu trả lời được tạo ra và trước khi giao dịch được lưu vào database.
- Giao diện hiện chưa có bước xác nhận đủ mạnh, nên lỗi model/OCR có thể lọt thẳng vào sổ chi tiêu.
- Cần ghi lại lỗi để nhóm biết pattern nào lặp lại nhiều: lấy nhầm tiền khách đưa, tiền thối, subtotal, VAT hoặc số khác.

**Hành động phòng vệ chính**:

- [x] Ngăn lỗi bằng bước kiểm tra cấu trúc hóa đơn trước khi lưu
- [x] Phát hiện khi bill có nhiều dòng số hoặc OCR confidence thấp
- [x] Khắc phục bằng UI xác nhận / chọn dòng đúng / nhập tay / chụp lại
- [x] Ghi lại lỗi để cải thiện sau

---

## 3. Demo nằm ở đâu?

**File demo**: [`demo.md`](./demo.md)

Demo cần có:

- Sơ đồ cách dữ liệu đi từ upload bill → OCR → validator → UI state → database.
- Nguồn dữ liệu/rule-set về các nhãn thường gặp trên bill Việt Nam.
- Bước kiểm tra cấu trúc bill trước khi AI đề xuất số tiền.
- Cách xử lý khi ảnh mờ, thiếu dòng tổng, nhiều số gây nhầm hoặc user ép AI "ước chừng".
- Cách ghi lại hoặc theo dõi lỗi OCR chọn sai dòng tiền.

---

## 4. Tác dụng phụ

**Có thể gây vấn đề gì?**

- Trả lời chậm hơn vì phải chạy thêm OCR layout parser và receipt validator.
- Một số bill hợp lệ nhưng format lạ có thể bị chuyển sang trạng thái `UNCERTAIN`, khiến user phải xác nhận thủ công.
- Nếu rule-set quá cứng, hệ thống có thể không nhận ra bill viết tay, bill vỉa hè, hoặc bill dùng từ khác như `Thanh toán`, `Phải trả`, `Grand total`.
- User đang vội có thể thấy phiền vì app không cho lưu ngay khi có nhiều dòng số.

**Nhóm giảm vấn đề đó bằng cách nào?**

- Chỉ bắt buộc review mạnh khi bill có nhiều dòng số dễ nhầm hoặc confidence thấp.
- Dùng cache rule-set nhãn bill phổ biến để giảm latency.
- Trong trạng thái `UNCERTAIN`, UI cho user chọn nhanh đúng dòng số thay vì nhập lại toàn bộ.
- Với ảnh mờ hoặc thiếu dòng tổng, chuyển sang `REFUSE` và yêu cầu chụp lại/nhập tay thay vì để AI đoán.
- Theo dõi `wrong_amount_correction_rate`, `uncertain_rate`, `refuse_rate` để điều chỉnh rule và UI sau launch.

---

## 5. Checklist trước khi nộp

- [x] Sơ đồ cho thấy dữ liệu đi từ đâu đến đâu.
- [x] Có bước kiểm tra cấu trúc bill trước khi AI trả lời.
- [x] Có cách xử lý khi không xác định được dòng "Tổng cộng".
- [x] Có cách chuyển sang xác nhận thủ công / nhập tay với tình huống rủi ro cao.
- [x] Có cách biết lỗi này có đang lặp lại không.

**Người phụ trách**: Trần Thị Kim Ngân
