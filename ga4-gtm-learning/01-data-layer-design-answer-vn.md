# 01 — Thiết kế Data Layer

## Data Layer, GTM và GA là gì?

```text
Website/App
↓
Data Layer
↓
GTM - xử lý các message trong Data Layer theo thứ tự vào trước, xử lý trước
↓
GA4 / Ads / các công cụ khác
```

**Data Layer** là một object JavaScript dùng để lưu trữ dữ liệu có cấu trúc mà Google Tag Manager có thể đọc và gửi tới GA4.

**GTM (Google Tag Manager)** là công cụ quản lý và triển khai tracking tag trên website hoặc app mà không cần thay đổi code ứng dụng mỗi lần.

**GA (Google Analytics)** là nền tảng thu thập, phân tích và báo cáo hành vi người dùng trên website và app.

## Các nguyên tắc của Data Layer

### 1 — Mô tả sự kiện nghiệp vụ

Event nên mô tả điều đã xảy ra từ góc nhìn nghiệp vụ, thay vì hành động UI mà người dùng thực hiện.

**Không nên**

```js
dataLayer.push({
  event: "green_button_click",
});
```

**Nên**

```js
dataLayer.push({
  event: "sign_up",
});
```

UI có thể đổi từ nút màu xanh lá sang xanh dương, hoặc nội dung nút đổi từ `Create Account` thành `Register`, nhưng kết quả nghiệp vụ vẫn là người dùng đã đăng ký.

Cách này giúp analytics độc lập với cách triển khai UI.

### 2 — Phát event đáng tin cậy

Chỉ push event khi kết quả nghiệp vụ đã được xác nhận và chỉ phát một lần cho mỗi lần xảy ra.

**Không nên**

```js
const handleSubmit = () => {
  dataLayer.push({
    event: "sign_up",
  });

  createAccount();
};
```

**Nên**

```js
const handleSubmit = async () => {
  const response = await createAccount();

  if (response.success) {
    dataLayer.push({
      event: "sign_up",
    });
  }
};
```

### 3 — Sử dụng contract rõ ràng và ổn định

Team cần thống nhất cấu trúc chính xác của từng event.

```js
dataLayer.push({
  event: "calculation_completed",
  country: "us",
  connection_type: "ledger",
  result_count: 12,
});
```

```text
event
  type: string
  value: "calculation_completed"

country
  type: string
  allowed values: "us" | "ca" | "uk" | ...

connection_type
  type: string
  source: calculation input

result_count
  type: number
  source: API response
```

### 4 — Mỗi event phải tự chứa đủ thông tin

Mỗi event nên chứa toàn bộ thông tin cần thiết để hiểu event đó.

**Tránh**

```js
dataLayer.push({
  country: "us",
});

dataLayer.push({
  connection_type: "ledger",
});

dataLayer.push({
  event: "calculation_completed",
});
```

**Ưu tiên**

```js
dataLayer.push({
  event: "calculation_completed",
  country: "us",
  connection_type: "ledger",
  result_count: 12,
});
```

```text
Điều gì đã xảy ra?
→ calculation_completed

Ở đâu?
→ us

Loại connection nào?
→ ledger

Có bao nhiêu kết quả?
→ 12
```

### 5 — Chỉ thu thập dữ liệu an toàn và hữu ích

Mỗi field phải trả lời được một câu hỏi nghiệp vụ đã được phê duyệt.

Ví dụ, nếu business muốn biết loại connection nào được tính thường xuyên nhất, dữ liệu sau là hữu ích:

```js
{
  event: 'calculation_completed',
  connection_type: 'ledger'
}
```

Không nên gửi toàn bộ application state chỉ vì dữ liệu đó đang có sẵn. Object này có thể chứa thông tin không cần thiết hoặc nhạy cảm.

```js
dataLayer.push({
  event: "calculation_completed",
  ...formData,
});
```

Không bao giờ gửi các giá trị như:

```js
{
email: 'user@example.com', // PII ❌
access_token: 'eyJ...', // Secret ❌
password: '...', // Secret ❌
user_comment: inputValue // Unrestricted input ❌
}
```

### Tóm tắt

```text
1. ĐIỀU GÌ đã xảy ra?
   → Mô tả kết quả nghiệp vụ

2. Nó CÓ THỰC SỰ xảy ra không?
   → Chỉ phát event khi đã xác nhận, và chỉ một lần

3. Event CÓ CẤU TRÚC thế nào?
   → Định nghĩa contract ổn định

4. Event có TỰ ĐỦ thông tin không?
   → Bao gồm toàn bộ context cần thiết

5. Dữ liệu này CÓ THỰC SỰ CẦN không?
   → Chỉ thu thập dữ liệu hữu ích và an toàn

Cùng nhau, các nguyên tắc này giúp Data Layer hướng về nghiệp vụ,
đáng tin cậy, nhất quán, tự chứa đủ thông tin và an toàn về quyền riêng tư.
```

## Ví dụ review: FD Calculation Action

Payload FD ban đầu là điểm khởi đầu hữu ích, nhưng chưa đáp ứng đầy đủ các nguyên tắc vì sử dụng tên event mang tính UI chung chung, dùng nhãn hiển thị làm giá trị, trộn nhiều quy ước đặt tên, và dùng string cho các khái niệm số và Boolean. Ví dụ đã chỉnh sửa dưới đây làm rõ business fact, thời điểm, kiểu dữ liệu và contract, đồng thời giữ lại snapshot đầy đủ của calculation cần cho các câu hỏi báo cáo đã được phê duyệt.

## FD Calculation Action — Luồng nghiệp vụ và contract Data Layer

### Logic nghiệp vụ và luồng event

Business muốn đo tần suất người dùng thay đổi input của FD và tần suất các calculation đó tạo ra solution. Hành trình **Calculation Action** bắt đầu mỗi khi người dùng thay đổi một giá trị input:

```text
Người dùng thay đổi một giá trị input của FD
→ application ghi nhận snapshot đầy đủ của các input
→ application gửi snapshot đó tới calculation API
→ API trả về kết quả cho đúng snapshot đó
→ application xác định response có tạo ra output hay không
→ application đặt `solution_found` là `Yes` hoặc `No`
→ application push một message `calculation_action` đầy đủ
→ GTM ánh xạ các field đã được phê duyệt và gửi event tới GA4
```

Thay đổi input khởi động hành trình, nhưng application chỉ push event sau khi biết response tương ứng từ API. Vì vậy `solution_found` là dữ liệu có tính quyết định:

- `"Yes"`: API trả về một result được tạo trong phần output.
- `"No"`: response của API không tạo ra result nào trong phần output.

### Các field canonical của event

| Field                  | Kiểu    | Bắt buộc | Giá trị cho phép/ví dụ | Nguồn                  | Quy tắc                                              |
| ---------------------- | ------- | -------- | ---------------------- | ---------------------- | ---------------------------------------------------- |
| `event`                | string  | Có       | `calculation_action`   | Hằng số application    | Tên business event ổn định                           |
| `event_schema_version` | string  | Có       | `1.0`                  | Hằng số application    | Tăng version khi contract thay đổi không tương thích |
| `app_name`             | string  | Có       | `fd`                   | Hằng số application    | Định danh application ổn định                        |
| `solution_found`       | boolean | Có       | `true`, `false`        | Response API tương ứng | Chỉ là `true` khi response đó tạo ra output          |
| `inputs`               | object  | Có       | Xem bảng bên dưới      | Trạng thái calculation | Một snapshot đầy đủ gắn với request API tương ứng    |

### Các field trong `inputs`

UI hiện tại cung cấp các giá trị theo dạng hiển thị, nhưng contract canonical sử dụng giá trị ổn định, dễ đọc bằng máy và đúng kiểu tự nhiên. Việc ánh xạ từ giá trị UI/API sang các giá trị này phải nằm trong application code, không nằm trong GTM.

| Field                   | Kiểu    | Ví dụ                            | Mapping từ source/UI             |
| ----------------------- | ------- | -------------------------------- | -------------------------------- |
| `country`               | string  | `gb`                             | `United Kingdom`                 |
| `language`              | string  | `en`                             | `English`                        |
| `building_code`         | string  | `en_1995_1_1_2004_a2_2014`       | `EN 1995-1-1:2004/A2:2014`       |
| `design_method`         | string  | `lsd`                            | `Limit States Design (LSD)`      |
| `unit_system`           | string  | `metric`                         | `Metric`                         |
| `connection_type`       | string  | `clt_floor_floor_half_lap_joint` | `CLT Floor-Floor Half-Lap Joint` |
| `fastener_installation` | string  | `typical`                        | `Typical`                        |
| `fx`                    | number  | `1`                              | `fxInput: "1"`                   |
| `fy`                    | number  | `0`                              | `fyInput: "0"`                   |
| `load_duration`         | string  | `medium_term`                    | `Medium Term`                    |
| `main_member_thickness` | number  | `180`                            | `tm: "180"`                      |
| `side_member_thickness` | number  | `180`                            | `ts: "180"`                      |
| `side_member_grade`     | string  | `c24`                            | `sgs: "350 (C24)"`               |
| `side_member_density`   | number  | `350`                            | `sgs: "350 (C24)"`               |
| `main_member_grade`     | string  | `c24`                            | `sgm: "350 (C24)"`               |
| `main_member_density`   | number  | `350`                            | `sgm: "350 (C24)"`               |
| `contact_length`        | number  | `3000`                           | `contactLength: "3000"`          |
| `predrilled`            | boolean | `false`                          | `predrill: "No"`                 |
| `fastener_angle`        | number  | `90`                             | `alphaFastener: "90"`            |
| `service_class`         | string  | `service_class_1`                | `Service Class 1`                |

### Message Data Layer đầy đủ

Đây là message đầy đủ cho một calculation đã tạo ra solution:

```javascript
window.dataLayer.push({
  event: "calculation_action",
  event_schema_version: "1.0",
  app_name: "fd",
  solution_found: true,
  inputs: {
    country: "gb",
    language: "en",
    building_code: "en_1995_1_1_2004_a2_2014",
    design_method: "lsd",
    unit_system: "metric",
    connection_type: "clt_floor_floor_half_lap_joint",
    fastener_installation: "typical",
    fx: 1,
    fy: 0,
    load_duration: "medium_term",
    main_member_thickness: 180,
    side_member_thickness: 180,
    side_member_grade: "c24",
    side_member_density: 350,
    main_member_grade: "c24",
    main_member_density: 350,
    contact_length: 3000,
    predrilled: false,
    fastener_angle: 90,
    service_class: "service_class_1",
  },
});
```

Thiết kế này tuân theo các nguyên tắc Data Layer:

- **Business fact:** `calculation_action` xác định một lần thử calculation FD đã hoàn tất, độc lập với nhãn field, component hoặc button.
- **Event đáng tin cậy:** response API tương ứng là trigger có tính quyết định; một lần thay đổi hoàn tất tạo ra một event.
- **Contract ổn định:** tên field, kiểu string, giá trị cho phép và nguồn dữ liệu đều rõ ràng. Thay đổi phá vỡ tương thích cần được cập nhật contract version đồng bộ.
- **Message tự chứa đủ thông tin:** `solution_found` và snapshot đầy đủ của input được gửi cùng nhau.
- **Dữ liệu an toàn và hữu ích:** loại bỏ UI text không liên quan và metadata do GTM sở hữu; payload không chứa PII, credential hoặc user text không giới hạn.

## Tài liệu tham khảo

- [Google for Developers — The data layer](https://developers.google.com/tag-platform/tag-manager/datalayer): cấu trúc data layer, `dataLayer.push()`, thứ tự xử lý event, persistence, quy tắc đặt tên và xử lý lỗi.
- [Tag Manager Help — Components of Google Tag Manager](https://support.google.com/tagmanager/answer/6103657?hl=en): mối quan hệ giữa tag, trigger, variable và data layer.
- [Google Analytics — Set up events](https://developers.google.com/analytics/devguides/collection/ga4/events): tên event GA4, parameter, custom event và kiểm tra trong Realtime/DebugView.
