**Status:** 🚧 - **Last Updated:** 1st May 2026

#### Table of Contents
- [Keywords](#keywords)


## Keywords

1. Bản chất & Mô hình Dữ liệu (Data Model)
Đây là những khái niệm nền tảng nhất của GA4. Bạn cần hiểu cách GA4 thu thập và cấu trúc dữ liệu.

Event (Sự kiện): Mọi tương tác của người dùng (click, xem trang, cuộn chuột) đều là sự kiện.

Parameter (Tham số): Thông tin bổ sung đi kèm để mô tả chi tiết cho sự kiện (ví dụ: event là view_item thì tham số là item_name, price).

User Property (Thuộc tính người dùng): Các đặc điểm của tệp khách hàng như ngôn ngữ, quốc gia, độ tuổi, hoặc trạng thái VIP.

Data Stream (Luồng dữ liệu): Nguồn thu thập dữ liệu đổ về GA4. Có 3 loại luồng: Web, iOS App, và Android App.

Measurement ID (ID đo lường): Mã định danh (có dạng G-XXXXXXX) dùng để gắn vào website để gửi dữ liệu về luồng.

2. Các Loại Sự Kiện (Event Types)
Trong GA4, không phải sự kiện nào cũng phải tự cài đặt thủ công. Việc phân biệt các loại sự kiện giúp bạn tiết kiệm rất nhiều thời gian.

Automatically Collected Events: Các sự kiện được GA4 tự động thu thập ngay khi gắn mã (như first_visit, session_start).

Enhanced Measurement (Đo lường nâng cao): Các tính năng tự động bật/tắt trong cài đặt mà không cần sửa code, bao gồm: Page views, Scrolls (cuộn 90%), Outbound clicks, Site search, Video engagement, File downloads.

Recommended Events (Sự kiện được đề xuất): Các sự kiện GA4 khuyên dùng cho từng ngành nghề (E-commerce, Jobs, Travel) để tối ưu hóa báo cáo chuẩn có sẵn.

Custom Events (Sự kiện tùy chỉnh): Các sự kiện do bạn tự định nghĩa và cài đặt khi các loại trên không đáp ứng được nhu cầu.

3. Thứ Nguyên & Chỉ Số (Dimensions & Metrics)
Từ khóa giúp bạn đọc và hiểu các bảng báo cáo.

Dimension (Thứ nguyên): Thuộc tính mô tả dữ liệu (thường là chữ, ví dụ: Trình duyệt, Thành phố, Kênh nguồn).

Metric (Chỉ số): Dữ liệu định lượng dạng số (ví dụ: Số người dùng, Số sự kiện, Doanh thu).

Active Users (Người dùng hoạt động): Số người dùng có tương tác thực sự (chỉ số người dùng chính trong GA4, khác với "Total Users").

Engagement Rate (Tỷ lệ tương tác): Tỷ lệ phần trăm số phiên (sessions) có tương tác tốt (kéo dài trên 10s, hoặc có ≥2 trang xem, hoặc có quy đổi). Từ khóa này thay thế cho Bounce Rate (Tỷ lệ thoát) của UA cũ.

Average Engagement Time (Thời gian tương tác trung bình): Thời gian thực tế mà website/app hiển thị trên màn hình người dùng.

4. Chuyển Đổi & Theo Dõi Chiến Dịch (Attribution & Acquisition)
Từ khóa giúp bạn biết khách hàng đến từ đâu và nguồn nào mang lại hiệu quả.

Key Event (Sự kiện chính): Định nghĩa mới của GA4 cho Conversion (Chuyển đổi). Bạn chọn những Event quan trọng nhất (như mua hàng, điền form) để đánh dấu làm Key Event.

Attribution Model (Mô hình phân bổ): Quy tắc quyết định kênh marketing nào được tính công cho lượt chuyển đổi (ví dụ: Data-driven attribution - Phân bổ dựa trên dữ liệu).

UTM Parameters (Tham số UTM): Các đoạn mã thêm vào URL để theo dõi chiến dịch (bao gồm: utm_source, utm_medium, utm_campaign).

User acquisition vs. Traffic acquisition:

User acquisition: Nguồn đã mang người dùng đến lần đầu tiên (gắn liền với User).

Traffic acquisition: Nguồn mang lại phiên truy cập mới (gắn liền với Session).

5. Báo Cáo Nâng Cao & Phân Tích Chuyên Sâu (Explorations)
Để thực sự "thành thạo", bạn không thể chỉ nhìn vào báo cáo mặc định mà phải làm chủ menu Explore (Khám phá).

Explorations (Khám phá): Không gian tự do giúp bạn tự kéo thả dữ liệu để phân tích chuyên sâu.

Free Form (Hình thức tự do): Dạng báo cáo bảng tùy chỉnh cơ bản, trực quan hóa bằng biểu đồ cột, tròn.

Funnel Exploration (Khám phá phễu): Phân tích các bước trong hành trình khách hàng để tìm ra nơi họ rơi rụng nhiều nhất (ví dụ phễu mua hàng).

Path Exploration (Khám phá đường dẫn): Biểu đồ dạng cây xem người dùng lướt qua những trang nào hoặc thực hiện các chuỗi hành động nào theo thứ tự.

Segment (Phân đoạn): Tập hợp con của dữ liệu người dùng (ví dụ: "Người dùng đến từ Mobile" hoặc "Người dùng đã bỏ giỏ hàng") để phân tích so sánh.

6. Cấu Hình Hệ Thống & Kết Nối (Admin & Integrations)
Từ khóa liên quan đến quản trị và kỹ thuật để thiết lập tài nguyên sạch, chuẩn.

Google Tag Manager (GTM): Công cụ quản lý thẻ, là "bạn thân" của GA4. Thành thạo GA4 bắt buộc phải biết dùng GTM để bắn Event.

Google Signals: Tính năng thu thập dữ liệu trên nhiều thiết bị (Cross-device) dựa trên những người dùng có bật cá nhân hóa quảng cáo của Google.

Data Retention (Giữ lại dữ liệu): Cài đặt thời gian lưu trữ dữ liệu dạng khám phá (mặc định là 2 tháng, cần chỉnh ngay lên 14 tháng để phân tích dài hạn).

Reporting Identity (Danh tính báo cáo): Cách GA4 hợp nhất người dùng (bằng User ID, Google Signals, Device ID, hoặc Modeling).

BigQuery Export: Tính năng kết nối và xuất dữ liệu thô từ GA4 sang kho lưu trữ BigQuery của Google (miễn phí trong GA4, rất quan trọng cho Data Analyst).