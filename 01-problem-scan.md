# Problem Scan & Quick Problem Cards

> **Bối cảnh:** Bài làm cá nhân cho Lab 02 — AI Product Scoping.
>
> **Lưu ý về dữ liệu:** Các con số trong tài liệu là giả định để thiết kế và
> đánh giá pilot, không phải số liệu vận hành chính thức của Vingroup. Trước khi
> triển khai, nhóm cần xác nhận lại bằng log và phỏng vấn stakeholder.

## Phase 1 — SCAN

Tôi dùng bốn lenses: tác vụ lặp lại, tác vụ tốn thời gian, dịch vụ có thể được
nâng cấp bằng AI, và pain point của stakeholder.

| # | Subsidiary | Lens | Mô tả ngắn bài toán |
|---:|---|---|---|
| 1 | Vinhomes | Lặp lại | Nhân viên CSKH đọc phản ánh tự do của cư dân, gắn loại sự cố và chuyển thủ công đến đúng ban quản lý hoặc đội kỹ thuật. |
| 2 | Vinpearl | Tốn thời gian | Nhân viên tổng hợp đánh giá từ nhiều kênh, dịch nội dung và gom các phàn nàn khẩn cấp để báo cho quản lý khách sạn. |
| 3 | Xanh SM | Pain từ người khác | Điều phối viên đọc ghi chú và nghe cuộc gọi để phân loại nguyên nhân hủy chuyến, khiến báo cáo nguyên nhân bị chậm. |
| 4 | VinFast | AI-upgrade | Cố vấn dịch vụ phải chuyển mô tả tiếng Việt đời thường của khách thành nhóm triệu chứng kỹ thuật trước khi đặt lịch kiểm tra xe. |
| 5 | Vinmec | Tốn thời gian | Nhân viên hành chính phải tổng hợp nhiều phần trong bệnh án thành bản nháp tóm tắt xuất viện để bác sĩ kiểm tra. |
| 6 | Vinhomes | Pain từ người khác | Cư dân phải chờ nhân viên tra cứu và giải thích thủ tục đăng ký thi công, thẻ xe hoặc sử dụng tiện ích. |

## Phase 2 — QUICK-ASSESS

Ba bài toán được chọn để đánh giá nhanh là #1, #2 và #3. Tiêu chí chọn gồm tần
suất, khả năng đo lường, mức sẵn có của dữ liệu và khả năng kiểm soát hậu quả khi
AI dự đoán sai.

### Quick Problem Card 1 — Phân loại và điều hướng phản ánh cư dân

| Trường | Nội dung |
|---|---|
| **Bài toán** | Phân loại phản ánh bằng ngôn ngữ tự do của cư dân và đề xuất đúng đội xử lý. |
| **Công ty thành viên** | Vinhomes |
| **Actor** | Nhân viên CSKH/Ban quản lý; người chịu ảnh hưởng cuối là cư dân và đội kỹ thuật. |
| **Workflow hiện tại** | 1. Cư dân gửi phản ánh → 2. CSKH đọc và chuẩn hóa thông tin → 3. Chọn category, mức ưu tiên và đội xử lý → 4. Chuyển ticket → 5. Đội nhận ticket kiểm tra hoặc trả lại. |
| **Bottleneck** | Đọc, diễn giải và chọn category/đội xử lý ở bước 2–3; giả định baseline **6 phút/ticket**. |
| **AI hỗ trợ** | Trích xuất tòa/căn hộ nếu có, phân loại chủ đề, phát hiện từ khóa khẩn cấp và tạo đề xuất routing kèm confidence. |
| **Metric pilot** | Ít nhất **85%** ticket top-level category đúng; giảm median triage từ **6 phút xuống dưới 2 phút**; tỷ lệ chuyển nhầm không vượt **5%**. |
| **Quick Architecture** | **LLM Feature + rules**: LLM hiểu nội dung; rule kiểm tra trường bắt buộc và ánh xạ category sang queue. Không dùng agent tự trị. |

### Quick Problem Card 2 — Phát hiện phàn nàn khẩn cấp trong review Vinpearl

| Trường | Nội dung |
|---|---|
| **Bài toán** | Tóm tắt review đa ngôn ngữ và đưa các phàn nàn cần xử lý sớm vào danh sách của quản lý. |
| **Công ty thành viên** | Vinpearl |
| **Actor** | Guest Relations và quản lý vận hành khách sạn. |
| **Workflow hiện tại** | 1. Nhân viên mở từng kênh review → 2. Dịch/đọc nội dung → 3. Gắn chủ đề và mức nghiêm trọng → 4. Tổng hợp bảng báo cáo → 5. Chuyển quản lý. |
| **Bottleneck** | Đọc, dịch và tổng hợp ở bước 2–4; giả định **8 phút/review** đối với review dài hoặc ngoại ngữ. |
| **AI hỗ trợ** | Dịch, tóm tắt, gắn nhiều nhãn và đánh dấu nội dung liên quan an toàn/vệ sinh để con người ưu tiên đọc. |
| **Metric pilot** | Recall tối thiểu **95%** đối với tập review khẩn cấp đã gán nhãn; giảm thời gian xử lý xuống dưới **2 phút/review**; 100% cảnh báo khẩn cấp được người quản lý duyệt. |
| **Quick Architecture** | **LLM Feature** kết hợp danh sách từ khóa/rule cảnh báo bắt buộc. |

### Quick Problem Card 3 — Phân loại nguyên nhân hủy chuyến Xanh SM

| Trường | Nội dung |
|---|---|
| **Bài toán** | Chuẩn hóa ghi chú và transcript cuộc gọi thành taxonomy nguyên nhân hủy chuyến phục vụ phân tích vận hành. |
| **Công ty thành viên** | Xanh SM |
| **Actor** | Chuyên viên vận hành và nhân viên Quality Assurance. |
| **Workflow hiện tại** | 1. Thu thập ghi chú/transcript → 2. Nhân viên đọc mẫu → 3. Chọn mã nguyên nhân → 4. Nhập bảng tổng hợp → 5. Phân tích xu hướng. |
| **Bottleneck** | Việc đọc và gán nhãn thủ công ở bước 2–3; giả định **4 phút/trường hợp**. |
| **AI hỗ trợ** | Tóm tắt nguyên nhân, gán một hoặc nhiều mã trong taxonomy cố định, và trả confidence để QA lấy mẫu kiểm tra. |
| **Metric pilot** | Macro-F1 tối thiểu **0,85** trên tập test; giảm thời gian gán nhãn xuống dưới **1 phút/trường hợp**; không dùng kết quả để tự động xử phạt tài xế. |
| **Quick Architecture** | **LLM Feature** cho phân loại offline; batch pipeline và human sampling, không cần agent. |

## Lựa chọn để Deep-Dive

Tôi chọn **Card 1 — Phân loại và điều hướng phản ánh cư dân Vinhomes** vì:

- Đầu vào ngôn ngữ tự do phù hợp với năng lực phân loại/trích xuất của LLM.
- Quy trình có taxonomy và queue rõ ràng nên có thể kết hợp rule-based validation.
- Hiệu quả có thể đo bằng thời gian triage, độ chính xác và tỷ lệ chuyển nhầm.
- Có thể bắt đầu ở chế độ đề xuất, giữ nhân viên CSKH trong vòng duyệt để giới
  hạn rủi ro.

Card 2 chưa được chọn vì dữ liệu nằm trên nhiều nền tảng và cần giải quyết quyền
truy cập trước. Card 3 có giá trị phân tích nhưng tác động tới SLA của khách hàng
không trực tiếp bằng Card 1.
