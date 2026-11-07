# 01 — Problem Scan & Quick Assessment (Vin Smart Future)

# 🔍 Phase 1 — SCAN (Cá nhân, 20 min)

Hãy sử dụng **4 Lenses** dưới đây để quét qua hoạt động vận hành của các công ty thành viên Vingroup. Ghi lại **ít nhất 5 bài toán/bottleneck** thực tế.

### 4 Lenses tìm bài toán AI cho Vingroup:

1. **Lặp lại (Repetitive):** Tác vụ lặp đi lặp lại nhiều lần hằng ngày. (Ví dụ: So khớp hóa đơn sạc điện tại VinFast, route lại chuyến taxi tại Xanh SM).	
2. **Tốn thời gian (Time-consuming):** Tác vụ ngốn thời gian xử lý thủ công của nhân viên. (Ví dụ: Soạn thảo phản hồi đánh giá 1-star của cư dân Vinhomes).
3. **AI có thể tốt hơn (AI-upgrade):** Dịch vụ khách hàng hiện tại còn chậm hoặc phản hồi rập khuôn. (Ví dụ: Chatbot CSKH Vinpearl hỗ trợ đặt vé vui chơi).
4. **Pain từ người khác (Stakeholder Pain):** Bottleneck khiến khách hàng hoặc nhân viên thực địa phàn nàn. (Ví dụ: Tài xế Xanh SM phàn nàn về việc hệ thống gợi ý điểm đón khách không chính xác).

> [!TIP]
> **🤖 AI Prompts — Partner brainstorm:**
> Hãy sử dụng prompt sau để brainstorm các bài toán thực tế nếu bạn chưa có ý tưởng:
> *"Tôi là AI Engineer tại Vin Smart Future (Vingroup). Tôi đang tìm kiếm các pain point vận hành cụ thể có thể tối ưu bằng AI cho mảng [Chọn một: VinFast / Xanh SM / Vinhomes / Vinmec]. Hãy gợi ý cho tôi 5 quy trình nghiệp vụ thủ công, tốn nhiều thời gian và gây rò rỉ hiệu suất kèm con số thống kê ước tính về tổn thất."*

### 📝 List bài toán của tôi:

| # | Subsidiary (VinFast/Xanh SM...) | Lens | Mô tả ngắn bài toán |
| - | ------------------------------- | ---- | ------------------------ |
| 1 | **Xanh SM (GSM)** | Pain từ người khác (Stakeholder Pain) | Xử lý gian lận tài xế "ngâm cuốc / hoàn thành khống" & tranh chấp hoàn tiền/phí hủy của khách hàng. |
| 2 | **Xanh SM (GSM)** | Pain từ người khác (Stakeholder Pain) | Thanh tra lập biên bản phạt tài xế "bẩn xe / sai quy chuẩn" cứng nhắc, thiếu ngữ cảnh vận hành thực tế. |
| 3 | **VinFast & V-Green** | AI có thể tốt hơn (AI-upgrade) | Tranh chấp phí phạt "chiếm dụng trụ sạc" (4.000đ/phút) do sự cố kẹt súng/lỗi trụ sạc đối soát tách rời với Camera AI. |
| 4 | **VinFast** | Tốn thời gian (Time-consuming) | Xe "nằm xưởng" chờ chẩn đoán lỗi & tra phụ tùng thủ công, chịu áp lực đền bù 500.000đ/ngày quá hạn sửa chữa. |
| 5 | **Vinhomes** | Lặp lại & Tốn thời gian | Phê duyệt thủ tục vận chuyển đồ nội thất và đăng ký thi công qua App Cư dân gây ùn ứ tại sảnh ngoài giờ. |

#### 📌 Bằng chứng thực tế từ MXH, Quy trình thủ công gây nghẽn & Tổn thất ước tính:

1. **🚕 Xanh SM: Gian lận tài xế "ngâm cuốc / hoàn thành khống" & Tranh chấp tiền hoàn vé / phí hủy:**
   * **Bằng chứng thực tế từ MXH (Otofun & Reddit r/VietNam):** Chủ đề nóng trên Otofun (Tháng 01/2026 - *"Huỷ chuyến nhưng Xanh SM không hoàn tiền"*) và Reddit r/VietNam (*"Xanh SM driver completed my ride without picking me up"*): Khách đặt xe tại sân bay/bến xe hoặc giờ cao điểm, tài xế nhận cuốc nhưng đứng im không di chuyển hoặc chạy vòng qua mặt khách để né cuốc ngắn/kẹt xe. Nếu tự hủy, tài xế sẽ bị phạt điểm hiệu suất và mất thưởng ngày (200k-300k), nên tài xế chọn cách "ngâm" cho khách tự hủy hoặc bấm "Đón khách -> Hoàn thành chuyến" khống dù không có khách trên xe. Khách liên kết thẻ tín dụng bị trừ tiền ngay hoặc bị thu phí hủy sau 10 phút chờ. Khi khiếu nại, tổng đài phải ghi nhận thủ công, chờ 2-5 ngày sau mới gọi lại xác minh vì nhân viên CSKH phải mở log định vị GPS của xe và khách ra đối chiếu bằng mắt.
   * **Quy trình thủ công gây nghẽn:** Hệ thống thiếu cơ chế Auto-Dispute & Real-time GPS Divergence Check khi phát sinh khiếu nại/hủy chuyến. Nhân viên CSKH phải nghe ghi âm, xem ảnh chụp màn hình của khách và tra log tọa độ thủ công để quyết định hoàn tiền.
   * **Thống kê tổn thất ước tính:** Mất khách vĩnh viễn (Churn rate): Nhiều người dùng trên Otofun tuyên bố xóa app hoặc gỡ thẻ tín dụng khỏi Xanh SM ngay sau 1 lần mất tiền oan không được giải quyết ngay. Chi phí vận hành CSKH: Mỗi vụ khiếu nại cuốc xe ngâm/hoàn thành khống ngốn 20–35 phút tra cứu chéo giữa bộ phận CSKH và bộ phận Quản lý tài xế; gây tồn đọng hàng nghìn ticket hoàn tiền mỗi tuần.

2. **🚕 Xanh SM: Thanh tra lập biên bản phạt tài xế "bẩn xe / sai quy chuẩn" cứng nhắc & Khiếu nại nội bộ:**
   * **Bằng chứng thực tế từ MXH (Otofun & Hội nhóm Tài xế Xanh):** Vụ việc viral trên Otofun (*"Tài xế taxi Xanh SM bị lập biên bản bởi thanh tra vì để xe bẩn"*): Tài xế chạy liên tục từ 5h sáng đến 10h sáng mới về trạm V-Green chờ sạc pin, chưa kịp lau vết giày bùn của khách vừa xuống xe và bụi ở kính sau thì bị Thanh tra mặt đất chụp ảnh lập biên bản phạt vi phạm quy chế. Ngược lại, để kiểm soát hàng chục nghìn xe, đội Thanh tra & Vận hành của GSM đang phải yêu cầu tài xế chụp ảnh nghiệm thu xe đầu ca/cuối ca và đi kiểm tra đột xuất tại trạm sạc, dẫn đến xung đột gay gắt giữa tài xế và thanh tra về tính công bằng (vừa đi dưới mưa về, khách vừa bước xuống chưa kịp dọn...).
   * **Quy trình thủ công gây nghẽn:** Duyệt ảnh kiểm tra xe thủ công hoặc lập biên bản cảm tính mà không đối chiếu với ngữ cảnh vận hành thực tế (lịch sử cuốc xe vừa kết thúc cách đó bao lâu, thời tiết khu vực xe vừa đi qua có mưa không).
   * **Thống kê tổn thất ước tính:** Mỗi biên bản phạt từ 200.000đ – 500.000đ (hoặc khóa app tạm thời) nếu xử lý thiếu ngữ cảnh sẽ gây bức xúc lớn, góp phần đẩy tỷ lệ nghỉ việc của tài xế (Driver Turnover) lên mức 15-25%/quý, làm tốn hàng tỷ đồng chi phí tuyển dụng và đào tạo lại (OKXE/GSM đang phải chi tới 300.000đ/tài xế mới chỉ để thu hút đăng ký).

3. **🚗 VinFast & V-Green: Tranh chấp phí phạt "Chiếm dụng trụ sạc" (4.000đ/phút) & Sự cố kẹt súng/lỗi trụ:**
   * **Bằng chứng thực tế từ MXH (Diễn đàn VinFast & Tinhte):** V-Green áp dụng chính sách thu phí Bồi thường thiệt hại doanh thu (BTTH) 4.000đ/phút (tối đa 1.500.000đ/lần) từ phút thứ 11 nếu xe đỗ vào ô sạc mà không cắm súng, hoặc sạc đầy đã rút súng nhưng không đánh xe ra. Nỗi đau thực tế của chủ xe: Rất nhiều chủ xe đỗ vào ô sạc nhưng trụ sạc bị lỗi đèn đỏ, kẹt khóa súng sạc, hoặc app không khởi tạo được phiên sạc nên không thể cắm sạc được (hoặc đang loay hoay gọi tổng đài 1900 2323 89 nhánh 5 tốn 1.000đ/phút để nhờ reset trụ), nhưng Camera AI tại trạm vẫn đếm giờ và hệ thống tự động áp phí phạt chiếm chỗ oan. Khi gửi khiếu nại về v.vgreencare@vgreen.net, thời gian chờ phản hồi quy định lên tới 24 giờ vì nhân viên phải kiểm tra lại log lỗi của trụ sạc và trích xuất camera tại trạm.
   * **Quy trình thủ công gây nghẽn:** Quy trình giải quyết khiếu nại phí phạt trạm sạc (Charging Penalty Dispute) đang tách rời giữa dữ liệu Camera AI (đếm giờ đỗ xe) và log phần cứng của trụ sạc (IoT Telemetry của trụ báo lỗi kết nối/kẹt súng). Nhân viên V-Green phải đối chiếu tay 2 luồng dữ liệu này khi khách khiếu nại.
   * **Thống kê tổn thất ước tính:** Trạm sạc bị giam vị trí vô ích, còn chủ xe bị giam tiền/khoá tính năng sạc cho đến khi đối soát xong. Nhân viên vận hành V-Green mất 15-20 phút/ca để tra cứu log trụ + video camera cho một khoản phạt chỉ vài chục nghìn đồng.

4. **🚗 VinFast: Xe "nằm xưởng" chờ phán đoán bệnh & Áp lực đền bù 500.000đ/ngày quá hạn sửa chữa:**
   * **Bằng chứng thực tế từ MXH (Otofun & Cộng đồng chủ xe VinFast):** Để giải quyết bức xúc của chủ xe về việc xe điện vào xưởng dịch vụ phải nằm chờ lâu do kỹ thuật viên lúng túng trước các lỗi phần mềm/điện phức tạp hoặc chậm điều phối phụ tùng, các xưởng dịch vụ VinFast đang phải áp dụng chính sách Cam kết thời gian sửa chữa: Hỗ trợ trực tiếp 500.000 VNĐ/ngày cho khách hàng nếu thời gian sửa chữa vượt quá cam kết trên Lệnh sửa chữa (RO - Repair Order) do lỗi xưởng hoặc chậm phụ tùng.
   * **Quy trình thủ công gây nghẽn:** Cố vấn dịch vụ (SA) khi tiếp nhận xe rất sợ ghi thời gian cam kết ngắn (vì sợ phạt 500k/ngày) nhưng cũng không biết chính xác lỗi trên màn hình xe (kèm hàng chục mã lỗi ảo do sụt áp 12V) cần phụ tùng gì trong kho tổng để báo hẹn khách. Cố vấn tiếp nhận mô tả triệu chứng của khách + ảnh chụp màn hình táp-lô, sau đó phải chờ kỹ thuật viên cắm máy đọc lỗi và tra cứu tồn kho phụ tùng thủ công rồi mới dám chốt Lệnh sửa chữa (RO).
   * **Thống kê tổn thất ước tính:** Mỗi ngày một chiếc xe nằm xưởng quá hạn do chẩn đoán ban đầu thiếu phụ tùng liên đới khiến VinFast/Xưởng mất đứt 500.000 VNĐ/xe/ngày tiền đền bù trực tiếp, chưa kể chi phí ùn tắc khoang sửa chữa.

5. **🏢 Vinhomes (Smart City / Ocean Park): "Ma trận" Hotline & Duyệt thủ tục ra/vào đồ, đăng ký thi công trên App Cư dân:**
   * **Bằng chứng thực tế từ MXH (Cộng đồng cư dân Vinhomes Ocean Park & Smart City):** Cư dân phản ánh sự rối rắm khi có việc gấp: Chỉ riêng Vinhomes Smart City có tới 4-5 số hotline khác nhau (Tổng đài chung 1900 2323 89 nhánh 4, An ninh căn hộ 0827..., Giám sát ngoài trời 0913..., Thẻ xe/gửi xe 0946..., Tiện ích chung 0852..., và số BQL gọi ra 0243... lại khóa chiều gọi lại!). Đặc biệt, nỗi ám ảnh lớn nhất của cư dân và người thuê nhà là đăng ký mang đồ nội thất/điện máy ra vào sảnh hoặc đăng ký thi công sửa chữa nhỏ. Nếu chủ nhà điền thiếu thông tin trên app Vinhomes Resident hoặc nộp form giấy ngoài giờ hành chính (sau 17h30), xe tải chở đồ đến nơi sẽ bị bảo vệ sảnh chặn lại không cho vào thang hàng vì "chưa có lệnh duyệt trên hệ thống từ BQL".
   * **Quy trình thủ công gây nghẽn:** Nhân viên BQL phải đọc từng đơn đăng ký vận chuyển đồ/thi công trên app trong giờ hành chính (8h30 - 17h30) để kiểm tra xem cư dân đã đóng hết phí dịch vụ chưa, đồ mang vào có thuộc diện phải đặt cọc thi công không, có giấy ủy quyền của chủ nhà cho người thuê không.
   * **Thống kê tổn thất ước tính:** Cư dân mất 300.000đ – 500.000đ/chuyến tiền đền bù xe tải chờ/quay đầu vì kẹt thủ tục dưới sảnh; nhân viên lễ tân và bảo vệ tốn hàng giờ mỗi ngày để nghe cư dân cãi vã dưới sảnh và gọi điện xin ý kiến BQL bằng miệng.

---

# 🃏 Phase 2 — QUICK-ASSESS (Cá nhân, 30 min)

Chọn **top 3 bài toán** từ danh sách trên và hoàn thiện **3 Quick Problem Cards** dưới đây (10 phút/card).

### 🃏 QUICK PROBLEM CARD #1 — Xanh SM: Tự động đối soát GPS & Xử lý tranh chấp gian lận cuốc xe

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #1                                       │
│                                                             │
│ Bài toán (1 câu): Khách hàng bị tài xế ngâm cuốc hoặc bấm   │
│ "hoàn thành khống" né phạt hủy chuyến khiến khách bị trừ    │
│ tiền oan; đối soát GPS và giải quyết hoàn tiền mất 2-5 ngày.│
│ Công ty thành viên: [x] Xanh SM (GSM)                       │
│                                                             │
│ Ai đang đau (Actor)? Khách hàng (bị trừ tiền oan, ức chế),  │
│ Nhân viên CSKH & Quản lý tài xế (quá tải đối soát thủ công).│
│                                                             │
│ Workflow thủ công hiện tại (5 bước):                        │
│   1. Khách gửi khiếu nại cuốc xe ngâm/khống qua App/Hotline │
│   ──> 2. CSKH tạo ticket, chuyển sang bộ phận Vận hành xe   │
│   ──> 3. Kỹ thuật viên trích xuất log GPS của xe và khách   │
│   ──> 4. Đối soát mắt độ lệch tọa độ và thời gian di chuyển │
│   ──> 5. Phê duyệt lệnh hoàn tiền thủ công và kỷ luật tài xế│
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 3-4 (⏱ 25 phút/lượt;   │
│ tổng thời gian chờ liên phòng ban kéo dài 2-5 ngày làm việc)│
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 3-4-5            │
│ (LLM phân loại khiếu nại + GPS Spatial Anomaly Detection    │
│ tự động tính khoảng cách sai lệch giữa xe và khách -> Auto  │
│ trigger lệnh hoàn tiền và gắn cờ gian lận tài xế).          │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                        │
│ Giảm thời gian xác minh & hoàn tiền từ 72 giờ ──> dưới 30s; │
│ giảm 85% thời gian xử lý thủ công của đội ngũ CSKH.         │
│                                                             │
│ Quick Architecture: [x] Rule + LLM (LLM NLP + Rule GPS check)│
└─────────────────────────────────────────────────────────────┘
```

---

### 🃏 QUICK PROBLEM CARD #2 — VinFast & V-Green: Tự động giải quyết tranh chấp phí phạt chiếm dụng trụ sạc

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #2                                       │
│                                                             │
│ Bài toán (1 câu): Chủ xe VinFast bị phạt 4.000đ/phút oan do  │
│ trụ sạc lỗi/kẹt súng sạc nhưng Camera AI vẫn đếm giờ phạt;   │
│ đối soát camera và log phần cứng trụ sạc thủ công mất 24h.  │
│ Công ty thành viên: [x] VinFast & V-Green                   │
│                                                             │
│ Ai đang đau (Actor)? Chủ xe điện VinFast (bị phạt oan, giam │
│ tiền, khóa ví sạc), CSKH V-Green (quá tải tra soát đa nguồn)│
│                                                             │
│ Workflow thủ công hiện tại (5 bước):                        │
│   1. Khách bị trừ tiền phạt chiếm chỗ, gọi 1900 2323 89     │
│   ──> 2. CSKH tiếp nhận biển số xe, mã trụ và giờ phát sinh │
│   ──> 3. Kỹ thuật viên tra cứu IoT Telemetry lỗi phần cứng   │
│   ──> 4. Trích xuất video Camera AI xác nhận thao tác cắm/rút│
│   ──> 5. CSKH đối chiếu biên bản, gửi yêu cầu hoàn tiền ví  │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 3-4 (⏱ 20 phút/ca;     │
│ SLA phản hồi cho khách hàng hiện tại lên tới 24 giờ)         │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 3-4-5            │
│ (AI Agent đối soát đa phương thức: đọc sự kiện ngắt sạc bất  │
│ thường từ IoT trụ + kiểm tra frame hình Camera AI -> Tự động│
│ miễn trừ phí phạt và gửi thông báo giải thích cho chủ xe).  │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                        │
│ Giảm thời gian giải quyết tranh chấp từ 24 giờ ──> dưới 1 ph;│
│ tự động hóa 90% ca tranh chấp lỗi kỹ thuật trụ sạc.         │
│                                                             │
│ Quick Architecture: [x] Agent (Event Correlation Engine)    │
└─────────────────────────────────────────────────────────────┘
```

---

### 🃏 QUICK PROBLEM CARD #3 — VinFast: AI Co-pilot chẩn đoán lỗi xe điện & Dự báo phụ tùng Lệnh sửa chữa (RO)

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #3                                       │
│                                                             │
│ Bài toán (1 câu): Cố vấn dịch vụ chẩn đoán chậm trước ma    │
│ trận mã lỗi ảo sụt áp 12V và tra phụ tùng thủ công, dẫn đến │
│ trễ hẹn RO và chịu phạt cam kết đền bù 500.000đ/xe/ngày.    │
│ Công ty thành viên: [x] VinFast                             │
│                                                             │
│ Ai đang đau (Actor)? Cố vấn dịch vụ (SA - Service Advisor),  │
│ Kỹ thuật viên xưởng dịch vụ, Chủ xe VinFast (chờ xe sửa).    │
│                                                             │
│ Workflow thủ công hiện tại (5 bước):                        │
│   1. SA tiếp nhận xe và ghi nhận mô tả triệu chứng của khách │
│   ──> 2. KTV cắm máy VDS quét mã lỗi DTC trên mạng CAN xe   │
│   ──> 3. KTV & SA tra sổ tay hướng dẫn sửa chữa tìm lỗi gốc  │
│   ──> 4. SA kiểm tra tồn kho phụ tùng trên ERP thủ công      │
│   ──> 5. SA ước lượng giờ hoàn thành và ký Lệnh sửa chữa RO │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 2-3-4 (⏱ 35-45 phút;   │
│ dễ sót linh kiện liên đới làm xe nằm xưởng quá hạn cam kết) │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2-3-4            │
│ (AI Co-pilot nhận danh sách mã lỗi + mô tả khách -> lọc nhiễu│
│ mã lỗi ảo 12V, RAG tra cứu sơ đồ tài liệu kỹ thuật và truy   │
│ vấn kho phụ tùng ERP -> Gợi ý thời gian cam kết RO chuẩn).   │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                        │
│ Rút ngắn thời gian chẩn đoán & tạo RO từ 45 phút ──> 10 phút;│
│ giảm 50% số xe quá hạn (tiết kiệm hàng trăm triệu đền bù).  │
│                                                             │
│ Quick Architecture: [x] LLM (RAG Service Manual + ERP API)  │
└─────────────────────────────────────────────────────────────┘
```

> [!TIP]
> **🤖 AI Prompts — Stress-Test thẻ bài toán:**
> Hãy dán nội dung thẻ bài toán của bạn vào LLM để nhận phản biện:
> *"Đây là một thẻ bài toán vận hành tôi đề xuất cho Vin Smart Future: [Dán nội dung]. Hãy đóng vai trò là một CFO và Trưởng phòng Vận hành cực kỳ khắt khe, chỉ ra cho tôi 3 điểm yếu về logic, metric, và giải thích vì sao rule-based code thông thường có thể giải quyết bài toán này tốt hơn là dùng AI."*

---
