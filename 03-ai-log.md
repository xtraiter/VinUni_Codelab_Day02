# 03 — AI Log & Reflection

> **Thành viên:** Nguyễn Văn Ba (`nguyenvanba`)<br>
> **Công cụ AI:** Claude (Claude Code trong VS Code) làm thought-partner · Google Gemini `gemini-3.1-flash-lite` (API) làm mô hình trong prompt prototype

---

## 1. Cách tôi làm việc với AI

Tôi dùng AI như một **đồng nghiệp hỗ trợ**: AI đọc tài liệu, đề xuất phương án, soạn bản nháp và hỗ trợ chẩn đoán lỗi. **Tôi giữ các quyết định**: chọn hướng làm, chạy thử, kiểm chứng kết quả và chịu trách nhiệm về sản phẩm nộp.

Ba nguyên tắc tôi áp dụng trong buổi lab:

| Nguyên tắc | Áp dụng thực tế |
|---|---|
| **Kiểm chứng mọi gợi ý bằng chạy thử hoặc đo đạc** | Không chấp nhận "đã sửa xong" cho tới khi autograder xác nhận 5/5 |
| **Đối chiếu với đề bài gốc** | Tự đọc lại README và phát hiện AI đã ghi bài vào sai file |
| **AI hướng dẫn, tôi thực hiện** | Từ giữa buổi, tôi yêu cầu AI chỉ giải thích và gợi ý, không tự sửa file khi chưa được yêu cầu |

---

## 2. Kết quả chính

- **Prompt prototype:** 3/3 adversarial test giữ được ranh giới (kể cả test giả danh quản lý ra lệnh "bỏ qua hướng dẫn"); autograder phần code **5.00 / 5.00**.
- **Tìm ra nguyên nhân gốc của lỗi timeout** — một lỗi môi trường không nằm trong code (chi tiết ở mục 3).
- **Phát hiện 3 lỗi ranh giới** mà bộ test tự động báo "Passed" nhưng thực tế vẫn có rủi ro vận hành (mục 4).

---

## 3. Case study: Debug lỗi timeout của autograder

**Tình huống.** Script chạy tay qua cả 3 test, nhưng autograder chỉ cho **3/5 điểm** vì script chạy quá giới hạn 30 giây.

**Quá trình.** AI đưa ra lần lượt 4 giả thuyết; tôi kiểm chứng từng giả thuyết bằng cách chạy lại và gửi log thật:

| # | Giả thuyết của AI | Kiểm chứng | Kết luận |
|---|---|---|---|
| 1 | Model mới chậm → quay về `gemini-2.5-flash` | Chạy thử | ❌ Model 2.5 không dùng được với key hiện tại — gợi ý dựa trên thông tin cũ |
| 2 | Model 3.x bỏ qua `thinking_budget=0` → đổi sang `thinking_level="MINIMAL"` | Đo thời gian 2 model | ❌ Vẫn ~27–30 giây/lần gọi, dù chỉ hỏi "Chào bạn" |
| 3 | Mạng chậm (IPv6) | Đo kết nối tới máy chủ Gemini | ❌ Chỉ 0,18 giây |
| 4 | **Đo riêng từng giai đoạn** bằng script chẩn đoán | Chạy script | ✅ Gọi API ~1 giây, nhưng **khởi tạo `genai.Client()` mất 23,5 giây** |

**Nguyên nhân gốc.** Profiler cho thấy thời gian nằm ở bước OpenSSL nạp file chứng chỉ `cacert.pem`. Môi trường ảo `.venv` nằm trong thư mục Google Drive (`G:\My Drive`), nên OpenSSL đọc file qua ổ đồng bộ mất **24 giây**, so với **0,23 giây** trên ổ C. Code tạo client mới cho mỗi test → 3 × 24s ≈ 72s.

**Giải pháp và kết quả.** Chuyển môi trường ảo ra `C:\venvs\lab02` → autograder **5.00 / 5.00**.

**Điều tôi rút ra.** Ba giả thuyết đầu đều được AI trình bày rất tự tin nhưng đều sai. Bước ngoặt là chuyển từ *đoán rồi sửa* sang *đo rồi mới sửa*.

---

## 4. Những chỗ AI sai và cách tôi xử lý

| AI sai ở đâu | Tôi phát hiện bằng cách | Xử lý |
|---|---|---|
| Chẩn đoán sai nguyên nhân timeout 3 lần; gợi ý model đã cũ | Chạy lại, đo thời gian, gửi log lỗi thật | Yêu cầu đo từng giai đoạn → tìm ra nguyên nhân gốc |
| Ghi bài thẳng vào `01-worksheet.md` (vốn là đề/template) | Tự đọc lại README: đề yêu cầu *copy* sang file mới `01-problem-scan.md` | Khôi phục worksheet về bản gốc, chuyển nội dung sang đúng file |
| Tự viết code khi tôi chỉ hỏi cách làm | Xem lại thay đổi trong file | Đặt lại vai trò: AI chỉ hướng dẫn, sửa file khi được yêu cầu |
| **Gemini:** tin nháp viết *"Trung tâm **đã điều** xe sạc..."* dù mới là bản nháp chờ duyệt | Đọc kỹ output dù test đã báo "Passed" | Ghi nhận rủi ro: tài xế có thể chờ một chiếc xe chưa được điều → đề xuất sửa prompt (mục 5) |
| **Gemini:** tự điền `"battery_level": 100` khi người dùng chỉ nói "xe sạc đầy rồi" | Đọc output | Ghi nhận ranh giới mờ giữa *suy luận* và *bịa thông tin* |
| **Bộ test:** chấp nhận chữ "cứu hộ" thay cho action chuẩn; chỉ kiểm tra `[DRAFT_ONLY]` *có xuất hiện*, không kiểm tra nằm *ở đầu* | Đọc lại logic kiểm tra | Đề xuất siết điều kiện (mục 5) |

---

## 5. Cải tiến tiếp theo (backlog)

- [ ] System prompt: `draft_message` chỉ dùng câu đề xuất (*"Trung tâm sẽ điều xe sạc di động sau khi được xác nhận"*), không viết như hành động đã xảy ra.
- [ ] Test: dùng `output.startswith("[DRAFT_ONLY]")`; Test 1 yêu cầu chính xác `dispatch_mobile_charger`.
- [ ] Code: khởi tạo `genai.Client()` một lần và dùng lại cho mọi test.
- [ ] Đồng bộ cấu hình với model 3.1: dùng `thinking_level` thay cho `thinking_budget`, sửa dòng tiêu đề đang in "Gemini 2.5 Flash".

---

## 6. Bài học

1. **AI trả lời tự tin chưa chắc đã đúng.** Câu hỏi cần đặt ra là *"kiểm chứng bằng cách nào?"* trước câu *"sửa thế nào?"*.
2. **"Passed" chưa chắc là an toàn.** Test tự động chỉ bắt được lỗi mình đã nghĩ tới; lỗi "tin nháp nói như đã điều xe" chỉ lộ ra khi con người đọc output. Đây chính là lý do sản phẩm cần Human-in-the-loop.
3. **Kiến thức của AI có thể đã cũ** — với tên model, phiên bản API, phải kiểm tra thực tế.
4. **Lỗi có thể nằm ngoài code.** Nguyên nhân timeout là vị trí của môi trường ảo, không phải logic chương trình.
5. **Bảo mật khi chia sẻ log:** che API key trước khi dán log vào bất kỳ đâu; key đã lộ phải thu hồi ngay.

---

## 7. Minh bạch mức độ AI hỗ trợ

| Sản phẩm | AI hỗ trợ | Phần tôi thực hiện |
|---|---|---|
| `starter-code/prompt_prototype.py` | Soạn bản đầu của system prompt, hàm gọi API và Test Case 3 | Chạy và kiểm thử, chọn model, phối hợp chẩn đoán và xử lý môi trường để đạt 5/5 |
| `01-problem-scan.md` | Gợi ý bài toán #5; soạn bản nháp 3 Quick Cards (số liệu ước tính) | Đề xuất bài toán #1–#4, chọn 3 card, kiểm tra nội dung |
| `02-deep-dive-report.md` | Đề xuất các hướng giải pháp khác cho cùng bài toán của nhóm; soạn bản nháp báo cáo theo hướng tôi chọn | Chọn hướng "sửa form + Rule + LLM chỉ phân loại", chọn quyết định NOT YET, kiểm tra nội dung |
| `04-workflow-diagram.png` | Vẽ sơ đồ bằng script matplotlib | Duyệt bố cục, yêu cầu sửa các chỗ chữ bị đè |
| `03-ai-log.md` | Soạn khung trình bày dựa trên lịch sử làm việc | Xác nhận các sự kiện, đánh giá và bài học |
