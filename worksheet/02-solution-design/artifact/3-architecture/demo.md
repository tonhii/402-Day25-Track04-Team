---
artifact: 3 — Demo kiến trúc dữ liệu
format: sơ đồ pipeline ASCII + bảng thành phần + bảng xử lý lỗi
rủi ro: C1 — Hallucination (trích xuất sai số tiền từ bill)
---

# demo.md — Demo kiến trúc dữ liệu (Lớp Architecture)

---

## 1. Sơ đồ pipeline xử lý

```
  USER
   │  chụp ảnh bill / paste SMS
   ▼
┌─────────────────────────────────────────┐
│  BƯỚC 1 — Image Quality Gate            │
│  (chạy on-device trước khi upload)      │
│                                         │
│  Kiểm tra: độ sáng, độ nét, góc chụp   │
│  confidence < 0.6  ──► CHẶN            │
│         │                    │          │
│         │              Hiện UI:         │
│         │         “Ảnh chưa đủ rõ,      │
│         │          chụp lại hoặc        │
│         │          nhập tay”            │
│  confidence ≥ 0.6                       │
└──────────────┬──────────────────────────┘
               │
               ▼
┌─────────────────────────────────────────┐
│  BƯỚC 2 — PII Filter                    │
│  (chạy trên raw image trước OCR)        │
│                                         │
│  Quét: OTP pattern, số thẻ 13-19 chữ,  │
│         số tài khoản đầy đủ            │
│  Phát hiện PII ──► CHẶN toàn bộ input  │
│         │                    │          │
│         │              Hiện UI:         │
│         │         “Tin nhắn chứa dữ    │
│         │          liệu nhạy cảm —     │
│         │          không thể xử lý”    │
│  Không có PII                           │
└──────────────┬──────────────────────────┘
               │
               ▼
┌─────────────────────────────────────────┐
│  BƯỚC 3 — OCR Engine                    │
│  Đọc văn bản từ ảnh, trả về:           │
│  • danh sách text lines + bounding box  │
│  • confidence score mỗi dòng           │
└──────────────┬──────────────────────────┘
               │
               ▼
┌─────────────────────────────────────────┐
│  BƯỚC 4 — Multi-number Detector         │
│  (chạy song song khi gọi LLM)          │
│                                         │
│  Đếm dòng số tiền > 10.000đ            │
│                                         │
│  ≥ 2 dòng tiền lớn ──► flag UNCERTAIN  │
│  < 2 dòng            ──► flag NORMAL    │
└──────────────┬──────────────────────────┘
               │
               ▼
┌─────────────────────────────────────────┐
│  BƯỚC 5 — LLM (với System Prompt)       │
│  Nhận: text OCR + flag từ Bước 4        │
│                                         │
│  flag NORMAL   → trích xuất tổng tiền   │
│  flag UNCERTAIN→ liệt kê + hỏi xác nhận│
└──────────────┬──────────────────────────┘
               │
               ▼
┌─────────────────────────────────────────┐
│  BƯỚC 6 — UI Confirm Gate               │
│  Người dùng xác nhận số tiền            │
│  (không có auto-save)                   │
│                                         │
│  Xác nhận ──► lưu vào sổ chi tiêu      │
│  Sửa      ──► user nhập lại            │
│  Bỏ qua   ──► không lưu               │
└──────────────┬──────────────────────────┘
               │
               ▼
┌─────────────────────────────────────────┐
│  BƯỚC 7 — Audit Log                     │
│  Ghi lại: flag, kết quả OCR thô,        │
│  số tiền AI đề xuất, số tiền user xác   │
│  nhận, delta (chênh lệch nếu có)        │
└─────────────────────────────────────────┘
```

---

## 2. Thành phần chính

| Thành phần | Nhận gì? | Làm gì? | Trả ra gì? |
|---|---|---|---|
| Image Quality Gate | Ảnh gốc từ camera | Tính confidence độ nét/sáng/góc | Pass / Block + lý do |
| PII Filter | Ảnh gốc + raw OCR text | Regex scan OTP, số thẻ, số TK | Pass / Block + loại PII phát hiện |
| OCR Engine | Ảnh đã qua filter | Đọc văn bản, tính confidence từng dòng | Danh sách text lines + confidence |
| Multi-number Detector | Kết quả OCR | Đếm dòng số tiền > 10.000đ | Flag: NORMAL / UNCERTAIN |
| Audit Log | Flag + OCR thô + quyết định user | Ghi lại mọi request | Báo cáo lỗi lặp lại theo tuần |

---

## 3. Khi hệ thống gặp vấn đề

| Khi nào lỗi xảy ra? | Hệ thống làm gì? | Người dùng thấy gì? |
|---|---|---|
| Ảnh mờ / ngược sáng (confidence < 0.6) | Image Quality Gate chặn, không gọi OCR | “Ảnh chưa đủ rõ — chụp lại hoặc nhập tay” |
| SMS chứa OTP hoặc số thẻ | PII Filter chặn, không đưa sang LLM | “Tin nhắn chứa dữ liệu nhạy cảm — không thể xử lý tự động” |
| Bill có ≥ 2 dòng số tiền lớn | Multi-number Detector gán flag UNCERTAIN | UI State 2 — danh sách số để user chọn |
| User ép “cứ đoán đi” | LLM giữ nguyên từ chối theo System Prompt Luật 4 | “Mình không thể đoán — bạn có thể nhập tay không?” |
| Lỗi lặp lại theo loại bill | Audit Log phát hiện pattern | Báo cáo nội bộ → team cải thiện OCR model |

---

## 4. Phối hợp 3 lớp

| Lớp | Chặn lỗi ở đâu? | Phụ thuộc gì từ lớp khác? |
|---|---|---|
| Architecture | Trước khi LLM nhìn thấy dữ liệu | Cung cấp flag UNCERTAIN cho LLM và UI |
| Prompt | Trong LLM — luật xử lý flag và từ chối | Nhận flag UNCERTAIN từ Architecture |
| UI | Sau LLM — bắt buộc user xác nhận | Nhận kết quả + flag để chọn State hiển thị |

---

## 5. Kiểm tra nhanh

- [x] Sơ đồ có bước kiểm tra cụ thể trước khi AI xử lý (không chỉ “AI trả lời tốt hơn”).
- [x] Có cách xử lý khi ảnh không đủ chất lượng (Image Quality Gate chặn từ đầu vào).
- [x] Có cách xử lý khi có dữ liệu nhạy cảm (PII Filter trước OCR).
- [x] Có cách chuyển sang người thật (nhập tay hoặc UI confirm).
- [x] Có cách theo dõi lỗi lặp lại (Audit Log ghi delta giữa AI đề xuất và user xác nhận).
