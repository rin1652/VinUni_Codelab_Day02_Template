# Deep-Dive Report — Vinhomes Resident Complaint Triage

> **Phạm vi:** Hệ thống hỗ trợ phân loại và điều hướng phản ánh cư dân.
>
> **Giả định:** Các giá trị baseline và volume dưới đây là giả định phục vụ thiết
> kế pilot, chưa phải số liệu nội bộ đã được xác minh.

## 1. Current-State Workflow

Phản ánh có thể chứa tiếng Việt không dấu, lỗi chính tả, nhiều vấn đề trong cùng
một tin nhắn hoặc thông tin vị trí chưa đầy đủ. Nhân viên CSKH hiện phải diễn
giải nội dung trước khi chuyển ticket.

| Bước | Người/Hệ thống | Input | Hoạt động | Output | Thời gian giả định |
|---:|---|---|---|---|---:|
| 1 | Cư dân/App | Nội dung, ảnh, tài khoản | Gửi phản ánh | Ticket mới | 1 phút |
| 2 🔄 | CSKH | Ticket mới | Kiểm tra thông tin tòa/căn hộ và hỏi bổ sung nếu thiếu | Ticket đủ dữ liệu | 2 phút |
| 3 🔴 | CSKH | Nội dung tự do | Đọc, chọn category, mức ưu tiên và queue phụ trách | Ticket đã phân loại | 4 phút |
| 4 🔄 | Hệ thống ticket | Ticket đã phân loại | Chuyển sang Ban quản lý/An ninh/Kỹ thuật/Vệ sinh | Ticket trong queue | <1 phút |
| 5 | Đội xử lý | Ticket | Xác nhận đúng phạm vi; nhận xử lý hoặc trả lại | Accepted/Reassigned | 2 phút nếu đúng; lâu hơn nếu sai |

**Tổng thời gian thao tác trực tiếp giả định:** khoảng **9 phút/ticket** khi thông
tin tương đối đầy đủ. Hai handoff chính nằm giữa cư dân và CSKH, rồi giữa CSKH và
đội xử lý. Bottleneck là bước 3; chuyển sai còn tạo vòng lặp quay lại bước 3.

## 2. Problem Statement 6-field

| Field | Nội dung |
|---|---|
| **1. Actor / Operator** | Nhân viên CSKH hoặc nhân viên Ban quản lý đang tiếp nhận và phân luồng ticket cư dân. Stakeholder liên quan gồm cư dân và các đội An ninh, Kỹ thuật, Vệ sinh, Kế toán. |
| **2. Current Workflow** | Nhân viên đọc nội dung tự do, kiểm tra thông tin vị trí, chọn category/priority trong hệ thống ticket và chuyển tới queue phụ trách. Khi chọn sai hoặc thiếu dữ liệu, ticket bị trả lại và phải phân loại lại. |
| **3. Bottleneck** | Việc hiểu câu chữ không chuẩn, tách nhiều ý định và lựa chọn taxonomy phù hợp mất khoảng 4 trong tổng số 9 phút thao tác. Nhân viên mới dễ chọn sai queue. |
| **4. Business Impact** | Với giả định pilot **500 ticket/ngày**, riêng 4 phút phân loại tương đương khoảng **33 giờ công/ngày**. Chuyển nhầm làm tăng thời gian phản hồi và khiến cư dân phải mô tả lại vấn đề. Đây là giả thuyết cần xác nhận bằng log. |
| **5. Success Metric** | (a) ≥85% top-level category accuracy trên test set; (b) ≥95% recall cho nhóm khẩn cấp; (c) median triage <2 phút; (d) routing error ≤5%; (e) ít nhất 80% đề xuất được nhân viên chấp nhận sau pilot. |
| **6. Operational Boundary** | AI chỉ đề xuất category, priority, queue và bản tóm tắt. AI **không** tự gửi phản hồi cuối cho cư dân, không cam kết SLA/bồi thường, không thay đổi phí, không đóng ticket và không đưa quyết định kỷ luật. Ticket khẩn cấp, confidence thấp, nội dung pháp lý/tài chính hoặc dữ liệu thiếu bắt buộc được con người duyệt. |

## 3. AI Fit Analysis

| Phương án | Điểm mạnh | Điểm yếu | Quyết định |
|---|---|---|---|
| **Rule / State Machine** | Rẻ, dễ kiểm tra; tốt với mã tòa, trường bắt buộc và từ khóa rõ ràng. | Kém với câu nhập tự do, lỗi chính tả, nhiều ý định và ngữ cảnh. | Dùng làm lớp validation và routing policy. |
| **LLM Feature** | Hiểu ngôn ngữ tự nhiên, tóm tắt và phân loại theo taxonomy; có thể trả confidence/reason. | Có thể hallucinate hoặc chọn nhãn không tồn tại; cần đánh giá và HITL. | **Chọn cho pilot.** |
| **Agentic Loop** | Có thể tự tra cứu và thực hiện nhiều bước. | Quá phức tạp cho scope; tăng quyền truy cập và rủi ro hành động sai. | Chưa dùng. |

Kiến trúc phù hợp là **LLM Feature + deterministic rules + Human-in-the-loop**.
LLM không được quyền gọi hành động thay đổi trạng thái ticket trong pilot.

## 4. Future-State Flow

1. Cư dân gửi phản ánh qua app.
2. Rule kiểm tra trường bắt buộc, chuẩn hóa ID dự án/tòa và loại bỏ dữ liệu không
   cần thiết khỏi prompt.
3. 🔵 **AI Step:** LLM trả về JSON gồm `summary`, `category`, `priority`,
   `suggested_queue`, `confidence`, `missing_fields` và `reason`.
4. Rule kiểm tra schema, chỉ cho phép category/queue trong danh mục và áp dụng
   từ khóa escalation bắt buộc.
5. Nếu confidence thấp, thiếu dữ liệu, có nhiều ý định, hoặc thuộc nhóm khẩn
   cấp/pháp lý/tài chính, ticket đi thẳng tới hàng chờ review.
6. 🟢 **Human Step:** Nhân viên xem nội dung gốc và đề xuất; chấp nhận, sửa hoặc
   yêu cầu cư dân bổ sung thông tin.
7. Chỉ sau khi nhân viên xác nhận, hệ thống mới chuyển ticket tới queue xử lý.
8. Nhãn đã sửa được ghi vào evaluation dataset sau khi loại bỏ/che dữ liệu cá
   nhân theo chính sách.

### Fallback

- API/model timeout hoặc JSON sai schema → quay về màn hình phân loại thủ công.
- Category không thuộc allowlist → không route, chuyển CSKH review.
- Confidence dưới ngưỡng pilot (đề xuất ban đầu: `0.80`) → manual review.
- Phản ánh có từ khóa cháy, khói, mắc kẹt, đe dọa an toàn → rule cảnh báo và
  chuyển người trực ngay; không chờ kết luận của LLM.
- Drift hoặc routing error vượt 5% trong một tuần → tắt auto-suggestion cho nhóm
  nhãn bị ảnh hưởng và điều tra.

## 5. Pilot and Evaluation Plan

### Dữ liệu và thiết kế thử nghiệm

1. Lấy một tập ticket lịch sử đã được phê duyệt quyền sử dụng và khử/che dữ liệu
   cá nhân không cần thiết.
2. Hai annotator vận hành gán nhãn theo taxonomy; bất đồng được trưởng nhóm xử lý.
3. Chia dữ liệu theo thời gian thành development set và held-out test set để
   giảm rò rỉ mẫu gần giống.
4. Chạy shadow mode trong hai tuần: AI chỉ đề xuất, không tác động routing.
5. So sánh với baseline nhân viên về accuracy, macro-F1, recall khẩn cấp, thời
   gian triage và tỷ lệ sửa đề xuất.

### AI Readiness Checklist

- [ ] **Có dữ liệu mẫu/log sạch:** Chưa xác nhận. Cần audit taxonomy, quyền dùng
  dữ liệu và tỷ lệ nhãn sai trước pilot.
- [x] **Rủi ro AI sai có thể kiểm soát:** Có, nếu duy trì schema validation,
  allowlist, escalation rule, HITL và manual fallback.
- [ ] **Stakeholder sẵn sàng đổi workflow:** Chưa xác nhận. Cần workshop với
  CSKH và ít nhất hai đội nhận ticket để chốt taxonomy và màn hình duyệt.

## 6. Final Decision

**Quyết định: NOT YET — cần xác lập baseline và chuẩn bị dữ liệu, sau đó mới chạy
pilot shadow mode.**

Use case có AI fit tốt và rủi ro có thể giới hạn vì mô hình chỉ tạo đề xuất.
Tuy nhiên, hiện chưa có bằng chứng rằng taxonomy nhất quán, dữ liệu đủ sạch, con
số 500 ticket/ngày là đúng, hoặc stakeholder chấp nhận quy trình mới. Vì vậy,
quyết định GO ngay sẽ dựa trên giả định chưa kiểm chứng.

Điều kiện chuyển sang **GO**:

- Xác nhận baseline volume, median triage và routing-error rate từ log tối thiểu
  bốn tuần.
- Có data owner phê duyệt cách xử lý dữ liệu cá nhân.
- Có tối thiểu 1.000 ticket được gán nhãn/kiểm tra chất lượng cho pilot.
- CSKH và các đội nhận ticket thống nhất taxonomy, escalation rule và SLA review.
- Kế hoạch shadow mode, rollback và người chịu trách nhiệm monitoring được ký
  duyệt.

Nếu không đạt ≥95% recall với ticket khẩn cấp trong held-out test, hệ thống không
được mở rộng ngoài shadow mode; nhóm phải cải thiện dữ liệu/rules hoặc thu hẹp
phạm vi nhãn.
