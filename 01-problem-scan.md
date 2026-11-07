# Lab 02 — AI Product Scoping (Vin Smart Future)

* **Học viên thực hiện nhánh (`lesongthao`):** Lê Sông Thao — MSSV: `2A202603534` — Email: `sirothao123@gmail.com` — Lớp: `L3A-H209`
* **Tên nhóm / Repository nhóm:** `xtraiter/VinUni_Codelab_Day02` (L3A-H209)
* **Danh sách thành viên nhóm:**
  1. Nguyễn Văn Ba (Trưởng nhóm - `@xtraiter`) — Email: `tetutetua1@gmail.com`
  2. Lê Sông Thao (MSSV: `2A202603534` - `@lesongthao`) — Email: `sirothao123@gmail.com`
  3. Nguyễn Quang Duy (MSSV: `2A202603462` - `@nduy120505`) — Email: `nduy120505@gmail.com`
  4. Thanh Lâm (`@LysLama`) — Email: `thanhlam211204@gmail.com`
  5. Thành (`@thanhntda`)

---

# Phase 1 — SCAN

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

# Phase 2 — QUICK-ASSESS

Chọn top 3 bài toán từ danh sách trên: **#1 (Vinhomes — Phân loại & điều phối sự cố kỹ thuật tòa nhà)**, **#2 (Xanh SM — Xử lý sự cố sạc pin thực địa)**, **#4 (Vinmec — Tóm tắt hồ sơ xuất viện)**.

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

### Card #2 — Xanh SM: Điều phối xử lý sự cố sạc pin thực địa

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
