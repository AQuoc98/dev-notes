- [ ] Step to step build a workflow from business requirement, manage in GTM and GA

## Basic flow

Trong flow **Application → GTM → GA4**, Data Layer đóng vai trò là **lớp trung gian (data contract)** giữa application và GTM.

Có thể hiểu đơn giản:

> **Application đưa business data vào Data Layer → GTM đọc data đó → Tag gửi data cần thiết lên GA4.**

Ví dụ user thực hiện calculation:

```js
dataLayer.push({
  event: "calculation_complete",
  solution_found: true,
  inputs: {
    connection_type: "wood_to_wood",
    country: "us",
  },
});
```

Flow sẽ là:

```text
Application
    │
    │ dataLayer.push(...)
    ▼
Data Layer
    │
    │ Business data
    │ - event
    │ - solution_found
    │ - connection_type
    │ - country
    ▼
GTM
    │
    ├── Trigger
    │   "calculation_complete happened?"
    │
    ├── Variables
    │   Read solution_found
    │   Read inputs.connection_type
    │   Read inputs.country
    │
    └── GA4 Event Tag
            │
            ▼
           GA4
```

Ví dụ GTM có các Data Layer Variables:

```text
FD - DLV - solution_found
→ solution_found

FD - DLV - inputs - connection_type
→ inputs.connection_type

FD - DLV - inputs - country
→ inputs.country
```

Sau đó GA4 Tag có thể gửi:

```text
Event name:
calculation_complete

Parameters:
solution_found   = {{FD - DLV - solution_found}}
connection_type  = {{FD - DLV - inputs - connection_type}}
country          = {{FD - DLV - inputs - country}}
```

### Tại sao cần Data Layer?

Nếu không có Data Layer, GTM có thể phải lấy dữ liệu trực tiếp từ UI:

```text
DOM
 ↓
Find selected dropdown
 ↓
Read displayed text
 ↓
GTM
 ↓
GA4
```

Cách này dễ bị ảnh hưởng khi Front-End thay đổi.

Ví dụ hôm nay:

```html
<select id="connection-type"></select>
```

ngày mai refactor thành React component khác. Business concept `connection_type` không thay đổi nhưng tracking có thể hỏng.

Với Data Layer:

```text
UI thay đổi
     ↓
Application vẫn biết business value
     ↓
connection_type = "wood_to_wood"
     ↓
Data Layer contract không đổi
     ↓
GTM không cần thay đổi
```

### Phân biệt trách nhiệm

| Layer           | Responsibility                                                   |
| --------------- | ---------------------------------------------------------------- |
| **Application** | Biết business logic và khi nào business action thực sự xảy ra    |
| **Data Layer**  | Cung cấp một **stable contract** chứa business data cho tracking |
| **GTM**         | Đọc data, quyết định Tag nào fire và cấu hình data cần gửi       |
| **GA4**         | Nhận, lưu trữ và phân tích analytics data                        |

Điểm rất quan trọng là **Data Layer không phải nơi gửi dữ liệu lên GA4**.

Nó chỉ cung cấp dữ liệu:

```text
Data Layer = "Here is what happened and its data."

GTM = "Based on that information, which tag should fire
       and what should be sent?"

GA4 = "I receive and store the analytics event."
```

Vì vậy, nếu cần một câu ngắn để đưa vào tài liệu của bạn:

> **The Data Layer acts as a stable data contract between the application and GTM. The application exposes business events and values through the Data Layer, GTM reads and maps those values, and tags send the required analytics data to GA4.**

## Research blueprint data-layer, list the thing is bad, and propose the solution

```js
dataLayer.push({
  event: "webApps",
  app_name: "FD_Events",
  app_action: "Calculation_Action",
  solution_found: "Yes",
  link_text: undefined,
  inputs: {
    settings_country: "United Kingdom",
    settings_language: "English",
    settings_buildingCode: "EN 1995-1-1:2004/A2:2014",
    settings_designMethod: "Limit States Design (LSD)",
    settings_unit: "Metric",
    settings_connectionType: "Multi-Ply Connection",
    loadType: "Uniform Load",
    loadingSideInput: "Head Side",
    loadEntry: "1",
    loadDurationFactor: "Medium Term",
    serviceClassEU: "Service Class 1",
    memberType: "Glulam",
    memberWidth: "95",
    memberThickness: "38",
    memberDensity: "385 (GL24h)",
    noOfPlies: "2",
    numberOfRows: "All",
    predrill: "No"
  },
  gtm.uniqueEventId: 1479
})
```
