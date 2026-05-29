# 📘 Phase 3 & 5 — Deep-Dive Report & Evaluation

## 👥 Khai báo thông tin Nhóm
*   **Tên nhóm:** Naturally Gentle
*   **Thành viên dự án:**
    1.  **Lê Hoàng Đạt** — MSSV: 22010003 (Trưởng nhóm / AI Product Engineer)
    2.  **Nguyễn Văn Anh** — MSSV: 22010001 (AI Engineer)
    3.  **Trần Thị Bình** — MSSV: 22010002 (Product Specialist)

---

## 🗳️ 1. Quyết định lựa chọn của nhóm

Nhóm đã họp và quyết định chọn bài toán **"Card #1 — Xanh SM Xử lý sự cố sạc pin thực địa"** để thực hiện Deep-Dive.

### Lý do lựa chọn và loại bỏ các thẻ khác:
*   **Lựa chọn Card #1 (Sự cố sạc pin thực địa):** Đây là sự cố thời gian thực (real-time) xảy ra hằng ngày trên đường đón khách. Tốc độ xử lý ảnh hưởng trực tiếp đến SLA phục vụ khách hàng, tâm lý tài xế và an toàn giao thông. Giải pháp sử dụng LLM Feature kết hợp API định vị là cực kỳ khả thi, mang lại giá trị kinh tế trực tiếp và tức thì cho Xanh SM.
*   **Loại bỏ Card #2 (Vinhomes CSKH Resident App):** Mặc dù tốn nhiều thời gian đọc phân loại nhưng nội dung khiếu nại liên quan đến các vấn đề phức tạp như tranh chấp tài sản, tài chính và pháp lý. Tác động của sai sót AI ở đây rất lớn, cần nhiều dữ liệu huấn luyện lịch sử trước khi có thể tích hợp.
*   **Loại bỏ Card #3 (Xanh SM Tóm tắt lý do hủy chuyến):** Đây là tác vụ phân tích ngoại tuyến (offline, back-office). Việc tóm tắt cuộc gọi hủy chuyến có thể thực hiện theo lô (batch processing) vào cuối tuần hoặc cuối ngày, không mang tính khẩn cấp cao như xử lý sự cố trực tiếp trên đường của tài xế.

---

## 🏗️ 2. Phase 3 — DEEP-DIVE

### 3.1. Current-State Workflow Mapping
Quy trình thủ công hiện tại khi tài xế báo sự cố hết pin giữa đường:

```text
┌──────────────┐     ┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│ Bước 1       │     │ Bước 2       │     │ Bước 3       │     │ Bước 4       │
│ Nhận cuộc    │     │ Tra cứu định │     │ Tra cứu trạm │     │ Soạn văn bản │
│ gọi sự cố    │ ──→ │ vị GPS xe   │ ──→ │ sạc VinFast  │ ──→ │ hướng dẫn    │
│              │     │              │     │ còn trụ trống│     │ gửi tài xế   │
│ Ai: Dispatch │     │ Ai: Dispatch │     │ Ai: Dispatch │     │ Ai: Dispatch │
│ ⏱ 2 phút     │     │ ⏱ 2 phút     │     │ ⏱ 5 phút 🔴  │     │ ⏱ 5 phút 🔴  │
│ In: Điện thoại│     │ In: Biển số  │     │ In: Vị trí GPS│     │ In: Raw data │
│ Out: Log sự cố│     │ Out: Toạ độ  │     │ Out: Địa chỉ │     │ Out: SMS     │
└──────────────┘     └──────────────┘     └──────────────┘     └──────────────┘
                                                                       │
                                                                       ▼
                                                                ┌──────────────┐
                                                                │ Bước 5       │
                                                                │ Gọi xe cứu   │
                                                                │ hộ (nếu cần) │
                                                                │ Ai: Dispatch │
                                                                │ ⏱ 1 phút     │
                                                                └──────────────┘
🔴 = Bottlenecks
⏱ Tổng thời gian xử lý thủ công trung bình: 15 phút/lượt.
```

### 3.2. Problem Statement (6-field) — Vin Smart Future Standard

| Field | Nội dung chi tiết |
|---|---|
| **1. Actor / Operator** | Điều phối viên (Dispatcher) thuộc Trung tâm Điều vận Xanh SM. |
| **2. Current Workflow** | Khi tài xế báo hết pin, điều phối viên tra cứu vị trí định vị trên bản đồ nội bộ, mở Dashboard trạm sạc VinFast để tìm trụ sạc trống gần nhất, viết tin nhắn chỉ dẫn/định vị gửi qua App tài xế, và gọi cứu hộ nếu pin dưới 5%. 5 bước, hoàn toàn thủ công, mất 15 phút/lượt. |
| **3. Bottleneck** | Bước 3 & 4 (mất 10 phút): Tra cứu thủ công trụ sạc trống phù hợp với dòng xe (VF5/VFe34/VF8) và soạn thảo tin nhắn hướng dẫn đường đi chi tiết bằng Tiếng Việt thân thiện. |
| **4. Business Impact** | Mỗi ngày có ~80 sự cố pin thực địa tại Hà Nội. Gây lãng phí 20 giờ làm việc/ngày của team điều vận. Tăng thời gian chờ đợi của tài xế, dẫn đến rò rỉ doanh thu ~15% do xe không thể đón khách và tài xế bị stress. |
| **5. Success Metric** | 1. Giảm tổng thời gian xử lý sự cố từ 15 phút xuống dưới 3 phút (Efficiency).<br>2. Tỉ lệ hướng dẫn đúng địa điểm và đúng loại trụ sạc phù hợp đạt 98% (Quality). |
| **6. Operational Boundary** | AI được phép truy xuất API định vị xe, API trạm sạc VinFast trống, tự động soạn thảo tin nhắn hướng dẫn dạng nháp (draft). **CẤM:** AI không được tự động gửi tin đi mà không có điều phối viên phê duyệt (Bắt buộc Human-in-the-loop); không được đề xuất trạm sạc cách xe > 5km nếu lượng pin hiện tại dưới 5%. |

### 3.3. Future-State Flow & AI Fit
*   **AI Fit Classification:** Chọn **LLM Feature**. Quy trình có cấu trúc cố định và các API đầu vào rõ ràng, chỉ cần LLM để tổng hợp thông tin, đưa ra quyết định dựa trên quy tắc an toàn và soạn thảo tin nhắn. Không cần Agent tự trị cao để tránh rủi ro hệ thống tự gửi thông tin sai cho tài xế.
*   **Future-State Workflow Diagram:**

```text
┌──────────────┐     ┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│ Bước 1       │     │ Bước 2       │     │ Bước 3       │     │ Bước 4       │
│ Nhận cuộc    │     │ 🔵 Auto-pull │     │ 🔵 AI draft  │     │ 🟢 Dispatch  │
│ gọi sự cố    │ ──→ │ vị trí &     │ ──→ │ SMS chỉ dẫn  │ ──→ │ click duyệt  │
│              │     │ trạm sạc trống│    │ & chỉ đường  │     │ & gửi tài xế │
└──────────────┘     └──────────────┘     └──────────────┘     └──────────────┘
                                                                       │
                                                                       ▼
                                                                ↩️ Fallback:
                                                                Nếu AI draft lỗi,
                                                                Dispatcher tự viết
                                                                tay lại như cũ.
```

*   **Ranh giới an toàn (Operational Boundary) bảo vệ nghiêm ngặt:**
    *   **Human-in-the-loop (HITL):** Mọi tin nhắn do AI soạn thảo phải có tag `[DRAFT_ONLY]` ở đầu để hệ thống bắt buộc giữ ở trạng thái chờ duyệt. Dispatcher phải đọc lại và click "Gửi" thủ công trên giao diện điều phối.
    *   **Battery Safety Rule (Critical Guardrail):** Khi mức pin xe báo `< 5%`, xe không được phép đi quá 5km. AI bắt buộc phải phát hiện điều này và từ chối đề xuất trạm sạc xa. Thay vào đó, AI phải tự động đề xuất kích hoạt **Xe cứu hộ pin di động (Mobile Charging Vehicle)** với cú pháp JSON phản hồi:
        `{"action": "dispatch_mobile_charger", "reason": "<lý_do_chi_tiết>"}`.

---

## 🏁 3. Phase 5 — EVALUATE

### AI Readiness Checklist:
1.  [x] **Dữ liệu:** Chúng tôi có sẵn API thời gian thực định vị xe (GPS) và trạng thái trụ sạc VinFast trống.
2.  [x] **Kiểm soát rủi ro:** Rủi ro khi AI sai nằm trong tầm kiểm soát nhờ cơ chế **HITL** duyệt tin nháp và ranh giới an toàn **Fallback** bằng JSON phản hồi cứu hộ pin.
3.  [x] **Sự sẵn sàng của Con người:** Các điều phối viên đã quen thuộc với việc duyệt tin nhắn mẫu, việc chuyển đổi sang duyệt tin draft do AI viết là cực kỳ tự nhiên.

### Quyết định cuối cùng của Ban Giám Đốc Vin Smart Future:
[x] **GO (Bắt đầu xây dựng Prototype):** Bắt đầu phát triển với scope hẹp (Hà Nội, thử nghiệm trên 10 xe cứu hộ và 100 tài xế).

**Justification (Lý giải quyết định dựa trên bằng chứng kỹ thuật và chi phí):**
1.  **Tính khả thi cao:** Chúng ta đã xây dựng thành công bản mẫu Prompt Prototype trên **Gemini 2.5 Flash** chứng minh khả năng bảo vệ ranh giới cực kỳ tốt trước các cuộc tấn công prompt bypass.
2.  **Chi phí cực kỳ tối ưu:** Gemini 2.5 Flash có chi phí API rất rẻ. Chi phí chạy API cho 80 cuộc gọi hằng ngày chỉ khoảng chưa tới 0.05 USD/ngày.
3.  **Lợi ích kinh tế lớn:** Giảm thời gian xử lý sự cố từ 15 phút xuống 3 phút giúp tiết kiệm hàng trăm giờ làm việc/tháng, tăng hiệu suất hoạt động đội xe Xanh SM lên 15% và cải thiện đáng kể trải nghiệm của tài xế.
