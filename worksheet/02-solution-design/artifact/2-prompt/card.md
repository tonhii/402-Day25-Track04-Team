---
artifact: 2 — Lớp chỉ dẫn AI
bai-tap: 2 — Thiết kế giải pháp
demo: ./demo.md
---

# Lớp chỉ dẫn AI

**Tình huống xử lý**: U-15 — Lộ OTP từ SMS ngân hàng  
Xem `../../1-map-and-format.md` Phần A và `2-converge.md` Phần C.

---

## 1. Giải pháp là gì?

Thêm vào system prompt một luật **PII Detection & Hard-Stop** bắt buộc AI quét chuỗi nhạy cảm (OTP, số thẻ, số tài khoản) TRƯỚC khi xử lý nội dung SMS/ảnh bill. Nếu phát hiện bất kỳ pattern nào trong danh sách cấm, AI phải **dừng ngay lập tức**, chỉ trích xuất số tiền giao dịch sau khi đã xóa (mask) phần nhạy cảm, và hiển thị cảnh báo rõ ràng cho người dùng.

> **Luật cốt lõi**: Khi nội dung đầu vào chứa chuỗi khớp với pattern OTP (6–8 chữ số sau từ khóa "OTP", "mã xác nhận", "ma xac nhan", "verification code"), số thẻ tín dụng (16 chữ số theo định dạng Visa/Mastercard/JCB), hoặc số tài khoản ngân hàng đầy đủ — AI **không được lưu, hiển thị lại, hay log** bất kỳ chuỗi đó. AI chỉ được trích xuất số tiền giao dịch (sau khi đã mask dữ liệu nhạy cảm) và phải thông báo cho người dùng rằng nội dung nhạy cảm đã được tự động xóa.

---

## 2. Vì sao sửa ở lớp chỉ dẫn AI?

**Lý do chính:**
- **AI đang thiếu luật rõ về: xử lý / từ chối / mask dữ liệu nhạy cảm.** Hiện tại AI không có quy tắc phân biệt "số tiền cần lưu" với "chuỗi số nhạy cảm cần hủy". Cả hai trông giống nhau trong raw text SMS.
- **Có thể fix ngay bằng prompt** mà không cần thay đổi hệ thống backend hay pipeline OCR. Đây là lớp phòng thủ đầu tiên, nhanh nhất và có thể deploy ngay trước launch.

**Hành động phòng vệ chính**:

- [x] Ngăn câu trả lời sai ngay từ đầu — AI học cách nhận dạng và bỏ qua OTP/thẻ
- [ ] Bắt buộc nêu nguồn khi nói về thông tin quan trọng
- [x] Từ chối trả lời khi thiếu căn cứ — từ chối lưu nội dung khi detect PII
- [x] Chuyển người thật khi vượt phạm vi — cảnh báo người dùng về hành động vừa được chặn

**Tại sao không chỉ dùng luật giao diện?**  
Giao diện chỉ hiện cảnh báo sau khi AI đã xử lý. Nếu AI log raw SMS trước khi giao diện render, dữ liệu nhạy cảm đã đi vào hệ thống. Cần chặn ở lớp instruction — trước khi AI làm bất cứ việc gì với input.

---

## 3. Demo nằm ở đâu?

**File demo**: [`demo.md`](./demo.md)

Demo gồm:

- System prompt đầy đủ với luật PII Detection
- Danh sách pattern cần chặn
- Mẫu câu phản hồi khi phát hiện OTP / số thẻ
- 4 ví dụ hỏi–đáp với tình huống thật từ bộ test
- Bảng thử lại với U-15, U-16, U-21 và các case liên quan

---

## 4. Tác dụng phụ

**Vấn đề có thể gặp:**

| Tác dụng phụ | Lý do xảy ra |
|---|---|
| **Over-refusal:** AI từ chối cả SMS hợp lệ vì nhận nhầm số điện thoại 10 chữ số là số thẻ | Regex pattern số thẻ 16 chữ số quá rộng, khớp với chuỗi số trong số điện thoại VN (10 chữ số) hoặc mã giao dịch |
| **False positive OTP:** Chuỗi "Mã đơn hàng: 123456" bị hiểu là OTP | Pattern chuỗi 6 chữ số sau từ khóa mơ hồ có thể nhầm mã vận đơn, mã đơn hàng |
| **Trải nghiệm cứng:** Mỗi SMS bị chặn AI giải thích dài dòng, user mất kiên nhẫn | Thông báo lỗi quá verbose, lặp đi lặp lại |

**Cách giảm tác dụng phụ:**

1. **Tách "soft-block" và "hard-block":**
   - *Hard-block*: OTP rõ ràng (có từ khóa "OTP", "Ma xac nhan", "han dung X phut"), số thẻ 16 chữ số theo Luhn checksum, password — **Không bao giờ lưu, không log**.
   - *Soft-block*: Số điện thoại người thứ 3, số tài khoản chưa chắc — **Lưu nhưng mask (4 số cuối), cảnh báo user**.

2. **Giới hạn phạm vi áp dụng:** Chỉ kích hoạt pattern detection khi input là SMS ngân hàng hoặc ảnh bill. Không áp dụng cho câu hỏi thông thường dạng text.

3. **Thông báo ngắn gọn:** Dùng mẫu câu chuẩn 1–2 câu, không giải thích dài. (Xem demo.md)

4. **Kiểm thử lại** với U-02 (ảnh mờ), U-06 (viết tắt 35k), U-11 (batch upload) để đảm bảo luật mới không phá vỡ flow bình thường.

---

## 5. Checklist trước khi nộp

- [x] Luật viết đủ cụ thể để AI làm theo — có danh sách pattern, có if/else rõ ràng.
- [x] Có mẫu câu khi AI không có đủ thông tin — xem demo.md Mục 2.
- [x] Có ví dụ cho tình huống dễ sai — 4 ví dụ, bao gồm edge case OTP không dấu.
- [x] Có thử lại bằng tình huống trong bộ test — bảng thử lại với U-15, U-16, U-17, U-21.
- [x] Không dùng prompt như cách duy nhất — Lớp kiến trúc (artifact/3-architecture/) xử lý redact ở pipeline OCR trước khi vào LLM.

