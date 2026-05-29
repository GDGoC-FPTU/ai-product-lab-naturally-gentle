
# 🔍 Phase 1 — SCAN (Cá nhân, 20 min)

Hãy sử dụng **4 Lenses** dưới đây để quét qua hoạt động vận hành của các công ty thành viên Vingroup. Ghi lại **ít nhất 5 bài toán/bottleneck** thực tế.

### 4 Lenses tìm bài toán AI cho Vingroup:
1. **Lặp lại (Repetitive):** Tác vụ lặp đi lặp lại nhiều lần hằng ngày. (Ví dụ: So khớp hóa đơn sạc điện tại VinFast, route lại chuyến taxi tại Xanh SM).
2. **Tốn thời gian (Time-consuming):** Tác vụ ngốn thời gian xử lý thủ công của nhân viên. (Ví dụ: Soạn thảo phản hồi đánh giá 1-star của cư dân Vinhomes).
3. **AI có thể tốt hơn (AI-upgrade):** Dịch vụ khách hàng hiện tại còn chậm hoặc phản hồi rập khuôn. (Ví dụ: Chatbot CSKH Vinpearl hỗ trợ đặt vé vui chơi).
4. **Pain từ người khác (Stakeholder Pain):** Bottleneck khiến khách hàng hoặc nhân viên thực địa phàn nàn. (Ví dụ: Tài xế Xanh SM phàn nàn về việc hệ thống gợi ý điểm đón khách không chính xác).

> [!TIP]
> **🤖 AI Prompts — Partner brainstorm:**
> Hãy sử dụng prompt sau để brainstorm các bài toán thực tế nếu bạn chưa có ý tưởng:
> *"Tôi là AI Engineer tại Vin Smart Future (Vingroup). Tôi đang tìm kiếm các pain point vận hành cụ thể có thể tối ưu bằng AI cho mảng [Chọn một: VinFast / Xanh SM / Vinhomes / Vinmec]. Hãy gợi ý cho tôi 5 quy trình nghiệp vụ thủ công, tốn nhiều thời gian và gây rò rỉ hiệu suất kèm con số thống kê ước tính về tổn thất."*

### 📝 List bài toán của tôi:
| # | Subsidiary (VinFast/Xanh SM...) | Lens | Mô tả ngắn bài toán |
|---|----------------------------------|------|---------------------|
| 1 | Xanh SM | Tốn thời gian | Dispatcher xử lý sự cố tài xế hết pin giữa đường bằng cách tra cứu thủ công trạm sạc, soạn hướng dẫn và gọi cứu hộ, tốn ~15 phút/lượt. |
| 2 | VinFast | Lặp lại | Nhân viên đối chiếu hóa đơn sạc và dữ liệu trạm sạc đối tác hàng tuần thủ công, mất 2-3 giờ/tuần và dễ sai sót. |
| 3 | Vinhomes | AI-upgrade | CSKH phản hồi khiếu nại cư dân bằng mẫu thủ công nên chậm, không cá nhân hóa, mất ~12 giờ xử lý mỗi ngày. |
| 4 | Vinmec | Pain từ người khác | Bác sĩ và điều dưỡng viết tóm tắt xuất viện thủ công cho mỗi bệnh nhân, mất 20-30 phút/patient và gây tắc luồng ra viện. |
| 5 | Vinpearl | Tốn thời gian | Nhân viên hỗ trợ vé/vui chơi trả lời nhiều câu hỏi lặp lại về giá vé và combo, mất nhiều thời gian và dễ sai thông tin đối với khách nước ngoài đa quốc gia. |

---

# 🃏 Phase 2 — QUICK-ASSESS (Cá nhân, 30 min)

Chọn **top 3 bài toán** từ danh sách trên và hoàn thiện **3 Quick Problem Cards** dưới đây (10 phút/card).

```
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #1                                       │
│                                                             │
│ Bài toán (1 câu): Nhân viên Vinpearl Guest Support trả lời yêu cầu từ khách nước ngoài (tiếng Anh / Trung / Hàn) về dịch vụ, lịch trình hoặc đặt phòng. │
│ Công ty thành viên: [ ] VinFast  [ ] Xanh SM  [ ] Vinhomes   │
│                     [ ] Vinmec   [x] Vinpearl ________      │
│                                                             │
│ Ai đang đau (Actor)? Khách nước ngoài chờ phản hồi lâu và Nhân viên Support quá tải dịch/soạn. │
│                                                             │
│ Workflow thủ công hiện tại (5 bước):                        │
│   1. Khách gửi yêu cầu qua App/hotline bằng ngoại ngữ       │
│   2. Nhân viên đọc và dịch yêu cầu                          │
│   3. Tra cứu thủ công thông tin dịch vụ / lịch trình        │
│   4. Soạn phản hồi bằng tiếng Anh chuẩn bằng tay            │
│   5. Gửi lại khách và chờ xác nhận                          │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Tra cứu + soạn phản hồi (⏱ 12-15 phút/yêu cầu)│
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 3-4: AI phân tích yêu cầu, tra cứu dịch vụ, draft phản hồi bằng Anh chuẩn. │
│                                                             │
│ Đo thành công bằng gì (Metric có số)? Giảm thời gian xử lý từ 18 phút xuống dưới 5 phút; tỷ lệ phản hồi đúng từ 65% lên 90%.│
│                                                             │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM  [ ] Agent │
└─────────────────────────────────────────────────────────────┘
```

```
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #2                                       │
│                                                             │
│ Bài toán (1 câu): CSKH Vinhomes phân loại và trả lời khiếu nại cư dân bằng template thủ công nên chậm và thiếu nhất quán.│
│ Công ty thành viên: [ ] VinFast  [ ] Xanh SM  [x] Vinhomes   │
│                     [ ] Vinmec   [ ] Khác (Ghi rõ)________  │
│                                                             │
│ Ai đang đau (Actor)? Nhân viên CSKH và cư dân phản hồi chậm.│
│                                                             │
│ Workflow thủ công hiện tại (3-5 bước):                      │
│   1. Nhân viên nhận ticket khiếu nại trên app                │
│   2. Đọc nội dung, xác định loại vấn đề                      |
│   3. Chọn mẫu trả lời phù hợp và chỉnh sửa bằng tay          |
│   4. Gửi phản hồi và theo dõi escalation nếu cần             │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Phân loại + soạn câu trả lời (⏱ 10-12 phút/ticket)│
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2-3: tự động phân loại và draft phản hồi.│
│                                                             │
│ Đo thành công bằng gì (Metric có số)? Giảm thời gian phản hồi từ 12 phút xuống dưới 3 phút; 90% ticket được phân loại đúng.│
│                                                             │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM  [ ] Agent │
└─────────────────────────────────────────────────────────────┘
```

```
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #3                                       │
│                                                             │
│ Bài toán (1 câu): Bác sĩ Vinmec tốn nhiều thời gian viết tay tóm tắt xuất viện cho từng bệnh nhân.│
│ Công ty thành viên: [ ] VinFast  [ ] Xanh SM  [ ] Vinhomes  │
│                     [x] Vinmec   [ ] Khác (Ghi rõ)________  │
│                                                             │
│ Ai đang đau (Actor)? Bác sĩ, điều dưỡng và bệnh nhân chờ giấy tờ.│
│                                                             │
│ Workflow thủ công hiện tại (3-5 bước):                      │
│   1. Bác sĩ đọc lại hồ sơ bệnh án và kết quả cận lâm sàng     │
│   2. Viết tóm tắt xuất viện bằng tay                          |
│   3. Kiểm tra lại nội dung và ký duyệt                        |
│   4. Gửi tóm tắt cho bệnh nhân/thu viện                       │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Viết tóm tắt (⏱ 20-30 phút/patient)│
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2: tự động trích xuất và draft summary.│
│                                                             │
│ Đo thành công bằng gì (Metric có số)? Giảm thời gian tạo tóm tắt từ 25 phút xuống dưới 7 phút; sai sót nội dung ≤ 2 ~ 3%.│
│                                                             │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM  [ ] Agent │
└─────────────────────────────────────────────────────────────┘
```

> [!TIP]
> **🤖 AI Prompts — Stress-Test thẻ bài toán:**
> Hãy dán nội dung thẻ bài toán của bạn vào LLM để nhận phản biện:
> *"Đây là một thẻ bài toán vận hành tôi đề xuất cho Vin Smart Future: [Dán nội dung]. Hãy đóng vai trò là một CFO và Trưởng phòng Vận hành cực kỳ khắt khe, chỉ ra cho tôi 3 điểm yếu về logic, metric, và giải thích vì sao rule-based code thông thường có thể giải quyết bài toán này tốt hơn là dùng AI."*