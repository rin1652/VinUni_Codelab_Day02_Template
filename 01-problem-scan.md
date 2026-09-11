# 🔍 01 - Problem Scan & Quick Problem Cards (Vin Smart Future)

**Dự án:** AI Product Scoping — Vin Smart Future (Vingroup)  
**Tác giả / Trưởng nhóm:** Nguyễn Phúc (AI Product Engineer & Team Leader)  
**Đơn vị mục tiêu:** GSM — Taxi Điện Xanh SM  
**Ngày thực hiện:** Tháng 9, 2026

---

# 🔍 Phase 1 — SCAN: Quét tìm cơ hội (4 Lenses)

Áp dụng phương pháp luận **4 Lenses** quét qua toàn bộ hoạt động vận hành của các công ty thành viên trong hệ sinh thái Vingroup để tìm kiếm các điểm nghẽn (bottlenecks) và cơ hội tối ưu hóa bằng AI:

| #   | Subsidiary   | Lens               | Mô tả bài toán                                                                                                                                   |
| --- | ------------ | ------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------ |
| 1   | **Vinhomes** | AI-upgrade         | AI tự động điều chỉnh nhiệt độ điều hòa theo từng khu vực dựa trên mật độ/phân bổ cư dân thực tế để tối ưu năng lượng tòa nhà.                   |
| 2   | **Vinhomes** | Pain từ người khác | Cảm biến + AI cảnh báo sớm nguy cơ chập điện, đồng thời định vị chính xác khu vực có nguy cơ sự cố trước khi xảy ra cháy nổ.                     |
| 3   | **Vinmec**   | AI-upgrade         | AI phân tích dữ liệu đo, hình ảnh nhiệt và chỉ số sinh hiệu để phát hiện sớm dấu hiệu bất thường ở bệnh nhân trước khi triệu chứng biểu hiện rõ. |
| 4   | **VinFast**  | Pain từ người khác | AI theo dõi quãng đường, tình trạng pin và các thông số vận hành để chủ động cảnh báo bảo dưỡng/sạc trước khi xe gặp sự cố.                      |
| 5   | **Xanh SM**  | Lặp lại            | AI phân tích dữ liệu đặt xe theo khu vực và khung giờ, từ đó đề xuất phương án phân bổ xe tối ưu cho điều phối viên.                             |
| 6   | **Vinhomes** | Tốn thời gian      | Robot + AI tự động vận chuyển hàng hóa/đơn đặt hàng trong khu đô thị đến từng căn hộ, giảm công việc giao nhận thủ công.                         |
| 7   | **Vinpearl** | AI-upgrade         | AI phân tích sở thích, thời gian lưu trú và các điểm tham quan phù hợp để **cá nhân hóa lịch trình du lịch cho khách**.                          |

---

# 🃏 Phase 2 — QUICK-ASSESS: 3 Quick Problem Cards

Chọn lọc ra **Top 3 bài toán tiềm năng nhất** từ danh sách trên (#5 Xanh SM, #4 VinFast, #2 Vinhomes) để phân tích sơ bộ qua Quick Problem Cards:

---

### 📇 QUICK PROBLEM CARD #1 (Bài toán nhóm chọn — Từ Bài #5 Xanh SM)

```text
┌─────────────────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #1                                                   │
│                                                                         │
│ Bài toán: Điều phối viên Xanh SM phân bổ xe theo khu vực/khung giờ      │
│ chủ yếu dựa vào kinh nghiệm cá nhân, dẫn đến xe dồn sai chỗ — nơi       │
│ thừa xe rảnh, nơi khách chờ lâu.                                        │
│ Công ty thành viên: [x] Xanh SM (GSM)                                   │
│                                                                         │
│ Ai đang đau?                                                            │
│ - Điều phối viên: Quá tải nhận thức, stress giờ cao điểm.               │
│ - Tài xế Xanh SM: Đứng chờ ở vùng ế khách, giảm thu nhập.               │
│ - Khách hàng: Chờ xe lâu (ETA > 12 phút), tỉ lệ hủy chuyến cao.         │
│                                                                         │
│ Workflow thủ công hiện tại (5 bước):                                    │
│   1. Theo dõi bản đồ realtime số xe và cuốc đặt theo khu vực            │
│   ──> 2. Ước lượng khu vực sắp "nóng" dựa trên kinh nghiệm cá nhân     │
│   ──> 3. Nhắn tin/gọi điện yêu cầu nhóm tài xế di chuyển đến khu vực    │
│   ──> 4. Theo dõi tài xế có tuân thủ di chuyển đến điểm nóng không      │
│   ──> 5. Điều chỉnh lại thủ công nếu dự đoán sai                        │
│                                                                         │
│ Bước nào tốn thời gian/lỗi nhất?                                         │
│   Bước 2 (⏱ 5-10 phút/lượt, không có số liệu định lượng, hoàn toàn      │
│   phụ thuộc cảm tính cá nhân).                                          │
│                                                                         │
│ AI có thể nhảy vào hỗ trợ ở bước nào?                                   │
│   Bước 2 & Bước 3: Mô hình thống kê/time-series dự báo nhu cầu + LLM     │
│   tổng hợp lý do và soạn nháp khuyến nghị phân bổ xe gửi Dispatcher.     │
│                                                                         │
│ Đo thành công bằng gì (Metric có số)?                                   │
│   - Giảm ETA trung bình giờ cao điểm từ ~12 phút xuống dưới 6 phút.     │
│   - Tỉ lệ khu vực được đề xuất đúng (phát sinh cuốc sau 15p) ≥ 80%.     │
│   - Giảm thời gian ra quyết định điều phối từ 10 phút xuống dưới 1 phút. │
│                                                                         │
│ Quick Architecture: [x] LLM Feature (Kết hợp rule/time-series tính toán │
│ và LLM đóng vai trò Copilot giải thích lý do + draft lệnh điều vận).    │
└─────────────────────────────────────────────────────────────────────────┘
```

---

### 📇 QUICK PROBLEM CARD #2 (Từ Bài #4 VinFast)

```text
┌─────────────────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #2                                                   │
│                                                                         │
│ Bài toán: AI theo dõi quãng đường, tình trạng pin và các thông số vận    │
│ hành để chủ động cảnh báo bảo dưỡng/sạc trước khi xe gặp sự cố.         │
│ Công ty thành viên: [x] VinFast                                         │
│                                                                         │
│ Ai đang đau? Chủ xe điện (lo lắng pin cạn/hỏng hóc), Kỹ thuật viên bảo  │
│ dưỡng (quá tải tiếp nhận xe hỏng đột xuất tại xưởng).                   │
│                                                                         │
│ Workflow thủ công hiện tại (4 bước):                                    │
│   1. Xe báo lỗi trên màn hình taplo hoặc dừng xe do cạn pin             │
│   ──> 2. Khách hàng gọi hotline CSKH VinFast                            │
│   ──> 3. Nhân viên kỹ thuật tra cứu lịch sử bảo dưỡng và hướng dẫn      │
│   ──> 4. Đặt lịch cứu hộ hoặc hẹn mang xe vào xưởng                     │
│                                                                         │
│ Bước nào tốn nhất? Bước 2 & 3 (⏱ 15-20 phút/ca sự cố).                  │
│ AI có thể nhảy vào hỗ trợ: Đọc dữ liệu telemetry pin và cảm biến xe     │
│ để dự báo sớm tế bào pin yếu, gửi thông báo bảo dưỡng phòng ngừa.       │
│                                                                         │
│ Đo thành công bằng gì: Giảm 40% ca chết máy đột ngột trên đường.        │
│ Quick Architecture: [x] Predictive ML + LLM Alert                       │
└─────────────────────────────────────────────────────────────────────────┘
```

---

### 📇 QUICK PROBLEM CARD #3 (Từ Bài #2 Vinhomes)

```text
┌─────────────────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #3                                                   │
│                                                                         │
│ Bài toán: Cảm biến + AI cảnh báo sớm nguy cơ chập điện, định vị chính   │
│ xác khu vực có nguy cơ sự cố trước khi xảy ra cháy nổ tại Vinhomes.     │
│ Công ty thành viên: [x] Vinhomes                                        │
│                                                                         │
│ Ai đang đau? Cư dân tòa nhà (nguy cơ an toàn cháy nổ), Đội bảo trì PCCC │
│ (phải đi kiểm tra tuần tra thủ công các hộp kỹ thuật điện).            │
│                                                                         │
│ Workflow thủ công hiện tại (4 bước):                                    │
│   1. Đội kỹ thuật đi tuần tra kiểm tra tủ điện định kỳ                  │
│   ──> 2. Đo nhiệt độ bằng súng bắn nhiệt cầm tay                        │
│   ──> 3. Ghi chép sổ tay số liệu tủ điện                                │
│   ──> 4. Lập biên bản sửa chữa nếu phát hiện quá nhiệt                  │
│                                                                         │
│ Bước nào tốn nhất? Bước 1 & 2 (⏱ 4-6 tiếng/ngày cho cả tòa nhà).         │
│ AI có thể nhảy vào hỗ trợ: Phân tích realtime dòng tải và nhiệt độ cảm  │
│ biến IoT, tự động phát hiện anomaly cảnh báo cháy sớm theo tầng.       │
│                                                                         │
│ Đo thành công bằng gì: Phát hiện nguy cơ chập điện trước 30-60 phút.    │
│ Quick Architecture: [x] IoT Anomaly Detection + Rule                    │
└─────────────────────────────────────────────────────────────────────────┘
```

---

# 🗳️ Quyết định lựa chọn của nhóm & Lý giải

### Lựa chọn chính thức:

Nhóm quyết định chọn **QUICK PROBLEM CARD #1: Điều phối viên Xanh SM phân bổ xe theo khu vực/khung giờ (Intelligent Fleet Rebalancing Co-Pilot)** — phát triển từ bài toán **#5** trong danh sách SCAN làm đề tài trọng tâm để thực hiện Deep-Dive.

### Lý do lựa chọn Card #1:

1. **Tác động kinh doanh trực tiếp và tức thì (High Business Impact):** Vấn đề rò rỉ cuốc xe và thời gian chờ của khách hàng vào giờ cao điểm là bài toán sống còn đối với Xanh SM trong cuộc cạnh tranh dịch vụ gọi xe công nghệ. Việc giảm ETA từ 12 phút xuống dưới 6 phút trực tiếp làm tăng tỉ lệ hoàn thành cuốc (+18%) và tối ưu hóa doanh thu đội xe.
2. **Dữ liệu sẵn sàng và phong phú (High Data Readiness):** Hệ thống GSM có sẵn toàn bộ dữ liệu telemetry GPS xe, lịch sử đặt cuốc theo từng ô bản đồ (H3 hexagons), dữ liệu thời tiết và lịch trình các sự kiện lớn tại Hà Nội và TP.HCM.
3. **Mô hình triển khai rõ ràng, an toàn (Safe Co-Pilot Pattern):** Áp dụng kiến trúc LLM Co-Pilot với con người duyệt (Human-in-the-loop) giúp giảm thiểu hoàn toàn rủi ro hallucination, đảm bảo điều phối viên luôn nắm quyền quyết định tối hậu.

### Lý do không chọn Card #2 và Card #3 ở giai đoạn này:

- **Loại bỏ Card #2 (VinFast - Dự báo bảo dưỡng pin):** Đòi hỏi tích hợp sâu vào firmware BMS (Battery Management System) của từng dòng xe và cần thời gian kiểm định an toàn nghiêm ngặt từ Cục Đăng kiểm, chu kỳ phát triển dài (6-12 tháng).
- **Loại bỏ Card #3 (Vinhomes - Cảnh báo chập điện):** Yêu cầu đầu tư phần cứng hạ tầng cảm biến IoT rất lớn phủ kín toàn bộ các tủ điện tòa nhà. Chưa tối ưu về mặt chi phí triển khai ngắn hạn so với giải pháp phần mềm cho Xanh SM.
