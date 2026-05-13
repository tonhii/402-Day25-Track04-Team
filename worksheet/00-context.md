---
title: 00 — Bối cảnh sản phẩm của nhóm
section: Day 25 — dùng lại cho mọi cuộc trò chuyện với AI
format: Nhóm
time: Điền 5 phút đầu buổi
---

# 00-context.md — Bối cảnh sản phẩm của nhóm

Điền file này một lần ở đầu buổi. Sau đó, mỗi lần dùng AI, hãy đưa toàn bộ nội dung file này vào đầu cuộc trò chuyện.

Lý do: AI không tự nhớ bối cảnh giữa các cuộc trò chuyện. Nếu mỗi lần đưa bối cảnh khác nhau, câu trả lời cũng sẽ lệch.

---

## 1. Sản phẩm

- **Tên sản phẩm / bot**: Trợ lý ghi chú và tổng hợp chi tiêu
- **Sản phẩm giúp ai làm gì**: Giúp sinh viên/nhân viên văn phòng tự động đọc thông tin từ ảnh chụp bill hoặc SMS ngân hàng, tự động trích xuất số tiền và phân loại vào các hạng mục chi tiêu.
- **Người dùng gặp sản phẩm ở đâu**: Ứng dụng điện thoại
- **Giai đoạn hiện tại**: Chuẩn bị ra mắt (đang kiểm thử trước khi launch)

---

## 2. Phạm vi

**AI được làm gì**

- Đọc thông tin từ ảnh chụp bill hoặc SMS ngân hàng (import).
- Tự động trích xuất số tiền.
- Phân loại khoản chi vào các hạng mục chi tiêu tương ứng.

**AI không được làm gì**

- Không được quyền tư vấn tài chính.
- Không được xóa hoặc chỉnh sửa giao dịch mà không hỏi ý kiến người dùng.
- Không được tự động lưu khoản chi vào sổ mà không có bước xác nhận rõ ràng từ người dùng.
- Không được trích xuất và lưu văn bản chứa thông tin nhạy cảm (mã OTP, thẻ tín dụng).

**Vì sao có giới hạn này**

Nhằm tránh rủi ro mô hình AI (OCR) nhận diện sai số tiền (ví dụ: lấy nhầm "Tiền thối lại" thay vì "Tổng cộng") dẫn đến sai lệch báo cáo cuối tháng, gây hoang mang và lo âu tài chính cho người dùng. Đồng thời đảm bảo quyền riêng tư, tránh rò rỉ dữ liệu nhạy cảm (Privacy/Data leak) từ tin nhắn SMS.

---

## 3. Người dùng

- **Là ai**: Sinh viên/nhân viên văn phòng mới bắt đầu quản lý tài chính, thường xuyên quên ghi chép nên hay dồn lại chụp bill cuối tuần.
- **Họ hỏi AI khi nào**: Thường dùng qua app điện thoại vào buổi tối cuối tuần ở nhà để tổng hợp chi tiêu.
- **Họ cần quyết định gì sau khi hỏi AI**: Xác nhận tính chính xác của số tiền AI vừa trích xuất trước khi chốt lưu vào sổ.
- **Khi nào họ dễ bị tổn thương / dễ hiểu sai**: Khi họ thao tác nhanh, có xu hướng lướt qua nhanh và tin tưởng tuyệt đối vào khả năng nhận diện của AI. Hoặc khi nhìn thấy báo cáo ngân sách bị lố do lỗi trích xuất của AI mà họ không phát hiện ra.
- **Họ thường tin AI đến mức nào**: Dễ bị over-reliance (tin tưởng quá mức), thường nghĩ AI đọc từ ảnh thì luôn chính xác, dẫn đến việc lười kiểm tra lại.

---

## 4. Bối cảnh ngành

- **Sự cố tương tự đã từng xảy ra**: Các model OCR thiếu khả năng nhận thức không gian (spatial) nên khi gặp hóa đơn phức tạp (có dòng "tiền khách đưa", "tiền thối lại" to hơn dòng tổng cộng) sẽ trích xuất sai. AI cũng hay có xu hướng cố gắng (hallucination) tự đoán/bịa ra con số khi ảnh hóa đơn bị mờ, ngôn ngữ lạ, chữ viết tay.
- **Quy định hoặc ràng buộc liên quan**: Quy định bảo mật dữ liệu PII (Personal Identifiable Information), nghiêm cấm việc thu thập/lưu trữ các thông tin như mã OTP, số thẻ, số tài khoản ngân hàng từ SMS người dùng upload.
- **Nguồn chính thức nên ưu tiên**: Chỉ dựa vào hình ảnh hóa đơn rõ ràng, hợp lệ để trích xuất. Nếu ảnh mờ, chứa dữ liệu nhạy cảm hoặc bất thường thì phải yêu cầu người dùng nhập tay.

---

## 5. Ghi chú thêm

- Nhóm đang ở bước kiểm thử (test & eval) với các rủi ro trọng tâm là Hallucination (nhận diện sai thông tin hóa đơn) và Privacy/Data leak (lưu nhầm OTP).
- Kế hoạch đánh giá chú trọng vào việc AI phải có khả năng từ chối xử lý (nếu là bill mờ, bill chứa OTP) và bắt buộc thiết kế giao diện có yếu tố "Human-in-the-loop" (người dùng phải luôn được yêu cầu xác nhận số tiền trước khi lưu).

---

## Cách dùng

```text
1. Mở công cụ AI phù hợp với bước đang làm.
2. Đưa toàn bộ nội dung file này vào đầu cuộc trò chuyện.
3. Chọn prompt tham khảo từ thư mục ../prompts/ và chỉnh lại nếu cần.
4. Đọc lại bản nháp AI tạo ra.
5. Sửa lại cho đúng bối cảnh nhóm.
6. Lưu kết quả vào đúng file trong worksheet/.
```

Ghi chú: nội dung trong `[...]` là chỗ cần điền. Sau khi điền xong, xóa dấu ngoặc nếu không cần giữ.
