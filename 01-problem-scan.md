# 01 — Problem Scan & Quick Cards (Vin Smart Future)

> **Thành viên:** Nguyễn Văn Ba (`nguyenvanba`)<br>
> **Mảng tập trung:** Xanh SM (GSM) — vận hành taxi/xe máy điện
>
> ⚠️ Các con số thời gian và metric trong file là **ước tính** để định hướng; cần xác minh với đội vận hành / dữ liệu thực trước khi Deep-Dive.

---

# 🔍 Phase 1 — SCAN

Dùng **4 Lenses** (Lặp lại · Tốn thời gian · AI có thể tốt hơn · Pain từ người khác) để quét vận hành của Xanh SM.

### 📝 List bài toán của tôi:
| # | Subsidiary | Lens | Mô tả ngắn bài toán |
|---|------------|------|---------------------|
| 1 | Xanh SM | Pain từ người khác | Đặt xe qua tổng đài cho người lớn tuổi: tổng đài viên hỏi và nhập tay địa chỉ, giờ đi; giờ cao điểm khách chờ máy lâu, địa chỉ hay bị nhập sai. |
| 2 | Xanh SM | Lặp lại | Phân loại phiếu báo lỗi xe của tài xế (xe kêu, điều hòa yếu, đèn báo) để quyết định chạy tiếp hay rút xe khỏi ca. |
| 3 | Xanh SM | Tốn thời gian | Xử lý báo cáo quấy rối/an toàn hành khách: case nghiêm trọng bị lẫn trong phàn nàn thông thường, đội an toàn đọc tay từng báo cáo. |
| 4 | Xanh SM | Lặp lại | Giải đáp thắc mắc thu nhập tài xế: đội hỗ trợ mở từng chuyến, tra chính sách thưởng/phạt rồi giải thích qua Zalo. |
| 5 | Xanh SM | AI có thể tốt hơn | Chatbot CSKH xử lý khiếu nại cước/hoàn tiền và đồ thất lạc: chỉ trả lời theo kịch bản cứng, không đọc được lịch sử chuyến nên phần lớn case vẫn phải chuyển nhân viên, khách chờ lâu. |

---

# 🃏 Phase 2 — QUICK-ASSESS: 3 Quick Problem Cards

Chọn top 3 từ danh sách SCAN: **#1 (Đặt xe qua tổng đài), #2 (Phân loại phiếu báo lỗi xe), #3 (Sàng lọc báo cáo an toàn).**

## Card #1 — Đặt xe qua tổng đài cho người lớn tuổi

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #1 — Đặt xe qua tổng đài                 │
│                                                             │
│ Bài toán (1 câu): Khách lớn tuổi không dùng app gọi hotline │
│   đặt xe; tổng đài viên hỏi và nhập tay địa chỉ, giờ cao    │
│   điểm khách chờ máy lâu và địa chỉ hay bị nhập sai.        │
│ Công ty thành viên: [ ] VinFast  [x] Xanh SM  [ ] Vinhomes  │
│                     [ ] Vinmec   [ ] Khác                   │
│                                                             │
│ Ai đang đau (Actor)? Tổng đài viên hotline Xanh SM (thao    │
│   tác); khách lớn tuổi (chờ máy, xe đến sai điểm); tài xế   │
│   (gọi lại tìm khách).                                      │
│                                                             │
│ Workflow thủ công hiện tại (5 bước):                        │
│ 1. Khách gọi hotline, chờ máy ──> 2. Tổng đài viên hỏi điểm │
│   đón, điểm đến, giờ đi ──> 3. Nhập tay địa chỉ, dò trên    │
│   bản đồ ──> 4. Đọc lại xác nhận, tạo cuốc ──> 5. Tài xế    │
│   gọi lại nếu không tìm thấy điểm đón                       │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 2-3 (~3-4 phút/cuộc   │
│   gọi, ước tính): địa chỉ đọc qua điện thoại (tên hẻm, tòa  │
│   chung cư, giọng địa phương) dễ nhập sai.                  │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 3: chuyển giọng  │
│   nói thành văn bản + LLM trích xuất điểm đón/điểm đến/giờ  │
│   đi thành form có cấu trúc, gợi ý 3 địa chỉ khớp nhất trên │
│   bản đồ; tổng đài viên chỉ chọn và xác nhận.               │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                       │
│ - Thời gian xử lý 1 cuộc gọi: ~4 phút ──> dưới 1.5 phút     │
│ - Tỉ lệ cuốc sai điểm đón do nhập địa chỉ: dưới 2%          │
│                                                             │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM  [ ] Agent │
└─────────────────────────────────────────────────────────────┘
```

## Card #2 — Phân loại phiếu báo lỗi xe của tài xế

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #2 — Phân loại phiếu báo lỗi xe          │
│                                                             │
│ Bài toán (1 câu): Tài xế gửi phiếu báo lỗi xe bằng văn bản  │
│   tự do (xe kêu, điều hòa yếu, đèn báo); kỹ thuật viên đọc  │
│   tay từng phiếu để quyết định xe chạy tiếp, vào xưởng sau  │
│   ca hay rút ngay khỏi ca.                                  │
│ Công ty thành viên: [ ] VinFast  [x] Xanh SM  [ ] Vinhomes  │
│                     [ ] Vinmec   [ ] Khác                   │
│                                                             │
│ Ai đang đau (Actor)? Kỹ thuật viên / đội vận hành đội xe    │
│   (đọc tay, gọi hỏi lại); tài xế (chờ quyết định, mất       │
│   cuốc).                                                    │
│                                                             │
│ Workflow thủ công hiện tại (5 bước):                        │
│ 1. Tài xế gửi phiếu (chữ/ảnh) qua app ──> 2. Phiếu vào hàng │
│   đợi chung theo thứ tự thời gian ──> 3. Kỹ thuật viên đọc, │
│   đoán nhóm lỗi, gọi tài xế hỏi thêm ──> 4. Quyết định:     │
│   chạy tiếp / xưởng sau ca / rút xe ngay ──> 5. Tạo lệnh    │
│   sửa chữa                                                  │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 2-3 (~5-10            │
│   phút/phiếu, ước tính): phiếu nguy hiểm (phanh, lái, cảnh  │
│   báo pin) nằm chung hàng với phiếu nhẹ (điều hòa yếu) nên  │
│   có thể bị xử lý muộn.                                     │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2-3: LLM đọc     │
│   phiếu, gắn nhóm lỗi + mức độ khẩn và đưa phiếu nguy hiểm  │
│   lên đầu hàng; rule cứng: phiếu có từ khóa an toàn (phanh, │
│   lái, pin, khói, cháy) luôn ở mức cao nhất. Kỹ thuật viên  │
│   vẫn là người quyết định rút xe.                           │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                       │
│ - Phiếu nguy hiểm được kỹ thuật viên xem trong dưới 10 phút │
│ - 0 phiếu nguy hiểm bị xếp mức thấp; phân loại đúng nhóm    │
│   lỗi >= 90% so với kỹ thuật viên                           │
│                                                             │
│ Quick Architecture: [ ] No AI  [x] Rule  [x] LLM  [ ] Agent │
│   (LLM phân loại + rule an toàn chặn dưới)                  │
└─────────────────────────────────────────────────────────────┘
```

## Card #3 — Sàng lọc báo cáo quấy rối/an toàn hành khách

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #3 — Sàng lọc báo cáo an toàn            │
│                                                             │
│ Bài toán (1 câu): Báo cáo quấy rối/an toàn của hành khách   │
│   bị lẫn trong khối lượng lớn phàn nàn thông thường (thái   │
│   độ, đi đường vòng), đội an toàn phải đọc tay từng báo cáo │
│   nên case nghiêm trọng bị phát hiện muộn.                  │
│ Công ty thành viên: [ ] VinFast  [x] Xanh SM  [ ] Vinhomes  │
│                     [ ] Vinmec   [ ] Khác                   │
│                                                             │
│ Ai đang đau (Actor)? Đội An toàn & Tin cậy / CSKH Xanh SM   │
│   (đọc tay); hành khách bị quấy rối (chờ được xử lý).       │
│                                                             │
│ Workflow thủ công hiện tại (5 bước):                        │
│ 1. Khách gửi phản ánh qua app/hotline/email ──> 2. Gộp vào  │
│   hàng đợi CSKH chung ──> 3. Nhân viên đọc tay, phân loại   │
│   ──> 4. Chuyển case nghiêm trọng cho đội an toàn ──> 5.    │
│   Đội an toàn liên hệ khách, tạm khóa tài xế, phối hợp cơ   │
│   quan chức năng nếu cần                                    │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 2-3 (~3 phút/báo cáo; │
│   case nghiêm trọng có thể chờ nhiều giờ, ước tính): không  │
│   có ưu tiên theo mức độ nghiêm trọng.                      │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 3: LLM sàng lọc, │
│   gắn cờ mức độ (khẩn cấp / nghiêm trọng / thông thường) và │
│   tóm tắt sự việc cho đội an toàn. AI không được tự khóa    │
│   tài khoản, không kết luận ai đúng ai sai, không tự trả    │
│   lời nạn nhân.                                             │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                       │
│ - Thời gian từ lúc nhận đến lúc đội an toàn xem case nghiêm │
│   trọng: nhiều giờ ──> dưới 30 phút                         │
│ - Gắn cờ đúng >= 98% case nghiêm trọng (ưu tiên không bỏ    │
│   sót)                                                      │
│                                                             │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM  [ ] Agent │
└─────────────────────────────────────────────────────────────┘
```

---
