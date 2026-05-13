---
artifact: 3 — Demo kiến trúc dữ liệu
format: sơ đồ pipeline ASCII + bảng thành phần + bảng xử lý lỗi
rủi ro: C1 — Hallucination (trích xuất sai số tiền từ bill)
---

# demo.md — Demo kiến trúc dữ liệu (Lớp Architecture)

**Rủi ro chính**: T-03 — Bill có nhiều dòng số lớn, AI lấy dòng to nhất (`tiền khách đưa` / `tiền thối`) thay vì dòng `Tổng cộng`.

---

## 1. Sơ đồ pipeline xử lý

```text
                         ┌──────────────────────────┐
                         │ USER (Mobile App)        │
                         │ Upload ảnh bill          │
                         └────────────┬─────────────┘
                                      │ HTTPS
                                      ▼
              ┌──────────────────────────────────────┐
              │ USER-FACING API / CHATBOT ENDPOINT   │
              │ - Auth token                         │
              │ - Rate limit                         │
              │ - request_id                         │
              │ - Temp image TTL: 5 phút             │
              └──────────────────┬───────────────────┘
                                 │
                                 ▼
              ┌──────────────────────────────────────┐
              │ INTENT + INPUT CLASSIFIER            │
              │ rule-based first, LLM optional       │
              │ - receipt_image                      │
              │ - bank_sms                           │
              │ - banking_screenshot                 │
              │ - out_of_scope                       │
              └──────────────┬──────────────┬────────┘
                             │              │
                   receipt_image       out_of_scope
                             │              │
                             │              ▼
                             │      ┌─────────────────────┐
                             │      │ REFUSE TEMPLATE      │
                             │      │ "Mình chỉ hỗ trợ     │
                             │      │ ghi chép chi tiêu."  │
                             │      └─────────────────────┘
                             ▼
              ┌──────────────────────────────────────┐
              │ OCR + LAYOUT PARSER                  │
              │ - OCR text                           │
              │ - bounding boxes                     │
              │ - font size                          │
              │ - spatial order                      │
              └──────────────────┬───────────────────┘
                                 │
                                 ▼
              ┌──────────────────────────────────────┐
              │ RECEIPT STRUCTURE VALIDATOR          │
              │ - Detect "Tổng cộng"                 │
              │ - Detect "Tiền khách đưa"            │
              │ - Detect "Tiền thối lại"             │
              │ - Detect VAT / subtotal / service    │
              │ - Output amount_candidates           │
              └──────────────┬──────────────┬────────┘
                             │              │
                  confident total      ambiguous / low confidence
                             │              │
                             ▼              ▼
              ┌──────────────────┐  ┌─────────────────────────────┐
              │ DEFAULT STATE    │  │ UNCERTAIN STATE             │
              │ Show suggested   │  │ Show all candidate amounts  │
              │ total + confirm  │  │ User chooses / enters hand  │
              └────────┬─────────┘  └──────────────┬──────────────┘
                       │                           │
                       │                           │ unreadable / missing total
                       │                           ▼
                       │            ┌─────────────────────────────┐
                       │            │ REFUSE STATE                │
                       │            │ No guessing; ask retake or  │
                       │            │ manual entry                │
                       │            └──────────────┬──────────────┘
                       │                           │
                       └──────────────┬────────────┘
                                      ▼
              ┌──────────────────────────────────────┐
              │ RAG SERVICE                          │
              │ - Embedding + retriever              │
              │ - Receipt label rules                │
              │ - VN bill examples                   │
              │ - OCR failure examples               │
              └──────────────┬───────────────────────┘
                             │
                             ▼
              ┌──────────────────────────────────────┐
              │ CACHE LAYER: REDIS                   │
              │ - TTL receipt label rules: 7 ngày    │
              │ - TTL category taxonomy: 1 ngày      │
              └──────────────┬───────────────────────┘
                             │ miss
                             ▼
              ┌──────────────────────────────────────┐
              │ PRIMARY SOURCE: INTERNAL POLICY DB   │
              │ - Receipt label rules                │
              │ - Save policy: confirm before write  │
              │ - Timeout: 2s                        │
              └──────────────┬───────────────────────┘
                             │ index / retrieve
                             ▼
              ┌──────────────────────────────────────┐
              │ SECONDARY SOURCE: VECTOR DB          │
              │ - Indexed bill examples              │
              │ - khách đưa / thối lại / tổng cộng   │
              │ - similarity threshold               │
              └──────────────┬───────────────────────┘
                             │
          cache miss + API timeout + weak retrieval
                             │
                             ▼
              ┌──────────────────────────────────────┐
              │ REFUSE: NO RELIABLE RULE             │
              │ "Mình không đủ chắc để chọn dòng     │
              │ tổng. Bạn nhập tay số tiền nhé."     │
              └──────────────────────────────────────┘

              ┌──────────────────────────────────────┐
              │ LLM SERVICE                          │
              │ OpenAI / Anthropic / Gemini          │
              │ - Input: OCR text + candidates       │
              │ - Output JSON: chosen_amount, label, │
              │   confidence, explanation            │
              │ - Fallback: static template if quota │
              │   exhausted                          │
              └──────────────────┬───────────────────┘
                                 │
                                 ▼
              ┌──────────────────────────────────────┐
              │ UI STATE ROUTER + SAVE GATE          │
              │ - DEFAULT: suggested total           │
              │ - UNCERTAIN: user selects amount     │
              │ - REFUSE: retake / manual entry      │
              │ - PRESSURE-TRAP: user asks guessing  │
              │ - Save only if user_confirmed=true   │
              └──────────────────┬───────────────────┘
                                 │
                                 ▼
              ┌──────────────────────────────────────┐
              │ DATABASE                             │
              │ Save only normalized confirmed txn   │
              │ - amount                             │
              │ - category                           │
              │ - merchant?                          │
              │ - timestamp                          │
              └──────────────────┬───────────────────┘
                                 │
                                 ▼
              ┌──────────────────────────────────────┐
              │ MONITORING SERVICE                   │
              │ Logs + metrics + alerts              │
              │ - query log                          │
              │ - rag_hit_rate                       │
              │ - refuse_rate                        │
              │ - correction_rate                    │
              │ - alert if rag_failure > 5% / 5min   │
              └──────────────────────────────────────┘
```
| Component | Input / Output | Latency | Cost | Failure mode | Fallback |
|---|---|---:|---:|---|---|
| User-facing API | In: ảnh bill + token. Out: `request_id`, routed task. | p50 50ms, p95 150ms | Thấp, infra cost | API quá tải, auth fail | Rate limit, retry nhẹ |
| Intent Classifier | In: metadata ảnh + message. Out: `receipt_image` / OOS / screenshot. | p50 80ms, p95 250ms | Rule-based gần 0; LLM optional | Phân loại nhầm bill thành OOS | Default route sang receipt review nếu không chắc |
| OCR + Layout Parser | In: ảnh bill. Out: text boxes, bounding boxes, font size, spatial order. | p50 700ms, p95 2.5s | Vừa | OCR thiếu dòng tổng, sai bounding box | `UNCERTAIN` hoặc `REFUSE` |
| Receipt Structure Validator | In: OCR boxes. Out: `amount_candidates`, labels, confidence. | p50 80ms, p95 250ms | Thấp | Gắn nhãn sai khách đưa/thối/tổng | LLM cross-check + UI confirm |
| RAG Service | In: OCR context + candidates. Out: rules/examples. | p50 250ms, p95 900ms | Thấp-vừa | Retrieval yếu, rule cũ | Redis cache; nếu vẫn yếu thì `UNCERTAIN` |
| Redis Cache | In: rule/template key. Out: cached rules. | p50 5ms, p95 30ms | Thấp | Cache miss/stale | Primary Policy DB |
| Primary Policy DB/API | In: rule lookup. Out: official receipt rules + save policy. | p50 100ms, p95 500ms | Thấp-vừa | Timeout/down | Redis cache; nếu miss thì `REFUSE` |
| Vector DB | In: embedding OCR context. Out: similar bill examples. | p50 150ms, p95 700ms | Vừa | Similarity thấp, index lỗi | Không dùng examples, dựa validator + confirm |
| LLM Service | In: OCR text + candidates + rules. Out: JSON chosen amount/confidence. | p50 1.5s, p95 5s | Cao nhất | Quota exhausted, hallucination | Static template, no auto-save |
| UI State Router + Save Gate | In: validator + LLM output + user message. Out: `DEFAULT/UNCERTAIN/REFUSE/PRESSURE-TRAP`. | p50 50ms, p95 150ms | Thấp | Cho lưu khi chưa confirm | Backend reject save thiếu `user_confirmed=true` |
| Monitoring Service | In: events/metrics. Out: dashboard + alerts. | Alert p95 < 60s | Vừa | Mất log, alert nhiễu | Local counters, daily audit export |



## 2. Thành phần chính

| Component | Input / Output | Latency | Cost | Failure mode | Fallback |
|---|---|---:|---:|---|---|
| User-facing API | In: ảnh bill + token. Out: `request_id`, routed task. | p50 50ms, p95 150ms | Thấp, infra cost | API quá tải, auth fail | Rate limit, retry nhẹ |
| Intent Classifier | In: metadata ảnh + message. Out: `receipt_image` / OOS / screenshot. | p50 80ms, p95 250ms | Rule-based gần 0; LLM optional | Phân loại nhầm bill thành OOS | Default route sang receipt review nếu không chắc |
| OCR + Layout Parser | In: ảnh bill. Out: text boxes, bounding boxes, font size, spatial order. | p50 700ms, p95 2.5s | Vừa | OCR thiếu dòng tổng, sai bounding box | `UNCERTAIN` hoặc `REFUSE` |
| Receipt Structure Validator | In: OCR boxes. Out: `amount_candidates`, labels, confidence. | p50 80ms, p95 250ms | Thấp | Gắn nhãn sai khách đưa/thối/tổng | LLM cross-check + UI confirm |
| RAG Service | In: OCR context + candidates. Out: rules/examples. | p50 250ms, p95 900ms | Thấp-vừa | Retrieval yếu, rule cũ | Redis cache; nếu vẫn yếu thì `UNCERTAIN` |
| Redis Cache | In: rule/template key. Out: cached rules. | p50 5ms, p95 30ms | Thấp | Cache miss/stale | Primary Policy DB |
| Primary Policy DB/API | In: rule lookup. Out: official receipt rules + save policy. | p50 100ms, p95 500ms | Thấp-vừa | Timeout/down | Redis cache; nếu miss thì `REFUSE` |
| Vector DB | In: embedding OCR context. Out: similar bill examples. | p50 150ms, p95 700ms | Vừa | Similarity thấp, index lỗi | Không dùng examples, dựa validator + confirm |
| LLM Service | In: OCR text + candidates + rules. Out: JSON chosen amount/confidence. | p50 1.5s, p95 5s | Cao nhất | Quota exhausted, hallucination | Static template, no auto-save |
| UI State Router + Save Gate | In: validator + LLM output + user message. Out: `DEFAULT/UNCERTAIN/REFUSE/PRESSURE-TRAP`. | p50 50ms, p95 150ms | Thấp | Cho lưu khi chưa confirm | Backend reject save thiếu `user_confirmed=true` |
| Monitoring Service | In: events/metrics. Out: dashboard + alerts. | Alert p95 < 60s | Vừa | Mất log, alert nhiễu | Local counters, daily audit export |


## 3. Khi hệ thống gặp vấn đề

| Khi nào lỗi xảy ra? | Hệ thống làm gì? | Người dùng thấy gì? |
|---|---|---|
| Bill có nhiều dòng số: khách đưa, thối lại, tổng cộng | Không tự chọn số lớn nhất; validator tạo danh sách candidate và UI yêu cầu xác nhận | "Mình thấy nhiều số trên bill. Mình đề xuất Tổng cộng 150k, bạn xác nhận trước khi lưu nhé." |
| Không tìm thấy label "Tổng cộng" | Chuyển sang `UNCERTAIN`, yêu cầu user chọn dòng đúng hoặc nhập tay | "Mình chưa chắc dòng nào là tổng tiền. Bạn chọn số đúng giúp mình nhé." |
| Ảnh mờ hoặc mất dòng tổng | Chuyển sang `REFUSE`, không gọi AI để đoán số | "Ảnh chưa đủ rõ để đọc tổng tiền. Bạn chụp lại hoặc nhập tay nhé." |
| User ép "ước chừng thôi" | Chuyển sang `PRESSURE-TRAP`, giữ boundary không đoán | "Mình không thể ước chừng số tiền vì sẽ làm sai báo cáo. Bạn nhập tay giúp mình nhé." |
| Nguồn rule bị lỗi hoặc quá chậm | Dùng Redis cache; nếu cache miss thì chuyển sang `UNCERTAIN` hoặc `REFUSE` | "Mình chưa đủ chắc để tự chọn dòng tổng. Bạn xác nhận thủ công nhé." |
| LLM service hết quota | Dùng static template và candidates từ validator | "Mình chưa thể xử lý tự động lúc này. Bạn có thể chọn số tiền từ các dòng đọc được." |
| User sửa số tiền sau khi AI đề xuất | Ghi correction event, không xem đó là lỗi user | "Mình đã sửa khoản này. Cảm ơn bạn, lỗi này sẽ được ghi nhận để cải thiện." |
| Lỗi này lặp lại nhiều lần | Monitoring tăng `wrong_amount_correction_rate`, `uncertain_rate`; gửi alert cho nhóm | User không thấy alert nội bộ; chỉ thấy UI review kỹ hơn với bill tương tự |

---

## 4. Phối hợp 3 lớp

- [x] Sơ đồ không chỉ là “AI trả lời tốt hơn”, mà có bước kiểm tra cấu trúc bill cụ thể.
- [x] Có cách xử lý khi thiếu dữ liệu hoặc không xác định được dòng "Tổng cộng".
- [x] Có cách chuyển sang xác nhận thủ công / nhập tay.
- [x] Có cách theo dõi để lần sau sửa tốt hơn.
