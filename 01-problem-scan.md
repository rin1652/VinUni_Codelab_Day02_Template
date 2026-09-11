# Lab 02 — Worksheet: AI Product Scoping (Vin Smart Future)

---

## 🏛️ 1. Bối cảnh thực tế: Vin Smart Future (Vingroup)

**Vingroup** — Tập đoàn tư nhân lớn nhất Việt Nam — vừa sáp nhập toàn bộ các phòng ban công nghệ thuộc các công ty thành viên thành một đơn vị công nghệ thống nhất mang tên **Vin Smart Future**. 

Trong buổi Lab hôm nay, nhóm chúng tôi đóng vai trò là **AI Product Engineer** tại **Vin Smart Future**, tiến hành tìm kiếm, scoping, phân tích độ khả thi và thiết lập ranh giới vận hành cho các bài toán kinh doanh cốt lõi.

---

# 🔍 Phase 1 — SCAN (Cá nhân)

Sử dụng 4 Lenses để quét qua hoạt động vận hành của các công ty thành viên Vingroup. Dưới đây là 5 bài toán/bottleneck thực tế mà nhóm đã xác định được:

### 📝 List bài toán của tôi:
| # | Subsidiary | Lens | Mô tả ngắn bài toán |
|---|------------|------|---------------------|
| 1 | **Vinschool** | Tốn thời gian | Giáo viên chủ nhiệm tốn hàng chục giờ cuối mỗi học kỳ để tổng hợp điểm số, hành vi và soạn thảo Báo cáo Đánh giá Học sinh (IEP) cá nhân hóa cho từng em. |
| 2 | **VinWonders** | Pain từ khách hàng | Khách hàng phàn nàn vì thời gian xếp hàng tại các trò chơi hot (vd: Tàu lượn siêu tốc) quá lâu, trong khi các khu vực khác lại vắng khách, gây lãng phí công suất vận hành. |
| 3 | **VinFast** | AI-upgrade | KCS (Kiểm tra chất lượng) dùng mắt thường để phát hiện các vết xước vi mô trên lớp sơn thân xe xuất xưởng, dễ sai sót do mỏi mắt và thiếu tính nhất quán. |
| 4 | **Vincom Retail** | Lặp lại | Đội pháp chế tốn hàng tuần rà soát hàng trăm hợp đồng thuê mặt bằng thương mại khổng lồ để tìm kiếm các điều khoản dị biệt, rủi ro pháp lý ẩn. |
| 5 | **Xanh SM** | Tốn thời gian | Điều phối viên xử lý thủ công các phản hồi khẩn cấp từ tài xế về sự cố sạc pin/hết pin giữa đường, rủi ro xe chết máy giữa đường nếu điều phối trạm sạc sai lệch. |

---

# 🃏 Phase 2 — QUICK-ASSESS (Cá nhân)

Từ danh sách trên, nhóm tiến hành chọn lọc và xây dựng 3 Quick Problem Cards chi tiết để phân tích sơ bộ độ khả thi:

### 🎓 QUICK PROBLEM CARD #1 (Giáo Dục)
```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #1                                       │
│                                                             │
│ Bài toán: Tự động hóa soạn thảo Báo cáo Đánh giá Học sinh   │
│ cá nhân hóa (IEP) vào cuối kỳ cho giáo viên chủ nhiệm.      │
│ Công ty thành viên: [x] Vinschool                           │
│                                                             │
│ Ai đang đau (Actor)? Giáo viên chủ nhiệm (quá tải khối      │
│ lượng công việc hành chính cuối kỳ).                        │
│                                                             │
│ Workflow thủ công hiện tại (4 bước):                        │
│   1. Thu thập điểm số và nhận xét từ các môn học            │
│   ──> 2. Đọc lại sổ ghi chép hành vi học sinh trong kỳ      │
│   ──> 3. Ngồi gõ thủ công báo cáo dài 2-3 trang/học sinh    │
│   ──> 4. Gửi Tổ trưởng chuyên môn duyệt và gửi phụ huynh    │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 3 (⏱ 30 phút/báo cáo) │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 3                │
│ (AI tổng hợp data + voice note của GV để draft báo cáo)     │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                       │
│ Giảm thời gian soạn thảo từ 30 phút ──> dưới 3 phút/học sinh│
│ (Giảm 90% thời gian làm việc hành chính).                   │
│                                                             │
│ Quick Architecture: [x] LLM Feature                         │
└─────────────────────────────────────────────────────────────┘
```

### 🎢 QUICK PROBLEM CARD #2 (Vui chơi Giải trí)
```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #2                                       │
│                                                             │
│ Bài toán: AI điều hướng đám đông linh hoạt dựa trên dữ liệu │
│ xếp hàng thời gian thực để nâng cao trải nghiệm khách.      │
│ Công ty thành viên: [x] VinWonders                          │
│                                                             │
│ Ai đang đau (Actor)? Khách hàng (mệt mỏi), Giám đốc Vận     │
│ hành (lãng phí công suất hệ thống).                         │
│                                                             │
│ Workflow thủ công hiện tại (4 bước):                        │
│   1. Khách hàng tự xem bản đồ tĩnh để tìm trò chơi          │
│   ──> 2. Đi bộ tới nơi mới phát hiện hàng chờ dài 60 phút   │
│   ──> 3. Khách hàng bực bội đứng chờ hoặc bỏ đi chơi trò nhẹ│
│   ──> 4. Ban quản lý nhận review phàn nàn trên TripAdvisor  │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 1 & 2                 │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2                │
│ (AI push notification Zalo gợi ý trò chơi đang vắng khách   │
│ kèm mã giảm giá đồ uống tại khu vực đó để kéo khách sang)   │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                       │
│ Giảm thời gian chờ đợi trung bình của khách hàng xuống 30%  │
│ và tăng 15% doanh thu F&B tại các phân khu vắng.            │
│                                                             │
│ Quick Architecture: [x] Agentic Loop (Theo dõi và Action)   │
└─────────────────────────────────────────────────────────────┘
```

### 🚕 QUICK PROBLEM CARD #3 (Vận hành & Di chuyển)
```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #3                                       │
│                                                             │
│ Bài toán: Trợ lý AI phân tích rủi ro pin và điều phối cứu   │
│ hộ/trạm sạc khẩn cấp cho đội xe taxi điện.                  │
│ Công ty thành viên: [x] Xanh SM                             │
│                                                             │
│ Ai đang đau (Actor)? Điều phối viên (áp lực thời gian       │
│ thực), Tài xế (nguy cơ chết máy giữa đường).                │
│                                                             │
│ Workflow thủ công hiện tại (4 bước):                        │
│   1. Tài xế gọi tổng đài báo sự cố sạc/hết pin              │
│   ──> 2. Điều phối viên tra cứu thủ công vị trí xe & trạm   │
│   ──> 3. Ước lượng rủi ro lượng pin còn lại bằng cảm tính   │
│   ──> 4. Soạn tin nhắn hướng dẫn hoặc gọi đội cứu hộ pin    │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 2 & 3 (⏱ 10 phút)     │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2 & 3 & 4        │
│ (AI kéo data GPS + Battery level ──> Kiểm tra ranh giới an  │
│ toàn ──> Tự động soạn Draft SMS chỉ đường hoặc Cứu hộ)      │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                       │
│ Giảm thời gian xử lý sự cố khẩn cấp từ 10 phút ──> 1 phút.  │
│ Triệt tiêu 100% rủi ro điều phối sai khiến xe chết máy.     │
│                                                             │
│ Quick Architecture: [x] LLM Feature (kết hợp HITL)          │
└─────────────────────────────────────────────────────────────┘
```
