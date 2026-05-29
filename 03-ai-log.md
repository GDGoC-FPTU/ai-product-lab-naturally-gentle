# 📝 Phase 6 — AI Log & Reflection (Nhật Ký Chiêm Nghiệm)

Tài liệu này ghi lại quá trình tôi tương tác và phối hợp với AI (đóng vai trò trợ lý đồng hành - Thought-partner) để scoping bài toán, xây dựng bản mẫu Prompt và gỡ lỗi code Python trong suốt buổi học.

---

## 🤖 1. AI đã giúp tôi làm được gì?

Trong bài lab hôm nay, tôi đã tận dụng AI làm một trợ lý đắc lực trong 4 tác vụ chính:
1.  **Brainstorm ý tưởng bài toán (Phase 1):** AI hỗ trợ tôi quét nhanh các hoạt động của Vingroup qua 4 lenses, gợi ý ra các nút thắt cổ chai cụ thể trong việc điều phối trạm sạc của Xanh SM và tóm tắt hồ sơ bệnh án của Vinmec kèm theo các ước lượng con số kinh tế thực tế.
2.  **Thiết kế và tối ưu Prompt bảo vệ ranh giới (Phase 4):** AI giúp tôi viết một `SYSTEM_PROMPT` chặt chẽ, xác lập rõ ranh giới cấm vượt qua (battery < 5% không đề xuất trạm xa > 5km) và cấu trúc hóa dữ liệu đầu ra dưới dạng JSON cứu hộ pin.
3.  **Lập trình Python & Gemini 2.5 SDK:** AI hỗ trợ tôi viết nhanh cú pháp kết nối SDK mới `google-genai` (Client-based) để thay thế cho thư viện cũ `google-generativeai`.
4.  **Tối ưu hóa khả năng chống chịu lỗi (Resilience):** Khi gặp lỗi giới hạn cuộc gọi của gói miễn phí API (`429 RESOURCE_EXHAUSTED`), AI đã đề xuất cho tôi giải pháp thiết lập vòng lặp thử lại tự động với thời gian chờ tăng dần theo cấp số nhân (Exponential Backoff), giúp script chạy cực kỳ ổn định mà không bị crash ngang.

---

## ⚠️ 2. AI đã sai sót hoặc đưa ra thông tin lệch lạc gì?

Trong quá trình đồng hành, tôi nhận thấy AI có hai điểm hạn chế lớn:
1.  **Đề xuất kiến trúc quá phức tạp:** Ở bước đầu thiết kế sơ bộ (Quick Card), AI đề xuất sử dụng kiến trúc Multi-Agent tự trị (Autonomous Agents) cho quy trình điều phối trạm sạc. Điều này cực kỳ rủi ro vì xe hết pin giữa đường là một sự cố nguy hiểm, không thể để AI tự động gửi chỉ dẫn trực tiếp cho tài xế mà thiếu bước con người phê duyệt (Human-in-the-loop) hay các ranh giới quy tắc cứng (Rule-based rules).
2.  **Lỗi mã hóa ký tự trên hệ điều hành Windows (UnicodeEncodeError):** Khi viết code chạy stress-test, AI đưa ra các dòng lệnh `print` chứa ký tự biểu cảm (emojis) như `🚀`, `✅`, `❌`, `⏳`. Khi chạy trên môi trường PowerShell mặc định của Windows, Python đã crash hoàn toàn do codec mã hóa mặc định `cp1252` không thể mã hóa được các emoji này thành byte tương ứng.

---

## 🛠️ 3. Tôi đã điều chỉnh và khắc phục ra sao?

Để khắc phục các sai sót trên của AI, tôi đã tiến hành:
1.  **Hạ cấp kiến trúc & Thêm HITL:** Tôi đã ép AI hạ cấp kiến trúc từ **Autonomous Agent** xuống **LLM Feature** hỗ trợ soạn thảo nháp (`[DRAFT_ONLY]`), đồng thời thiết lập ranh giới cứng bảo vệ lượng pin qua chỉ thị hệ thống cực kỳ nghiêm ngặt.
2.  **Sửa lỗi mã hóa ký tự Windows:** Tôi đã bổ sung đoạn mã cấu hình luồng ghi stdout và stderr thành mã hóa UTF-8 ở ngay đầu file `prompt_prototype.py`:
    ```python
    if sys.stdout.encoding != 'utf-8':
        try:
            import io
            sys.stdout = io.TextIOWrapper(sys.stdout.buffer, encoding='utf-8')
            sys.stderr = io.TextIOWrapper(sys.stderr.buffer, encoding='utf-8')
        except Exception:
            pass
    ```
    Đoạn mã này đã giúp hệ thống tự động nhận diện và in ra các biểu tượng trạng thái trực quan một cách trơn tru trên mọi terminal Windows mà không gặp bất kỳ lỗi crash nào.
3.  **Khắc phục lỗi Rate Limit API:** Tôi đã tái cấu trúc hàm `evaluate_prompt` để bọc lệnh gọi Gemini bằng một try-except bắt lỗi `ClientError` mã `429` và sleep tự động trước khi gọi lại:
    ```python
    for attempt in range(6):
        try:
            response = client.models.generate_content(...)
            return response.text
        except ClientError as e:
            if e.code == 429 and attempt < 5:
                sleep_time = (2 ** attempt) + 1.5
                time.sleep(sleep_time)
                continue
            raise e
    ```
    Nhờ có cơ chế tự thích ứng này, script đã vượt qua hoàn toàn các bài kiểm tra stress-test của autograder một cách xuất sắc!
