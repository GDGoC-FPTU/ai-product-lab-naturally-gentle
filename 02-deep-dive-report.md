# 02 - Deep Dive Report: Vinpert International Guest Support

## 1. Quyết định lựa chọn
Nhóm chọn bài toán **"Một chat bot hỗ trợ khách nước ngoài sử dụng dịch vụ Vinpert tại Vinpearl / VinCity"** để thực hiện Deep-Dive.

### Lý do chọn
* Đây là vấn đề thực tế của Vinpert khi đón khách du lịch/khách thuê nhà quốc tế: họ cần thông tin hướng dẫn, đặt dịch vụ, và giải đáp nhanh bằng tiếng Anh hoặc ngôn ngữ thân thiện cho khu vực khách du lịch.
* Bài toán vừa có yếu tố **tốn thời gian** vừa có **pain point của khách hàng nước ngoài**; giải pháp AI giúp tăng trải nghiệm khách và giảm tải cho bộ phận Guest Support.
* Hiện tại baseline rõ: quy trình xử lý thủ công, thời gian trả lời lâu và tỷ lệ phản hồi không đồng đều.

---

## 2. Problem Statement (6-field)

| Field | Nội dung |
|---|---|
| **1. Actor / Operator** | Nhân viên Vinpert Guest Support hoặc Concierge Team chịu trách nhiệm trả lời yêu cầu của khách nước ngoài tại Vinpearl / VinCity (tiếng Anh / tiếng Trung / tiếng Hàn, v.v.). |
| **2. Current Workflow** | Khi khách nước ngoài gửi yêu cầu qua app Vinpert hoặc hotline, nhân viên phải: 1) đọc và hiểu yêu cầu bằng ngôn ngữ khác; 2) tra cứu thủ công thông tin dịch vụ, lịch trình, quy định an toàn; 3) dịch nội dung chỉ dẫn sang tiếng Anh chuẩn; 4) soạn phản hồi cá nhân hóa; 5) gửi lại và chờ khách xác nhận. Quy trình này kéo dài và phụ thuộc nhiều vào năng lực ngoại ngữ của từng nhân viên. |
| **3. Bottleneck** | Bước tốn nhất là **hiểu đúng yêu cầu khách nước ngoài + soạn phản hồi ngôn ngữ tự nhiên**. Nhân viên mất nhiều thời gian dịch, tìm thông tin phù hợp và viết lời giải thích rõ ràng, dẫn đến phản hồi chậm và dễ sai sót. |
| **4. Business Impact** | Chậm trễ trả lời làm giảm điểm hài lòng lượt khách quốc tế, tăng tỷ lệ khiếu nại và giảm khả năng upsell trải nghiệm cao cấp. Ước tính: mỗi lần trả lời chậm trên 15 phút có thể giảm 1-2% NPS và khiến 10-15% khách hàng bỏ qua dịch vụ giá trị cao. |
| **5. Success Metric** | 1) Giảm thời gian xử lý yêu cầu từ trung bình 18 phút xuống dưới 5 phút. 2) Tăng tỷ lệ phản hồi lần đầu đúng yêu cầu từ 65% lên 90%. 3) Duy trì mức đánh giá khách hàng quốc tế về hỗ trợ `>= 4.7/5`. |
| **6. Operational Boundary** | AI chỉ được phép: phân tích yêu cầu nhập bằng ngôn ngữ tự nhiên, gợi ý dịch vụ phù hợp, tạo **draft phản hồi** bằng tiếng Anh/tiếng khách. AI không được phép: tự động gửi phản hồi, xác nhận đặt dịch vụ thay khách, hoặc đưa ra cam kết an toàn y tế/luật pháp. Mọi output phải được duyệt bởi nhân viên Vinpert trước khi gửi. |

---

## 3. Future-State Flow & AI Fit

### AI Fit: LLM Feature
* **Đặc tính kỹ thuật**: đầu vào là câu hỏi không cấu trúc từ khách nước ngoài, đầu ra là văn bản trả lời linh hoạt. Có con người giám sát/duyệt lại.
* **Tại sao không phải Rule / Agent**:
  * **Rule / Script**: không phù hợp vì ngôn ngữ và nhu cầu khách đa dạng, input không cấu trúc.
  * **Agent**: không cần tự động quyết định nhiều API/phản hồi động, quá cao rủi ro cho quy trình hỗ trợ khách. Cần giữ quyền kiểm soát bằng nhân viên phê duyệt.

### Future-State Flow (text-diagram)

```text
Khách nước ngoài gửi yêu cầu qua app Vinpert / hotline
        │
        ▼
[1] Nhân viên Vinpert nhận yêu cầu
        │
        ▼
[2] AI LLM phân tích nội dung yêu cầu và ngôn ngữ
        │
        ▼
[3] AI tra cứu dữ liệu dịch vụ có cấu trúc (lịch trình, giá, availability)
        │
        ▼
[4] AI tạo draft phản hồi bằng tiếng Anh / ngôn ngữ phù hợp
        │
        ▼
[5] Nhân viên Vinpert kiểm tra, chỉnh sửa, phê duyệt draft
        │
        ▼
[6] Nhân viên gửi phản hồi chính thức cho khách
```

### Bước AI và Human-in-the-loop
* **AI Step**: phân tích ngôn ngữ, gợi ý dịch vụ phù hợp, tạo nội dung trả lời cá nhân hóa.
* **Human-in-the-loop**: nhân viên duyệt draft, kiểm tra dữ liệu giá/trạng thái, đảm bảo compliance và chính xác. Không có bước nào được gửi trực tiếp mà không qua phê duyệt.

### Fallback
* Nếu AI không tự tin hoặc trả về output mơ hồ / chứa thông tin sai:
  * Thực hiện fallback sang **rule-based template** chuyên dụng cho 3 loại yêu cầu phổ biến (đặt phòng, hỏi giờ mở cửa, yêu cầu hỗ trợ di chuyển).
  * Nếu nội dung quá phức tạp, chuyển tiếp lên nhân viên cấp cao hoặc bộ phận dịch thuật chuyên môn.
* Nếu API dịch vụ hoặc database không sẵn sàng: nhân viên sử dụng bản mẫu văn bản cứng và trả lời tạm thời, kèm lời xin lỗi và hẹn lại thời gian xử lý.

---

## 4. Evaluate

### 4.1. AI Readiness Checklist
* [x] Có data mẫu và log yêu cầu khách quốc tế để phân tích.
* [x] Rủi ro AI sai được kiểm soát qua quy trình **Human-in-the-loop**.
* [x] Stakeholders Vinpert sẵn sàng thay đổi quy trình từ trả lời tay sang hỗ trợ AI draft.
* [ ] Cần thêm dữ liệu báo lỗi thực tế cho tình huống ngôn ngữ hiếm.
* [x] Operational boundary rõ: AI chỉ gợi ý, nhân viên phê duyệt trước khi gửi.

### 4.2. Quyết định cuối cùng
**GO**

### 4.3. Luận điểm kỹ thuật
* Bài toán rõ ràng: vấn đề khách nước ngoài không nhận được phản hồi nhanh và chuẩn ngôn ngữ.
* Baseline rõ: thời gian xử lý hiện tại ~18 phút / yêu cầu, tỷ lệ đúng yêu cầu 65%.
* Solution fit: **LLM Feature** phù hợp vì input/ output là ngôn ngữ tự nhiên và cần tốc độ tạo draft, nhưng vẫn giữ HITL để kiểm soát sai sót.
* Risk controls: Ranh giới cấm tự gửi, giới hạn AI chỉ trả draft, và fallback rule-based template nếu AI không chắc chắn.

### 4.4. Ước lượng chi phí kỹ thuật
* Thời gian xây dựng prototype: 2-3 engineer × 3 tuần.
* Công việc chính:
  * tích hợp LLM với hệ thống Vinpert request log;
  * xây dựng prompt template và đầu ra JSON cho draft phản hồi;
  * thiết kế UI duyệt draft cho nhân viên support.
* Chi phí vận hành LLM: trung bình thấp đến vừa phải vì mỗi yêu cầu chỉ cần một call tạo draft, không cần agent liên tục.
* Rủi ro chi phí lớn nhất là nếu không kiểm soát được chất lượng draft, phải tăng thêm nhân sự review; nhưng trong phase đầu, yêu cầu vẫn có thể duy trì bằng human review.

---

## 5. Kết luận
Nhóm khuyến nghị **GO** cho dự án Vinpert International Guest Support. Giải pháp AI nên được triển khai theo scope hẹp: chỉ hỗ trợ soạn draft phản hồi khách nước ngoài và vẫn giữ nhân viên duyệt trước khi gửi để đảm bảo an toàn và compliance.