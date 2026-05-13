---
artifact: 1 — Mở rộng bộ kiểm thử
bai-tap: 1 — Rà bộ kiểm thử
phase: Mở rộng
time: 9:35-10:05
input: 00-context.md + prompts/01-deep-research.md + prompts/02-brainstorm.md
nop-cuoi: Không — file trung gian
---

# 1 — Giai đoạn Mở rộng

Mục tiêu: mỗi thành viên mở rộng từ 5 tình huống ban đầu lên khoảng 15 tình huống kiểm thử.

Lý do làm bước này: bộ kiểm thử Day 24 mới là bản nháp. Bước Mở rộng giúp nhóm tìm thêm rủi ro từ nguồn thật và từ bối cảnh riêng của chủ đề, trước khi lọc lại ở `2-converge.md`.

Nhóm dùng 2 hướng:

- Hướng 1: tìm sự cố thật có nguồn.
- Hướng 2: dùng AI gợi ý thêm tình huống theo 4 góc nhìn.

## Quy trình 30 phút

```text
10 phút — Tìm sự cố thật
10 phút — Dùng AI gợi ý tình huống
10 phút — Chọn 15 tình huống tốt nhất của mỗi người
```

---

## Phần A — Tìm sự cố thật

Dán `00-context.md` và `prompts/01-deep-research.md` vào công cụ AI có khả năng tìm nguồn.

Yêu cầu đầu ra: 3-5 sự cố thật có nguồn kiểm chứng.

### Cần tìm gì?

Tìm sự cố AI hoặc chatbot trong 5 năm gần đây có bối cảnh gần với sản phẩm của nhóm.

Ưu tiên 3 kiểu sự cố:

- **Cùng ngành**: giáo dục, hàng không, y tế, ngân hàng, tuyển dụng, chăm sóc khách hàng.
- **Cùng kiểu lỗi**: AI bịa thông tin, rò rỉ dữ liệu, thiên lệch, chiều theo người dùng, không chuyển sang người thật.
- **Cùng nhóm người dùng**: học sinh, bệnh nhân, ứng viên, khách hàng đang vội hoặc lo lắng.

### Nguồn nên ưu tiên

| Mức ưu tiên | Loại nguồn | Ví dụ |
|---|---|---|
| 1 | Nguồn gốc | Hồ sơ tòa án, thông báo chính thức, báo cáo cơ quan quản lý |
| 2 | Báo chí uy tín | Reuters, BBC, NYT, AP, VnExpress, Tuổi Trẻ |
| 3 | Báo cáo ngành / học thuật | Microsoft AI Red Team, OpenAI, Anthropic, Stanford HAI |

Tránh dùng bài đăng ngắn trên mạng xã hội, bài marketing, blog không có nguồn, hoặc khẳng định chưa kiểm chứng.

| # | Ngày | Tổ chức | Việc đã xảy ra | Nguồn | Mức độ | Đã kiểm chứng? |
|---|---|---|---|---|---|---|
| R-01 | | | | | | Có / Chưa / Không chắc |
| R-02 | | | | | | |
| R-03 | | | | | | |

### Checklist kiểm chứng

- [ ] Mở từng URL và kiểm tra có truy cập được không.
- [ ] Nội dung nguồn có khớp với điều mình ghi không.
- [ ] Ưu tiên nguồn gốc: hồ sơ tòa án, thông báo chính thức, báo lớn.
- [ ] Với sự cố nghiêm trọng, đối chiếu ít nhất 2 nguồn.
- [ ] Nếu chưa chắc, đánh dấu `[CHƯA KIỂM CHỨNG]`, không viết như sự thật đã xác nhận.

Lưu ý quan trọng: AI có thể bịa cả nguồn trích dẫn. Không dùng nguồn chỉ vì AI đưa ra nghe có vẻ thật.

Ví dụ cảnh báo: trong vụ luật sư dùng ChatGPT ở hồ sơ Mata v. Avianca, AI tạo ra nhiều án lệ không tồn tại. Vấn đề không phải là AI "viết chưa hay"; vấn đề là người dùng đã không tự kiểm chứng nguồn trước khi nộp.

---

## Phần B — Dùng AI gợi ý tình huống

Dán `00-context.md`, kết quả Phần A, và `prompts/02-brainstorm.md` vào AI.

Yêu cầu AI tạo thêm tình huống theo 4 góc nhìn:

| Góc nhìn | Câu hỏi gợi mở | Mục tiêu |
|---|---|---|
| Góc 1 — Hậu quả trước | Nếu AI sai, hậu quả nặng nhất là gì? | 4-5 tình huống |
| Góc 2 — Tình huống đời thường | Người dùng đang vội, mơ hồ, lười đọc, hoặc cố thuyết phục AI sẽ hỏi gì? | 3-4 tình huống |
| Góc 3 — Bối cảnh riêng | Tình huống nào chỉ chủ đề của nhóm mới có? | 3-4 tình huống |
| Góc 4 — Yếu tố con người | Tình huống nào cần người thật đọc được mỉa mai, văn hóa, cảm xúc? | 2-3 tình huống |

### Gợi ý cụ thể cho từng góc nhìn

**Góc 1 — Hậu quả trước**

Bắt đầu từ hậu quả xấu nhất, rồi truy ngược lại câu hỏi người dùng có thể hỏi.

Ví dụ hậu quả:

- Mất tiền.
- Lỡ hạn nộp hồ sơ.
- Chọn sai ngành / sai dịch vụ.
- Rủi ro sức khỏe, pháp lý, danh tiếng.

**Góc 2 — Tình huống đời thường**

Đừng chỉ kiểm thử người dùng "ngoan". Hãy kiểm thử người dùng:

- Hỏi thiếu bối cảnh.
- Viết tắt, viết sai chính tả.
- Đang vội.
- Cố ép AI trả lời dù AI không nên trả lời.

**Góc 3 — Bối cảnh riêng**

Hỏi: người ngoài chủ đề này có nghĩ ra tình huống này không?

Ví dụ:

- Quy định riêng ở Việt Nam.
- Văn hóa gia đình.
- Cách nói lịch sự / vòng vo.
- Thuật ngữ địa phương hoặc thuật ngữ ngành.

**Góc 4 — Yếu tố con người**

Tìm tình huống AI dễ đọc sai cảm xúc hoặc ngữ cảnh.

Ví dụ:

- Mỉa mai.
- Lo lắng nhưng không nói thẳng.
- "Vâng ạ" không có nghĩa là đồng ý.
- Người dùng đổi chủ đề giữa cuộc trò chuyện.

| ID | Góc nhìn | Kiểu lỗi | Tình huống kiểm thử | Hành vi AI kỳ vọng | Nguồn |
|---|---|---|---|---|---|
| C-01 | Góc 1 | Bịa thông tin | | | sự cố thật / AI gợi ý / kết hợp |
| C-02 | Góc 2 | Chiều theo người dùng | | | |
| C-03 | Góc 3 | Bối cảnh riêng | | | |

Ghi nhãn nguồn:

- `sự cố thật`: lấy từ Phần A.
- `AI gợi ý`: AI tạo mới từ bối cảnh.
- `kết hợp`: lấy ý từ sự cố thật, rồi biến thể cho chủ đề của nhóm.

### Cảnh báo khi dùng AI gợi ý

- AI có thể lặp lại tình huống nổi tiếng nhưng không phù hợp chủ đề.
- AI có thể tạo tình huống quá chung chung.
- AI có thể tự thêm số liệu hoặc nguồn không có thật.
- Nhóm phải tự lọc lại: giữ tình huống sát bối cảnh, bỏ tình huống chung chung.

---

## Phần C — Chọn 15 tình huống cuối của mỗi người

Mỗi thành viên tự đọc lại Phần A và Phần B, rồi chọn khoảng 15 tình huống tốt nhất.

Checklist trước khi chốt:

- [ ] Có đủ 4 góc nhìn.
- [ ] Có cả mức nhẹ, vừa, nặng.
- [ ] Có nhiều kiểu lỗi, không chỉ một kiểu.
- [ ] Có ít nhất một tình huống AI phải từ chối.
- [ ] Mỗi tình huống đủ rõ để người khác kiểm thử được.

Ưu tiên giữ:

- Tình huống có hậu quả lớn.
- Tình huống rất riêng của chủ đề.
- Tình huống có nguồn thật.
- Tình huống có câu người dùng cụ thể.

Nên bỏ:

- Tình huống trùng với tình huống đã có từ Day 24.
- Tình huống mọi AI chatbot đều có, không đặc thù sản phẩm.
- Tình huống không chấm được vì mô tả quá mơ hồ.

| ID | Góc nhìn | Kiểu lỗi | Tình huống kiểm thử | Hành vi AI kỳ vọng | Nguồn |
|---|---|---|---|---|---|
| C-01 | | | | | |
| C-02 | | | | | |
| ... | | | | | |
| C-15 | | | | | |

Sau bước này, chuyển các tình huống đã chọn sang `2-converge.md` Phần A để nhóm gộp lại.
