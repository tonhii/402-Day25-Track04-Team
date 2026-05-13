---
artifact: 1 — Lớp giao diện
bai-tap: 2 — Thiết kế giải pháp
demo: ./demo.md
---

# card.md — Lớp giao diện

**Tình huống xử lý**: C1 — Hallucination (Trích xuất sai số tiền từ bill)  
Xem `01-risk-map.md` Phần 4 — Primary failure deep dive.

---

## 1. Giải pháp là gì?

Khi AI đọc xong ảnh bill, giao diện **bắt buộc** hiện màn hình xác nhận với số tiền AI vừa trích xuất, đoạn text gốc AI đã đọc được, và mức độ tin cậy (confidence). Người dùng **phải bấm “Xác nhận đúng”** thì mới lưu vào sổ — không có nút “Lưu tất cả” hay tự động lưu. Nếu AI phát hiện nhiều con số lớn (vd có dòng “Tiền thối lại”), giao diện đổi sang trạng thái cảnh báo màu vàng và yêu cầu người dùng tự chọn con số đúng. Nếu ảnh quá mờ hoặc không đọc được, giao diện từ chối và hướng người dùng nhập tay.

---

## 2. Vì sao sửa ở lớp giao diện?

- **Người dùng dễ tin câu trả lời của AI quá mức** — Sinh viên/nhân viên văn phòng thao tác nhanh vào buổi tối, có xu hướng lướt qua và tin AI đọc đúng từ ảnh.
- **Nếu model vẫn sót lỗi, giao diện là lớp chặn cuối** — Model OCR có thể nhận diện sai dòng số trong bill phức tạp (tiền thối lại > tổng cộng). Nếu UI không có bước confirm, lỗi lọt thẳng vào database.

**Hành động phòng vệ chính**:

- [x] Thông báo rõ giới hạn — Luôn hiện “AI đọc từ ảnh có thể sai, vui lòng kiểm tra”
- [x] Phát hiện dấu hiệu có nhiều con số — Flag khi bill có dòng “Tiền thối / Khách đưa / Change”
- [x] Chuyển người thật khi cần — Nút “Nhập tay” luôn hiện ở mọi trạng thái
- [x] Giúp người dùng kiểm tra lại nguồn — Hiện đoạn text gốc AI đọc được để user tự đối chiếu

---

## 3. Demo nằm ở đâu?

**File demo**: [`demo.md`](./demo.md)

**Định dạng demo**:

- [x] Phác thảo màn hình (ASCII sketch)
- [ ] Luồng màn hình
- [ ] Bản HTML đơn giản
- [ ] Ảnh hoặc link prototype

**Thành phần có trong demo**:

- State 1 — DEFAULT: Bill rõ ràng, AI trích xuất 1 con số, confidence cao → màn hình xác nhận xanh
- State 2 — UNCERTAIN: Bill có nhiều con số lớn (tiền thối, tiền khách đưa) → cảnh báo vàng, user tự chọn
- State 3 — REFUSE: Bill mờ / không đọc được → từ chối, hướng nhập tay
- State 4 — PRESSURE-TRAP: User ép “cứ đoán đi” → UI không cho qua, giữ yêu cầu xác nhận

---

## 4. Tác dụng phụ

**Có thể gây vấn đề gì?**

Thêm bước xác nhận làm chậm thao tác — đặc biệt khi user chụp nhiều bill cùng lúc cuối tuần, phải confirm từng cái sẽ mất thêm thời gian và dễ gây mỏi (confirmation fatigue).

**Nhóm giảm vấn đề đó bằng cách nào?**

- Chỉ hiện cảnh báo mức UNCERTAIN khi AI phát hiện nhiều con số nghi ngờ — bill đơn giản vẫn nhanh.
- Màn hình xác nhận DEFAULT thiết kế tối giản: 1 tap “Đúng, lưu lại” là xong.
- Với batch bill, group các bill confidence cao thành 1 màn review, không confirm từng bill riêng lẻ.

---

## 5. Checklist trước khi nộp

- [x] Giải pháp gắn đúng với rủi ro chính C1 (Hallucination — trích xuất sai số tiền).
- [x] Demo nhìn vào là hiểu vấn đề được chặn ở đâu (bước confirm bắt buộc trước khi lưu).
- [x] Có đủ trạng thái bình thường (DEFAULT) và trạng thái lỗi (UNCERTAIN, REFUSE).
- [x] Có cách chuyển sang nhập tay khi AI không nên tự xử lý.
- [x] Câu chữ trong giao diện ngắn, không đổ hết trách nhiệm cho người dùng.

**Người phụ trách**: Hồ Thị Tố Nhi
