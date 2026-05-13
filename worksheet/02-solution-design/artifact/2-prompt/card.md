
# card.md — Lớp chỉ dẫn AI 

**Tình huống xử lý chính**: T-03 — Bill có nhiều dòng số lớn, AI lấy nhầm "Tiền khách đưa" / "Tiền thối" thay vì dòng "Tổng cộng"  



---

## 1. Giải pháp là gì?

Thêm vào system prompt một luật **Field Priority & Disambiguation**: AI không được lấy số tiền lớn nhất trong ảnh, mà phải ưu tiên theo thứ tự từ khóa: `"Tổng cộng" > "Tổng" > "Thành tiền" > "Total"`. Nếu không tìm thấy từ khóa ưu tiên hoặc phát hiện nhiều dòng số lớn tương đương, AI **bắt buộc phải hỏi lại người dùng** — không được tự chọn.

> **Luật cốt lõi (copy-paste được):**
>
> *Khi đọc hóa đơn, AI phải tìm dòng có nhãn "Tổng cộng", "Tổng", "Thành tiền", hoặc "Total" để lấy số tiền thanh toán thực tế. Không được lấy dòng "Tiền khách đưa", "Tiền thối", "Tiền thừa", "Cash", "Change". Nếu không xác định được dòng nào là tổng thanh toán cuối cùng, AI phải hiển thị tất cả dòng số tìm thấy và hỏi người dùng chọn, không được tự quyết định.*

**Cơ chế if/else (4 trạng thái):**

| Trạng thái | Điều kiện | Hành vi AI |
|---|---|---|
| **DEFAULT** | Tìm thấy đúng 1 dòng "Tổng cộng" rõ ràng | Đề xuất số tiền, hỏi xác nhận |
| **UNCERTAIN** | Nhiều dòng số lớn, không rõ dòng nào là tổng | Liệt kê tất cả, hỏi user chọn |
| **REFUSE** | Ảnh quá mờ / bị che / không đọc được dòng nào | Từ chối trích xuất, yêu cầu chụp lại hoặc nhập tay |
| **PRESSURE-TRAP** | User ép "áng chừng đi", "đoán cũng được" | Giữ nguyên boundary: "Mình không thể đoán số tiền — nhập tay để chắc chắn nhé" |

---

## 2. Vì sao sửa ở lớp chỉ dẫn AI?

**Lý do 1 — AI đoán khi không biết, không có luật ưu tiên trường dữ liệu.**  
Model OCR hiện tại xử lý bill như một danh sách số, lấy số xuất hiện nổi bật nhất (font to, vị trí giữa ảnh). Không có luật nào nói "ưu tiên từ khóa Tổng cộng" → AI tự suy luận sai → lỗi hệ thống.

**Lý do 2 — Có thể fix ngay bằng prompt trước khi có giải pháp OCR sâu hơn.**  
Sửa tầng model OCR cần thời gian và data. Luật prompt là lớp phòng thủ đầu tiên, deploy được ngay, không cần chờ pipeline thay đổi.

**Hành động phòng vệ chính:**

- [x] **Ngăn** — AI có danh sách từ khóa ưu tiên, không lấy số lớn nhất một cách mù quáng
- [ ] Bắt buộc nêu nguồn (không áp dụng cho tình huống này)
- [x] **Từ chối** — khi ảnh mờ hoặc không xác định được, bắt buộc yêu cầu nhập tay
- [x] **Thông báo** — hiển thị tất cả dòng số khi UNCERTAIN, để người dùng tự chọn

**Tại sao cần cả lớp UI/UX?**  
Lớp prompt xử lý phần AI hiểu bill. Nhưng dù AI đề xuất đúng, nếu UI cho phép user bấm "Lưu tất cả" mà không đọc từng khoản, lỗi vẫn lọt. Cần phối hợp với `artifact/1-uiux/` để buộc user xác nhận từng số tiền — đặc biệt khi AI đang ở trạng thái UNCERTAIN.

---

## 3. Demo nằm ở đâu?

**File demo**: [`demo.md`](./demo.md)

Demo gồm:
- System prompt đầy đủ với luật Field Priority & 4 States
- Danh sách từ khóa ưu tiên và từ khóa bị cấm lấy
- 3 mẫu câu chuẩn cho DEFAULT / UNCERTAIN / PRESSURE-TRAP
- 5 ví dụ hỏi–đáp thực tế (AI sai vs AI đúng)
- Bảng thử lại với toàn bộ test case T-01 đến T-04 và các case liên quan

---

## 4. Tác dụng phụ

**Vấn đề có thể gặp:**

| Tác dụng phụ | Lý do xảy ra | Mức nghiêm trọng |
|---|---|---|
| Over-refusal: AI hỏi lại quá nhiều dù bill rõ ràng | Danh sách từ khóa ưu tiên quá hẹp, không nhận ra biến thể ("Tổng tt", "TOTAL DUE") | Trung bình |
| Trải nghiệm chậm: mỗi bill UNCERTAIN đều hiện bảng chọn | User phải tương tác thêm 1 bước khi bill phức tạp | Thấp — chấp nhận được vì đổi lấy độ chính xác |
| AI hiển thị danh sách số quá dài (bill siêu thị nhiều dòng) | Trạng thái UNCERTAIN liệt kê quá nhiều lựa chọn | Trung bình |

**Cách giảm tác dụng phụ:**

1. **Mở rộng từ khóa ưu tiên:** Bổ sung các biến thể VN thực tế: "Tổng tt", "T.Cộng", "TOTAL", "Grand Total", "Tổng thanh toán", "Số tiền phải trả".

2. **Giới hạn UNCERTAIN:** Nếu có dòng "Tổng cộng" nhưng cũng có nhiều số lớn khác → vẫn chọn "Tổng cộng", chỉ dùng UNCERTAIN khi **không có** từ khóa ưu tiên nào.

3. **Giới hạn hiển thị UNCERTAIN:** Chỉ liệt kê tối đa 3 dòng số có khả năng là tổng cao nhất — không liệt kê toàn bộ.

4. **Kiểm thử lại** với T-01 (bill rõ ràng) và T-02 (user nhắc số) để xác nhận luật không làm chậm flow hợp lệ.

---

## 5. Checklist trước khi nộp

- [x] Luật viết đủ cụ thể — có danh sách từ khóa ưu tiên, danh sách từ khóa cấm, 4 states rõ ràng
- [x] Có mẫu câu chuẩn cho từng trạng thái (DEFAULT / UNCERTAIN / REFUSE / PRESSURE-TRAP)
- [x] Có 5 ví dụ hỏi–đáp, bao gồm bill phổ biến nhất (quán ăn VN có tiền thối)
- [x] Có thử lại bằng toàn bộ test set — xem bảng demo.md Mục 4
- [x] Không dùng prompt như cách duy nhất — phối hợp với lớp UI/UX (1-uiux/) để chặn lỗi tại bước xác nhận

