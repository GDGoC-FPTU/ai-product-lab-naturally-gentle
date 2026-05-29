# 🔍 Phase 1 — SCAN & Phase 2 — QUICK-ASSESS: AI Product Scoping (Vin Smart Future)

Tài liệu này thể hiện tư duy quét bài toán cá nhân và đánh giá nhanh cơ hội áp dụng AI tại các công ty thành viên Vingroup.

---

## 🔍 Phase 1 — SCAN: Bảng quét cơ hội vận hành

Dưới đây là danh sách 6 bài toán/nút thắt cổ chai được quét qua hoạt động vận hành của các công ty thành viên Vingroup bằng cách áp dụng **4 Lenses**:

| # | Subsidiary | Lens | Mô tả ngắn bài toán |
|---|------------|------|---------------------|
| 1 | **Xanh SM** | Lặp lại | So khớp và phân bổ lại cuốc xe khi khách hàng yêu cầu thay đổi điểm đến giữa chừng. |
| 2 | **Xanh SM** | Tốn thời gian | Điều phối viên xử lý thủ công các phản hồi khẩn cấp từ tài xế về sự cố sạc pin hoặc va chạm thực địa (mất 15-20 phút/lượt). |
| 3 | **VinFast** | Lặp lại | So khớp hóa đơn sạc điện và đối chiếu số liệu trạm sạc đối tác hằng tuần. |
| 4 | **Vinhomes** | AI-upgrade | Hệ thống phân loại và route tự động các phản hồi/khiếu nại của cư dân trên App Vinhomes Resident (CSKH phản hồi rập khuôn, mất 12 tiếng). |
| 5 | **Vinmec** | Pain từ người khác | Bác sĩ mất quá nhiều thời gian viết tóm tắt hồ sơ xuất viện (mất 20-30 phút/bệnh nhân, bác sĩ phàn nàn vì quá tải). |
| 6 | **Xanh SM** | Tốn thời gian | Tóm tắt lý do khách hàng hủy chuyến từ cuộc gọi ghi âm và ghi chú của tài xế để tìm pattern lỗi hệ thống. |

---

## 🃏 Phase 2 — QUICK-ASSESS: 3 Quick Problem Cards

Tôi đã chọn ra 3 bài toán tiềm năng nhất từ danh sách trên để tiến hành đánh giá sơ bộ:

### 1. QUICK PROBLEM CARD #1: Xanh SM Xử lý sự cố sạc pin thực địa

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #1                                       │
│                                                             │
│ Bài toán: Tài xế Xanh SM báo cáo sự cố sạc pin / hết pin    │
│ giữa đường cần điều phối cứu hộ hoặc trạm sạc gần nhất.     │
│ Công ty thành viên: [x] Xanh SM (GSM)                       │
│                                                             │
│ Ai đang đau? Tài xế (chờ đợi), Điều phối viên (quá tải)     │
│                                                             │
│ Workflow thủ công hiện tại (5 bước):                        │
│   1. Tài xế gọi tổng đài điều vận báo hết pin               │
│   → 2. Điều phối viên tra cứu thủ công vị trí xe trên bản đồ│
│   → 3. Tra cứu thủ công các trạm sạc VinFast còn trụ trống   │
│   → 4. Viết tin nhắn chỉ dẫn/đường đi gửi qua App tài xế    │
│   → 5. Liên hệ đội xe cứu hộ nếu xe đã cạn kiệt pin         │
│                                                             │
│ Bước nào tốn nhất? Bước 3-4 (⏱ 12 phút/lượt)                │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 3-4              │
│ (Tự động hóa lấy vị trí -> Tra cứu trạm trống -> Draft tin) │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                        │
│ Giảm thời gian xử lý sự cố từ 15 phút ──> dưới 3 phút.      │
│                                                             │
│ Quick Architecture: [x] LLM Feature (Tự động soạn chỉ dẫn)   │
└─────────────────────────────────────────────────────────────┘
```

### 2. QUICK PROBLEM CARD #2: Vinhomes CSKH Resident App Classification

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #2                                       │
│                                                             │
│ Bài toán: Phân loại và định tuyến tự động khiếu nại của cư   │
│ dân gửi qua ứng dụng Vinhomes Resident đến đúng ban quản lý.│
│ Công ty thành viên: [x] Vinhomes                            │
│                                                             │
│ Ai đang đau? Cư dân (chờ lâu), Nhân viên CSKH (quá tải lọc) │
│                                                             │
│ Workflow thủ công hiện tại (4 bước):                        │
│   1. Cư dân gửi khiếu nại (bằng text/ảnh) lên App           │
│   → 2. Nhân viên CSKH đọc thủ công và phân loại chủ đề      │
│   → 3. Chuyển tiếp (route) thủ công phiếu sự cố đến BQL tòa   │
│   → 4. BQL phản hồi lại cư dân theo mẫu có sẵn              │
│                                                             │
│ Bước nào tốn nhất? Bước 2-3 (⏱ 15 phút/lượt đọc và phân loại)│
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2-3              │
│ (LLM đọc nội dung phản ánh -> Tự phân loại và gán tag BQL)  │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                        │
│ Giảm thời gian route khiếu nại từ 12 giờ xuống dưới 10 phút. │
│ Tỉ lệ phân loại chính xác đạt trên 95%.                     │
│                                                             │
│ Quick Architecture: [x] LLM Feature                         │
└─────────────────────────────────────────────────────────────┘
```

### 3. QUICK PROBLEM CARD #3: Xanh SM Tóm tắt lý do khách hàng hủy chuyến

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #3                                       │
│                                                             │
│ Bài toán: Tóm tắt lý do khách hàng hủy chuyến từ cuộc gọi   │
│ ghi âm tổng đài và lịch sử chat để cải thiện chất lượng dịch │
│ vụ.                                                         │
│ Công ty thành viên: [x] Xanh SM (GSM)                       │
│                                                             │
│ Ai đang đau? Đội ngũ quản lý chất lượng QA/QC (đọc mỏi mắt) │
│                                                             │
│ Workflow thủ công hiện tại (4 bước):                        │
│   1. Hệ thống lọc danh sách các chuyến xe bị hủy            │
│   → 2. Nhân viên QA nghe lại file ghi âm cuộc gọi / lịch chat│
│   → 3. Điền lý do chi tiết vào bảng báo cáo Excel thủ công   │
│   → 4. Tổng hợp pattern lỗi hệ thống định kỳ hàng tuần       │
│                                                             │
│ Bước nào tốn nhất? Bước 2-3 (⏱ 8-10 phút/cuốc xe bị hủy)     │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2-3              │
│ (Speech-to-Text cuộc gọi -> LLM tóm tắt lý do & tag phân loại)│
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                        │
│ Phân tích tự động 100% cuốc xe bị hủy ngay khi kết thúc.    │
│ Giảm thời gian tổng hợp báo cáo tuần từ 2 ngày xuống 5 phút.│
│                                                             │
│ Quick Architecture: [x] LLM Feature (Speech-to-Text + Summary)│
└─────────────────────────────────────────────────────────────┘
```
