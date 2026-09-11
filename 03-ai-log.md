# AI Log & Reflection

## 1. Tôi đã dùng AI như thế nào

Tôi dùng AI như một thought-partner trong ba hoạt động:

1. Brainstorm các pain point vận hành ở Vinhomes, Vinpearl, Xanh SM, VinFast và
   Vinmec theo bốn lenses của worksheet.
2. So sánh ba phương án rule-based, LLM feature và agentic loop cho bài toán
   phân loại phản ánh cư dân.
3. Stress-test problem statement bằng cách yêu cầu AI đóng vai CFO/Trưởng vận
   hành và tìm các metric thiếu cơ sở, rủi ro dữ liệu và quyền hạn quá rộng.

AI cũng hỗ trợ tổ chức nội dung thành bảng, làm rõ Human-in-the-loop, fallback và
các tiêu chí chuyển từ NOT YET sang GO.

## 2. Prompt đã sử dụng và kết quả

### Prompt 1 — Brainstorm

> Tôi là AI Product Engineer tại Vin Smart Future. Hãy đề xuất các bottleneck
> vận hành cụ thể ở Vinhomes, Xanh SM, Vinpearl, VinFast và Vinmec theo bốn
> lenses: repetitive, time-consuming, AI-upgrade và stakeholder pain. Với mỗi ý
> tưởng, hãy nêu actor, workflow và metric có thể đo. Không được trình bày số liệu
> ước tính như dữ liệu thật.

**Kết quả hữu ích:** AI tạo được nhiều lựa chọn để so sánh và chỉ ra rằng tác vụ
có ngôn ngữ tự do nhưng đầu ra bị giới hạn bởi taxonomy là một scope tốt cho LLM.

**Điểm chưa tốt:** Một số gợi ý ban đầu quá rộng, chẳng hạn “trợ lý cư dân toàn
diện”, và không chỉ rõ bước nào tạo ra bottleneck. Tôi thu hẹp thành một quyết
định cụ thể: đề xuất category, priority và queue cho một ticket.

### Prompt 2 — Phản biện như stakeholder

> Hãy đóng vai CFO và Trưởng vận hành khắt khe. Phản biện đề xuất dùng LLM để
> phân loại phản ánh cư dân. Tìm các giả định chưa có bằng chứng, giải thích phần
> nào rule-based đủ tốt, và đề xuất điều kiện dừng pilot nếu chất lượng thấp.

**Kết quả hữu ích:** AI chỉ ra rằng volume, thời gian xử lý và tỷ lệ chuyển nhầm
không thể tự suy ra. AI cũng đề xuất shadow mode và đo recall riêng cho nhóm khẩn
cấp thay vì chỉ báo cáo accuracy tổng thể.

**Điểm chưa tốt:** AI từng đề xuất tự động route các ticket có confidence cao.
Đề xuất này vượt quá mức rủi ro phù hợp cho pilot vì confidence của LLM không
đồng nghĩa với xác suất đúng đã được hiệu chỉnh.

### Prompt 3 — Thiết kế ranh giới

> Với use case triage phản ánh cư dân, hãy lập bảng “AI được phép / không được
> phép / phải chuyển người”. Ưu tiên privacy, nội dung khẩn cấp, pháp lý, tài
> chính và fallback khi model hoặc API lỗi. AI chỉ được tạo đề xuất.

**Kết quả hữu ích:** Prompt giúp tách rõ chức năng hiểu ngôn ngữ của LLM khỏi lớp
rule kiểm tra schema, allowlist và escalation. Tôi giữ nhân viên ở bước duyệt và
giữ manual workflow làm fallback.

## 3. AI đã sai hoặc hallucinate ở đâu

Rủi ro lớn nhất là AI có thể viết các con số nghe hợp lý như số ticket mỗi ngày,
thời gian trung bình hoặc chi phí tiết kiệm dù không có dữ liệu nội bộ. Tôi không
coi các con số đó là fact. Trong báo cáo, **500 ticket/ngày**, **9 phút/ticket**
và các baseline liên quan đều được ghi rõ là giả định pilot cần xác minh.

AI cũng có xu hướng gọi mọi workflow nhiều bước là “agent”. Sau khi phân tích,
tôi loại agentic loop vì bài toán chỉ cần một lần phân loại có cấu trúc; quyền
thay đổi trạng thái ticket vẫn thuộc hệ thống deterministic và con người.

Cuối cùng, AI có thể đề xuất dùng confidence threshold như một đảm bảo an toàn.
Tôi sửa lại: threshold `0.80` chỉ là điểm khởi đầu phải được calibration trên dữ
liệu thực; các nhóm khẩn cấp vẫn cần rule escalation và human review dù model tự
tin cao.

## 4. Tôi đã sửa prompt và scope như thế nào

- Thay yêu cầu chung “tự động xử lý phản ánh” bằng output cụ thể gồm summary,
  category, priority, suggested queue, confidence và missing fields.
- Thêm câu “không được trình bày số liệu ước tính như dữ liệu thật”.
- Yêu cầu AI so sánh với phương án rule-based trước khi chọn LLM.
- Quy định AI không gửi phản hồi, không cam kết bồi thường/SLA, không đóng ticket
  và không đưa quyết định kỷ luật.
- Thêm negative test cho nội dung thiếu vị trí, đa ý định, tài chính/pháp lý và
  tình huống an toàn khẩn cấp.
- Đổi quyết định từ GO sang **NOT YET** vì dữ liệu và mức sẵn sàng stakeholder
  chưa được xác minh.

## 5. Bài học cá nhân

Bài học quan trọng nhất là bắt đầu từ workflow và quyền quyết định, không bắt đầu
từ tên công nghệ. AI hữu ích để mở rộng không gian ý tưởng và phản biện nhanh,
nhưng câu trả lời trôi chảy dễ che giấu giả định không có bằng chứng. Người làm
sản phẩm phải truy ngược mỗi metric về log, chủ sở hữu dữ liệu và cách đo.

Tôi cũng nhận ra “human-in-the-loop” chỉ có ý nghĩa khi xác định rõ người duyệt,
thông tin họ nhìn thấy, SLA duyệt và fallback khi họ không phản hồi. Trong scope
này, LLM là công cụ hỗ trợ ra quyết định; nhân viên vận hành vẫn chịu trách nhiệm
cho hành động chuyển ticket.

## 6. Việc cần xác minh sau buổi lab

- Phỏng vấn nhân viên CSKH để xác nhận current-state workflow và thời gian từng
  bước.
- Lấy thống kê volume, routing error và re-open/reassign rate trong bốn tuần.
- Kiểm tra chất lượng taxonomy và mức đồng thuận giữa các annotator.
- Làm rõ retention, consent, masking và quyền truy cập dữ liệu cư dân.
- Thử nghiệm prompt trên held-out dataset và ghi lại cả lỗi nghiêm trọng, không
  chỉ các ví dụ thành công.
