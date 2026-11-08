### 📝 List bài toán của Duy:

| # | Subsidiary (VinFast/Xanh SM...) | Lens | Mô tả ngắn bài toán |
| --- | --- | --- | --- |
| 1 | Xanh SM | **Stakeholder Pain** *(Tài xế & Khách)* | **Tối ưu hóa điều phối trạm sạc & đón khách thông minh:** Chuyển từ tài xế tự tìm trạm sang gợi ý trạm/giờ sạc tối ưu theo cung - cầu thực tế; giải quyết tắc nghẽn trụ sạc V-Green và thiếu xe phục vụ khách giờ cao điểm. |
| 2 | Xanh SM | **Time-consuming** *(Nhân viên bãi xe)* | **Tự động hóa kiểm định ngoại quan khi giao nhận ca xe (Inspection AI):** Ứng dụng Computer Vision tự động quét vết trầy xước/hư hại thân vỏ khi đổi ca trong 30–60s; thay thế quy trình kiểm tra thủ công 15–25 phút và chấm dứt tranh chấp đền bù giữa các ca lái. |
| 3 | Xanh SM | **AI-upgrade** *(Chất lượng dịch vụ)* | **Giám sát an toàn khoang lái & kiểm toán dịch vụ tập trung (Cabin DMS):** Quét real-time 100% video/audio cabin để cảnh báo tài xế mất tập trung, hút thuốc, buồn ngủ; nâng cấp quy trình QA/QC thủ công vốn chỉ rà soát được dưới 3% dữ liệu. |
| 4 | Xanh SM | **Repetitive** *(CSKH & Fraud Ops)* | **Đối soát tự động khiếu nại cước phí & gian lận lộ trình:** Tự động phân tích bất thường quỹ đạo GPS (né đường ngập vs. cố tình đi vòng, cuốc xe ma); cắt giảm thời gian tra cứu thủ công từ 8–15 phút/ticket xuống phê duyệt tự động cho 70% trường hợp. |
| 5 | Xanh SM / VinFast | **Time-consuming** *(Kỹ thuật viên xưởng)* | **Bảo trì dự đoán hệ thống pin & động cơ từ xa (EV Telematics):** Phân tích dữ liệu chuỗi thời gian CAN-bus/BMS để phát hiện sớm mất cân bằng cell pin trước 10–14 ngày; thay thế việc cắm máy quét OBD thủ công và giảm thời gian xe nằm xưởng đột xuất. |


### Quickcard

┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #02                                      │
│                                                             │
│ Bài toán (1 câu): Tự động phát hiện và khoanh vùng vết hư   │
│ hại, trầy xước ngoại quan xe điện khi giao nhận ca.         │
│ Công ty thành viên: [ ] VinFast  [x] Xanh SM  [ ] Vinhomes  │
│                     [ ] Vinmec   [ ] Khác                   │
│                                                             │
│ Ai đang đau (Actor)? Tài xế nhận/bàn giao ca & Quản lý bãi  │
│                                                             │
│ Workflow thủ công hiện tại (3-5 bước):                      │
│   1. Đỗ xe tại bãi ──> 2. Đi vòng quanh chụp 8-12 góc ảnh   │
│   ──> 3. So đối chiếu ảnh ca trước ──> 4. Ký biên bản app   │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 2 & 3 (⏱ 15-20 min)   │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 3 (Computer      │
│ Vision so sánh và phát hiện sai khác/hư hại tự động).       │
│                                                             │
│ Đo thành công bằng gì (Metric có số)? Giảm thời gian giao   │
│ nhận ca từ 20 phút ──> dưới 2 phút/lượt; giảm 80% tranh chấp│
│                                                             │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] CV (Vision AI) │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #04                                      │
│                                                             │
│ Bài toán (1 câu): Tự động phát hiện bất thường quỹ đạo GPS  │
│ để đối soát khiếu nại cước phí và gian lận lộ trình.        │
│ Công ty thành viên: [ ] VinFast  [x] Xanh SM  [ ] Vinhomes  │
│                     [ ] Vinmec   [ ] Khác                   │
│                                                             │
│ Ai đang đau (Actor)? Khách hàng, Tài xế & Đội CSKH / Fraud  │
│                                                             │
│ Workflow thủ công hiện tại (3-5 bước):                      │
│   1. Khách gửi ticket khiếu nại đi đường vòng/bị cước cao   │
│   ──> 2. Agent mở log GPS chuyến đi đối soát tuyến đề xuất  │
│   ──> 3. Kiểm tra lý do (kẹt xe, đường cấm) ──> 4. Hoàn tiền│
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 2 & 3 (⏱ 8-15 min)    │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2 & 3 (Sequence  │
│ Matching & Spatial-Temporal GNN phát hiện lộ trình dị thường│
│                                                             │
│ Đo thành công bằng gì (Metric có số)? Giảm thời gian xử lý  │
│ ticket từ 12 phút ──> dưới 1 phút; tự động duyệt 70% case   │
│                                                             │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] ML / GNN       │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #05                                      │
│                                                             │
│ Bài toán (1 câu): Phân tích dữ liệu CAN-bus/Telematics thời │
│ gian thực để dự đoán suy thoái pin và lỗi động cơ điện.    │
│ Công ty thành viên: [x] VinFast  [x] Xanh SM  [ ] Vinhomes  │
│                     [ ] Vinmec   [ ] Khác                   │
│                                                             │
│ Ai đang đau (Actor)? Quản trị đội xe, Kỹ thuật viên xưởng & │
│                      Tài xế chạy xe ngoài đường.            │
│                                                             │
│ Workflow thủ công hiện tại (3-5 bước):                      │
│   1. Xe báo lỗi trên táp-lô hoặc đến kỳ bảo dưỡng km        │
│   ──> 2. Đưa xe vào xưởng VinFast, cắm máy quét OBD thủ công│
│   ──> 3. Kỹ thuật viên đọc mã DTC, đo dung lượng/áp cell    │
│   ──> 4. Đặt linh kiện pack pin thay thế & lên lịch sửa     │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 2 & 3 (⏱ 4-8 giờ nằm  │
│ xưởng kiểm tra; chờ linh kiện pin mất thêm 2-5 ngày).       │
│                                                             │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 1 & 2 (Mô hình   │
│ Time-Series / Digital Twin dự báo bất thường cell pin từ xa)│
│                                                             │
│ Đo thành công bằng gì (Metric có số)? Phát hiện hỏng hóc pin│
│ trước 10-14 ngày; giảm thời gian chết (downtime) 40%.       │
│                                                             │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] Time-Series AI │
└─────────────────────────────────────────────────────────────┘


### 📝 List bài toán của tôi:

| # | Subsidiary (VinFast/Xanh SM...) | Lens | Mô tả ngắn bài toán |
| --- | --- | --- | --- |
| 1 | Xanh SM | **Stakeholder Pain** *(Tài xế & Khách)* | **Tối ưu hóa điều phối trạm sạc & đón khách thông minh:** Chuyển từ tài xế tự tìm trạm sang gợi ý trạm/giờ sạc tối ưu theo cung - cầu thực tế; giải quyết tắc nghẽn trụ sạc V-Green và thiếu xe phục vụ khách giờ cao điểm. |
| 2 | Xanh SM | **Time-consuming** *(Nhân viên bãi xe)* | **Tự động hóa kiểm định ngoại quan khi giao nhận ca xe (Inspection AI):** Ứng dụng Computer Vision tự động quét vết trầy xước/hư hại thân vỏ khi đổi ca trong 30–60s; thay thế quy trình kiểm tra thủ công 15–25 phút và chấm dứt tranh chấp đền bù giữa các ca lái. |
| 3 | Xanh SM | **AI-upgrade** *(Chất lượng dịch vụ)* | **Giám sát an toàn khoang lái & kiểm toán dịch vụ tập trung (Cabin DMS):** Quét real-time 100% video/audio cabin để cảnh báo tài xế mất tập trung, hút thuốc, buồn ngủ; nâng cấp quy trình QA/QC thủ công vốn chỉ rà soát được dưới 3% dữ liệu. |
| 4 | Xanh SM | **Repetitive** *(CSKH & Fraud Ops)* | **Đối soát tự động khiếu nại cước phí & gian lận lộ trình:** Tự động phân tích bất thường quỹ đạo GPS (né đường ngập vs. cố tình đi vòng, cuốc xe ma); cắt giảm thời gian tra cứu thủ công từ 8–15 phút/ticket xuống phê duyệt tự động cho 70% trường hợp. |
| 5 | Xanh SM / VinFast | **Time-consuming** *(Kỹ thuật viên xưởng)* | **Bảo trì dự đoán hệ thống pin & động cơ từ xa (EV Telematics):** Phân tích dữ liệu chuỗi thời gian CAN-bus/BMS để phát hiện sớm mất cân bằng cell pin trước 10–14 ngày; thay thế việc cắm máy quét OBD thủ công và giảm thời gian xe nằm xưởng đột xuất. |


### Quickcard

┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #02                                      │
│                                                             │
│ Bài toán (1 câu): Tự động phát hiện và khoanh vùng vết hư   │
│ hại, trầy xước ngoại quan xe điện khi giao nhận ca.         │
│ Công ty thành viên: [ ] VinFast  [x] Xanh SM  [ ] Vinhomes  │
│                     [ ] Vinmec   [ ] Khác                   │
│                                                             │
│ Ai đang đau (Actor)? Tài xế nhận/bàn giao ca & Quản lý bãi  │
│                                                             │
│ Workflow thủ công hiện tại (3-5 bước):                      │
│   1. Đỗ xe tại bãi ──> 2. Đi vòng quanh chụp 8-12 góc ảnh   │
│   ──> 3. So đối chiếu ảnh ca trước ──> 4. Ký biên bản app   │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 2 & 3 (⏱ 15-20 min)   │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 3 (Computer      │
│ Vision so sánh và phát hiện sai khác/hư hại tự động).       │
│                                                             │
│ Đo thành công bằng gì (Metric có số)? Giảm thời gian giao   │
│ nhận ca từ 20 phút ──> dưới 2 phút/lượt; giảm 80% tranh chấp│
│                                                             │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] CV (Vision AI) │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #04                                      │
│                                                             │
│ Bài toán (1 câu): Tự động phát hiện bất thường quỹ đạo GPS  │
│ để đối soát khiếu nại cước phí và gian lận lộ trình.        │
│ Công ty thành viên: [ ] VinFast  [x] Xanh SM  [ ] Vinhomes  │
│                     [ ] Vinmec   [ ] Khác                   │
│                                                             │
│ Ai đang đau (Actor)? Khách hàng, Tài xế & Đội CSKH / Fraud  │
│                                                             │
│ Workflow thủ công hiện tại (3-5 bước):                      │
│   1. Khách gửi ticket khiếu nại đi đường vòng/bị cước cao   │
│   ──> 2. Agent mở log GPS chuyến đi đối soát tuyến đề xuất  │
│   ──> 3. Kiểm tra lý do (kẹt xe, đường cấm) ──> 4. Hoàn tiền│
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 2 & 3 (⏱ 8-15 min)    │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2 & 3 (Sequence  │
│ Matching & Spatial-Temporal GNN phát hiện lộ trình dị thường│
│                                                             │
│ Đo thành công bằng gì (Metric có số)? Giảm thời gian xử lý  │
│ ticket từ 12 phút ──> dưới 1 phút; tự động duyệt 70% case   │
│                                                             │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] ML / GNN       │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #05                                      │
│                                                             │
│ Bài toán (1 câu): Phân tích dữ liệu CAN-bus/Telematics thời │
│ gian thực để dự đoán suy thoái pin và lỗi động cơ điện.    │
│ Công ty thành viên: [x] VinFast  [x] Xanh SM  [ ] Vinhomes  │
│                     [ ] Vinmec   [ ] Khác                   │
│                                                             │
│ Ai đang đau (Actor)? Quản trị đội xe, Kỹ thuật viên xưởng & │
│                      Tài xế chạy xe ngoài đường.            │
│                                                             │
│ Workflow thủ công hiện tại (3-5 bước):                      │
│   1. Xe báo lỗi trên táp-lô hoặc đến kỳ bảo dưỡng km        │
│   ──> 2. Đưa xe vào xưởng VinFast, cắm máy quét OBD thủ công│
│   ──> 3. Kỹ thuật viên đọc mã DTC, đo dung lượng/áp cell    │
│   ──> 4. Đặt linh kiện pack pin thay thế & lên lịch sửa     │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 2 & 3 (⏱ 4-8 giờ nằm  │
│ xưởng kiểm tra; chờ linh kiện pin mất thêm 2-5 ngày).       │
│                                                             │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 1 & 2 (Mô hình   │
│ Time-Series / Digital Twin dự báo bất thường cell pin từ xa)│
│                                                             │
│ Đo thành công bằng gì (Metric có số)? Phát hiện hỏng hóc pin│
│ trước 10-14 ngày; giảm thời gian chết (downtime) 40%.       │
│                                                             │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] Time-Series AI │
└─────────────────────────────────────────────────────────────┘