# 03 - AI Log & Reflection

## AI giúp gì?

Tôi đã dùng AI như một thought partner để brainstorm các pain point vận hành trong mảng Vinmec, đặc biệt là các quy trình có khả năng bị rò rỉ hiệu suất như: tóm tắt bệnh án sau khám, theo dõi bệnh nhân nội trú, tổng đài đặt lịch, sàng lọc hình ảnh y khoa và phân loại phản hồi bệnh nhân.

## AI sai gì?

Ban đầu AI có xu hướng đề xuất giải pháp AI quá rộng, ví dụ:

- Với bài toán đọc và sàng lọc hình ảnh y khoa, AI đề xuất “AI hỗ trợ phát hiện tổn thương, tạo báo cáo nháp” cho cả X-quang, CT, MRI. Đây là scope quá lớn vì mỗi modality và mỗi bệnh lý cần model, dữ liệu kiểm định và phê duyệt chuyên môn riêng.
- Với bài toán theo dõi bệnh nhân nội trú, AI gộp nhiều vấn đề khác nhau vào một thẻ: nhập sinh hiệu, cảnh báo xấu đi, tóm tắt diễn biến và ưu tiên danh sách bệnh nhân. Điều này làm scope mơ hồ, khó đo ROI và có rủi ro false alarm.
- Một số con số như “5-12 phút/lượt” hoặc “giảm backlog 20-30%” chỉ là benchmark/giả định, chưa phải số liệu thật của Vinmec. Nếu không ghi rõ là baseline cần xác minh, dễ biến thành hallucination hoặc claim quá chắc.

## Sửa đổi ra sao?

Tôi đã điều chỉnh prompt và ranh giới bằng cách yêu cầu AI đóng vai CFO + Trưởng phòng Vận hành cực kỳ khắt khe. Prompt mới buộc AI phải phản biện 3 điểm: logic, metric và lý do rule-based code có thể tốt hơn AI.

Sau đó tôi sửa lại hướng giải pháp:

- Không chọn “AI full automation” ngay từ đầu.
- Với bệnh án sau khám: dùng Rule + LLM draft + bác sĩ duyệt, không cho AI tự chẩn đoán hoặc tự lưu hồ sơ.
- Với nội trú: dùng threshold rule + priority score trước; AI chỉ được tóm tắt ghi chú phi cấu trúc.
- Với hình ảnh y khoa: dùng rule-based queue theo metadata/SLA trước; AI đọc ảnh chỉ là phase sau cho một use case hẹp và có bác sĩ duyệt.

Tôi cũng bổ sung operational boundaries rõ hơn:

- AI không được tự chẩn đoán.
- AI không được tự đổi thuốc hoặc tạo y lệnh.
- AI không được tự phát hành báo cáo y khoa.
- Mọi output liên quan đến bệnh nhân phải có human-in-the-loop.
- Nếu AI thiếu dữ liệu nguồn hoặc không đủ tự tin, fallback về template/rule-based workflow.
