# Phase 3 - Deep-Dive Report: Vinmec Operations AI Scoping

## Executive Decision



# 3.1. Current-State Workflow Mapping

## Case 1 - Tóm tắt bệnh án và ghi chú sau khám

```text
Bệnh nhân vào khám
  -> Bác sĩ hỏi bệnh, khám lâm sàng
  -> 🔄 Bác sĩ/điều dưỡng nhập triệu chứng, chẩn đoán, chỉ định vào EMR
  -> 🔴 Chuẩn hóa note, dặn dò, ICD/dịch vụ, thiếu trường bắt buộc
  -> 🔄 Hồ sơ chuyển sang thanh toán/bảo hiểm/lưu trữ
```

**Tổng thời gian vận hành trung bình:** khoảng 8-15 phút/lượt, trong đó phần nhập và chuẩn hóa note chiếm khoảng 5-12 phút/lượt.

**Bottleneck chính:** bác sĩ/điều dưỡng phải nhập lại thông tin sau khám, dễ thiếu trường, sai mã ICD/dịch vụ, hoặc viết dặn dò không đồng nhất.

**Handoff chính:** từ bác sĩ sang EMR; từ EMR sang bộ phận hồ sơ, thanh toán, bảo hiểm.

## Case 2 - Theo dõi bệnh nhân nội trú

```text
Điều dưỡng đo sinh hiệu theo lịch
  -> 🔄 Nhập sinh hiệu/triệu chứng/thuốc vào hồ sơ
  -> 🔴 Tự rà bất thường trên nhiều bệnh nhân cùng lúc
  -> Gọi bác sĩ trực nếu nghi ngờ xấu đi
  -> 🔄 Bác sĩ xem hồ sơ và quyết định can thiệp
```

**Tổng thời gian vận hành trung bình:** cần đo thực tế theo khoa; giả định 3-7 phút/lần ghi nhận/bệnh nhân, lặp lại nhiều lần trong ngày.

**Bottleneck chính:** ưu tiên bệnh nhân nào cần kiểm tra trước trong ca trực; rủi ro bỏ sót bất thường hoặc tạo quá nhiều cảnh báo.

**Handoff chính:** từ điều dưỡng sang hồ sơ nội trú; từ điều dưỡng sang bác sĩ trực khi escalation.

## Case 3 - Đọc và sàng lọc hình ảnh y khoa

```text
Khoa lâm sàng gửi chỉ định X-quang/CT/MRI
  -> 🔄 Phim vào PACS/RIS
  -> 🔴 Bác sĩ CĐHA chọn phim để đọc trong queue lẫn ca thường/khẩn
  -> Bác sĩ viết báo cáo
  -> 🔄 Báo cáo trả về khoa gửi chỉ định/bệnh nhân
```

**Tổng thời gian vận hành trung bình:** phụ thuộc modality và SLA; cần đo turnaround time theo X-quang, CT, MRI và theo cấp cứu/thường.

**Bottleneck chính:** phân luồng ca thường/khẩn và kiểm soát SLA; không nhất thiết là năng lực đọc ảnh của AI.

**Handoff chính:** từ khoa gửi chỉ định sang PACS/RIS; từ bác sĩ CĐHA sang khoa lâm sàng.

---

# 3.2. Problem Statement 6-Field & Metrics

## Case 1 - Tóm tắt bệnh án và ghi chú sau khám

| Field | Nội dung chi tiết |
|---|---|
| 1. Actor / Operator | Bác sĩ khám bệnh, điều dưỡng hỗ trợ, bộ phận hồ sơ/bảo hiểm. |
| 2. Current Workflow | Sau khám, bác sĩ/điều dưỡng nhập lại triệu chứng, chẩn đoán, chỉ định, dặn dò, mã ICD/dịch vụ vào EMR. |
| 3. Bottleneck | Nhập và chuẩn hóa note sau khám mất 5-12 phút/lượt; dễ thiếu trường bắt buộc hoặc thiếu dặn dò. |
| 4. Business Impact | Nếu 500 lượt/ngày và tiết kiệm 5 phút/lượt, có thể giải phóng khoảng 2.500 phút/ngày. Tuy nhiên chỉ tính là ROI thật nếu giảm overtime, giảm lỗi hồ sơ, tăng lượt khám, hoặc giảm hồ sơ bảo hiểm bị trả lại. |
| 5. Success Metric | Giảm thời gian hoàn tất note từ baseline 8 phút xuống dưới 3 phút/lượt; giảm 30% hồ sơ thiếu trường; dưới 10% note cần sửa lớn; 100% note AI được bác sĩ duyệt trước khi lưu. |
| 6. Operational Boundary | AI được tạo note nháp và checklist thiếu thông tin. AI không được tự chẩn đoán, tự đổi thuốc, tự phát hành hồ sơ, hoặc tự gửi claim bảo hiểm. Bác sĩ phải duyệt nội dung cuối cùng. |

## Case 2 - Theo dõi bệnh nhân nội trú

| Field | Nội dung chi tiết |
|---|---|
| 1. Actor / Operator | Điều dưỡng ca trực, điều dưỡng trưởng, bác sĩ trực. |
| 2. Current Workflow | Điều dưỡng đo/nhập sinh hiệu, ghi triệu chứng/thuốc, tự rà bất thường và gọi bác sĩ nếu cần. |
| 3. Bottleneck | Rà nhiều bệnh nhân cùng lúc và quyết định ai cần kiểm tra trước; dễ có false alarm hoặc bỏ sót cảnh báo. |
| 4. Business Impact | Ảnh hưởng đến thời gian ca trực, chất lượng escalation và rủi ro an toàn bệnh nhân. Chưa đủ dữ liệu để tính ROI nếu chưa có baseline về false positive, false negative, escalation đúng/sai. |
| 5. Success Metric | 95% sinh hiệu bất thường theo rule được flag dưới 1 phút; giảm 30% thời gian rà danh sách mỗi ca; false positive dưới 20%; mọi cảnh báo nghiêm trọng có log người xác nhận. |
| 6. Operational Boundary | Hệ thống được xếp hạng ưu tiên và cảnh báo theo rule. AI không được tự kết luận bệnh nhân xấu đi, tự ra y lệnh, hoặc thay thế bác sĩ/điều dưỡng trong quyết định lâm sàng. |

## Case 3 - Đọc và sàng lọc hình ảnh y khoa

| Field | Nội dung chi tiết |
|---|---|
| 1. Actor / Operator | Bác sĩ chẩn đoán hình ảnh, khoa cấp cứu, khoa gửi chỉ định, bệnh nhân chờ kết quả. |
| 2. Current Workflow | Khoa gửi chỉ định, phim vào PACS/RIS, bác sĩ chọn phim để đọc, viết báo cáo và trả kết quả. |
| 3. Bottleneck | Queue phim thường/khẩn trộn lẫn; SLA có thể bị trễ do thiếu rule phân luồng rõ. |
| 4. Business Impact | Trễ báo cáo ảnh hưởng SLA cấp cứu, thời gian chờ bệnh nhân, backlog cuối ngày và overtime bác sĩ CĐHA. |
| 5. Success Metric | 95% ca cấp cứu được đưa lên đầu queue dưới 30 giây; giảm 30% ca khẩn đọc trễ SLA; giảm backlog cuối ngày 20%; không có báo cáo AI tự phát hành. |
| 6. Operational Boundary | Rule engine được phép ưu tiên queue theo metadata/SLA. AI đọc ảnh, nếu có, chỉ là second reader cho một use case hẹp và phải có bác sĩ duyệt. AI không được tự phát hành kết luận chẩn đoán. |

---

# 3.3. Future-State Flow & AI Fit

## Case 1 - Future-State Flow

**AI-Fit Matrix:** [x] Rule / State-Machine [x] LLM Feature [ ] Agentic Loop

```text
1. Bác sĩ khám và ghi âm/ghi chú ngắn
2. 🔵 LLM tạo note nháp từ hội thoại hoặc ghi chú phi cấu trúc
3. Rule engine kiểm tra trường bắt buộc, ICD/dịch vụ, dặn dò
4. 🟢 Bác sĩ review, sửa và duyệt
5. Hồ sơ chính thức được lưu vào EMR
6. ↩️ Fallback: nếu AI thiếu tự tin hoặc thiếu dữ liệu nguồn, chỉ hiển thị template trống + checklist rule
```

**Đánh giá chọn bài toán:** GO với prototype hẹp.

**Lý do:** Đây là case cân bằng nhất giữa impact, khả năng demo, và kiểm soát rủi ro. Rule-based xử lý dữ liệu cấu trúc; LLM chỉ xử lý ngôn ngữ tự nhiên và tạo bản nháp.

## Case 2 - Future-State Flow

**AI-Fit Matrix:** [x] Rule / State-Machine [x] LLM Feature [ ] Agentic Loop

```text
1. Điều dưỡng nhập sinh hiệu và ghi chú ca trực
2. Rule engine flag bất thường theo ngưỡng: SpO2, nhiệt độ, huyết áp, mạch
3. Rule priority score xếp hạng danh sách bệnh nhân cần kiểm tra
4. 🔵 LLM tóm tắt diễn biến 12 giờ gần nhất từ ghi chú phi cấu trúc
5. 🟢 Điều dưỡng/bác sĩ xác nhận cảnh báo và quyết định hành động
6. ↩️ Fallback: nếu dữ liệu thiếu hoặc note mơ hồ, chỉ dùng rule threshold và yêu cầu kiểm tra thủ công
```

**Đánh giá chọn bài toán:** NOT YET cho AI dự đoán lâm sàng; GO cho rule dashboard.

**Lý do:** Giá trị vận hành lớn nhưng rủi ro cảnh báo sai cao. Phase đầu nên là dashboard rule-based, sau đó mới đánh giá AI tóm tắt note.

## Case 3 - Future-State Flow

**AI-Fit Matrix:** [x] Rule / State-Machine [ ] LLM Feature [ ] Agentic Loop

```text
1. Khoa gửi chỉ định chụp
2. PACS/RIS nhận phim và metadata
3. Rule engine tính priority theo order_type, khoa gửi, SLA, trước mổ, ICU/ER
4. Queue tự sắp xếp: cấp cứu trước, SLA sắp trễ trước
5. 🟢 Bác sĩ CĐHA đọc phim và phát hành báo cáo
6. ↩️ Fallback: nếu thiếu metadata, chuyển sang queue thủ công và báo điều phối viên
```

**Đánh giá chọn bài toán:** NOT YET cho AI đọc ảnh đa modality; GO cho rule-based queue triage.

**Lý do:** Nếu mục tiêu là giảm trễ và backlog, rule-based queue có thể tạo giá trị nhanh hơn AI đọc ảnh. AI computer vision cần validation dataset, kiểm định chuyên môn và scope hẹp hơn nhiều.

---

# Final Recommendation

| Tiêu chí | Case 1: Ghi chú sau khám | Case 2: Nội trú | Case 3: Hình ảnh y khoa |
|---|---:|---:|---:|
| Tác vụ lặp lại rõ | Cao | Trung bình | Trung bình |
| Dữ liệu đầu vào sẵn có | Trung bình | Trung bình | Cao cho metadata, thấp cho AI ảnh |
| Rủi ro nếu AI sai | Trung bình nếu có bác sĩ duyệt | Cao | Rất cao |
| Rule-based thay thế được bao nhiêu | 50-70% | 70-90% | 70-90% cho queue |
| Phù hợp prototype lab | Cao | Trung bình | Thấp nếu đọc ảnh, trung bình nếu chỉ queue |

Sau khi deep-dive 3 bài toán từ Phase 2, chọn được 1:

**Case được chọn để prototype:** Tóm tắt bệnh án và ghi chú sau khám.

**Lý do:** Có tác vụ lặp lại rõ, metric đo được trong lab, có thể giới hạn AI ở mức tạo nháp và bắt buộc bác sĩ duyệt. Hai case còn lại có giá trị vận hành lớn nhưng nên bắt đầu bằng rule-based trước, hoặc cần dữ liệu kiểm định y khoa nghiêm ngặt hơn.

| Case | AI Fit | Quyết định | Lý do ngắn |
|---|---|---|---|
| Tóm tắt bệnh án và ghi chú sau khám | Rule + LLM Feature | GO với scope hẹp | Phù hợp GenAI nếu chỉ tạo note nháp từ thông tin có nguồn và có human review. |
| Theo dõi bệnh nhân nội trú | Rule / State-Machine + LLM phụ trợ | NOT YET cho AI dự đoán; GO cho rule dashboard | Sinh hiệu có thể xử lý tốt bằng threshold rule và priority score trước. |
| Đọc và sàng lọc hình ảnh y khoa | Rule queue trước; AI CV phase sau | NOT YET cho AI đọc ảnh | Rủi ro clinical validation cao; queue/SLA có thể tối ưu bằng metadata rule. |



---