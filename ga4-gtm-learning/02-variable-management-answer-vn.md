# 02 — Quản lý Variable trong GTM

## 1. Mục tiêu, phạm vi và đầu ra

Tài liệu này hướng dẫn cách thiết kế, cấu hình, review và retire Google Tag Manager (GTM) Variable cho quy trình đo lường GA4 ổn định.

GTM Variable là một placeholder có tên, dùng để lấy một giá trị có thể thay đổi. Trigger có thể dùng giá trị đó để quyết định Tag có fire hay không; Tag dùng giá trị đó để điền vào event parameter. Variable nên expose dữ liệu đã được Application phê duyệt, không dựng lại business logic của Application.

### Trong phạm vi

- Chọn source và GTM Variable type đơn giản nhất.
- Cách Data Layer Variable hoạt động, nested path, persistence và phòng stale value.
- Scope, reuse, naming, folder, environment routing, consent và missing-data behavior.
- Guardrail cho Custom JavaScript, QA, inventory, deprecation và retirement.
- Setup Variable cho `calculation_action` của FD làm pattern tham chiếu.

### Ngoài phạm vi

- Chi tiết thiết kế Trigger hoặc Tag; xem Sections 03 và 04.
- Consent management; xem Section 05.
- Phát triển custom template; xem Section 06.
- Measurement question và field approval; xem Section 07.
- Debug evidence, report và release monitoring; xem Sections 08–10.
- Thiết kế Variable phục vụ quảng cáo hoặc campaign.

### Đầu ra cần có

Mỗi Variable được phê duyệt phải có:

1. Contract record ghi rõ source, type, ý nghĩa, allowed values và missing-data behavior.
2. Canonical name và folder phù hợp.
3. Danh sách consumer cùng behavior theo environment/consent.
4. QA evidence và record của lần publish có version.
5. Inventory entry có owner và lifecycle status.

## 2. Tổng quan: Variable làm gì?

### 2.1 Định nghĩa đơn giản

| Thành phần | Vai trò trong việc quản lý Variable |
| --- | --- |
| Data Layer Variable (DLV) | Đọc giá trị do Application đưa vào Data Layer, theo key hoặc nested path. Đây là source ưu tiên cho business value. |
| Constant | Cung cấp một giá trị cố định, không nhạy cảm, để nhiều Tag dùng lại. |
| Lookup Table (LUT) | Map một input chính xác sang output đã phê duyệt, ví dụ hostname sang Measurement ID. |
| RegEx Table (RLT) | Map các pattern text có kiểm soát sang output. Rule được kiểm tra từ trên xuống dưới. |
| URL Variable | Đọc một thành phần cụ thể của URL hiện tại. |
| First-Party Cookie | Đọc giá trị của cookie đã được phê duyệt. |
| DOM Element | Đọc nội dung trên trang khi không có source ổn định từ Application/Data Layer. Không nên dùng cho business value nếu có lựa chọn khác. |
| Custom JavaScript | Thực hiện một phép biến đổi nhỏ, đồng bộ, khi các type native không đáp ứng được. Đây là lựa chọn cuối cùng. |

Mối quan hệ giữa các thành phần:

```text
Application
    ↓ publish giá trị đã phê duyệt
Data Layer
    ↓ được đọc bởi
GTM Variables
    ↓ được dùng bởi
Triggers và Tags
    ↓ gửi tới
GA4 hoặc destination đã được phê duyệt
```

Variable = “Cần dùng giá trị nào?”

Trigger = “Khi nào Tag chạy?”

Tag = “Cần gửi hoặc thực thi điều gì?”

### 2.2 Ranh giới trách nhiệm

Application là nơi biết giá trị nào đúng và kết quả nghiệp vụ có thực sự xảy ra hay không. Application cũng chuẩn hóa dữ liệu trước khi đưa vào Data Layer. Variable trong GTM chỉ đọc giá trị đó hoặc thực hiện một phép map đơn giản đã được duyệt; Variable không tự quyết định business result. Nếu giá trị bị thiếu hoặc sai, hãy kiểm tra Application và Data Layer trước, thay vì thêm fallback GTM để che lỗi.

## 3. Quy trình quyết định Variable

Áp dụng đúng thứ tự này cho mọi Variable mới hoặc thay đổi:

```text
1. Phân loại scope và lifecycle
        ↓
2. Tìm trong container và inventory
        ↓
3. Định nghĩa Variable contract
        ↓
4. Chọn native type đơn giản nhất
        ↓
5. Đặt tên và sắp xếp folder
        ↓
6. Cấu hình missing data, environment, consent và privacy
        ↓
7. Test toàn bộ flow
        ↓
8. Review, publish, cập nhật inventory và maintain
```

Không tạo Variable trước khi hoàn thành bước 1–3.

### 3.1 Phân loại scope và lifecycle

| Phân loại | Dùng khi | Ví dụ tên |
| --- | --- | --- |
| Shared | Source, ý nghĩa, type, allowed values, missing behavior, consent và environment behavior tương thích giữa các project. | `SHARED - DLV - solution_found` |
| Project-specific | Giá trị thuộc về một application hoặc Measurement Plan. | `FD - DLV - inputs - connection_type` |
| Temporary/experiment | Migration, proof of concept hoặc test ngắn hạn cần Variable riêng. | `FD - TEST - normalize_input` |
| Deprecated | Đã có Variable thay thế nhưng consumer chưa migrate xong. | `FD - OLD - calculation_status` |

Hai Variable có tên gần giống nhau chưa đủ để dùng chung. Chỉ reuse khi toàn bộ contract giống nhau. Cùng một standard có thể được dùng trong nhiều container, nhưng mỗi container vẫn cần tạo và approve Variable riêng.

### 3.2 Tìm trước khi tạo

Tìm trong container và Variable inventory theo Data Layer key, purpose và type. Ưu tiên một canonical Variable dùng chung cho các Tag hoặc Trigger tương thích. Không tạo bản sao theo từng Tag như `FD - DLV - print - solution_found` và `FD - DLV - download - solution_found` nếu cả hai cùng đọc field `solution_found`.

Trước khi reuse, hãy so sánh:

- source key/path;
- data type và allowed values;
- business meaning;
- required/optional status và missing behavior;
- environment và consent behavior;
- owner và downstream consumer.

Nếu có khác biệt quan trọng, hãy tạo Variable project-specific và ghi rõ lý do.

### 3.3 Định nghĩa Variable contract

Ghi các thuộc tính sau trước khi cấu hình trong GTM:

| Thuộc tính | Cần ghi rõ |
| --- | --- |
| Name và scope | Tên canonical trong GTM và project/group sở hữu. |
| Business meaning | Giá trị đại diện cho điều gì và phục vụ câu hỏi nào. |
| Source | Data Layer key/path hoặc native source chính xác. |
| Type | String, number, Boolean, URL component, lookup output, v.v. |
| Allowed values/units | Giá trị, pattern hoặc unit được phép. |
| Required | Event hợp lệ có bắt buộc phải có giá trị không. |
| Missing behavior | Omit, block/fail QA hoặc approved default. |
| Environment | QA, staging, production hoặc tất cả. |
| Consent/privacy | Phụ thuộc consent và phân loại dữ liệu. |
| Consumers | Tag, Trigger, Lookup/RegEx Table hoặc Variable khác. |
| Owner/status | Team chịu trách nhiệm và trạng thái Active, Test, Deprecated hoặc Retired. |
| Replacement/review date | Variable thay thế và ngày review tiếp theo. |

## 4. Chọn source và type

### 4.1 Thứ tự ưu tiên source

Ưu tiên giá trị ổn định do Application đưa vào Data Layer thay vì đọc từ UI:

| Requirement | Variable nên dùng | Tránh dùng |
| --- | --- | --- |
| Đọc giá trị Application đã phê duyệt | Data Layer Variable | DOM scraping hoặc JavaScript tạm thời |
| Dùng lại giá trị cố định không nhạy cảm | Constant | Lặp literal trong nhiều Tag |
| Map input có kiểm soát và khớp chính xác | Lookup Table | Chuỗi `if` dài bằng JavaScript |
| Map pattern có kiểm soát | RegEx Table | Pattern rộng hoặc chồng lấn |
| Đọc thông tin URL | URL Variable | Tự parse `location` |
| Đọc first-party cookie đã duyệt | First-Party Cookie | Custom code không review |
| Đọc nội dung UI legacy ổn định | DOM Element | Text UI thay đổi theo locale |
| Biến đổi giá trị đồng bộ | Custom JavaScript | Đưa business logic vào GTM |

### 4.2 Quy tắc đọc Data Layer

GTM xử lý các message trong Data Layer theo thứ tự vào trước, xử lý trước. Giá trị có thể tiếp tục tồn tại sau một push; push mới có cùng key sẽ ghi đè giá trị cũ. Vì vậy:

- khởi tạo một `window.dataLayer` và luôn dùng `.push()`; không gán lại array sau khi GTM đã load;
- đặt `event` và toàn bộ field của event trong cùng một message;
- dùng Data Layer Variable Version 2 cho nested path như `inputs.connection_type`;
- giữ tên field và cách viết hoa/thường nhất quán;
- định nghĩa rõ optional field sẽ được bỏ qua hay xóa tường minh;
- không để giá trị của event trước tự động thay cho field đang thiếu.

Data Layer là object truyền dữ liệu, không phải nơi lưu một snapshot tách biệt cho từng event. Vì vậy message đầy đủ trong cùng một push là điều kiện để Variable đọc đúng.

### 4.3 Cách xử lý khi thiếu dữ liệu

Chọn một trong ba kết quả rõ ràng:

| Kết quả | Dùng khi | Kết quả thực tế |
| --- | --- | --- |
| Omit | Field là optional hoặc không áp dụng. | Variable không trả về giá trị; Tag bỏ parameter đó khỏi request. |
| Block/fail QA | Field bắt buộc để event hợp lệ. | Chặn flow event hoặc ghi nhận contract defect. |
| Approved default | Contract nghiệp vụ định nghĩa fallback thật sự. | Dùng default đã được ghi nhận. |

Không đổi giá trị thiếu thành `unknown`, `N/A`, empty string hoặc giá trị cũ nếu chưa có ý nghĩa được phê duyệt. Environment không xác định phải fail closed và không được fallback về production destination.

## 5. Quy chuẩn naming và folder

### 5.1 Naming

Dùng format:

```text
[SCOPE] - [TYPE] - [PURPOSE OR SOURCE]
```

Prefix type khuyến nghị:

| Prefix | GTM type | Ví dụ |
| --- | --- | --- |
| `DLV` | Data Layer Variable | `FD - DLV - inputs - connection_type` |
| `CONST` | Constant | `FD - CONST - GA4 Measurement ID - QA` |
| `LUT` | Lookup Table | `FD - LUT - Hostname to Measurement ID` |
| `RLT` | RegEx Table | `WEB - RLT - Page Path to Page Type` |
| `URL` | URL Variable | `WEB - URL - Query - campaign_id` |
| `DOM` | DOM Element | `WEB - DOM - Checkout Total` |
| `COOKIE` | First-Party Cookie | `SHARED - COOKIE - Consent State` |
| `JS` | Custom JavaScript | `FD - JS - Normalize Method` |

Với Data Layer Variable trực tiếp, giữ nguyên key/path `snake_case`. Với configuration hoặc mapping do GTM quản lý, dùng tên purpose dễ đọc. Tránh tên như `Variable 3`, `New Variable`, `Connection` hoặc `Test`.

### 5.2 Folder

Folder mô tả trách nhiệm; namespace trong tên Variable mô tả project sở hữu. Chỉ tạo những folder container thực sự cần:

```text
00 - Documentation
01 - Shared Foundations
02 - Project Data Layer
03 - Routing and Environment
04 - Consent and Privacy
05 - Measurement Configuration
07 - Utilities and Transformations
08 - Third-Party Integrations (chỉ khi đã được phê duyệt)
90 - Experiments
95 - Migration
99 - Deprecated
```

Không tạo folder chỉ vì nó có trong danh sách. Không chuyển Variable có vẻ không dùng vào `99 - Deprecated` trước khi kiểm tra toàn bộ consumer.

## 6. Cấu hình các pattern Variable phổ biến

### 6.1 Data Layer Variable

Dùng đúng Data Layer key và chọn Version 2 cho nested path. Giữ quan hệ một-một giữa Variable và contract; không thêm business interpretation hoặc fallback tùy ý.

### 6.2 Environment routing

Dùng Lookup Table hoặc configuration tương đương đã được review để map hostname QA, staging và production tới destination tương ứng. Hostname không xác định phải trả về blank/undefined hoặc chặn Tag phụ thuộc. Không dùng production Measurement ID làm default không kiểm soát.

### 6.3 Consent và privacy

Consent Variable có thể hỗ trợ configuration nhưng không thay thế Consent Mode hoặc consent setup đã phê duyệt. Consent default và update phải được thiết lập trước khi Tag phụ thuộc fire; xem Section 05. Không tạo Variable chứa PII, credential, token, payment data, text không giới hạn hoặc secret. Cấu hình GTM đã publish có thể nhìn thấy trên browser, vì vậy không lưu secret trong Constant.

### 6.4 Chính sách Custom JavaScript

Chỉ dùng Custom JavaScript khi native Variable type không đáp ứng được requirement. Code phải:

- nhỏ, đồng bộ, deterministic và không có side effect;
- xử lý an toàn `undefined`, `null`, sai type và giá trị bất ngờ;
- ghi rõ return type và failure behavior;
- an toàn về privacy, có tài liệu, owner, review và test.

Code không được gọi API, sửa application state/DOM/cookie, push thêm Data Layer event hoặc tự suy luận business result. Nếu tạm dùng để migrate legacy value, ghi rõ owner, lý do, kế hoạch thay thế và điều kiện xóa.

## 7. Test, publish và maintain

### 7.1 Test trước khi publish

Trong **GTM Preview**, kiểm tra Variable đang trả về giá trị nào và Tag/Trigger sử dụng Variable đó có hoạt động đúng không. Chỉ mở **Browser Network panel** khi Variable này góp phần tạo request ra ngoài và cần xác nhận request thực tế: request có được gửi không, gửi bao nhiêu lần, chứa parameter nào và đi tới đúng GA4 Measurement ID/destination không.

Với mỗi Variable mới hoặc thay đổi, hãy kiểm tra:

- giá trị bình thường, thiếu, optional, invalid và edge case;
- nested Data Layer path và việc các field xuất hiện trong cùng push;
- routing QA/staging/production và hostname không xác định;
- consent granted, denied và updated;
- toàn bộ Tag/Trigger consumer;
- parameter, request count và GA4 destination cuối cùng.

Với FD, kiểm tra toàn bộ flow:

```text
Data Layer message
    → giá trị canonical Variable
    → environment routing
    → Trigger
    → GA4 Event Tag
    → Network request
    → đúng GA4 Measurement ID
```

“Tag Fired” một mình chưa đủ evidence. Link kết quả với Evidence Template của Section 08.

### 7.2 Review và publish

Trước khi publish:

- Với Variable **shared** (được nhiều project hoặc Tag dùng chung), kiểm tra tất cả consumer để tránh làm hỏng flow khác.
- Với Variable **environment-sensitive** (giá trị thay đổi giữa QA, staging và production), kiểm tra từng environment và bảo đảm không có đường fallback về production.

Sau đó thực hiện các bước:

1. Xác nhận Variable contract và toàn bộ consumer.
2. Test mọi environment và consent state bị ảnh hưởng.
3. Review privacy và missing-data behavior.
4. Ghi QA evidence và owner.
5. Publish GTM change có version và release note dễ hiểu.

Ví dụ release note: `FD: separate staging and production Measurement ID routing by hostname.`

### 7.3 Variable inventory

Duy trì một registry có thể search với tối thiểu các thông tin:

```text
Variable name và scope
GTM type và exact source
Business meaning và allowed values/units
Required và missing-data behavior
Environment và consent/privacy classification
Consumers
Owner và status
Review date
Replacement nếu có
```

### 7.4 Deprecate và retire

Không xóa Variable chỉ vì nhìn thấy nó có vẻ không được dùng. Kiểm tra Tags, Triggers, Lookup/RegEx Tables, Custom Templates, Custom JavaScript và tham chiếu trong container/tài liệu khác. Dùng flow:

```text
Xác định Variable thay thế
    → đánh dấu Variable cũ là Deprecated
    → migrate consumer
    → test trong Preview
    → publish version đã review
    → monitor
    → xóa Variable cũ
```

## 8. Audit checklist và tham chiếu chéo

| Vấn đề phát hiện | Hành động đầu tiên |
| --- | --- |
| Nhiều Variable đọc cùng một contract | Gộp về một canonical Variable. |
| Variable đọc text UI phụ thuộc locale | Đưa giá trị ổn định vào Data Layer. |
| Staging trỏ về production | Thêm environment routing tường minh và fail-closed. |
| Missing data bị đổi thành `unknown` không được duyệt | Khôi phục quy tắc omit/block/default. |
| Custom JavaScript dựng lại business logic | Thiết kế lại Application/Data Layer contract. |
| Test Variable trở thành dependency production | Chủ động promote hoặc retire. |
| Variable không có consumer được phê duyệt | Xác nhận owner và xóa an toàn. |

- [Section 01 — Data Layer Design](01-data-layer-design-answer-vn.md): application-owned event contract và payload shape.
- [Section 03 — Trigger Management](03-trigger-management-answer-vn.md): dùng Variable trong điều kiện Custom Event Trigger.
- [Section 04 — Tag Management](04-tag-management-answer-vn.md): map canonical Variable vào GA4 Event parameter.
- [Section 05 — Consent Management](05-consent-answer-vn.md): consent default, update và denied-state behavior.
- [Section 06 — Template Governance](06-template-governance-answer-vn.md): governance khi custom template là lựa chọn bắt buộc.
- [Section 07 — Measurement Plan](07-measurement-plan-answer-vn.md): field approval, owner và reporting question.
- [Section 08 — Debug/QA](08-debug-qa-answer-vn.md): Variable evidence, test matrix và defect/retest record.
- [Section 09 — Reports and Charts](09-reports-charts-answer-vn.md): field readiness và processed-data interpretation.
- [Section 10 — Release Monitoring](10-release-monitoring-answer-vn.md): release gate, monitoring và rollback.

## 9. Ví dụ hoàn chỉnh: setup Variable cho FD

Đây là walkthrough FD cụ thể duy nhất. Thay project ID và hostname bằng giá trị đã được phê duyệt.

### 9.1 Contract

| Data Layer path | Type | Required | Missing behavior | Consumer |
| --- | --- | --- | --- | --- |
| `event` | string | Yes | Block | Custom Event Trigger |
| `event_schema_version` | string | Yes | Fail QA | Trigger và GA4 Event Tag |
| `app_name` | string | Yes | Fail QA | Trigger và GA4 Event Tag |
| `solution_found` | boolean | Yes | Fail QA | GA4 Event Tag |
| `inputs.connection_type` | string | Yes | Fail QA | GA4 Event Tag |
| `inputs.fx` | number | Conditional | Omit khi không áp dụng | GA4 Event Tag |
| `inputs.fy` | number | Conditional | Omit khi không áp dụng | GA4 Event Tag |

### 9.2 Data Layer message

```javascript
window.dataLayer.push({
  event: "calculation_action",
  event_schema_version: "1.0",
  app_name: "fd",
  solution_found: true,
  inputs: {
    connection_type: "clt_floor_floor_half_lap_joint",
    unit_system: "metric",
    fx: 1,
    fy: 0,
  },
});
```

### 9.3 Canonical Variable

```text
SHARED - DLV - solution_found
  Data Layer Variable Name: solution_found

FD - DLV - event_schema_version
  Data Layer Variable Name: event_schema_version

FD - DLV - inputs - connection_type
  Data Layer Variable Name: inputs.connection_type
  Version: 2

FD - DLV - inputs - fx
  Data Layer Variable Name: inputs.fx
  Version: 2

FD - DLV - inputs - fy
  Data Layer Variable Name: inputs.fy
  Version: 2
```

### 9.4 Environment lookup

```text
FD - LUT - Hostname to Measurement ID

app.example.com         → production Measurement ID
app-staging.example.com → QA/staging Measurement ID
unknown host            → blank/blocked
```

### 9.5 GA4 Tag mapping và missing value

```text
solution_found
  → {{SHARED - DLV - solution_found}}

connection_type
  → {{FD - DLV - inputs - connection_type}}

fx
  → {{FD - DLV - inputs - fx}}

fy
  → {{FD - DLV - inputs - fy}}
```

```text
fx là optional và không áp dụng
    → Variable trả về undefined
    → fx bị bỏ khỏi GA4 request

solution_found là required nhưng bị thiếu
    → QA failure / event bị block
```

### 9.6 Test end-to-end

```text
Application push một calculation_action message đầy đủ
    → GTM Preview hiển thị đúng DLV value
    → hostname map tới QA Measurement ID
    → Trigger đã duyệt match
    → một GA4 Event Tag fire
    → Network request chứa scalar parameter đã duyệt
    → request tới đúng GA4 destination
```

Test valid output, valid no-output, invalid input, API failure, stale response, duplicate callback, missing required field, unknown host và consent denied/granted. Ghi evidence trong Section 08 trước khi publish.

## Tài liệu tham khảo

- [Tag Manager Help — Variable](https://support.google.com/tagmanager/answer/13355320?hl=en)
- [Tag Manager Help — User-defined variable types for web](https://support.google.com/tagmanager/answer/7683362?hl=en)
- [Tag Manager Help — Components of Google Tag Manager](https://support.google.com/tagmanager/answer/6103657?hl=en)
- [Google for Developers — The data layer](https://developers.google.com/tag-platform/tag-manager/datalayer)
