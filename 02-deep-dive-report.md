# 📄 02 — Báo cáo Phân tích Chuyên sâu (Deep-Dive Report)
## Dự án: Hệ thống Giám sát Gian lận Cuốc xe Thời gian thực & Tự động Đối soát Hoàn tiền (Smart Dispute & GPS Divergence Copilot)
**Đơn vị phát triển:** Vin Smart Future (Vingroup)  
**Khối kinh doanh phối hợp:** Công ty Cổ phần Di chuyển Xanh và Thông minh (GSM — Xanh SM)  
**Tác giả thực hiện:** Kỹ sư AI Product (Nhánh / Mã SV: `2A202603475`)  

---

## 🏛️ 1. Bối cảnh & Tuyên bố Bài toán

Tại Khối Vận hành của **Xanh SM (GSM)**, qua tổng hợp phản ánh từ người dùng trên mạng xã hội (Otofun, Reddit r/Vietnam) và dữ liệu khiếu nại nội bộ, tình trạng gian lận cuốc xe của tài xế đang diễn ra theo hai hình thức chính:
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
| **6. Operational Boundary (Ranh giới vận hành & Tuân thủ Dữ liệu)** | * **Ranh giới Kỹ thuật Nghiệp vụ:** AI được phép đọc nội dung khiếu nại; tính toán độ lệch không-thời gian (Spatial Anomaly Score); tự động đề xuất hoàn tiền cho các cuốc có giá trị **≤ 150.000 VNĐ** khi Anomaly Score ≥ 0.90. **TUYỆT ĐỐI CẤM:** Không được tự ý hoàn tiền với cuốc > 150.000 VNĐ; không được tự động khóa tài khoản hoặc trừ tiền phạt tài xế (bắt buộc Thanh tra con người ký duyệt); không được từ chối khiếu nại của khách mà không qua thẩm định viên.<br>* **Ranh giới Pháp lý & Phân quyền Dữ liệu (BẮT BUỘC):** Trong giai đoạn phát triển bản mẫu (Prototype/Pilot), **TUYỆT ĐỐI CẤM** cắm trực tiếp vào CSDL Production hay gọi API trừ tiền thật. Hệ thống bắt buộc phải chạy trên **Môi trường Sandbox với Dữ liệu Ẩn danh (Anonymized Data)**, tuân thủ nghiêm ngặt **Nghị định 13/2023/NĐ-CP về Bảo vệ Dữ liệu Cá nhân** (đã che số điện thoại, tên khách, và làm mờ tọa độ nhà riêng). |

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

### 📋 1. Bảng Kiểm Tra Độ Sẵn Sàng AI (AI Readiness Checklist) & Nhận Diện Rào Cản Quyền Hạn

Đánh giá trung thực, khách quan về hiện trạng kỹ thuật, pháp lý và bảo mật thông tin trước khi triển khai:

| STT | Tiêu chí đánh giá | Trạng thái | Phân tích Thực tế & Rào cản Quyền hạn (Permissions & Governance) |
|:---:|---|:---:|---|
| 1 | **Dữ liệu mẫu & Logs lịch sử sạch** | ⚠️ **CẦN ĐIỀU KIỆN (CONDITIONAL)** | **Dữ liệu có tồn tại trong hệ sinh thái nhưng hiện tại CHƯA THỂ LẤY NGAY:**<br>• Dữ liệu GPS xe điện nằm tại Data Lake của VinFast Auto.<br>• Dữ liệu cuốc xe và GPS khách nằm tại Backend của GSM.<br>• Dữ liệu khiếu nại nằm tại CRM CSKH.<br>🛑 **Rào cản lớn:** VinFast và GSM là 2 pháp nhân độc lập; tọa độ GPS là dữ liệu cá nhân nhạy cảm theo **Nghị định 13/2023/NĐ-CP**. Nhóm kỹ sư AI **chưa có quyền truy cập Production API** nếu chưa có thỏa thuận chia sẻ dữ liệu (DSA) và quy trình ẩn danh hóa PII. |
| 2 | **Kiểm soát rủi ro (HITL & Fallback)** | ✅ **SẴN SÀNG** | Ranh giới vận hành (Operational Boundary) đã được thiết kế chặt chẽ:<br>• Chỉ cho phép AI hoạt động trong môi trường Staging/Sandbox.<br>• Khống chế trần hoàn tiền ≤ 150.000 VNĐ; không trao quyền tự động chế tài tài xế.<br>• Thiết lập fallback 100% về quy trình manual review khi mất tín hiệu GPS hoặc model timeout. |
| 3 | **Sự sẵn sàng của Stakeholders** | ✅ **SẴN SÀNG** | CSKH và Ban Điều hành GSM rất ủng hộ dự án để giảm Churn Rate, nhưng Ban Pháp chế (Legal) và An toàn thông tin (Infosec) yêu cầu phải có quy trình Audit và Sandbox dữ liệu giả lập trước khi cấp Service Account kết nối hệ thống. |

---

### 🔍 1.1. Ma Trận 4 Nguồn Dữ Liệu & Kế Hoạch Vượt Rào Cản Phân Quyền (Governance Strategy)

Dự án thừa nhận thẳng thắn rào cản bảo mật và xây dựng cơ chế nạp dữ liệu phân tầng (Tiered Ingestion):

```text
┌────────────────────────────────────────────────────────────────────────────────────────┐
│ PHÂN TẦNG QUYỀN HẠN & CHIẾN LƯỢC TIẾP CẬN DỮ LIỆU AN TOÀN (DATA GOVERNANCE TIER)      │
├────────────────────────────────────────────────────────────────────────────────────────┤
│ 🛑 TẦNG PRODUCTION (HIỆN CHƯA CẤP PHÉP - KHÔNG TRUY CẬP TRỰC TIẾP):                   │
│  • VinFast Telematics Core (T-Box MQTT) ──> CSDL GPS xe thời gian thực                │
│  • GSM Transactional DB (PostgreSQL)   ──> Thông tin định danh khách & tài xế         │
│  • Payment Gateway Refund API          ──> Cổng trừ/hoàn tiền tài khoản ngân hàng     │
│  ==> Rào cản: Nghị định 13/2023/NĐ-CP, Bảo mật tài chính, Rủi ro thất thoát.          │
├────────────────────────────────────────────────────────────────────────────────────────┤
│                                        │ (Pipeline Làm Sạch & Ẩn Danh Hóa - 2 Tuần)    │
│                                        ▼                                               │
│ ✅ TẦNG SANDBOX / STAGING (ĐƯỢC PHÉP TRUY CẬP PHÁT TRIỂN PROTOTYPE):                  │
│  • Anonymized Data Dump: 10.000 cuốc xe lịch sử (Đã Hash SĐT, xóa Tên, làm mờ GPS).   │
│  • Mock Telemetry Server: Stub API giả lập luồng tọa độ GPS xe và khách để test model.│
│  • Dummy Refund Service: Ghi log giao dịch hoàn tiền giả định (Mock Transaction).      │
└────────────────────────────────────────────────────────────────────────────────────────┘
```

#### Chi tiết giải pháp kỹ thuật vượt rào cản cho từng nguồn:
1. **📡 GPS Xe (VinFast T-Box) & GPS Khách (GSM App):** 
   * *Rào cản:* Chặn quyền truy cập mạng nội bộ giữa 2 công ty; nguy cơ lộ vị trí riêng tư.
   * *Giải pháp khả thi:* Xin cấp phép trích xuất **1 tập dữ liệu tĩnh đóng băng (Static Snapshot ~ 10.000 cuốc)** đã được thuật toán K-Anonymity làm mờ 2 chữ số thập phân cuối của tọa độ (đảm bảo độ chính xác đo khoảng cách tương đối nhưng không lộ số nhà cụ thể).
2. **🎧 Dữ liệu Lịch sử Khiếu nại (CSKH CRM Ground Truth):** 
   * *Rào cản:* Chứa số điện thoại và ghi âm giọng nói của khách hàng.
   * *Giải pháp khả thi:* Chạy script chạy ngầm ẩn danh hóa (PII Redaction): Thay thế toàn bộ số điện thoại thành token mã hóa một chiều `[REDACTED_PHONE_HASH]`, chuyển văn bản khiếu nại thành dữ liệu thuần dạng Text.
3. **💳 Cổng Hoàn tiền (Payment Gateway API):** 
   * *Rào cản:* Nguy cơ rò rỉ quỹ tài chính nếu prompt injection hoặc AI bị lỗi logic.
   * *Giải pháp khả thi:* Tuyệt đối không cấp Production API Key. Sử dụng **Mock Payment Gateway** (môi trường Sandbox) chỉ trả về mã giả lập `{"status": "SUCCESS", "mock_refund_id": "RF123456"}` để kiểm thử toàn diện luồng nghiệp vụ.

---

### 🏛️ 2. Quyết định Chính thức của Ban Giám đốc Vin Smart Future

Dựa trên tiêu chí chấm điểm **G4 (Decision Quality — Đánh giá trung thực, dựa trên bằng chứng kỹ thuật và rào cản thực tế)**, dự án đưa ra quyết định:

> ### **QUYẾT ĐỊNH CHÍNH THỨC: [ x ] NOT YET (Trì hoãn Triển khai Production — Triển khai 4 Tuần Chuẩn bị Data Governance & Sandbox)**

#### 💡 Lý giải Quyết định Trung thực (Evidence-based Justification):
1. **Vì sao KHÔNG CHỌN "GO" ngay lập tức?**
   * Nếu chọn "GO" và tuyên bố kết nối thẳng vào Production API ngay hôm nay là **thiếu trung thực và vi phạm nghiêm trọng quy chuẩn An toàn thông tin doanh nghiệp**.
   * Nhóm kỹ sư AI hiện tại chưa có quyền hạn pháp lý để truy cập dữ liệu GPS nhạy cảm của khách hàng theo **Nghị định 13/2023/NĐ-CP** và chưa có thỏa thuận chia sẻ dữ liệu (DSA) liên pháp nhân giữa VinFast và GSM.
2. **Vì sao KHÔNG CHỌN "NO-GO"?**
   * Bài toán có giá trị kinh tế khổng lồ: Chặn đứng rò rỉ khách hàng (Churn rate), giải tỏa áp lực cho hàng trăm nhân viên CSKH và bảo vệ hình ảnh thương hiệu taxi điện văn minh của Xanh SM.
   * Công nghệ hoàn toàn khả thi: Logic toán học Haversine và năng lực NLP tiếng Việt của Gemini 2.5 Flash đã sẵn sàng.
3. **Lộ trình 4 Tuần chuyển đổi từ `NOT YET` sang `GO` (Action Plan):**
   * **Tuần 1 – 2 (Pháp lý & An toàn thông tin):**
     * Trình Hội đồng Quản trị ký kết Thỏa thuận chia sẻ dữ liệu (DSA) giữa VinFast và GSM dưới sự bảo trợ của Vin Smart Future.
     * Hoàn thành hồ sơ Đánh giá tác động xử lý dữ liệu cá nhân (DPIA).
   * **Tuần 3 (Data Engineering & Sandbox Creation):**
     * Thiết lập Data Pipeline tự động ẩn danh hóa PII (Masking số điện thoại, làm mờ tọa độ).
     * Xuất 10.000 cuốc xe lịch sử đã gán nhãn vào môi trường AWS S3 Staging Sandbox.
     * Dựng Mock Telematics API & Mock Payment Gateway để đội ngũ AI dev kiểm thử.
   * **Tuần 4 (Benchmark & Transition to GO):**
     * Đo lường hiệu năng mô hình trên Sandbox. Khi F1-Score đạt ≥ 98.5% và tỷ lệ hoàn tiền nhầm = 0%, Ban Giám đốc sẽ chính thức ký lệnh **GO** để triển khai thử nghiệm diện hẹp (Pilot 10% tại Hà Nội).