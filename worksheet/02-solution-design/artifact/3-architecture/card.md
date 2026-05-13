---
artifact: 3 — Lớp kiến trúc dữ liệu
bai-tap: 2 — Thiết kế giải pháp
demo: ./demo.md
---

# card.md — Lớp kiến trúc dữ liệu

**Tình huống xử lý**: T-03 — Bill có nhiều dòng số lớn, AI lấy nhầm dòng "Tiền khách đưa / Tiền thối" thay vì "Tổng cộng"  
Rủi ro chính: C1 — Hallucination (trích xuất sai số tiền)

---

## 1. Giải pháp là gì?

Thêm vào pipeline xử lý 3 bước kiểm tra trước khi kết quả OCR đến tay AI: (1) **Image Quality Gate** — đánh giá độ rõ của ảnh, nếu confidence < ngưỡng thì chặn và yêu cầu chụp lại, không đưa vào model; (2) **Multi-number Detector** — sau khi OCR chạy xong, quét kết quả để phát hiện có ≥ 2 dòng số tiền lớn, nếu có thì gán flag `UNCERTAIN` trước khi đưa sang LLM; (3) **PII Filter** — lọc OTP, số thẻ, số tài khoản ra khỏi kết quả OCR trước khi LLM nhìn thấy. Toàn bộ kết quả OCR thô, flag, và quyết định của user đều được ghi vào **audit log** để theo dõi lỗi lặp lại.

---

## 2. Vì sao sửa ở lớp kiến trúc dữ liệu?

- **Cần kiểm tra dữ liệu trước khi câu trả lời được tạo ra** — Prompt và UI chỉ chặn được sau khi LLM đã xử lý. Image Quality Gate và Multi-number Detector chặn từ trước khi LLM nhìn thấy dữ liệu — không phụ thuộc vào model có "hiểu luật" hay không.
- **Cần ghi lại lỗi để nhóm biết lỗi nào lặp lại nhiều** — Audit log cho phép team phân tích tỷ lệ bill bị flag UNCERTAIN, phát hiện loại bill nào model đang xử lý kém nhất để cải thiện.

**Hành động phòng vệ chính**:

- [x] Ngăn lỗi trước khi vào model — Image Quality Gate chặn ảnh mờ từ đầu vào
- [x] Phát hiện khi dữ liệu có rủi ro — Multi-number Detector flag bill phức tạp
- [x] Lọc dữ liệu nhạy cảm — PII Filter loại OTP và số thẻ trước khi LLM nhìn thấy
- [x] Ghi lại lỗi để cải thiện sau — Audit log theo dõi flag, kết quả confirm, và lỗi lặp lại

---

## 3. Demo nằm ở đâu?

**File demo**: [`demo.md`](./demo.md)

**Nội dung demo có**:

- Sơ đồ pipeline ASCII từ ảnh → OCR → kiểm tra → LLM → UI confirm → lưu sổ
- Bảng thành phần: 5 component chính, input/output của từng bước
- Bảng xử lý lỗi: 4 tình huống lỗi và cách hệ thống phản ứng
- Ghi chú phối hợp với lớp Prompt và lớp UI

---

## 4. Tác dụng phụ

**Có thể gây vấn đề gì?**

Thêm Image Quality Gate và Multi-number Detector làm tăng latency mỗi request (thêm 1-2 bước xử lý). Với user chụp batch nhiều bill cuối tuần, thời gian chờ có thể tăng rõ.

**Nhóm giảm vấn đề đó bằng cách nào?**

- Image Quality Gate chạy trên thiết bị (on-device) trước khi upload — không cần round-trip server.
- Multi-number Detector chạy song song với bước gọi LLM, không nối tiếp.
- PII Filter là regex đơn giản, latency < 5ms, không ảnh hưởng đáng kể.

---

## 5. Checklist trước khi nộp

- [x] Sơ đồ cho thấy dữ liệu đi từ ảnh → qua các bước kiểm tra → đến lưu sổ.
- [x] Có bước kiểm tra trước khi AI xử lý (Image Quality Gate, Multi-number Detector).
- [x] Có cách xử lý khi ảnh không đủ chất lượng (chặn và yêu cầu chụp lại / nhập tay).
- [x] Có cách chuyển sang người thật — UI nhận flag từ kiến trúc và hiện cảnh báo.
- [x] Có cách biết lỗi đang lặp lại — Audit log ghi flag, kết quả confirm, tỷ lệ sai.

**Người phụ trách**: Hồ Thị Tố Nhi
