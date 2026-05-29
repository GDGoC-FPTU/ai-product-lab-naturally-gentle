# 03 - AI Log & Reflection

## AI giúp gì
Trong buổi Lab này, tôi đã dùng AI để triển khai thêm các ý tưởng sẵn có và làm rõ những nội dung mơ hồ thành một cấu trúc hoàn chỉnh. Cụ thể, tôi dùng AI để:
- Brainstorm các tình huống khách nước ngoài cần hỗ trợ tại vinpearl.
- Kết nối các ý thành một giải pháp rõ ràng cho `02-deep-dive-report.md`.
- Hổ trợ triển khai thêm problem statement, future-state flow và checklist sao cho logic, mạch lạc.
- Đề xuất cách phân loại AI Fit giữa Rule, LLM Feature và Agent để tôi có thể so sánh .

## AI sai gì
Lần đầu tôi thử để AI định nghĩa ranh giới, nó đã trả lời quá chung chung và có xu hướng đề xuất giải pháp “tự động gửi phản hồi” mà chưa nhấn mạnh Human-in-the-loop. Đây là một dạng hallucination về quy trình vận hành, vì với bài toán khách nước ngoài tại vinpearl, nếu AI tự động gửi thông tin thì rủi ro rất cao.

## Sửa đổi ra sao
Để sửa lỗi này, tôi đã bổ sung ranh giới an toàn rõ ràng vào prompt:
- Yêu cầu AI chỉ trả về **draft phản hồi** và không được tự động gửi.
- Bắt buộc ghi rõ bước duyệt bởi nhân viên vinpearl.
- Thêm điều kiện nếu AI không chắc chắn thì phải chuyển sang template rule-based hoặc nhân viên cấp cao.

Kết quả là tôi đã có một phiên bản phản hồi an toàn hơn, tách biệt rõ: AI gợi ý + nhân viên phê duyệt. Điều này phù hợp với yêu cầu của Lab và giảm rủi ro khi triển khai thực tế.