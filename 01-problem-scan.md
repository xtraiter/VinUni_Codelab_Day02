# Lab 02 — Problem Scan & Quick Assessment (Vin Smart Future)

* **Học viên thực hiện nhánh (`lesongthao`):** Lê Sông Thao — MSSV: `2A202603534` — Email: `sirothao123@gmail.com` — Lớp: `L3A-H209`
* **Repository nhóm:** `xtraiter/VinUni_Codelab_Day02`
* **Danh sách thành viên nhóm:**
  1. Nguyễn Văn Ba (Trưởng nhóm — `@xtraiter`) — `tetutetua1@gmail.com`
  2. Lê Sông Thao (MSSV: `2A202603534` — `@lesongthao`) — `sirothao123@gmail.com`
  3. Nguyễn Quang Duy (MSSV: `2A202603462` — `@nduy120505`) — `nduy120505@gmail.com`
  4. Thanh Lâm (`@LysLama`) — `thanhlam211204@gmail.com`
  5. Thành (`@thanhntda`)

---

## Phase 1 — SCAN: Danh Sách Bài Toán Vận Hành (4 Lenses)

Sử dụng bộ khung **4 Lenses** (*Lặp lại*, *Tốn thời gian*, *AI có thể tốt hơn*, *Pain từ người khác*) để rà soát hoạt động vận hành tại các công ty thành viên thuộc Tập đoàn Vingroup:

| # | Subsidiary (Công ty thành viên) | Lens | Mô tả ngắn bài toán & Điểm nghẽn vận hành | Quy mô & Thời gian xử lý hiện tại |
| :-: | :--- | :--- | :--- | :--- |
| **1** | **Vinhomes** | Lặp lại | **Phân loại và điều phối xử lý sự cố kỹ thuật tòa nhà từ App Vinhomes Resident:** Nhân viên CSKH trực Ban Quản lý phải đọc thủ công từng phản ánh văn bản tự do (nhiều từ viết tắt, không dấu) về điện, nước, thang máy, điều hòa, PCCC để xác minh và chuyển cho đúng tổ kỹ thuật. | ~1,200 ticket/ngày tại các đại đô thị; mất **45 phút/ticket** (33 phút xử lý trung gian), tỷ lệ gán nhầm bộ phận ~15%. |
| **2** | **Xanh SM (GSM)** | Tốn thời gian | **Điều phối xử lý sự cố pin yếu và cứu hộ năng lượng thực địa:** Điều phối viên phải tra cứu thủ công tọa độ GPS của xe báo sắp hết pin, đối chiếu danh sách trụ sạc VinFast còn trống và gõ tin nhắn chỉ đường hoặc gọi xe cứu hộ pin khi mức pin dưới 5%. | ~80 ca sự cố pin/ngày tại Hà Nội & TP.HCM; mất **15 phút/lượt**, tiêu tốn ~20 giờ công/ngày của trung tâm điều vận. |
| **3** | **VinFast** | AI có thể tốt hơn | **Chẩn đoán sơ bộ nhóm lỗi kỹ thuật từ mô tả đời thường của chủ xe:** Cố vấn dịch vụ tại xưởng phải đối chiếu mô tả cảm tính của khách hàng (tiếng kêu gầm xe, cảnh báo màn hình) với sổ tay kỹ thuật để xếp lịch khoang sửa chữa. | Mất **20–25 phút/xe** ở khâu tiếp nhận; ~18% trường hợp xếp nhầm giữa khoang Cơ khí và khoang Điện cao áp. |
| **4** | **Vinmec** | Pain từ người khác | **Tổng hợp dữ liệu lâm sàng và soạn thảo tóm tắt hồ sơ xuất viện (Discharge Summary):** Bác sĩ nội trú phải mở nhiều tab trên hệ thống bệnh án điện tử (HIS/EMR) để sao chép kết quả xét nghiệm, chẩn đoán hình ảnh và đơn thuốc ra viện kèm hướng dẫn chăm sóc tại nhà. | Mất **25–30 phút/hồ sơ** vào cuối ca trực; gây quá tải thủ tục hành chính cho bác sĩ và kéo dài thời gian chờ ra viện. |
| **5** | **Vinpearl** | Pain từ người khác | **Phân tích đa ngôn ngữ và cảnh báo sớm đánh giá tiêu cực của khách lưu trú:** Nhân viên quản lý chất lượng phải đọc thủ công hàng nghìn review tiếng Việt, Anh, Hàn, Trung trên Booking, Agoda, Google Maps để lọc ra các sự cố vận hành khẩn cấp tại resort. | Độ trễ phát hiện phàn nàn nghiêm trọng kéo dài **24–48 giờ**, làm giảm khả năng xử lý nóng khi khách còn đang lưu trú. |
| **6** | **VinUni** | Lặp lại | **Phân tích log lỗi lập trình và gợi ý hướng dẫn sư phạm cho sinh viên:** Giảng viên và trợ giảng đọc từng log lỗi cú pháp/logic của sinh viên trong các bài lab để giải thích nguyên nhân sai và gợi ý hướng sửa mà không đưa sẵn đáp án. | Mất **10–15 phút/bài nộp** khi quy mô lớp học thực hành lên tới 150–200 sinh viên/đợt. |

---

## Phase 2 — QUICK-ASSESS: Ba Thẻ Bài Toán Trọng Điểm (Quick Problem Cards)

Chọn top 3 bài toán có tác động vận hành trực tiếp nhất từ danh sách Phase 1: **#1 (Vinhomes)**, **#2 (Xanh SM)** và **#4 (Vinmec)**.

### Card #1 — Vinhomes: Phân loại & điều phối xử lý sự cố kỹ thuật tòa nhà

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

### Card #2 — Xanh SM (GSM): Điều phối xử lý sự cố sạc pin thực địa

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

### Card #3 — Vinmec: Hỗ trợ soạn thảo tóm tắt hồ sơ xuất viện

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
