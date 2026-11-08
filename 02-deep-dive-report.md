# 📄 02 — Báo cáo Phân tích Chuyên sâu (Deep-Dive Report)
## Dự án: Hệ thống Giám sát Gian lận Cuốc xe Thời gian thực & Tự động Đối soát Hoàn tiền (Smart Dispute & GPS Divergence Copilot)
**Đơn vị phát triển:** Vin Smart Future (Vingroup)  
**Khối kinh doanh phối hợp:** Công ty Cổ phần Di chuyển Xanh và Thông minh (GSM — Xanh SM)  
**Tác giả thực hiện:** Kỹ sư AI Product (Nhánh: `lyslama`)  

---

## 🏛️ 1. Bối cảnh & Tuyên bố Bài toán

Tại Khối Vận hành của **Xanh SM (GSM)**, trải nghiệm khách hàng và tính minh bạch là hai trụ cột cốt lõi để xây dựng hình ảnh thương hiệu taxi điện văn minh. Tuy nhiên, qua phân tích thực tế từ các kênh mạng xã hội (Otofun, Reddit r/Vietnam) và dữ liệu khiếu nại nội bộ, một điểm nóng gây bức xúc nghiêm trọng nhất hiện nay là tình trạng tài xế:
* **"Ngâm cuốc":** Tài xế nhận cuốc nhưng không di chuyển đón khách, đứng im hoặc cố tình đi hướng ngược lại nhằm gây áp lực để khách hàng tự hủy cuốc (tránh bị phạt điểm tỷ lệ chấp nhận và giữ thưởng ngày 200k - 300k).
* **"Hoàn thành khống":** Tài xế bấm "Đã đón khách" và "Hoàn thành chuyến" khống dù không hề có khách trên xe để nhận tiền từ thẻ/ví điện tử của khách đã liên kết.

Khách hàng bị trừ tiền oan, phải đợi từ **2 đến 5 ngày làm việc** để nhân viên CSKH đối soát định vị GPS thủ công bằng mắt. Dự án này được thiết kế nhằm xây dựng giải pháp AI kết hợp Rule-based Telemetry Engine để tự động hóa quy trình đối soát và hoàn tiền trong vòng dưới 30 giây.

---

# 🏗️ Phase 3 — DEEP-DIVE: Phân tích Quy trình Vận hành

## 3.1. Sơ đồ Quy trình Hiện tại (Current-State Workflow)

Quy trình xử lý khiếu nại cuốc xe ngâm / hoàn thành khống hiện tại giữa khách hàng, CSKH, đội Vận hành tài xế và Kế toán:

```text
┌─────────────────┐       ┌─────────────────┐       ┌─────────────────┐
│ Bước 1          │       │ Bước 2          │       │ Bước 3          │
│ Khách gửi       │       │ CSKH tiếp nhận  │       │ Trích xuất log  │
│ khiếu nại qua   │ ──(🔄)─>│ thông tin, tạo  │ ──(🔄)─>│ GPS xe taxi và  │
│ App / Tổng đài  │       │ ticket hỗ trợ   │       │ GPS điện thoại  │
│                 │       │                 │       │                 │
│ Actor: Khách    │       │ Actor: CSKH     │       │ Actor: KTV Data │
│ ⏱ 3 phút        │       │ ⏱ 5 phút        │       │ ⏱ 10 phút 🔴    │
│ In: App form/Call│      │ In: SĐT, Mã trip│       │ In: Mã chuyến đi│
│ Out: Ticket ID  │       │ Out: Jira/Zendesk│      │ Out: Raw GPS log│
└─────────────────┘       └─────────────────┘       └─────────────────┘
                                                             │
                                                             ▼
┌─────────────────┐       ┌─────────────────┐       ┌─────────────────┐
│ Bước 6          │       │ Bước 5          │       │ Bước 4          │
│ Gửi thông báo   │       │ Kế toán duyệt   │       │ Chuyên viên mở  │
│ xin lỗi & kích  │ <─(🔄)─│ lệnh hoàn tiền  │ <─(🔄)─│ bản đồ đối soát │
│ hoạt hoàn tiền  │       │ qua cổng Payment│       │ thủ công bằng mắt│
│                 │       │                 │       │                 │
│ Actor: App CSKH │       │ Actor: Kế toán  │       │ Actor: QA/Ops   │
│ ⏱ 2 phút        │       │ ⏱ 5 phút        │       │ ⏱ 15 phút 🔴    │
│ In: Lệnh duyệt  │       │ In: Biên bản    │       │ In: GPS 2 nguồn │
│ Out: Tiền ví/thẻ│       │ Out: Refund API │       │ Out: Kết luận   │
└─────────────────┘       └─────────────────┘       └─────────────────┘

* Ký hiệu:
  🔴 = Bottleneck (Điểm nghẽn gây tốn nhiều thời gian và dễ sai sót nhất).
  🔄 = Handoff (Điểm bàn giao công việc giữa các phòng ban: Khách ➔ CSKH ➔ Ops ➔ Finance).
* Tổng thời gian thao tác thủ công tích lũy: ~40 phút/ticket.
* Tổng thời gian khách hàng phải chờ đợi giải quyết (SLA thực tế): 48 – 120 giờ (2 đến 5 ngày làm việc).
```

---

## 3.2. Bản đặc tả Bài toán (Problem Statement 6-Field) — Chuẩn Vin Smart Future

| Trường thông tin (Field) | Nội dung chi tiết |
|---|---|
| **1. Actor / Operator** | * Nhân viên Chăm sóc khách hàng (CSKH Xanh SM Tier 1 & Tier 2).<br>* Chuyên viên Giám sát vận hành & Thanh tra tài xế (Driver Ops QA).<br>* Khách hàng sử dụng dịch vụ taxi Xanh SM liên kết thẻ/ví điện tử. |
| **2. Current Workflow** | Khi phát sinh tranh chấp cuốc xe ngâm/hoàn thành khống, CSKH tiếp nhận thông tin, tạo ticket chuyển tiếp sang phòng Vận hành. Kỹ thuật viên trích xuất tọa độ GPS của xe từ IoT Telemetry và vị trí app của khách, sau đó mở phần mềm bản đồ để soi mắt từng mốc thời gian: xe cách khách bao xa khi ấn nút "Đã đón"? Xe có dừng đón không hay chạy thẳng? Sau khi xác định gian lận, chuyển biên bản sang Kế toán duyệt lệnh hoàn tiền thủ công. |
| **3. Bottleneck** | **Bước 3 & Bước 4 (mất 25 phút/ticket):** Trích xuất dữ liệu đa nguồn và đối soát thị giác bằng mắt. Do lượng cuốc cao điểm tại Hà Nội/TP.HCM lên tới hàng trăm nghìn cuốc/ngày, ticket dồn ứ hàng nghìn ca mỗi tuần, dẫn đến thời gian phản hồi bị kéo dài tới 2–5 ngày. |
| **4. Business Impact** | * **Tổn thất Churn Rate:** Khách hàng bị mất tiền oan cảm thấy bị lừa dối, hủy liên kết thẻ hoặc xóa app Xanh SM (rò rỉ thị phần sang Grab/Be).<br>* **Chi phí vận hành:** Mỗi ca tốn 40 phút nhân sự qua 3 phòng ban. Với ~3.000 ca khiếu nại/tháng, GSM tiêu tốn ~2.000 giờ công lao động chất lượng cao.<br>* **Xung đột nội bộ:** Tài xế bị trừ tiền/khóa app oan trong các ca định vị GPS chập chờn dưới hầm hoặc nhà cao tầng mà không được giải thích minh bạch. |
| **5. Success Metric** | 1. **Thời gian hoàn tiền (SLA):** Rút ngắn từ **72 giờ thủ công xuống dưới 30 giây** đối với các ca gian lận rõ ràng (độ tin cậy > 90%).<br>2. **Độ chính xác đối soát (Precision):** Đạt trên **98.5%** tính đúng của quyết định hoàn tiền tự động.<br>3. **Năng suất vận hành (Efficiency):** Giảm **85%** số lượng ticket phải xử lý thủ công bằng mắt. |
| **6. Operational Boundary (Ranh giới vận hành)** | * **AI ĐƯỢC PHÉP:** Tự động đọc nội dung khiếu nại; tự động truy xuất API GPS của xe và app khách; tính toán khoảng cách vector và độ lệch không-thời gian (Spatial-Temporal Divergence); tự động duyệt hoàn tiền cho các cuốc có giá trị **≤ 150.000 VNĐ** khi điểm bất thường (Anomaly Score) ≥ 0.90.<br>* **AI TUYỆT ĐỐI CẤM:** Không được tự ý hoàn tiền với cuốc > 150.000 VNĐ mà không có phê duyệt của con người; không được tự động khóa tài khoản/phạt trừ tiền tài xế (chỉ được gắn cờ nghi vấn và gửi đề xuất cho thanh tra con người thẩm định); không được tự ý từ chối khiếu nại của khách mà không qua bước Reviewer. |

---

## 3.3. Quy trình Tương lai (Future-State Flow) & Ma trận Lựa chọn AI (AI Fit)

### 📊 Đánh giá Ma trận Công nghệ (AI Fit Matrix):
* **Rule-based thông thường:** *Không đủ* — Vì ngôn ngữ khiếu nại của khách rất phong phú, mang tính cảm xúc, cần hiểu ngữ cảnh ("tài xế đứng im đối diện cổng mà không qua", "bảo tôi hủy rồi chạy mất"). Ngoài ra, sai số định vị GPS tại đô thị cao tầng (Multipath error) cần lọc nhiễu thông minh.
* **Autonomous Multi-Agent Loop:** *Không phù hợp* — Hệ thống tự trị nhiều vòng lặp có chi phí tính toán (Token cost) cao, độ trễ lớn (Latency > 15s) và tiềm ẩn nguy cơ "ảo giác" tự ý xuất quỹ hoàn tiền hàng loạt nếu bị tấn công prompt injection.
* **Lựa chọn tối ưu: Hybrid Architecture (LLM Classifier + Rule-based Spatial Engine):**
  * LLM đóng vai trò phân tích cảm xúc & trích xuất thực thể khiếu nại (Intent & Entity Extraction).
  * Rule-based Telemetry Engine tính toán toán học chính xác khoảng cách Euclid/Haversine giữa xe và khách tại thời điểm click nút.

### 🔄 Sơ đồ Quy trình Vận hành Tương lai (Future-State Workflow):

```text
┌─────────────────┐       ┌─────────────────┐       ┌─────────────────┐
│ Khách gửi       │       │ 🔵 AI NLP Step: │       │ 🔵 Spatial Check:│
│ khiếu nại trên  │ ────> │ Phân loại ý định│ ────> │ Tính toán khoảng│
│ App Xanh SM     │       │ & trích xuất lỗi│       │ cách GPS xe-khách│
│ (Text/Voice)    │       │ (Gemini 2.5)    │       │ (Spatial Engine)│
└─────────────────┘       └─────────────────┘       └─────────────────┘
                                                             │
                                                             ▼
                                                    [Anomaly Score > 0.90
                                                     & Tiền <= 150.000đ?]
                                                    /                   \
                                           (YES)   /                     \  (NO / Nghi vấn)
                                                  ▼                       ▼
                                       ┌─────────────────┐      ┌─────────────────┐
                                       │ 🔵 Auto-Refund: │      │ 🟢 HITL Step:   │
                                       │ Kích hoạt hoàn  │      │ Chuyển ticket   │
                                       │ tiền tức thì    │      │ kèm bảng tóm tắt│
                                       │ & gửi SMS cảm ơn│      │ AI cho CSKH xem │
                                       │ (Thời gian: 5s) │      │ (CSKH bấm duyệt)│
                                       └─────────────────┘      └─────────────────┘
                                                                         │
                                                                         ▼
                                                                ↩️ Fallback Plan:
                                                                Nếu GPS mất tín hiệu
                                                                hoặc LLM Timeout,
                                                                chuyển luồng ticket
                                                                thủ công truyền thống.
```

* **🔵 Bước AI (AI Step):**
  1. *NLP Intent Parser:* Phân loại khiếu nại thuộc nhóm "Tài xế ngâm không đón" hay "Hoàn thành khống" hay "Khiếu nại lộ trình".
  2. *GPS Divergence Scoring:* Đo khoảng cách tại thời điểm tài xế bấm "Đã đón": Nếu khoảng cách giữa xe và khách > 300 mét và vận tốc xe không dừng lại điểm đón ➔ Gán nhãn `FAKE_PICKUP` (Độ tin cậy 95%).
* **🟢 Con người tham gia (HITL - Human-In-The-Loop):**
  * Với các cuốc đường dài/liên tỉnh có giá trị lớn (> 150.000 VNĐ), hoặc cuốc có sai số GPS hầm gửi xe (Anomaly Score từ 0.60 đến 0.89), AI tự động tạo bản tóm tắt bằng chứng gồm bản đồ nhiệt và biểu đồ vận tốc để nhân viên CSKH chỉ cần **1-Click để duyệt** hoàn tiền trong 30 giây.
  * Toàn bộ việc xử phạt chế tài tài xế đều phải do Thanh tra con người ký duyệt.
* **↩️ Kế hoạch Dự phòng (Fallback Plan):**
  * Nếu hệ thống GPS của xe bị mất sóng (offline telemetry) hoặc Gemini API gặp sự cố timeout (> 5 giây), hệ thống tự động fallback: Gửi thông báo hẹn khách xử lý trong 24 giờ và đẩy vào hàng đợi manual review cũ, đảm bảo không bao giờ trừ tiền hoặc hoàn tiền sai lệch.

---

# 🏁 Phase 5 — EVALUATE: Đánh giá Tính Khả thi & Quyết định Dự án

### 📋 1. Bảng Kiểm Tra Độ Sẵn Sàng AI (AI Readiness Checklist)

| STT | Tiêu chí đánh giá | Trạng thái | Minh chứng thực tế tại Xanh SM |
|:---:|---|:---:|---|
| 1 | **Dữ liệu mẫu & Logs lịch sử sạch** | ✅ **SẴN SÀNG** | Hệ thống GSM lưu trữ 100% telemetry GPS của xe điện VinFast (tần suất 5 giây/lần) và log GPS từ app khách hàng. Đã có sẵn hàng chục nghìn ticket khiếu nại trong quá khứ đã được gán nhãn kết quả (Ground Truth) để benchmark mô hình. |
| 2 | **Kiểm soát rủi ro (HITL & Fallback)** | ✅ **SẴN SÀNG** | Ranh giới vận hành đã đóng chặt: Hạn mức hoàn tự động tối đa 150k, không tự động khóa tài xế. Mọi ca không tự tin đều fallback về con người thẩm định. Rủi ro thất thoát tài chính được khống chế ở mức 0%. |
| 3 | **Sự sẵn sàng của Stakeholders** | ✅ **SẴN SÀNG** | Bộ phận CSKH đang chịu áp lực quá tải nghiêm trọng từ hàng nghìn cuộc gọi mỗi ngày; ban lãnh đạo GSM đặt mục tiêu số 1 năm 2026 là giảm Churn Rate và bảo vệ sự hài lòng của khách hàng. |

---

### 🏛️ 2. Quyết định của Ban Giám đốc Vin Smart Future

> ### **QUYẾT ĐỊNH CHÍNH THỨC: [ x ] GO (Triển khai Xây dựng Bản mẫu)**

#### 💡 Lý giải Quyết định (Justification):
1. **Giá trị Kinh doanh vượt trội (High ROI & Churn Prevention):** Chi phí phát triển mô hình ước tính rất thấp (chỉ dùng Gemini 2.5 Flash kết hợp hàm toán học Haversine GPS có sẵn). Ngược lại, giải pháp giúp bảo vệ doanh thu hàng tỷ đồng mỗi năm bằng cách chặn đứng rò rỉ khách hàng trung thành, giảm 85% nhân lực CSKH tra soát tay.
2. **Độ phức tạp kỹ thuật vừa phải, tính khả thi cao (High Feasibility):** Không cần huấn luyện model nền tảng từ đầu. Tận dụng sức mạnh suy luận tiếng Việt vượt trội của Gemini 2.5 Flash để trích xuất thông tin khiếu nại, kết hợp với các logic toán học của hệ tọa độ GIS có sẵn của Vingroup.
3. **Chiến lược Triển khai An toàn (Phased Rollout):**
   * *Giai đoạn 1 (Tuần 1–2):* Chạy chế độ "Shadow Mode" (AI phân tích ngầm và so sánh với kết quả đối soát tay của con người mà chưa tự động hoàn tiền).
   * *Giai đoạn 2 (Tuần 3–4):* Bật Auto-Refund thử nghiệm cho 10% cuốc xe dưới 100.000 VNĐ tại khu vực Hà Nội.
   * *Giai đoạn 3 (Tháng tiếp theo):* Scale toàn bộ hệ thống taxi Xanh SM toàn quốc.
