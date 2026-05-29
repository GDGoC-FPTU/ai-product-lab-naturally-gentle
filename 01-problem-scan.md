# 🔍 Phase 1 — SCAN (Cá nhân, 20 min)

Hãy sử dụng **4 Lenses** dưới đây để quét qua hoạt động vận hành của các công ty thành viên Vingroup. Ghi lại **ít nhất 5 bài toán/bottleneck** thực tế.

### 4 Lenses tìm bài toán AI cho Vingroup:

1. **Lặp lại (Repetitive):** Tác vụ lặp đi lặp lại nhiều lần hằng ngày. (Ví dụ: So khớp hóa đơn sạc điện tại VinFast, route lại chuyến taxi tại Xanh SM).
2. **Tốn thời gian (Time-consuming):** Tác vụ ngốn thời gian xử lý thủ công của nhân viên. (Ví dụ: Soạn thảo phản hồi đánh giá 1-star của cư dân Vinhomes).
3. **AI có thể tốt hơn (AI-upgrade):** Dịch vụ khách hàng hiện tại còn chậm hoặc phản hồi rập khuôn. (Ví dụ: Chatbot CSKH Vinpearl hỗ trợ đặt vé vui chơi).
4. **Pain từ người khác (Stakeholder Pain):** Bottleneck khiến khách hàng hoặc nhân viên thực địa phàn nàn. (Ví dụ: Tài xế Xanh SM phàn nàn về việc hệ thống gợi ý điểm đón khách không chính xác).

### 📝 List bài toán của tôi:

| # | Subsidiary (VinFast/Xanh SM...) | Lens | Mô tả ngắn bài toán |
|---|----------------------------------|------|---------------------|
| 1 | Vinmec | Time-consuming, Repetitive | Tóm tắt bệnh án và ghi chú sau khám: bác sĩ/điều dưỡng mất 5-12 phút/lượt để nhập triệu chứng, chẩn đoán, chỉ định, dặn dò; nên bắt đầu bằng template/validation rule, AI chỉ hỗ trợ ghi chú phi cấu trúc. |
| 2 | Vinmec | Stakeholder Pain, Time-consuming | Theo dõi bệnh nhân nội trú: điều dưỡng phải ghi sinh hiệu, triệu chứng, thuốc và tự rà bất thường; nên dùng threshold rule + priority score trước khi dùng AI tóm tắt diễn biến. |
| 3 | Vinmec | Repetitive, AI-upgrade | Tổng đài đặt lịch khám: nhân viên lặp lại FAQ về chuyên khoa, bác sĩ, giá, lịch trống và hướng dẫn trước khám; phù hợp FAQ bot + rule flow đặt lịch. |
| 4 | Vinmec | Time-consuming, Stakeholder Pain | Đọc và sàng lọc hình ảnh y khoa: queue phim X-quang/CT/MRI lẫn ca thường và ca khẩn; nên ưu tiên bằng metadata/SLA trước, AI đọc ảnh chỉ thử ở use case hẹp. |
| 5 | Vinmec | Stakeholder Pain, AI-upgrade | Phân loại phản hồi bệnh nhân: feedback từ app, hotline, quầy, mạng xã hội bị phân tán; AI có thể hỗ trợ sentiment/topic sau khi có rule routing cơ bản. |

# 🃏 Phase 2 — QUICK-ASSESS (Cá nhân, 30 min)

Chọn **top 3 bài toán** từ danh sách trên và hoàn thiện **3 Quick Problem Cards** dưới đây (10 phút/card).

```
┌──────────────────────────────────────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #1                                                                         │
│                                                                                              │
│ Bài toán (1 câu): Giảm thời gian nhập ghi chú sau khám, nhưng không để AI tự tạo thông tin    │
│ y khoa không có nguồn.                                                                        │
│ Công ty thành viên: [ ] VinFast  [ ] Xanh SM  [ ] Vinhomes                                    │
│                     [x] Vinmec   [ ] Khác (Ghi rõ)________                                    │
│                                                                                              │
│ Ai đang đau (Actor)? Bác sĩ khám bệnh, điều dưỡng hỗ trợ, bộ phận hồ sơ/bảo hiểm.             │
│                                                                                              │
│ Workflow thủ công hiện tại (3-5 bước):                                                        │
│   1. Hỏi bệnh/khám --> 2. Nhập note EMR --> 3. Kiểm tra ICD/dịch vụ --> 4. Chốt hồ sơ         │
│                                                                                              │
│ Bước nào tốn thời gian/lỗi nhất? Nhập và chuẩn hóa note (5-12 phút/lượt)                      │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Tạo note nháp từ hội thoại/ghi chú phi cấu trúc.        │
│                                                                                              │
│ Đo thành công bằng gì (Metric có số)? Giảm thời gian hoàn tất note từ 8 phút xuống <3 phút;   │
│ giảm 30% hồ sơ thiếu trường; 100% note AI phải được bác sĩ duyệt trước khi lưu.               │
│                                                                                              │
│ Quick Architecture: [ ] No AI  [x] Rule  [x] LLM  [ ] Agent                                   │
│ CFO/Ops note: Rule-based template, ICD mapping và validation nên làm trước; AI chỉ là draft.  │
└──────────────────────────────────────────────────────────────────────────────────────────────┘

┌──────────────────────────────────────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #2                                                                         │
│                                                                                              │
│ Bài toán (1 câu): Giúp điều dưỡng ưu tiên bệnh nhân nội trú cần kiểm tra trước dựa trên       │
│ sinh hiệu, y lệnh và dấu hiệu bất thường.                                                     │
│ Công ty thành viên: [ ] VinFast  [ ] Xanh SM  [ ] Vinhomes                                    │
│                     [x] Vinmec   [ ] Khác (Ghi rõ)________                                    │
│                                                                                              │
│ Ai đang đau (Actor)? Điều dưỡng ca trực, điều dưỡng trưởng, bác sĩ trực.                      │
│                                                                                              │
│ Workflow thủ công hiện tại (3-5 bước):                                                        │
│   1. Đo/nhập sinh hiệu --> 2. Ghi triệu chứng/thuốc --> 3. Rà bất thường --> 4. Escalate      │
│                                                                                              │
│ Bước nào tốn thời gian/lỗi nhất? Rà nhiều bệnh nhân và quyết định ai cần kiểm tra trước.      │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Tóm tắt ghi chú điều dưỡng và diễn biến 12 giờ gần nhất.│
│                                                                                              │
│ Đo thành công bằng gì (Metric có số)? 95% sinh hiệu bất thường được flag <1 phút; giảm 30%    │
│ thời gian rà danh sách mỗi ca; false positive <20%; cảnh báo nghiêm trọng có log xác nhận.    │
│                                                                                              │
│ Quick Architecture: [ ] No AI  [x] Rule  [x] LLM  [ ] Agent                                   │
│ CFO/Ops note: Threshold rule + priority score nên làm trước; chưa dùng AI dự đoán lâm sàng.   │
└──────────────────────────────────────────────────────────────────────────────────────────────┘

┌──────────────────────────────────────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #3                                                                         │
│                                                                                              │
│ Bài toán (1 câu): Giảm trễ đọc phim bằng cách ưu tiên queue chẩn đoán hình ảnh theo metadata, │
│ SLA và mức độ khẩn trước khi cân nhắc AI đọc ảnh.                                             │
│ Công ty thành viên: [ ] VinFast  [ ] Xanh SM  [ ] Vinhomes                                    │
│                     [x] Vinmec   [ ] Khác (Ghi rõ)________                                    │
│                                                                                              │
│ Ai đang đau (Actor)? Bác sĩ chẩn đoán hình ảnh, khoa cấp cứu, bệnh nhân chờ kết quả.          │
│                                                                                              │
│ Workflow thủ công hiện tại (3-5 bước):                                                        │
│   1. Gửi chỉ định --> 2. Phim vào PACS/RIS --> 3. Bác sĩ chọn phim đọc --> 4. Trả báo cáo     │
│                                                                                              │
│ Bước nào tốn thời gian/lỗi nhất? Phân luồng phim thường/khẩn và kiểm soát SLA.                │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Chỉ phase sau: sàng lọc một use case hẹp có bác sĩ duyệt.│
│                                                                                              │
│ Đo thành công bằng gì (Metric có số)? 95% ca cấp cứu lên đầu queue <30 giây; giảm 30% ca      │
│ khẩn đọc trễ SLA; giảm backlog cuối ngày 20%; AI không tự phát hành kết luận chẩn đoán.       │
│                                                                                              │
│ Quick Architecture: [ ] No AI  [x] Rule  [ ] LLM  [ ] Agent                                   │
│ CFO/Ops note: Queue theo metadata/SLA giải quyết trước; AI đọc ảnh đa modality là NOT YET.    │
└──────────────────────────────────────────────────────────────────────────────────────────────┘
```
