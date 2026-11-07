# Lab 02 — Worksheet: AI Product Scoping (Vin Smart Future)

* **Họ và tên:** Lê Sông Thao
* **Mã số học viên:** 2A202603534
* **Lớp:** L3A-H209
* **Email:** sirothao123@gmail.com

---

# Phase 1 — SCAN (Cá nhân, 20 min)

### List bài toán của tôi:
| # | Subsidiary (VinFast/Xanh SM...) | Lens | Mô tả ngắn bài toán |
|---|----------------------------------|------|---------------------|
| 1 | **Vinhomes** | Lặp lại | Phân loại và điều phối xử lý các phản ánh sự cố kỹ thuật tòa nhà (điện, nước, thang máy, điều hòa, PCCC) do cư dân gửi qua App Vinhomes Resident đến đúng tổ kỹ thuật của Ban Quản lý (mất ~45 phút/ticket). |
| 2 | **Xanh SM** | Tốn thời gian | Điều phối viên xử lý thủ công các cuộc gọi báo sự cố pin yếu hoặc trụ sạc lỗi từ tài xế taxi điện ngoài thực địa, phải tra cứu bản đồ trụ sạc trống và nhắn tin chỉ đường hoặc gọi xe cứu hộ pin (mất ~15 phút/lượt). |
| 3 | **VinFast** | AI có thể tốt hơn | Cố vấn dịch vụ tại xưởng tiếp nhận mô tả lỗi xe bằng tiếng Việt đời thường của khách hàng (ví dụ: tiếng kêu gầm xe, báo lỗi màn hình) để tra cứu mã lỗi kỹ thuật sơ bộ và phân luồng khoang sửa chữa (mất 20-25 phút/xe). |
| 4 | **Vinmec** | Pain từ người khác | Bác sĩ nội trú tốn nhiều thời gian tổng hợp kết quả xét nghiệm, chẩn đoán hình ảnh và đơn thuốc từ bệnh án điện tử để viết tóm tắt hồ sơ xuất viện (Discharge Summary) bằng ngôn ngữ dễ hiểu cho bệnh nhân (mất 25-30 phút/ca). |
| 5 | **Vinpearl** | Pain từ người khác | Nhân viên quản lý chất lượng phải đọc thủ công hàng nghìn đánh giá đa ngôn ngữ trên Booking, Agoda, Google Maps để lọc ra các phàn nàn vận hành khẩn cấp tại khu nghỉ dưỡng (độ trễ phát hiện 24-48 giờ). |
| 6 | **VinUni** | Lặp lại | Giảng viên và trợ giảng đọc log lỗi code của sinh viên trong các bài lab lập trình để giải thích nguyên nhân sai logic và gợi ý hướng sửa bài (mất 10-15 phút/bài nộp). |

---

# Phase 2 — QUICK-ASSESS (Cá nhân, 30 min)

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #1                                       │
│                                                             │
│ Bài toán (1 câu): Phân loại chuyên môn, mức độ khẩn cấp và  │
│ soạn lệnh điều phối kỹ thuật từ phản ánh của cư dân trên    │
│ App Vinhomes Resident.                                      │
│ Công ty thành viên: [ ] VinFast  [ ] Xanh SM  [x] Vinhomes  │
│                     [ ] Vinmec   [ ] Khác (Ghi rõ)________  │
│                                                             │
│ Ai đang đau (Actor)? Nhân viên CSKH trực tổng đài BQL,      │
│ Điều phối viên kỹ thuật tòa nhà, và Cư dân chờ xử lý sự cố. │
│                                                             │
│ Workflow thủ công hiện tại (5 bước):                        │
│   1. Cư dân gửi phản ánh văn bản tự do trên App (2p)        │
│   ──> 2. CSKH đọc ticket, gọi lại xác minh vị trí/lỗi (15p) │
│   ──> 3. Tra lịch ca trực, gán cho tổ kỹ thuật (10p)        │
│   ──> 4. Soạn tin nhắn chỉ dẫn & phiếu giao việc (8p)       │
│   ──> 5. Kỹ sư tòa nhà nhận việc và xuống căn hộ (10p)      │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 2, 3 & 4 (⏱ 33 phút)  │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2, 3 & 4         │
│ (Bóc tách vị trí/lỗi -> Phân loại tổ kỹ thuật -> Draft tin) │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                       │
│   Giảm thời gian phân loại & soạn điều phối từ 33 min       │
│   ──> dưới 2 min/ticket; độ chính xác phân luồng >= 95%.    │
│                                                             │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM  [ ] Agent │
└─────────────────────────────────────────────────────────────┘
```

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #2                                       │
│                                                             │
│ Bài toán (1 câu): Hỗ trợ điều phối viên Xanh SM tra cứu     │
│ trạm sạc VinFast trống hoặc gọi xe cứu hộ pin di động khi   │
│ tài xế báo pin yếu giữa đường.                              │
│ Công ty thành viên: [ ] VinFast  [x] Xanh SM  [ ] Vinhomes  │
│                     [ ] Vinmec   [ ] Khác (Ghi rõ)________  │
│                                                             │
│ Ai đang đau (Actor)? Tài xế taxi điện (chờ đợi, mất cuốc),  │
│ Điều phối viên trung tâm điều vận (quá tải giờ cao điểm).   │
│                                                             │
│ Workflow thủ công hiện tại (5 bước):                        │
│   1. Tài xế gọi tổng đài báo xe sắp hết pin (2p)            │
│   ──> 2. Điều phối viên tra cứu tọa độ GPS xe (2p)          │
│   ──> 3. Tìm trạm sạc VinFast còn trụ trống phù hợp (5p)    │
│   ──> 4. Soạn tin nhắn chỉ đường gửi qua App tài xế (5p)    │
│   ──> 5. Gọi xe cứu hộ pin nếu pin dưới ngưỡng 5% (1p)      │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 3 & 4 (⏱ 10 phút/lượt)│
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 3, 4 & 5         │
│ (Đối chiếu % pin & khoảng cách -> Draft tin / Gọi cứu hộ)   │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                       │
│   Giảm thời gian xử lý sự cố từ 15 min ──> dưới 3 min/lượt; │
│   0% trường hợp chỉ dẫn xe pin < 5% đi trạm xa quá 5km.     │
│                                                             │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM  [ ] Agent │
└─────────────────────────────────────────────────────────────┘
```

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #3                                       │
│                                                             │
│ Bài toán (1 câu): Tổng hợp dữ liệu lâm sàng từ bệnh án điện │
│ tử (EMR) để soạn bản nháp tóm tắt hồ sơ xuất viện và hướng  │
│ dẫn chăm sóc tại nhà cho bệnh nhân Vinmec.                  │
│ Công ty thành viên: [ ] VinFast  [ ] Xanh SM  [ ] Vinhomes  │
│                     [x] Vinmec   [ ] Khác (Ghi rõ)________  │
│                                                             │
│ Ai đang đau (Actor)? Bác sĩ nội trú (quá tải thủ tục hành   │
│ chính), Bệnh nhân (chờ giấy ra viện lâu, khó hiểu thuật ngữ)│
│                                                             │
│ Workflow thủ công hiện tại (4 bước):                        │
│   1. Bác sĩ mở HIS/EMR đọc lại diễn tiến điều trị (8p)      │
│   ──> 2. Copy kết quả xét nghiệm và đơn thuốc ra viện (7p)  │
│   ──> 3. Gõ tay phần tóm tắt bệnh sử & dặn dò (12p)         │
│   ──> 4. Bác sĩ điều trị kiểm tra, ký duyệt và in (3p)      │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 1, 2 & 3 (⏱ 27 phút)  │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 1, 2 & 3         │
│ (Trích xuất EMR -> Soạn bản nháp [DRAFT_ONLY] chờ bác sĩ ký)│
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                       │
│   Giảm thời gian bác sĩ soạn hồ sơ từ 30 min ──> dưới 5 min;│
│   100% hồ sơ được bác sĩ kiểm tra và ký duyệt trước khi in. │
│                                                             │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM  [ ] Agent │
└─────────────────────────────────────────────────────────────┘
```

---

# Phase 3 — DEEP-DIVE (Nhóm, 85 min)

## 3.1. Current-State Workflow Mapping (25 min)
* 🔴 **Bottleneck:** Bước 2 (Đọc ticket & gọi xác minh — 15 phút), Bước 3 (Phân loại & tra cứu ca trực — 10 phút), Bước 4 (Soạn tin nhắn chỉ dẫn & điều phối kỹ sư — 8 phút).
* 🔄 **Handoff:** 4 điểm chuyển giao thông tin (Cư dân/Tài xế -> CSKH BQL -> Điều phối viên -> Tổ trưởng kỹ thuật -> Kỹ sư hiện trường).
* Thời gian vận hành trung bình: **Tổng cộng = 45 phút/lượt** (Chi tiết tại `04-workflow-diagram.png` và `02-deep-dive-report.md`).

## 3.2. Problem Statement (6-field) & Metrics (15 min)

| Field | Nội dung chi tiết |
|---|---|
| **1. Actor / Operator** | Nhân viên CSKH và Điều phối viên kỹ thuật (Dispatcher) thuộc Trung tâm Vận hành Vinhomes & đội xe dịch vụ nội khu. |
| **2. Current Workflow** | Khi cư dân hoặc tài xế xe dịch vụ nội khu gửi phản ánh sự cố kỹ thuật/năng lượng qua App, nhân viên phải đọc từng ticket văn bản tự do, gọi điện xác minh lại vị trí/tình trạng, tra bảng phân ca trên Excel/Dashboard để tìm tổ kỹ thuật hoặc trạm sạc trống gần nhất, tự gõ tin nhắn hướng dẫn tạm thời và gọi đội cứu hộ nếu sự cố vượt ngưỡng an toàn. Quy trình gồm 5 bước, 4 handoffs, mất trung bình 45 phút/lượt. |
| **3. Bottleneck** | Bước 2, 3 và 4 (chiếm 33/45 phút): Đọc hiểu mô tả tiếng Việt tự do (nhiều từ viết tắt, không dấu), đối chiếu điều kiện an toàn kỹ thuật (như mức pin phương tiện/thiết bị dưới 5%, rò rỉ nước vào ổ điện) và gõ tay từng tin nhắn phản hồi trong khung giờ cao điểm (18:00 - 22:00). |
| **4. Business Impact** | Với ~1,200 ticket/ngày trên toàn hệ thống đại đô thị, khâu xử lý thủ công tiêu tốn khoảng 660 giờ công/ngày. Thời gian phản hồi trung bình 45 phút vượt ngưỡng SLA mục tiêu (15 phút), đồng thời tỷ lệ gán nhầm bộ phận (~15%) hoặc chỉ dẫn sai lộ trình khi thiết bị/xe điện cạn pin (< 5%) gây rủi ro gián đoạn vận hành. |
| **5. Success Metric** | 1. Giảm thời gian phân loại và soạn lệnh điều phối (Bước 2-4) từ 33 phút xuống dưới 2 phút/ticket (tổng thời gian xử lý còn dưới 12 phút).<br>2. Tỷ lệ phân loại đúng tổ kỹ thuật và địa điểm hỗ trợ đạt tối thiểu 95%.<br>3. 100% trường hợp chạm ngưỡng nguy kịch (pin < 5% hoặc sự cố khẩn cấp P0) được kích hoạt lệnh điều phối cứu hộ chính xác. |
| **6. Operational Boundary** | **Được phép:** Đọc nội dung phản ánh, phân loại chuyên môn kỹ thuật, tra cứu trạm sạc/tổ kỹ thuật gần nhất và soạn bản nháp tin nhắn hướng dẫn.<br>**TUYỆT ĐỐI CẤM:**<br>1. Không được tự động gửi trực tiếp tin nhắn cho cư dân/tài xế khi chưa có điều phối viên duyệt (mọi tin nhắn nháp bắt buộc phải mở đầu bằng thẻ `[DRAFT_ONLY]`).<br>2. Khi phát hiện mức pin dưới ngưỡng nguy kịch (< 5%), tuyệt đối không được đề xuất hoặc chỉ đường tới trạm sạc cách xa quá 5km, mà bắt buộc phải xuất lệnh JSON điều xe cứu hộ lưu động: `{"action": "dispatch_mobile_charger", "reason": "..."}`. |

## 3.3. Future-State Flow & AI Fit (25 min)
* **Xác định mức AI Fit (AI-Fit Matrix):** [ ] Rule / State-Machine  [x] LLM Feature  [ ] Agentic Loop.
* **Future-State Flow:**
  * 🔵 **AI Step (Bước 2 & 3):** Mô hình `gemini-2.5-flash` bóc tách vị trí, hạng mục kỹ thuật, mức độ ưu tiên; kiểm tra ngưỡng an toàn (`< 5%` pin) để xuất lệnh JSON cứu hộ hoặc tạo bản nháp `[DRAFT_ONLY]`.
  * 🟢 **Human Step (HITL - Bước 4):** Điều phối viên kiểm tra bản nháp `[DRAFT_ONLY]` hoặc lệnh điều phối trên màn hình và bấm nút phê duyệt gửi đi.
  * ↩️ **Fallback:** Nếu API lỗi, quá thời gian chờ (>5s) hoặc độ tự tin thấp, hệ thống tự động đẩy ticket về hàng đợi tiêu chuẩn để điều phối viên xử lý thủ công.

---

# Phase 4 — TECHNICAL PROMPT PROTOTYPE (Nhóm, 30 min)

Đã hoàn thiện tại file [`starter-code/prompt_prototype.py`](starter-code/prompt_prototype.py) và vượt qua toàn bộ 3 kịch bản kiểm thử tấn công ranh giới (`Rule 1 Passed` và `Rule 2 Passed`).

---

# Phase 5 — EVALUATE (Nhóm, 20 min)

### AI Readiness Checklist:
1. [x] Chúng tôi có sẵn dữ liệu mẫu/logs sạch để test?
2. [x] Rủi ro khi AI sai có nằm trong tầm kiểm soát (qua HITL hoặc Fallback)?
3. [x] Stakeholders sẵn sàng thay đổi quy trình làm việc cũ?

### Quyết định cuối cùng của Ban Giám Đốc Vin Smart Future:
[x] **GO (Bắt đầu xây dựng Prototype):** Bắt đầu phát triển với scope hẹp.
[ ] **NOT YET (Cần tích lũy thêm dữ liệu/xác lập baseline):** Trì hoãn để chuẩn bị thêm.
[ ] **NO-GO (Không khả thi / Rule-based tốt hơn):** Hủy bỏ dự án AI này.

**Justification (Lý giải quyết định dựa trên bằng chứng kỹ thuật và chi phí):**
> Dự án đạt điều kiện **GO** để triển khai thử nghiệm (Pilot) tại một phân khu của Vinhomes Smart City vì hai cơ sở rõ ràng:
> 1. **Về kỹ thuật & an toàn:** Bản mẫu `prompt_prototype.py` chạy trên `gemini-2.5-flash` với `temperature=0.0` đã vượt qua cả 3 kịch bản tấn công prompt injection, đảm bảo không tự ý gửi tin khi thiếu nhãn `[DRAFT_ONLY]` và luôn kích hoạt `dispatch_mobile_charger` khi pin dưới ngưỡng 5%.
> 2. **Về chi phí (Unit Economics):** Mỗi lượt phân loại và soạn nháp tốn khoảng 450 tokens (input + output). Với đơn giá của `gemini-2.5-flash`, chi phí API cho 1,200 ticket/ngày (36,000 ticket/tháng) chỉ tốn khoảng 2.5 USD/tháng (~65,000 VNĐ/tháng), rất nhỏ so với hàng trăm giờ công điều phối thủ công tiết kiệm được mỗi tháng.

---

# Phase 6 — REFLECTION (Cá nhân)
*Chi tiết tại file [`03-ai-log.md`](03-ai-log.md).*
