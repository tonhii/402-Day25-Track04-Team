---
artifact: 2 — Lớp chỉ dẫn AI
bai-tap: 2 — Thiết kế giải pháp
demo: ./demo.md
---

# card.md — Lớp chỉ dẫn AI

**Tình huống xử lý**: T-03 — Bill có nhiều dòng số lớn, AI lấy nhầm dòng "Tiền khách đưa / Tiền thối" thay vì "Tổng cộng"  
Rủi ro chính: C1 — Hallucination (trích xuất sai số tiền)

---

## 1. Giải pháp là gì?

Thêm vào system prompt 3 luật cứng: (1) AI phải xác định rõ dòng "Tổng cộng / Total / Thành tiền" — không được lấy dòng "Tiền khách đưa / Tiền thối / Change"; (2) khi phát hiện nhiều dòng số có giá trị lớn, AI bắt buộc liệt kê tất cả và hỏi user xác nhận thay vì tự chọn; (3) khi ảnh mờ hoặc không đọc rõ, AI phải từ chối đoán và yêu cầu nhập tay — không được đưa ra con số dù user ép. Thêm few-shot examples cho 2 tình huống dễ nhầm nhất (bill VN có tiền thối, hóa đơn VAT 3 dòng).

---

## 2. Vì sao sửa ở lớp chỉ dẫn AI?

- **AI cần luật rõ: khi nào trả lời, khi nào từ chối** — Model OCR/LLM mặc định cố gắng helpful, có xu hướng trả về con số to nhất hoặc rõ nhất. Cần prompt luật tường minh để override hành vi này.
- **Có thể sửa nhanh bằng prompt trước khi thay đổi hệ thống lớn hơn** — Luật prompt là lớp chặn có thể deploy ngay, không cần sửa model hay pipeline OCR.

**Hành động phòng vệ chính**:

- [x] Ngăn câu trả lời sai ngay từ đầu — luật ưu tiên dòng "Tổng cộng" rõ ràng
- [x] Từ chối trả lời khi thiếu căn cứ — không đoán khi ảnh mờ
- [x] Bắt buộc liệt kê khi có nhiều con số — không tự chọn thay user
- [x] Chuyển người thật khi vượt phạm vi — từ chối tư vấn tài chính, yêu cầu nhập tay khi cần

---

## 3. Demo nằm ở đâu?

**File demo**: [`demo.md`](./demo.md)

**Nội dung demo có**:

- System prompt đầy đủ với 5 luật cứng
- Mẫu câu khi AI không đọc được rõ
- Mẫu câu khi AI phát hiện nhiều con số nghi ngờ
- 4 ví dụ hỏi đáp bám sát T-02, T-03, T-07, T-09
- Bảng kết quả thử lại với 5 tình huống từ Bài 1

---

## 4. Tác dụng phụ

**Có thể gây vấn đề gì?**

Luật "luôn hỏi khi có nhiều con số" có thể khiến AI hỏi xác nhận ngay cả với bill đơn giản chỉ có 1 dòng tổng nhưng kèm dòng "Số lượng: 2" — gây thêm friction không cần thiết.

**Nhóm giảm vấn đề đó bằng cách nào?**

Luật được viết chính xác: chỉ flag khi có ≥ 2 dòng số **tiền tệ** (đơn vị đồng/VND/k) có giá trị > 10.000đ — không flag dòng số lượng sản phẩm. Few-shot examples minh hoạ rõ ngưỡng này để AI phân biệt.

---

## 5. Checklist trước khi nộp

- [x] Luật viết đủ cụ thể để AI làm theo (5 luật tường minh, không mơ hồ).
- [x] Có mẫu câu khi AI không đọc được rõ ảnh.
- [x] Có ví dụ cho tình huống dễ sai (bill có tiền thối, ảnh mờ, user ép đoán).
- [x] Có thử lại bằng tình huống T-02, T-03, T-07, T-09 từ Bài 1.
- [x] Không dùng prompt như cách duy nhất — phối hợp với lớp UI (bước confirm) và lớp Architecture (confidence score từ OCR pipeline).

**Người phụ trách**: Hồ Thị Tố Nhi
