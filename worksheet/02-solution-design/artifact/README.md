---
folder: artifact/ — 3 lớp giải pháp
bai-tap: 2 — Thiết kế giải pháp
phase: Xây demo
nop-cuoi: Có — nộp đủ 3 thư mục con
---

# Thư mục artifact/ — 3 lớp giải pháp

Thư mục này chứa 3 lớp giải pháp cho cùng một rủi ro chính.

Mỗi lớp có:

- `card.md`: giải thích vì sao chọn giải pháp.
- `demo.*`: bản demo để người khác xem, kiểm tra và phản biện.

## Cấu trúc cần có

```text
artifact/
├── 1-uiux/
│   ├── card.md
│   └── demo.{png/md/html}
│
├── 2-prompt/
│   ├── card.md
│   └── demo.md
│
└── 3-architecture/
    ├── card.md
    └── demo.md
```

## Vai trò của từng lớp

| Thư mục | Lớp giải pháp | Nên chứng minh điều gì |
|---|---|---|
| `1-uiux/` | Giao diện | Người dùng thấy cảnh báo, nguồn, trạng thái xác minh, nút chuyển sang người thật |
| `2-prompt/` | Chỉ dẫn AI | AI biết khi nào hỏi lại, từ chối, dẫn nguồn, hoặc chuyển sang người thật |
| `3-architecture/` | Kiến trúc dữ liệu | AI lấy nguồn từ đâu, kiểm tra nguồn thế nào, xử lý ra sao khi thiếu nguồn |

Ba lớp này phải bổ sung cho nhau. Không làm 3 biến thể giao diện giống nhau.

## Cách làm trong 40 phút

```text
20 phút — Mỗi thành viên làm một lớp
15 phút — Rà chéo trong nhóm
5 phút  — Sửa trạng thái trong 1-map-and-format.md
```

Nhóm 3 người:

- Hồ Thị Tố Nhi: `1-uiux/`
- Lê Thị Phương: `2-prompt/`
- Trần Thị Kim Ngân: `3-architecture/`

Nhóm 2 người:

- Một người làm 2 lớp.
- Người còn lại làm 1 lớp và rà lại 2 lớp kia.

## Demo có thể làm bằng gì?

| Lớp | Demo phù hợp |
|---|---|
| Giao diện | vẽ tay, Excalidraw, Figma, HTML, ASCII |
| Chỉ dẫn AI | Bản prompt trong Markdown, ví dụ câu trả lời |
| Kiến trúc dữ liệu | ASCII, Mermaid, sơ đồ hộp-mũi tên |

Có thể dùng AI để dựng nhanh bản nháp demo. Nhóm vẫn phải đọc lại, sửa thuật ngữ, và kiểm tra tính khả thi.

## Prompt tham khảo có thể dùng

| Lớp | Prompt gợi ý |
|---|---|
| `1-uiux/` | `../../../prompts/05a-ascii-ui-sketch.md` hoặc `05b-mermaid-ui-flow.md` |
| `2-prompt/` | Không có prompt riêng; viết trực tiếp trong `demo.md` |
| `3-architecture/` | `../../../prompts/05e-ascii-architecture.md` hoặc `05f-mermaid-architecture.md` |

## Card cần có gì?

Mỗi `card.md` cần 4 phần:

1. **Rủi ro xử lý**: tình huống nào trong bộ kiểm thử?
2. **Tầng giải pháp**: vì sao lớp này phù hợp?
3. **Bản demo**: demo nằm ở đâu, người đọc cần nhìn gì?
4. **Tác dụng phụ**: giải pháp có thể gây vấn đề gì, giảm bằng cách nào?

Mẫu viết nhanh:

```markdown
# card.md — [Tên lớp giải pháp]

## 1. Rủi ro xử lý
- ID tình huống: T-__
- Mẫu lỗi: [AI làm gì sai]
- Hậu quả: [ai bị ảnh hưởng, ảnh hưởng thế nào]

## 2. Tầng giải pháp
- Lớp này sửa phần nào của nguyên nhân gốc?
- Vì sao lớp này phù hợp?
- Lớp này cần phối hợp với lớp nào khác?

## 3. Bản demo
- File demo: ./demo...
- Người phản biện cần nhìn thấy gì?
- Thành phần chính: [...]

## 4. Tác dụng phụ và cách giảm
- Tác dụng phụ 1: [...] -> Cách giảm: [...]
- Tác dụng phụ 2: [...] -> Cách giảm: [...]

## 5. Hành động phòng vệ
- [ ] Ngăn
- [ ] Phát hiện
- [ ] Khắc phục
- [ ] Thông báo
```

## Ví dụ ngắn — lớp giao diện

```markdown
# card.md — Giao diện cảnh báo nguồn

## 1. Rủi ro xử lý
- ID tình huống: T-01
- Mẫu lỗi: AI bịa hạn nộp học bổng.
- Hậu quả: sinh viên lỡ hạn thật, mất cơ hội tài chính.

## 2. Tầng giải pháp
- Lớp giao diện giúp người dùng thấy câu trả lời có nguồn hay chưa.
- Phù hợp vì rủi ro có phần đến từ việc người dùng tin AI quá mức.

## 3. Bản demo
- File demo: ./demo.md
- Cần thấy nhãn "Đã xác minh", đường dẫn nguồn, nút hỏi tư vấn viên.

## 4. Tác dụng phụ và cách giảm
- Giao diện rối hơn -> chỉ hiện cảnh báo ở thông tin nhạy cảm như hạn chót, học phí, học bổng.

## 5. Hành động phòng vệ
- [ ] Ngăn
- [ ] Phát hiện
- [x] Khắc phục
- [x] Thông báo
```

## Checklist mỗi lớp

- [ ] Có `card.md`.
- [ ] Có `demo.*`.
- [ ] Card nói rõ rủi ro đang xử lý.
- [ ] Demo đủ trực quan để nhóm khác phản biện.
- [ ] Có ít nhất một hành động phòng vệ: ngăn, phát hiện, khắc phục, thông báo.
- [ ] Có ghi tác dụng phụ hoặc đánh đổi.

## Nhóm khác sẽ phản biện gì?

| Góc phản biện | Câu hỏi |
|---|---|
| Đúng tầng | Giải pháp này có sửa đúng nguyên nhân gốc không? |
| Cụ thể | Demo có đủ rõ để hiểu cách vận hành không? |
| Đủ lớp | 3 lớp có bổ sung cho nhau không? |
| Tác dụng phụ | Có làm chậm, tăng chi phí, làm rối giao diện, hoặc gây hiểu nhầm mới không? |
