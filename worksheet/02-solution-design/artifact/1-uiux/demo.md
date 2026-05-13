---
artifact: 1 — Demo giao diện
format: ASCII sketch — 4 states
rủi ro: C1 — Hallucination (trích xuất sai số tiền từ bill)
---

# demo.md — Demo giao diện (Lớp UI/UX)

**Sản phẩm**: Trợ lý ghi chú và tổng hợp chi tiêu  
**Rủi ro chặn**: AI đọc sai số tiền từ bill (lấy dòng "Tiền thối lại" thay vì "Tổng cộng")  
**Cơ chế chính**: Bắt buộc người dùng xác nhận số tiền trước khi lưu vào sổ

---

## State 1 — DEFAULT (Bill rõ, AI confidence cao)

```
╔══════════════════════════════════════╗
║  📷 Vừa đọc xong bill của bạn        ║
║                                      ║
║  ┌──────────────────────────────┐    ║
║  │ 🟢 Confidence: Cao            │    ║
║  │ AI đọc được 1 con số rõ ràng │    ║
║  └──────────────────────────────┘    ║
║                                      ║
║  Số tiền AI trích xuất:              ║
║  ┌──────────────────────────────┐    ║
║  │        54.000 đ              │    ║
║  │   Hạng mục: Siêu thị        │    ║
║  └──────────────────────────────┘    ║
║                                      ║
║  Text gốc AI đọc được:               ║
║  "Tổng cộng: 54.000đ"               ║
║                                      ║
║  ⚠ AI đọc từ ảnh có thể sai —        ║
║    vui lòng kiểm tra trước khi lưu. ║
║                                      ║
║  ┌────────────┐  ┌───────────────┐   ║
║  │ ✓ Đúng,    │  │ ✏ Sửa số tiền│   ║
║  │   lưu lại  │  │               │   ║
║  └────────────┘  └───────────────┘   ║
║                                      ║
║  [Nhập tay thay vì dùng AI]          ║
╚══════════════════════════════════════╝
```

**Annotations**:
- Nút chính "✓ Đúng, lưu lại" — 1 tap là xong, thiết kế tối giản để không cản flow
- Hiện "Text gốc AI đọc được" để user tự đối chiếu với bill thật — không phải đổ hết trách nhiệm
- Nút "Nhập tay" luôn hiện dù confidence cao — Defense in Depth, user luôn có lối thoát
- Disclaimer ngắn, không phán xét người dùng ("có thể sai" thay vì "bạn phải kiểm tra")

---

## State 2 — UNCERTAIN (Bill có nhiều con số lớn — nghi ngờ nhầm dòng)

```
╔══════════════════════════════════════╗
║  📷 Vừa đọc xong bill của bạn        ║
║                                      ║
║  ┌──────────────────────────────┐    ║
║  │ 🟡 Cảnh báo: Nhiều con số    │    ║
║  │ AI phát hiện bill có dòng    │    ║
║  │ "Tiền thối lại" hoặc         │    ║
║  │ "Tiền khách đưa"             │    ║
║  └──────────────────────────────┘    ║
║                                      ║
║  AI đọc được các con số sau:         ║
║  ┌──────────────────────────────┐    ║
║  │  Tổng cộng:    54.000 đ  ◀  │    ║
║  │  Tiền khách đưa: 500.000 đ  │    ║
║  │  Tiền thối lại: 446.000 đ   │    ║
║  └──────────────────────────────┘    ║
║                                      ║
║  Bạn muốn lưu số tiền nào?           ║
║  ┌──────────────────────────────┐    ║
║  │ ○  54.000 đ   (Tổng cộng)   │    ║
║  │ ○ 500.000 đ   (Khách đưa)   │    ║
║  │ ○ 446.000 đ   (Tiền thối)   │    ║
║  │ ○ Số khác — tôi tự nhập     │    ║
║  └──────────────────────────────┘    ║
║                                      ║
║  ┌────────────────────────────────┐  ║
║  │  ✓ Xác nhận và lưu            │  ║
║  └────────────────────────────────┘  ║
║                                      ║
║  [Nhập tay thay vì dùng AI]          ║
╚══════════════════════════════════════╝
```

**Annotations**:
- Badge vàng 🟡 và giải thích rõ lý do nghi ngờ — không chỉ hiện "có lỗi" chung chung
- Hiện đủ 3 con số AI đọc được kèm nhãn dòng — user thấy ngay AI đang nhầm dòng nào
- Radio button để user chọn — UI không đoán thay, nhưng gợi ý con số đúng (có ◀) để giảm cognitive load
- Không có nút "Lưu tự động" ở state này — bắt buộc phải chọn trước

---

## State 3 — REFUSE (Ảnh mờ / không đọc được)

```
╔══════════════════════════════════════╗
║  📷 Không đọc được ảnh này           ║
║                                      ║
║  ┌──────────────────────────────┐    ║
║  │ 🔴 Ảnh chưa đủ rõ để đọc    │    ║
║  │                              │    ║
║  │ Lý do có thể:                │    ║
║  │ • Ảnh bị mờ hoặc ngược sáng │    ║
║  │ • Chữ viết tay khó nhận diện│    ║
║  │ • Ảnh bị che khuất một phần │    ║
║  └──────────────────────────────┘    ║
║                                      ║
║  AI không đoán số khi ảnh không rõ  ║
║  để tránh lưu sai vào sổ của bạn.   ║
║                                      ║
║  ┌────────────────────────────────┐  ║
║  │  📷 Chụp lại ảnh rõ hơn       │  ║
║  └────────────────────────────────┘  ║
║                                      ║
║  ┌────────────────────────────────┐  ║
║  │  ✏ Nhập số tiền thủ công      │  ║
║  └────────────────────────────────┘  ║
║                                      ║
║  [Bỏ qua bill này]                   ║
╚══════════════════════════════════════╝
```

**Annotations**:
- AI từ chối rõ ràng và giải thích lý do cụ thể — không nói "có lỗi" mơ hồ
- Wording "AI không đoán số khi ảnh không rõ" — giải thích hành vi từ chối theo góc lợi ích của user
- 2 lối thoát cụ thể (chụp lại / nhập tay) — không bế tắc, user tiếp tục được ngay
- Không có nút "Cứ lưu thử đi" — UI không cho bypass kể cả user muốn

---

## State 4 — PRESSURE-TRAP (User ép AI đoán dù ảnh mờ)

```
╔══════════════════════════════════════╗
║  User: "Ảnh hơi mờ nhưng cứ đoán   ║
║         đại số đi, khoảng chừng     ║
║         cũng được rồi"              ║
║                                      ║
║  ┌──────────────────────────────┐    ║
║  │ 🔴 Mình không thể đoán số   │    ║
║  │    từ ảnh mờ được           │    ║
║  └──────────────────────────────┘    ║
║                                      ║
║  Lý do: số bị đoán sai sẽ làm       ║
║  báo cáo tháng của bạn lệch —       ║
║  mình muốn giữ sổ của bạn           ║
║  chính xác hơn là nhanh hơn.        ║
║                                      ║
║  ┌────────────────────────────────┐  ║
║  │  📷 Chụp lại ảnh rõ hơn       │  ║
║  └────────────────────────────────┘  ║
║                                      ║
║  ┌────────────────────────────────┐  ║
║  │  ✏ Nhập số tiền thủ công      │  ║
║  └────────────────────────────────┘  ║
║                                      ║
║  [Bỏ qua bill này]                   ║
╚══════════════════════════════════════╝
```

**Annotations**:
- UI giữ nguyên state REFUSE dù user ép — không có nút "Cứ thử đi" hidden ở đâu cả
- Wording giải thích lý do từ góc lợi ích của user ("giữ sổ chính xác hơn là nhanh hơn") — không phán xét
- Không cảm ơn hay chiều theo yêu cầu "đoán đại" — giữ vững giới hạn một cách lịch sự

---

## Tổng hợp trạng thái

| Trạng thái | Người dùng thấy gì? | Người dùng làm gì tiếp? |
|---|---|---|
| DEFAULT — Bill rõ, 1 con số | Số tiền + text gốc + badge xanh | Tap "Đúng, lưu lại" hoặc sửa |
| UNCERTAIN — Nhiều con số lớn | Badge vàng + danh sách 3 số để chọn | Chọn đúng số rồi xác nhận |
| REFUSE — Ảnh mờ | Badge đỏ + lý do cụ thể | Chụp lại hoặc nhập tay |
| PRESSURE-TRAP — User ép đoán | Giữ badge đỏ + giải thích lợi ích | Không lưu được cho đến khi chọn lối thoát |

---

## Kiểm tra nhanh

- [x] Nhìn vào demo là hiểu rủi ro đang được chặn ở đâu (bước confirm trước khi lưu).
- [x] Có trạng thái khi AI không có đủ thông tin (State 3 — REFUSE).
- [x] Có cách chuyển sang nhập tay ở mọi trạng thái.
- [x] Câu chữ đủ ngắn để đặt trên màn hình thật.
- [x] Có trạng thái gây áp lực (State 4 — PRESSURE-TRAP) — UI không bị bypass.

---

## Câu hỏi cần test với người dùng thật

| Scenario | Profile user | Task | Tiêu chí đạt |
|---|---|---|---|
| A — Bill phức tạp | Sinh viên, thao tác nhanh tối cuối tuần | Upload bill siêu thị có dòng tiền thối | Chọn đúng "54.000đ" (Tổng cộng), không chọn nhầm tiền thối |
| B — Ảnh mờ | Nhân viên văn phòng, vội | Upload ảnh chụp nghiêng mờ | Không lưu được số bịa — chụp lại hoặc nhập tay trong < 30s |
| C — Ép AI đoán | Bất kỳ | Nhắn "cứ đoán đi" | UI vẫn từ chối, user không lưu được số ngẫu nhiên |
