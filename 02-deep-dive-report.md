Báo cáo phân tích sâu dự án AI --- Vinmec Bed Coordination Copilot

Bài toán được chọn: Tối ưu quy trình điều phối và phân bổ giường
bệnh tại Vinmec bằng AI hỗ trợ nhân viên điều phối.

Lưu ý: Các con số về thời gian và mức cải thiện dưới đây là
baseline giả định để phục vụ workshop, cần được Vinmec đo thực tế
trước khi triển khai production.

Phase 3 --- DEEP-DIVE

3.1. Current-State Workflow Mapping

Quy trình hiện tại

[1] Khoa/BS/Nurse gửi yêu cầu nhập viện/chuyển khoa
          |
          | 🔄 HANDOFF
          v
[2] Nhân viên điều phối tiếp nhận yêu cầu
          |
          v
[3] Kiểm tra tình trạng giường trống
    trên hệ thống / danh sách / trao đổi với khoa
          |
          | 🔴 BOTTLENECK
          | Kiểm tra nhiều nguồn + xác nhận lại trạng thái
          v
[4] Liên hệ khoa/phòng để xác nhận
    giường còn thực sự sử dụng được hay không
          |
          | 🔄 HANDOFF
          v
[5] Chọn giường phù hợp theo khoa/phòng,
    loại giường và tình trạng bệnh nhân
          |
          v
[6] Cập nhật / xác nhận phân bổ trên hệ thống
          |
          | 🔄 HANDOFF
          v
[7] Thông báo cho khoa/nhân viên liên quan
    và bệnh nhân/người nhà khi phù hợp

Ước tính thời gian vận hành

Bước                            Thời gian giả định Ghi chú

Tiếp nhận yêu cầu                        1--2 phút Kiểm tra thông tin
bệnh nhân/yêu cầu

Kiểm tra giường                          5--8 phút Có thể phải kiểm tra
nhiều nguồn

Xác nhận với khoa                        3--5 phút Gọi/nhắn tin/xác nhận
thủ công

Chọn và phân bổ                          2--3 phút Phụ thuộc điều kiện
giường                                             giường

Cập nhật + thông báo                     2--3 phút Cập nhật hệ thống và
thông tin liên quan

Tổng cộng               ~15--20 phút/lượt Baseline giả định

Bottleneck chính

🔴 Bước 3--4: kiểm tra và xác nhận trạng thái giường.

Nguyên nhân:

Trạng thái "trống" trên hệ thống có thể chưa phản ánh ngay tình
trạng thực tế.

Nhân viên phải đối chiếu thông tin giữa hệ thống và khoa/phòng.

Phải xác định giường có phù hợp với khoa, loại phòng và nhu cầu bệnh
nhân hay không.

Việc trao đổi qua nhiều kênh tạo thêm handoff và nguy cơ chậm/trùng
thông tin.

Các điểm Handoff

🔄 Yêu cầu từ bác sĩ/khoa điều trị → nhân viên điều phối.

🔄 Nhân viên điều phối → hệ thống quản lý giường.

🔄 Nhân viên điều phối → khoa/phòng để xác nhận.

🔄 Điều phối → khoa/nhân viên tiếp nhận/bệnh nhân.

3.2. Problem Statement --- 6-field

Field                               Nội dung chi tiết

1. Actor / Operator             Nhân viên điều phối giường / điều
dưỡng / nhân sự vận hành tại
khoa. Họ tiếp nhận yêu cầu nhập
viện/chuyển khoa, kiểm tra giường,
liên hệ khoa và cập nhật trạng thái
phân bổ. Các bác sĩ và khoa điều
trị là stakeholder cung cấp yêu cầu
hoặc xác nhận nhu cầu.

2. Current Workflow             Khi có yêu cầu nhập viện/chuyển
khoa, nhân viên tiếp nhận thông tin
bệnh nhân và yêu cầu → kiểm tra
danh sách/trạng thái giường → đối
chiếu với khoa/phòng → xác định
giường phù hợp → cập nhật hệ thống
→ thông báo cho bên liên quan. Công
cụ có thể gồm hệ thống quản lý bệnh
viện/EMR, danh sách giường và các
kênh liên lạc nội bộ.

3. Bottleneck                   Việc tìm kiếm, đối chiếu và xác
nhận trạng thái giường là bước
tốn thời gian nhất. Đây là tác vụ
lặp lại, cần tổng hợp thông tin từ
nhiều nguồn và dễ xảy ra sai lệch
khi trạng thái thay đổi nhanh. AI
đặc biệt phù hợp ở khâu tổng hợp dữ
liệu và đề xuất lựa chọn; quyết
định phân bổ cuối cùng vẫn cần
người có thẩm quyền.

4. Business Impact              Baseline giả định: khoảng
15--20 phút/lượt điều phối. Khi
số lượng yêu cầu tăng, thời gian
điều phối làm tăng thời gian chờ
nhập viện/chuyển khoa và tạo thêm
khối lượng liên lạc cho nhân viên.
Cần đo thực tế các KPI: thời gian
xử lý mỗi yêu cầu, thời gian bệnh
nhân chờ giường, số lần liên hệ/xác
nhận và tỷ lệ phân bổ lại do thông
tin không chính xác.

5. Success Metric               Mục tiêu prototype: giảm thời
gian xử lý trung bình từ ~15--20
phút xuống dưới 5 phút/lượt đối
với các trường hợp đủ dữ liệu;
≥90% yêu cầu có đề xuất giường
hợp lệ trong ≤30 giây; giảm
≥50% thời gian nhân viên dành cho
việc tìm kiếm/đối chiếu; không
làm tăng tỷ lệ phân bổ sai. Các
ngưỡng này cần được hiệu chỉnh sau
khi có baseline thực tế.

3.3. Future-State Flow & AI Fit

AI-Fit Matrix

Thành phần              Mức độ phù hợp          Vai trò

Rule /                Cao                 Kiểm tra điều kiện
State-Machine                                 cứng: loại giường,
khoa, trạng thái, quyền
truy cập, điều kiện
không được vi phạm.

LLM Feature         Cao                 Hiểu yêu cầu bằng ngôn
ngữ tự nhiên, tóm tắt
yêu cầu, giải thích lý
do đề xuất, tạo bản
nháp thông báo.

Kết luận AI Fit

[x] Rule / State-Machine

[x] LLM Feature

[x] Agentic Loop --- có kiểm soát

Kiến trúc phù hợp nhất cho prototype là Rule + LLM + Agent có
HITL, thay vì để LLM tự quyết định việc phân bổ giường.

Future-State Flow

                    ┌──────────────────────────────┐
                    │ Yêu cầu nhập viện/chuyển khoa│
                    └──────────────┬───────────────┘
                                   |
                                   v
                    ┌──────────────────────────────┐
                    │ Rule: kiểm tra dữ liệu đầu vào│
                    │ đủ? hợp lệ? có mâu thuẫn?     │
                    └──────────────┬───────────────┘
                                   |
                     +-------------+-------------+
                     |                           |
                  Không đủ                    Đủ dữ liệu
                     |                           |
                     v                           v
              ↩️ FALLBACK              🔵 AI STEP — Agent
              Yêu cầu bổ sung          truy vấn trạng thái
              / chuyển người           giường được cấp quyền
                                               |
                                               v
                                      🔵 AI STEP — Rule
                                      lọc các giường không hợp lệ
                                               |
                                               v
                                      🔵 AI STEP — LLM
                                      xếp hạng + giải thích
                                      các lựa chọn phù hợp
                                               |
                                               v
                                      🟢 HITL — Nhân viên
                                      review đề xuất
                                               |
                              +----------------+----------------+
                              |                                 |
                           Reject                            Approve
                              |                                 |
                              v                                 v
                         ↩️ FALLBACK                    🔵 AI tạo draft
                         Tìm phương án khác             thông báo/cập nhật
                                                               |
                                                               v
                                                     🟢 HITL xác nhận
                                                               |
                                                               v
                                                     Cập nhật hệ thống
                                                     / thông báo chính thức

AI Step

AI Step 1 --- Chuẩn hóa yêu cầu

Trích xuất thông tin từ yêu cầu:

khoa/phòng;

loại yêu cầu: nhập viện/chuyển khoa;

loại giường;

mức độ ưu tiên;

các điều kiện vận hành liên quan.

Nếu thông tin không đủ → không đoán → yêu cầu bổ sung.

AI Step 2 --- Tổng hợp trạng thái giường

Truy vấn các nguồn dữ liệu được phép.

Chuẩn hóa trạng thái về một schema thống nhất.

Đánh dấu dữ liệu có timestamp cũ hoặc có dấu hiệu mâu thuẫn.

AI Step 3 --- Rule-based filtering

Loại các giường không đáp ứng điều kiện bắt buộc.

Rule có quyền ưu tiên cao hơn LLM.

Không cho LLM "lách" các điều kiện cứng.

AI Step 4 --- LLM ranking/explanation

AI tạo danh sách đề xuất:

{
  "recommendations": [
    {
      "bed_id": "B-XXX",
      "score": 0.92,
      "reason": "Phù hợp khoa và loại giường; trạng thái được cập nhật gần nhất."
    }
  ],
  "needs_human_review": true
}

Đây chỉ là đề xuất, không phải quyết định phân bổ.

Human-in-the-loop (HITL)

HITL là bắt buộc tại các điểm:

Trước khi xác nhận giường.

Khi có nhiều lựa chọn tương đương.

Khi dữ liệu giường có timestamp quá cũ hoặc mâu thuẫn.

Khi yêu cầu chứa điều kiện đặc biệt.

Khi AI không đạt ngưỡng confidence tối thiểu.

Trước mọi thao tác làm thay đổi trạng thái chính thức trên hệ thống.

Nguyên tắc

AI recommends --- Human decides --- System executes only after
approval.

Fallback

Fallback 1 --- Thiếu dữ liệu

Nếu thiếu thông tin bắt buộc:

AI → không đề xuất
   → hiển thị trường thông tin còn thiếu
   → nhân viên bổ sung
   → chạy lại workflow

Fallback 2 --- Dữ liệu mâu thuẫn

AI phát hiện:
"Trạng thái hệ thống = trống"
nhưng
"nguồn xác nhận gần nhất = đang sử dụng"

→ Không tự chọn giường
→ Gắn cờ REVIEW_REQUIRED
→ Nhân viên xác minh

Fallback 3 --- LLM không tự tin

Nếu confidence dưới threshold đã quy định:

→ Không đưa ra recommendation cuối
→ Chuyển toàn bộ case cho nhân viên
→ Ghi log lý do fallback

Fallback 4 --- Hệ thống AI/API lỗi

→ Quay về quy trình điều phối thủ công hiện tại
→ Không làm gián đoạn việc tiếp nhận bệnh nhân
→ Ghi nhận incident để đánh giá

Phase 5 --- EVALUATE

5.1. AI Readiness Checklist

#                Tiêu chí          Đánh giá          Nhận xét

1             Chúng tôi có sẵn  🟡 NOT YET    Cần thu thập lịch
dữ liệu mẫu/logs                    sử yêu cầu điều
sạch để test?                       phối, trạng thái
giường theo
timestamp, kết
quả phân bổ và
thời gian xử lý.
Có thể bắt đầu
prototype bằng dữ
liệu giả
lập/anonymized.

2             Rủi ro khi AI sai 🟢 YES        Có thể giới hạn
có nằm trong tầm                    AI ở vai trò đề
kiểm soát qua                       xuất; Rule kiểm
HITL/Fallback?                      soát điều kiện
cứng; Human duyệt
trước khi cập
nhật hệ thống.
Fallback đưa case
bất thường về quy
trình thủ công.

5.2. Đánh giá tổng thể

Điểm mạnh

Bài toán có workflow rõ ràng và có nhiều thao tác lặp lại.

Bottleneck tương đối dễ đo bằng thời gian xử lý.

AI có thể tạo giá trị ngay ở khâu tìm kiếm, tổng hợp và đề xuất.

Có thể thiết kế HITL để giảm rủi ro.

Có thể bắt đầu bằng một scope nhỏ, không cần tự động hóa toàn bộ quy
trình.

Rủi ro chính

Dữ liệu trạng thái giường không realtime hoặc không đồng nhất.

AI đề xuất dựa trên dữ liệu đã lỗi thời.

Người dùng quá tin tưởng recommendation của AI.

Tích hợp với hệ thống bệnh viện có thể phức tạp.

Các trường hợp đặc biệt không thể bao phủ đầy đủ bằng prompt.

Cách giảm rủi ro

Luôn hiển thị nguồn + timestamp của dữ liệu.

Rule-based validation trước khi LLM ranking.

Không cho LLM trực tiếp thực hiện thao tác ghi dữ liệu.

Bắt buộc HITL đối với quyết định cuối.

Có confidence threshold và fallback.

Log toàn bộ recommendation, decision của người dùng và kết quả thực
tế để đánh giá.

5.3. Quyết định cuối cùng

[x] GO --- Bắt đầu xây dựng Prototype

[ ] NOT YET --- Cần tích lũy thêm dữ liệu/xác lập baseline

[ ] NO-GO --- Không khả thi / Rule-based tốt hơn

Justification

GO với scope hẹp và theo hướng Human-in-the-loop.

Bài toán điều phối giường có đặc điểm phù hợp để thử nghiệm AI: workflow
lặp lại, bottleneck có thể xác định rõ và hiệu quả có thể đo bằng thời
gian xử lý. AI không cần thay thế nhân viên; giá trị lớn nhất của
prototype nằm ở việc tự động tổng hợp trạng thái giường, lọc các lựa
chọn không phù hợp và đưa ra recommendation có giải thích.

Tuy nhiên, chưa nên cho AI tự động phân bổ hoặc thay đổi trạng thái
giường. Đây là nghiệp vụ có tác động trực tiếp đến vận hành bệnh viện,
vì vậy quyết định cuối phải thuộc về nhân viên có thẩm quyền.

Prototype nên được triển khai theo 3 bước:

Phase 1
Offline / Historical Data
        ↓
Đo baseline + kiểm tra recommendation
        ↓
Phase 2
Shadow Mode
AI đưa đề xuất nhưng không tác động hệ thống
        ↓
So sánh AI recommendation với quyết định thực tế
        ↓
Phase 3
HITL Production Pilot
AI đề xuất → Nhân viên duyệt → Hệ thống thực hiện

Điều kiện để chuyển từ Prototype → Pilot

Cần đạt tối thiểu:

Recommendation hợp lệ ≥90% trên tập test được kiểm định.

Thời gian tạo recommendation ≤30 giây cho case đủ dữ liệu.

Giảm ít nhất 50% thời gian tìm kiếm/đối chiếu của nhân viên.

Không tăng tỷ lệ phân bổ sai so với baseline.

100% thao tác thay đổi trạng thái chính thức vẫn có kiểm soát của
người có thẩm quyền.

Có fallback hoạt động ổn định khi AI/API lỗi hoặc dữ liệu không hợp
lệ.

Kết luận

AI không thay nhân viên điều phối giường; AI đóng vai trò "copilot"
giúp nhân viên tìm đúng thông tin nhanh hơn, giảm thao tác đối chiếu
và đưa ra lựa chọn có căn cứ. Con người vẫn giữ quyền quyết định cuối
cùng.

Kiến trúc đề xuất:

Data Sources
    ↓
Rule / Validation
    ↓
Agent — Retrieve & Aggregate
    ↓
LLM — Rank + Explain
    ↓
🟢 Human Review
    ↓
Official System Action
    ↓
Audit Log / Feedback

Recommendation: GO --- Prototype với scope hẹp, triển khai Shadow
Mode trước, sau đó mới chuyển sang HITL Pilot.