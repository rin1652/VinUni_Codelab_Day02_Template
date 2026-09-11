### 📝 List bài toán của tôi:
| # | Subsidiary (VinFast/Xanh SM...) | Lens | Mô tả ngắn bài toán |
|---|----------------------------------|------|---------------------|
| 1 |Vinmec|Repetitive – Lặp lại|Theo dõi và nhắc bệnh nhân tái khám: Nhân viên phải lập danh sách bệnh nhân đến hạn tái khám, gọi điện/nhắn tin nhắc, ghi nhận phản hồi và cập nhật lịch. Quy trình lặp lại hằng ngày với số lượng lớn bệnh nhân.
| 2 |Vinmec|Time-consuming – Tốn thời gian|Tổng hợp và kiểm tra kết quả xét nghiệm/hình ảnh trước khi bác sĩ hội chẩn: Nhân viên phải tập hợp kết quả từ LIS/PACS/EMR, kiểm tra xem hồ sơ đã đủ dữ liệu chưa và chuẩn bị thông tin cho bác sĩ. Việc tìm kiếm và tổng hợp thủ công có thể làm chậm quá trình hội chẩn.|
| 3 |Vinmec|AI-upgrade – AI có thể tốt hơn|Trợ lý giải thích hướng dẫn sau khám: Bệnh nhân nhận nhiều hướng dẫn về thuốc, lịch tái khám, xét nghiệm, chế độ theo dõi... nhưng thông tin thường mang tính mẫu và bệnh nhân vẫn phải hỏi lại khi chưa hiểu. AI có thể chuyển hướng dẫn chuyên môn thành nội dung dễ hiểu, cá nhân hóa theo từng bệnh nhân và hỗ trợ Q&A trong phạm vi được phép.|
| 4 |Vinmec|Stakeholder Pain – Pain từ người khác|Bệnh nhân phải chờ để được cập nhật tình trạng hồ sơ/kết quả: Khi kết quả xét nghiệm hoặc chẩn đoán hình ảnh chưa hoàn tất, bệnh nhân có thể phải hỏi lễ tân/điều dưỡng nhiều lần. Nhân viên cũng phải liên tục kiểm tra hệ thống và trả lời các câu hỏi trạng thái tương tự.|
| 5 |Vinmec|Stakeholder Pain – Pain từ người khác|Điều phối giường bệnh: Khi bệnh nhân nhập viện/xuất viện/chuyển khoa, nhân viên phải cập nhật tình trạng giường, liên hệ các khoa và tìm giường phù hợp. Thông tin thay đổi liên tục, trong khi việc phân bổ thủ công dễ tạo bottleneck và khiến bệnh nhân phải chờ.|

┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #1                                      │
│                                                             │
│ Bài toán (1 câu): Điều phối giường bệnh thủ công khiến     │
│ nhân viên mất thời gian tìm giường phù hợp và bệnh nhân    │
│ phải chờ khi nhập viện hoặc chuyển khoa.                   │
│                                                             │
│ Công ty thành viên: [ ] VinFast  [ ] Xanh SM  [ ] Vinhomes │
│                     [x] Vinmec   [ ] Khác (Ghi rõ)________  │
│                                                             │
│ Ai đang đau (Actor)? Điều dưỡng/nhân viên điều phối giường,│
│ khoa điều trị và bệnh nhân.                                 │
│                                                             │
│ Workflow thủ công hiện tại (3-5 bước):                     │
│   1. Nhận yêu cầu → 2. Kiểm tra giường → 3. Xác nhận với   │
│   khoa → 4. Phân bổ giường & cập nhật hệ thống → 5.        │
│   Thông báo cho bệnh nhân/khoa                              │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Kiểm tra & đối chiếu       │
│ tình trạng giường (⏱ ~10–15 phút/lượt)                     │
│                                                             │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2–4: tổng hợp   │
│ tình trạng giường, dự đoán giường sắp trống và đề xuất     │
│ giường phù hợp.                                             │
│                                                             │
│ Đo thành công bằng gì (Metric có số)? Giảm thời gian phân  │
│ bổ giường từ ~15 phút → <5 phút/lượt; giảm thời gian chờ   │
│ giường 20%.                                                 │
│                                                             │
│ Quick Architecture: [ ] No AI  [x] Rule  [x] LLM  [x] Agent│
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #2                                      │
│                                                             │
│ Bài toán (1 câu): Nhân viên/bác sĩ mất nhiều thời gian tìm  │
│ kiếm và tổng hợp kết quả xét nghiệm, hình ảnh và bệnh án   │
│ trước mỗi buổi hội chẩn.                                   │
│                                                             │
│ Công ty thành viên: [ ] VinFast  [ ] Xanh SM  [ ] Vinhomes │
│                     [x] Vinmec   [ ] Khác (Ghi rõ)________  │
│                                                             │
│ Ai đang đau (Actor)? Bác sĩ điều trị, bác sĩ hội chẩn và   │
│ nhân viên y tế hỗ trợ.                                      │
│                                                             │
│ Workflow thủ công hiện tại (3-5 bước):                     │
│   1. Nhận yêu cầu → 2. Tìm bệnh án → 3. Mở kết quả xét    │
│   nghiệm/hình ảnh → 4. Tổng hợp thông tin → 5. Chuẩn bị    │
│   hồ sơ hội chẩn                                             │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Tìm kiếm & tổng hợp dữ    │
│ liệu từ nhiều nguồn (⏱ ~15–30 phút/bệnh nhân)              │
│                                                             │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2–4: truy xuất  │
│ thông tin liên quan và tạo bản tóm tắt hồ sơ theo timeline,│
│ làm nổi bật kết quả bất thường.                             │
│                                                             │
│ Đo thành công bằng gì (Metric có số)? Giảm thời gian chuẩn │
│ bị hồ sơ từ ~20 phút → <5 phút/bệnh nhân; giảm 50% thời    │
│ gian tìm kiếm dữ liệu.                                     │
│                                                             │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM  [x] Agent│
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #3                                      │
│                                                             │
│ Bài toán (1 câu): Nhân viên phải thủ công lập danh sách và  │
│ liên hệ hàng loạt bệnh nhân đến hạn tái khám, gây tốn      │
│ thời gian và dễ bỏ sót.                                     │
│                                                             │
│ Công ty thành viên: [ ] VinFast  [ ] Xanh SM  [ ] Vinhomes │
│                     [x] Vinmec   [ ] Khác (Ghi rõ)________  │
│                                                             │
│ Ai đang đau (Actor)? Nhân viên CSKH/điều phối và bệnh nhân.│
│                                                             │
│ Workflow thủ công hiện tại (3-5 bước):                     │
│   1. Kiểm tra danh sách → 2. Xác định bệnh nhân cần nhắc   │
│   → 3. Gọi điện/nhắn tin → 4. Ghi nhận phản hồi → 5.       │
│   Cập nhật hoặc đặt lại lịch                               │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Liên hệ & xử lý phản hồi  │
│ của từng bệnh nhân (⏱ ~5–10 phút/bệnh nhân)                │
│                                                             │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 1–4: xác định   │
│ bệnh nhân đến hạn, phân nhóm ưu tiên, gửi nhắc lịch cá     │
│ nhân hóa và xử lý phản hồi đơn giản.                       │
│                                                             │
│ Đo thành công bằng gì (Metric có số)? Giảm thời gian xử    │
│ lý từ ~7 phút → <2 phút/bệnh nhân; giảm 60% workload nhắc  │
│ lịch thủ công.                                             │
│                                                             │
│ Quick Architecture: [ ] No AI  [x] Rule  [x] LLM  [x] Agent│
└─────────────────────────────────────────────────────────────┘