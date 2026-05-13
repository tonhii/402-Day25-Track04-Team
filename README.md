# Day 25 — Chủ đề 4: Trợ lý ghi chú và tổng hợp chi tiêu

## Thành viên nhóm

| # | Mã học viên | Họ tên đầy đủ |
|---|-------------|---------------|
| 1 | 2A202600107 | Lê Thị Phương      |
| 2 | 2A202600369 | Hồ Thị Tố Nhi      |
| 3 | 2A202600432 | Trần Thị Kim Ngân  |

## Kết quả cuối

- 🎯 [Bộ kiểm thử cuối](./worksheet/01-test-set-review/3-FINAL-test-set-eval-plan.md)
- 🎯 [Thiết kế 3 lớp giải pháp](./worksheet/02-solution-design/1-map-and-format.md) + [artifact/](./worksheet/02-solution-design/artifact/)

---

# Hướng dẫn làm bài (Day 25 — Thiết kế giải pháp AI có trách nhiệm)

Day 25 là bài tập nhóm 2-3 người cùng chủ đề. Nhóm bắt đầu từ 2 file đã làm ở Day 24 (`01-risk-map.md` + `02-test-eval-plan.md`), kết thúc bằng **bộ kiểm thử cuối** (10-15 tình huống) + **3 lớp giải pháp** cho rủi ro quan trọng nhất.

---

## 📥 Nộp bài thế nào? (đọc trước khi vào lab)

### Tên kho GitHub

Cú pháp: **`Day25-MãNhóm`**

Ví dụ: `Day25-G001`, `Day25-G045`, `Day25-A1`.

### Cần nộp những file gì?

Nhóm tạo **1 kho GitHub công khai**, đưa toàn bộ thư mục `worksheet/` lên GitHub theo đúng cấu trúc dưới, rồi nộp link qua LMS.

```text
Day25-MãNhóm/                                       ← kho GitHub công khai
│
├── README.md                                       ← Thành viên nhóm (xem mẫu dưới)
│
└── worksheet/
    ├── 00-context.md                               ← Bối cảnh sản phẩm (đã điền)
    │
    ├── 01-test-set-review/
    │   ├── 1-diverge.md                            ← Trung gian: giai đoạn Mở rộng
    │   ├── 2-converge.md                           ← Trung gian: giai đoạn Hội tụ
    │   └── 3-FINAL-test-set-eval-plan.md           🎯 KẾT QUẢ CUỐI Bài 1
    │
    └── 02-solution-design/
        ├── 1-map-and-format.md                     🎯 KẾT QUẢ CUỐI Bài 2
        └── artifact/
            ├── 1-uiux/
            │   ├── card.md
            │   └── demo.{md|png|html}
            ├── 2-prompt/
            │   ├── card.md
            │   └── demo.md
            └── 3-architecture/
                ├── card.md
                └── demo.md
```

🎯 = file người chấm xem trước. Các file còn lại là **trung gian** — phải nộp kèm để người chấm thấy nhóm đã đi qua đủ quá trình.

### README đầu kho bài phải có gì?

Sao chép mẫu này vào `README.md` ở gốc kho bài và điền:

```markdown
# Day 25 — Chủ đề [N]: [Tên chủ đề]

## Thành viên nhóm

| # | Mã học viên | Họ tên đầy đủ |
|---|-------------|---------------|
| 1 | A20-XXXXX   | Nguyễn Văn A  |
| 2 | A20-XXXXX   | Trần Thị B    |
| 3 | A20-XXXXX   | Lê Văn C      |

## Kết quả cuối

- 🎯 [Bộ kiểm thử cuối](./worksheet/01-test-set-review/3-FINAL-test-set-eval-plan.md)
- 🎯 [Thiết kế 3 lớp giải pháp](./worksheet/02-solution-design/1-map-and-format.md) + [artifact/](./worksheet/02-solution-design/artifact/)
```

### Các bước nộp

1. Tạo kho GitHub công khai theo cú pháp `Day25-MãNhóm`.
2. Đưa toàn bộ thư mục `worksheet/` lên GitHub theo đúng cấu trúc trên.
3. Tạo `README.md` ở gốc kho bài theo mẫu, điền mã học viên + tên đầy đủ.
4. Một thành viên đại diện nộp link kho bài vào LMS Day 25.
5. Kiểm tra link mở được trước **23:59 hôm nay**.

---

## Quy trình làm bài

```text
Đọc lại 01-risk-map.md + 02-test-eval-plan.md từ Day 24
   -> Điền 00-context.md
   -> Bài 1: Rà bộ kiểm thử
      -> Mở rộng: tìm sự cố thật + dùng AI gợi ý tình huống
      -> Hội tụ: gộp, lọc trùng, chấm rủi ro
      -> Chốt 10-15 tình huống cuối
   -> Bài 2: Thiết kế giải pháp
      -> Chọn rủi ro chính
      -> Tìm nguyên nhân gốc
      -> Chọn tầng sửa và định dạng demo
      -> Xây 3 lớp giải pháp song song
   -> Phản biện chéo với nhóm khác
   -> Chỉnh lại file
   -> Nộp link kho bài qua LMS
```

## Bài 1 — Rà bộ kiểm thử

Mục tiêu: chọn ra 10-15 tình huống đáng kiểm thử nhất và viết kế hoạch chấm rõ ràng.

File cuối của Bài 1:

```text
worksheet/01-test-set-review/3-FINAL-test-set-eval-plan.md
```

### Giai đoạn Mở rộng — 30 phút

Mỗi thành viên làm trước, sau đó mới gộp nhóm.

1. Tìm sự cố thật có nguồn.
2. Dùng AI gợi ý thêm tình huống theo 4 góc nhìn.
3. Chọn khoảng 15 tình huống tốt nhất của mỗi người.

File dùng ở giai đoạn này:

```text
worksheet/01-test-set-review/1-diverge.md
```

### Giai đoạn Hội tụ — 30 phút

Nhóm cùng làm.

1. Gộp toàn bộ tình huống của nhóm.
2. Lọc trùng theo kiểu lỗi.
3. Chấm điểm rủi ro: Tác động x Độ khẩn cấp.
4. Chốt 10-15 tình huống cuối.

File dùng ở giai đoạn này:

```text
worksheet/01-test-set-review/2-converge.md
```

## Bài 2 — Thiết kế giải pháp

Mục tiêu: chọn rủi ro quan trọng nhất từ Bài 1, rồi xây 3 lớp giải pháp cho cùng rủi ro đó.

File cuối của Bài 2:

```text
worksheet/02-solution-design/1-map-and-format.md
```

Ba lớp giải pháp:

| Lớp | Thư mục | Mục đích |
|---|---|---|
| Giao diện | `artifact/1-uiux/` | Giúp người dùng thấy cảnh báo, nguồn, cách chuyển sang người thật |
| Chỉ dẫn AI | `artifact/2-prompt/` | Buộc AI hỏi lại, từ chối, hoặc dẫn nguồn khi cần |
| Kiến trúc dữ liệu | `artifact/3-architecture/` | Đảm bảo AI tra cứu đúng nguồn và biết xử lý khi thiếu nguồn |

Ba lớp này bổ sung cho nhau. Một lớp có thể lọt lỗi, nhiều lớp sẽ giảm rủi ro tốt hơn.

## Tài liệu trong thư mục này

| File / Thư mục | Dùng để làm gì |
|---|---|
| `track-bank-scenario-kit-v1.md` | Chọn và đọc lại bối cảnh chủ đề |
| `worksheet/00-context.md` | Điền bối cảnh một lần, đưa vào đầu mọi cuộc trò chuyện với AI |
| `worksheet/01-test-set-review/` | Làm Bài 1. Hướng dẫn chi tiết nằm ngay trong từng file worksheet |
| `worksheet/02-solution-design/` | Làm Bài 2. Hướng dẫn chọn tầng, demo, phản biện nằm trong worksheet |
| `prompts/` | Prompt tham khảo cho từng bước |

## Bảng dùng prompt tham khảo

| Prompt tham khảo | Dùng khi nào | Lưu kết quả vào |
|---|---|---|
| `prompts/01-deep-research.md` | Tìm sự cố thật | `1-diverge.md` Phần A |
| `prompts/02-brainstorm.md` | Dùng AI gợi ý tình huống | `1-diverge.md` Phần B |
| `prompts/03-convergent-analysis.md` | Lọc trùng và ưu tiên | `2-converge.md` |
| `prompts/04-solution-options.md` | Gợi ý hướng giải pháp | `1-map-and-format.md` |
| `prompts/05a-*` đến `05f-*` | Dựng demo nhanh | `artifact/*/demo.*` |

## Cách dùng prompt tham khảo

1. Mở Claude / ChatGPT / Gemini / Perplexity tùy bước.
2. Đưa toàn bộ `worksheet/00-context.md` vào đầu cuộc trò chuyện.
3. Chọn prompt tham khảo phù hợp từ thư mục `prompts/`, rồi chỉnh lại theo bối cảnh nhóm.
4. AI tạo bản nháp.
5. Nhóm đọc lại, sửa, rồi lưu vào đúng file bài tập.

AI chỉ hỗ trợ dựng bản nháp. Nhóm vẫn chịu trách nhiệm kiểm tra nguồn, sửa nội dung, và chốt quyết định cuối.

## Checklist trước khi nộp

- [ ] `worksheet/00-context.md` đã điền đủ.
- [ ] `1-diverge.md` có đủ Phần A, B, C.
- [ ] `2-converge.md` có bảng gộp, bảng lọc trùng, bảng chấm rủi ro.
- [ ] `3-FINAL-test-set-eval-plan.md` có 10-15 tình huống cuối và kế hoạch chấm.
- [ ] `1-map-and-format.md` có rủi ro được chọn, nguyên nhân gốc, 3 lớp giải pháp.
- [ ] `artifact/1-uiux/`, `artifact/2-prompt/`, `artifact/3-architecture/` đều có `card.md` và `demo.*`.
- [ ] Kho GitHub công khai, tên đúng cú pháp `Day25-MãNhóm`, mở được.
- [ ] README đầu kho bài có bảng mã học viên + tên đầy đủ 2-3 thành viên.
- [ ] Link kho bài đã nộp qua LMS trước **23:59**.

## Lỗi hay mắc

| Đừng làm | Nên làm |
|---|---|
| Bỏ qua `00-context.md` | Điền bối cảnh trước khi dùng AI |
| Nộp mỗi file cuối | Giữ cả file trung gian |
| AI viết xong là nộp | Nhóm phải đọc, sửa, kiểm chứng |
| Chỉ làm một lớp giải pháp | Làm đủ 3 lớp: giao diện, chỉ dẫn AI, kiến trúc |
| Demo chỉ để nhìn đẹp | Demo phải giúp người khác hiểu và phản biện |
| Để kho bài ở chế độ riêng tư | Kho GitHub phải công khai |
| Đặt tên kho bài `Day-25-team-final` | Đúng cú pháp `Day25-MãNhóm` |
