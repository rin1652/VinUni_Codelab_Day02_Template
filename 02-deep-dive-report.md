# 🏗️ 02 - Deep-Dive Report: Xanh SM Fleet Rebalancing Co-Pilot

**Dự án:** Phân bổ và Điều phối Đội xe Thông minh (Intelligent Fleet Rebalancing Co-Pilot)  
**Đơn vị áp dụng:** GSM — Taxi Điện Xanh SM (Hệ sinh thái Vin Smart Future)  
**Tác giả / Trưởng nhóm:** Nguyễn Phúc (AI Product Engineer & Team Leader)  
**Trạng thái phê duyệt:** GO (Tiến hành xây dựng Prototype)  

---

## 🏛️ 1. Bối cảnh Vận hành & Động lực Dự án

Tại Trung tâm Điều vận Xanh SM (GSM), bài toán điều tiết cung - cầu (Supply - Demand Matching) giữa đội xe taxi điện và lượng khách đặt chuyến là thách thức lớn nhất trong các khung giờ cao điểm (7:30 - 9:00 sáng và 17:00 - 19:30 tối), cũng như vào những thời điểm thời tiết xấu (mưa giông bất chợt).

Hiện tại, việc phân bổ và tái cân bằng đội xe (Fleet Rebalancing) giữa các quận/khu vực tại Hà Nội phụ thuộc gần như 100% vào trực giác và kinh nghiệm cá nhân của các điều phối viên (Dispatchers). Hậu quả là xảy ra nghịch lý:
- **Khu vực thừa xe:** Hàng chục xe rảnh dừng đỗ chờ cuốc tại các khu đô thị ven đô hoặc các trục đường vắng, lãng phí thời gian và pin.
- **Khu vực thiếu xe (Điểm nóng):** Khách hàng tại các cụm văn phòng (Duy Tân, Keangnam, Cầu Giấy, Hoàn Kiếm) phải chờ xe từ 12-18 phút, tỷ lệ hủy cuốc do không có xe nhận lên đến 25-30%.

Dự án **Xanh SM Fleet Rebalancing Co-Pilot** được thành lập nhằm cung cấp một trợ lý trí tuệ nhân tạo đồng hành cùng Dispatcher, tự động tổng hợp dữ liệu thời gian thực và đề xuất các quyết định điều xe tối ưu có căn cứ định lượng.

---

## 🔄 2. Current-State Workflow Mapping (Quy trình Hiện tại)

Quy trình điều phối và phân bổ xe thủ công đang vận hành tại trung tâm điều vận gồm 5 bước liên tiếp:

```text
┌─────────────────┐       ┌─────────────────┐       ┌─────────────────┐
│ Bước 1          │       │ Bước 2          │       │ Bước 3          │
│ Theo dõi bản đồ │       │ Ước lượng điểm  │       │ Soạn & phát lệnh│
│ realtime        │ ────> │ nóng bằng cảm   │ ────> │ điều xe qua app │
│                 │       │ tính cá nhân    │       │ tài xế          │
│ Actor: Dispatch │       │ Actor: Dispatch │       │ Actor: Dispatch │
│ ⏱ 3 phút       │       │ ⏱ 8 phút 🔴     │       │ ⏱ 4 phút 🔴     │
│ In: Heatmap GPS │ 🔄    │ In: Trực giác   │ 🔄    │ In: Phán đoán   │
│ Out: Nhận biết  │ Handoff│ Out: Danh sách  │ Handoff│ Out: Broadcast  │
└─────────────────┘       └─────────────────┘       └─────────────────┘
                                                           │
                                                           ▼
┌─────────────────┐                                 ┌─────────────────┐
│ Bước 5          │                                 │ Bước 4          │
│ Xử lý sự cố sai │                                 │ Theo dõi mức độ │
│ lệch/phàn nàn   │ <────────────────────────────── │ tuân thủ của    │
│                 │                                 │ tài xế          │
│ Actor: Dispatch │                                 │ Actor: Dispatch │
│ ⏱ 5 phút       │                                 │ ⏱ 3 phút       │
│ Out: Điều chỉnh │                                 │ Out: Số xe đến  │
└─────────────────┘                                 └─────────────────┘

Ký hiệu:
🔴 = Bottlenecks (Điểm nghẽn gây tắc nghẽn và sai sót nghiêm trọng)
🔄 = Handoff (Điểm chuyển giao thông tin thủ công giữa công cụ và con người)
⏱ Tổng thời gian chu kỳ điều phối thủ công: ~23 phút/lượt.
```

### Chi tiết các bước và phân tích điểm nghẽn:

| Bước | Hành động nghiệp vụ | Công cụ sử dụng | Thời gian (⏱) | Điểm nghẽn & Rủi ro sai sót |
|---|---|---|:---:|---|
| **1. Theo dõi bản đồ** | Mở dashboard heatmap xem mật độ xe rảnh và số cuốc đang chờ theo quận. | Web Dashboard nội bộ | 3 phút | Dữ liệu chỉ phản ánh quá khứ gần (lag 2-3 phút), không có khả năng nhìn trước xu hướng. |
| **2. Ước lượng điểm nóng** | Dispatcher tự nhẩm tính và đoán xem khu vực nào sắp bùng nổ nhu cầu dựa trên trí nhớ (ví dụ: *"hôm nay thứ 6 chắc Cầu Giấy sẽ đông"*). | Kinh nghiệm cá nhân | **8 phút 🔴** | **Bottleneck lớn nhất:** Hoàn toàn cảm tính; không tính toán được dữ liệu mưa gió, lịch tan tầm các tòa nhà, dẫn đến dự báo sai lệch >40%. |
| **3. Soạn & phát lệnh** | Viết thông báo dạng text gửi broadcast đến nhóm tài xế ở khu vực lân cận yêu cầu di chuyển đến điểm nóng. | Portal nhắn tin nội bộ | **4 phút 🔴** | **Bottleneck thứ 2:** Soạn thảo chậm, câu từ không rõ ràng, không có gợi ý lộ trình hoặc khoảng cách pin an toàn cho xe điện. |
| **4. Theo dõi tuân thủ** | Nhìn lại bản đồ sau 5-10 phút để xem có bao nhiêu tài xế thực sự di chuyển đến khu vực yêu cầu. | Dashboard GPS | 3 phút | Tài xế không muốn di chuyển nếu không tin tưởng lệnh điều xe của điều phối viên. |
| **5. Điều chỉnh sai sót** | Khi khách phàn nàn hủy chuyến tăng vọt hoặc tài xế đến nơi nhưng không có khách, điều phối viên vội vàng gọi điện điều chỉnh lại. | Tổng đài & Chat nội bộ | 5 phút | Gây tâm lý ức chế cho tài xế và mất uy tín thương hiệu dịch vụ Xanh SM. |

---

## 📋 3. Problem Statement (6-Field) — Tiêu chuẩn Vin Smart Future

| Trường thông tin | Nội dung chi tiết chuẩn xác thực tế |
|---|---|
| **1. Actor / Operator** | Điều phối viên (Dispatcher) tại Trung tâm Điều vận Xanh SM Hà Nội & TP.HCM. |
| **2. Current Workflow** | Mỗi 15-20 phút vào giờ cao điểm, Dispatcher theo dõi heatmap xe rảnh, dùng kinh nghiệm cá nhân phán đoán khu vực sắp thiếu xe, gõ thông báo điều động thủ công gửi tài xế, sau đó kiểm tra lại bằng mắt trên bản đồ. Toàn bộ 5 bước diễn ra thủ công, mất ~23 phút cho một chu kỳ điều phối. |
| **3. Bottleneck** | **Bước 2 & Bước 3:** Dispatcher mất 12 phút để tính toán trong đầu và soạn thảo thông báo điều phối. Việc thiếu số liệu định lượng (thời tiết, mật độ chuyến lịch sử, dung lượng pin khả dụng) khiến các quyết định điều xe mang tính hên xui. |
| **4. Business Impact** | - Khách hàng chờ xe trung bình **12.4 phút** trong giờ cao điểm.<br>- Tỷ lệ hủy cuốc do không có xe nhận đạt **26.8%** tại các điểm nóng (Cầu Giấy, Nam Từ Liêm, Hoàn Kiếm).<br>- Ước tính rò rỉ doanh thu **~380 triệu VNĐ/ngày** trên toàn mạng lưới do mất khách vào tay đối thủ (Grab/Be).<br>- Tài xế lãng phí trung bình **42 phút/ca làm việc** chỉ để chạy không tải tìm khách hoặc đứng chờ ở vùng ế khách. |
| **5. Success Metric** | **1. Hiệu suất thời gian (Efficiency):** Giảm thời gian ra quyết định phân bổ từ 12 phút xuống **dưới 1 phút**.<br>**2. Trải nghiệm khách hàng (Customer ETA):** Giảm thời gian chờ xe trung bình giờ cao điểm từ 12.4 phút xuống **dưới 6.0 phút**.<br>**3. Độ chính xác dự báo (Quality):** Tỷ lệ khu vực đề xuất có phát sinh cuốc trong 15 phút kế tiếp đạt **≥ 80%**.<br>**4. Tỷ lệ hủy chuyến (Business):** Giảm tỷ lệ hủy cuốc giờ cao điểm từ 26.8% xuống **dưới 12%**. |
| **6. Operational Boundary (Ranh giới cấm)** | **1. Bắt buộc Human-in-the-loop (HITL):** Mọi khuyến nghị điều phối và tin nhắn do AI tạo ra PHẢI bắt đầu bằng thẻ `[DRAFT_ONLY]` và chỉ được gửi đi khi Dispatcher bấm duyệt (Phê duyệt 1 chạm).<br>**2. Ranh giới an toàn pin xe điện (EV Safety Guardrail):** TUYỆT ĐỐI KHÔNG điều động xe có dung lượng pin dưới 20% di chuyển sang khu vực khác; KHÔNG điều động xe vượt quá bán kính 5km.<br>**3. Giới hạn điều động trần (Cap Limit):** Không điều quá 20 xe từ một khu vực nguồn trong một đợt để tránh tạo "vùng trắng" thiếu xe cục bộ. |

---

## 🤖 4. Future-State Flow & AI Fit Analysis

### 4.1. Ma trận lựa chọn kiến trúc (AI-Fit Matrix)

| Tiêu chí so sánh | Rule-based / Heuristic | LLM Feature (Được chọn) | Autonomous Agent |
|---|---|---|---|
| **Khả năng dự báo & tổng hợp đa nguồn** | Thấp — Khó xử lý dữ liệu phi cấu trúc (thời tiết, ghi chú giao thông, tin nhắn sự cố). | **Cao** — Kết hợp time-series dự báo nhu cầu + LLM tổng hợp lý do mạch lạc. | Rất cao — Tự suy luận chuỗi hành động phức tạp. |
| **Khả năng giải thích (Explainability)** | Chỉ hiện con số khô khan, điều phối viên khó tin tưởng. | **Xuất sắc** — Giải thích ngắn gọn bằng tiếng Việt nguyên nhân vì sao cần điều xe. | Trung bình — Khó giải thích chuỗi reasoning cho operator. |
| **Độ tin cậy & Kiểm soát rủi ro** | Rất cao (quy tắc cứng cố định). | **Rất cao** (có ranh giới `[DRAFT_ONLY]` và Dispatcher duyệt). | Thấp — Rủi ro tự động ra lệnh điều hàng trăm xe gây hỗn loạn giao thông. |
| **Chi phí & Độ phức tạp triển khai** | Thấp, nhưng hiệu quả tối ưu hạn chế. | **Vừa phải** (gọi API Gemini 3 Flash với structured JSON). | Rất cao (cần orchestration, multi-agent loop, memory). |

👉 **Kết luận lựa chọn:** **LLM Feature (Co-Pilot)** là kiến trúc hoàn hảo nhất. Mô hình Machine Learning/Thống kê đảm nhiệm phần tính toán số lượng xe cần bù đắp, còn LLM (Gemini) đóng vai trò Co-pilot: diễn giải lý do bằng tiếng Việt tự nhiên, kiểm tra các ranh giới an toàn pin xe điện, và soạn sẵn bản nháp lệnh điều vận kèm tin nhắn gửi tài xế.

---

### 4.2. Sơ đồ Quy trình Tương lai (Future-State Flow)

```text
┌─────────────────────────────────────────────────────────────────────────┐
│                    HỆ THỐNG DỮ LIỆU GSM REAL-TIME                       │
│    (GPS Telemetry + Đơn đặt cuốc + Dự báo thời tiết + Lịch sự kiện)     │
└────────────────────────────────────┬────────────────────────────────────┘
                                     │
                                     ▼
┌─────────────────────────────────────────────────────────────────────────┐
│ 🔵 BƯỚC 1: AI DEMAND PREDICTOR & REBALANCING ENGINE                     │
│ - Tự động quét 12 quận nội thành theo lưới không gian H3 (Hexagon).    │
│ - Phát hiện khu vực mất cân bằng cung - cầu trước 15-30 phút.          │
│ ⏱ Thời gian xử lý: 3 giây.                                             │
└────────────────────────────────────┬────────────────────────────────────┘
                                     │
                                     ▼
┌─────────────────────────────────────────────────────────────────────────┐
│ 🔵 BƯỚC 2: GEMINI CO-PILOT (Prompt Boundary & DRAFT Generator)          │
│ - Kiểm tra an toàn: Lọc bỏ xe pin < 20%, xe cách xa > 5km.             │
│ - Soạn nháp JSON khuyến nghị:                                           │
│   {                                                                     │
│     "recommendation": "[DRAFT_ONLY] Điều 15 xe VF5 từ Mỹ Đình sang     │
│                        Cầu Giấy do sắp mưa lớn và tan tầm tòa FPT",    │
│     "source_zone": "Mỹ Đình", "target_zone": "Cầu Giấy",               │
│     "num_vehicles": 15, "priority": "HIGH"                              │
│   }                                                                     │
│ ⏱ Thời gian xử lý: 2 giây.                                             │
└────────────────────────────────────┬────────────────────────────────────┘
                                     │
                                     ▼
┌─────────────────────────────────────────────────────────────────────────┐
│ 🟢 BƯỚC 3: HUMAN-IN-THE-LOOP (Dispatcher 1-Click Approval)              │
│ - Điều phối viên nhìn thấy thẻ khuyến nghị nổi bật trên màn hình.       │
│ - Đọc nhanh lý do tóm tắt (10 giây) -> Bấm "PHÊ DUYỆT" hoặc "TỪ CHỐI". │
│ ⏱ Thời gian Dispatcher: 15 giây.                                       │
└───────────────────┬─────────────────────────────────┬───────────────────┘
                    │ [Bấm Duyệt]                     │ [Bấm Từ chối/Sửa]
                    ▼                                 ▼
┌──────────────────────────────────────┐  ┌───────────────────────────────┐
│ 🚀 HỆ THỐNG GỬI LỆNH ĐIỀU VẬN        │  │ ↩️ FALLBACK STRATEGY           │
│ Broadcast thông báo đến App tài xế   │  │ Dispatcher tự gõ lệnh thủ     │
│ trong vùng nguồn đủ điều kiện pin.   │  │ công hoặc giữ nguyên đội hình.│
└──────────────────────────────────────┘  └───────────────────────────────┘
```

---

## 🛡️ 5. Chiến lược Dự phòng (Fallback Strategy)

Để đảm bảo hệ thống vận hành liên tục 99.99% ngay cả khi AI gặp sự cố:
1. **Lỗi mạng hoặc Gemini API Timeout (> 5s):** Hệ thống tự động chuyển sang chế độ Rule-based cơ bản (hiển thị danh sách top 3 khu vực chênh lệch cuốc xe nhiều nhất dưới dạng bảng số thô mà không có phần text giải thích của LLM).
2. **LLM Hallucination hoặc vi phạm ranh giới (thiếu `[DRAFT_ONLY]` hoặc điều xe pin yếu):** Lớp kiểm duyệt cục bộ (Python validation regex) sẽ chặn ngay lập tức, không cho phép hiển thị thẻ khuyến nghị lên màn hình Dispatcher, đồng thời gửi cảnh báo về kênh Telegram trực ban IT.
3. **Dispatcher từ chối khuyến nghị:** Hệ thống lưu log lý do từ chối để huấn luyện và tinh chỉnh lại prompt/few-shot examples cho ca trực sau.

---

## 🏁 6. Đánh giá Khả thi & Quyết định Dự án (Decision Quality - Gate G4)

### 6.1. Bảng Kiểm tra Sẵn sàng Triển khai (AI Readiness Checklist):
- [x] **Dữ liệu:** Đã có log đặt cuốc 6 tháng gần nhất tại Hà Nội, dữ liệu GPS xe cập nhật mỗi 5 giây qua Kafka topic.
- [x] **Kiểm soát rủi ro:** 100% quyết định phát lệnh điều xe đều có con người duyệt (HITL), có ranh giới bảo vệ pin < 20%.
- [x] **Sự sẵn sàng của Người dùng (Stakeholder Readiness):** Team Dispatcher cực kỳ ủng hộ vì dự án giúp họ giảm tải áp lực quyết định trong giờ cao điểm.
- [x] **Chi phí hạ tầng:** Chi phí gọi API Gemini 3 Flash ước tính chỉ ~$15 - $20/tháng cho toàn bộ trung tâm điều vận, trong khi giá trị mang lại hàng trăm triệu đồng/tháng.

### 6.2. Quyết định Chính thức:
**QUYẾT ĐỊNH: [x] GO (Tiến hành phát triển Prototype)**

**Lý giải quyết định (Justification):**  
Dự án giải quyết trực diện "nỗi đau" lớn nhất của vận hành Xanh SM với bài toán được định nghĩa sắc nét, chỉ số đo lường định lượng có thể kiểm chứng ngay (ETA, tỷ lệ hủy cuốc). Mô hình kiến trúc LLM Co-Pilot kết hợp HITL loại trừ rủi ro an toàn, chi phí API cực kỳ thấp so với tiềm năng tăng trưởng doanh thu vận tải. Đủ điều kiện phê duyệt triển khai ngay giai đoạn POC.
