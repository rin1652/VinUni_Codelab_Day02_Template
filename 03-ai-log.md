# 📝 03 - AI Collaboration Log & Engineering Reflection

**Học viên / Vai trò:** Nguyễn Phúc — Trưởng nhóm (Team Leader) & AI Product Engineer  
**Khóa học:** Lab 02 — AI Product Scoping (Vin Smart Future)  
**Mục tiêu bài viết:** Ghi nhận trung thực quá trình sử dụng AI (Gemini / AI Assistant) như một đối tác tư duy (Thought-Partner), phân tích các điểm AI làm tốt, các lỗi sai sót của AI và cách tôi định hướng, kiểm soát chất lượng kỹ thuật.

---

## 🤝 1. AI Đã Giúp Gì Cho Tôi? (What AI Did Well)

Trong suốt buổi Lab, tôi đã sử dụng AI không phải như một công cụ copy-paste, mà như một chuyên gia tư vấn kỹ thuật và phản biện độc lập (adversarial peer reviewer):

1. **Tăng tốc quét bài toán (Phase 1 SCAN):**
   - AI đã giúp tôi nhanh chóng tổng hợp và đối chiếu các hoạt động vận hành đặc thù giữa các công ty thành viên trong hệ sinh thái Vingroup (GSM Xanh SM, VinFast, Vinhomes, Vinmec).
   - Giúp tôi phân loại chính xác các bài toán vào 4 lăng kính (Lặp lại, Tốn thời gian, AI-upgrade, Pain từ người khác).

2. **Chuẩn hóa cấu trúc Problem Statement 6-Field (Phase 3):**
   - Khi tôi mô tả bối cảnh điều phối xe Xanh SM theo ngôn ngữ nghiệp vụ thông thường, AI đã hỗ trợ tôi cấu trúc lại thành 6 trường dữ liệu chuẩn của Vin Smart Future.
   - Đặc biệt, AI giúp tôi lượng hóa các chỉ số thành công (Success Metrics) với số liệu cụ thể: giảm ETA từ 12 phút xuống dưới 6 phút, độ chính xác dự báo ≥ 80%.

3. **Lập trình và Stress-Test Prompt Prototype (Phase 4):**
   - AI hỗ trợ viết cấu trúc mã nguồn Python tương tác với SDK mới `google-genai`.
   - Hỗ trợ xây dựng các ca kiểm thử tấn công (Adversarial Tests) đa dạng: cố tình ép bỏ qua thẻ `[DRAFT_ONLY]`, dụ AI tiết lộ system prompt bí mật, và gửi yêu cầu điều xe pin cạn kiệt đến trạm sạc xa.

---

## ❌ 2. AI Đã Sai Gì & Đưa Ra Lời Khuyên Kém? (Where AI Failed)

Là một kỹ sư trưởng nhóm, tôi đã phát hiện nhiều điểm yếu và sai sót nghiêm trọng của AI nếu người dùng cả tin chấp nhận ngay:

1. **Lỗi lựa chọn mô hình lỗi thời (Model Deprecation 404 Error):**
   - Khi chạy code mẫu ban đầu, AI sử dụng định danh `gemini-2.5-flash`. Khi thực thi, hệ thống trả về lỗi: `404 NOT_FOUND: This model models/gemini-2.5-flash is no longer available to new users`. 
   - AI không tự động nhận biết được rằng Google đã ngừng cấp phát phiên bản này cho các dự án/API key mới và cần chuyển sang model GA `gemini-3.6-flash`.

2. **Thiên kiến "Ảo tưởng tự trị" (Autonomous Agent Bias):**
   - Trong phiên brainstorm ban đầu về kiến trúc giải pháp, AI liên tục đề xuất xây dựng một "Full Autonomous Dispatching Agent" có khả năng tự động kết nối API tổng đài và tự động gửi lệnh điều chuyển hàng trăm tài xế mà không cần sự can thiệp của con người.
   - Đây là một đề xuất cực kỳ nguy hiểm và thiếu thực tế trong vận hành giao thông đô thị: nếu mô hình gặp ảo giác (hallucination) hoặc lỗi dữ liệu thời tiết, nó có thể điều động dồn hàng trăm xe vào một nút giao đang kẹt xe, gây tê liệt mạng lưới và thiệt hại hàng tỷ đồng cho Xanh SM.

3. **Bỏ quên các ràng buộc vật lý đặc thù của Xe Điện (EV Constraints):**
   - AI ban đầu coi xe taxi Xanh SM như xe xăng truyền thống: chỉ tính toán khoảng cách địa lý đơn thuần từ điểm A đến điểm B.
   - AI hoàn toàn bỏ quên yếu tố trạng thái pin (State of Charge - SoC), tốc độ hao hụt pin khi bật điều hòa vào giờ cao điểm nắng nóng, và khoảng cách đến trụ sạc dự phòng.

---

## 🎯 3. Tôi Đã Sửa Đổi, Phản Biện & Định Hướng AI Như Thế Nào? (How I Steered AI)

Để đảm bảo giải pháp đạt tiêu chuẩn kỹ thuật nghiêm ngặt của Vin Smart Future, tôi đã thực hiện các can thiệp quyết định:

1. **Ép buộc kiến trúc Human-in-the-Loop (HITL) & Ranh giới `[DRAFT_ONLY]`:**
   - Tôi kiên quyết bác bỏ đề xuất Agent tự trị của AI, giáng cấp kiến trúc xuống **LLM Feature (Co-Pilot)**.
   - Tôi thiết lập quy tắc ranh giới bất khả xâm phạm: Mọi khuyến nghị của AI bắt buộc phải có thẻ `[DRAFT_ONLY]`, chỉ đóng vai trò trợ lý soạn nháp cho Điều phối viên (Dispatcher) phê duyệt một chạm.

2. **Bổ sung các Guardrails an toàn năng lượng cho xe điện:**
   - Tôi bổ sung thêm điều kiện tiên quyết vào System Prompt: Xe có mức pin dưới 20% tuyệt đối không được điều động di chuyển liên vùng; nếu pin dưới 5% phải chuyển ngay sang trạng thái điều xe cứu hộ pin di động (`dispatch_mobile_charger`).

3. **Khắc phục lỗi kỹ thuật mã nguồn:**
   - Tôi trực tiếp cập nhật định danh mô hình thành `gemini-3.6-flash`.
   - Cấu hình cơ chế đọc API Key tự động từ `.env` và script kích hoạt môi trường ảo `.venv`, giải quyết dứt điểm lỗi `API Key status: MISSING` và giúp script chạy mượt mà đạt chuẩn kiểm thử Autograder với điểm số tuyệt đối.

---

## 💡 4. Bài Học Rút Ra Cho Bản Thân (Key Engineering Takeaways)

1. **AI chỉ là Co-Pilot, Con người là Captain:** Trong các hệ thống vận hành quy mô lớn như Vingroup / Xanh SM, sự can thiệp của con người (HITL) và cơ chế Fallback dự phòng là lằn ranh bảo vệ doanh nghiệp trước các rủi ro thảm họa.
2. **Boundary Engineering quan trọng hơn Prompt Engineering:** Một prompt viết hay nhưng không có ranh giới cấm (Operational Boundaries) rõ ràng và không được kiểm thử bằng các bài test tấn công (Adversarial Testing) thì không bao giờ sẵn sàng cho môi trường Production.
3. **Thái độ hoài nghi mang tính xây dựng (Constructive Skepticism):** Luôn kiểm chứng mọi dòng code, mọi đề xuất kiến trúc và mọi tham số mà AI đưa ra bằng thực nghiệm và chạy code trực tiếp trên terminal.
