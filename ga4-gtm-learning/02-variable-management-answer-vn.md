# 02 — Quản lý Variable trong GTM

## GTM variable là gì?

> Variable là một placeholder cung cấp giá trị động để Tag và Trigger sử dụng.

Variable chủ yếu được dùng ở hai nơi:

- **Trigger** — xác định KHI NÀO tag fire. Ví dụ: `Page URL contains "/products"` → fire GA4 tag.
- **Tag** — cung cấp GIÁ TRỊ cần gửi. Ví dụ: purchase event có thể dùng `transaction_id = "ABC123"` và `value = 100`.

Cách nhớ đơn giản:

```text
Variable = Giá trị là gì?
Trigger = Khi nào chạy?
Tag = Cần gửi/chạy điều gì?
```

## Quy tắc đánh giá Data Layer

Các quy tắc sau ảnh hưởng đến cách Data Layer Variable hoạt động:

- GTM xử lý các message trong Data Layer theo thứ tự vào trước, xử lý trước.
- Giá trị có thể vẫn còn sau một push trước đó, vì vậy event sau có thể vô tình đọc context cũ.
- Đặt event name và toàn bộ giá trị riêng của event trong cùng một push.
- Dùng Data Layer Variable Version 2 khi đọc path lồng nhau như `inputs.connection_type`; Version 2 hiểu dấu chấm là các value lồng nhau.
- Một push sau với cùng key sẽ ghi đè giá trị trước đó; nó không tạo object riêng biệt cho từng event.
- Với giá trị tùy chọn, phải quy định key được bỏ qua hay được xóa rõ ràng; không dựa vào giá trị trước đó chưa được document.

Khuyến nghị:

```javascript
window.dataLayer.push({
  event: "calculation_action",
  solution_found: true,
  inputs: {
    connection_type: "multi_ply_connection",
  },
});
```

Tránh chia một event thành nhiều message:

```javascript
window.dataLayer.push({ inputs: { connection_type: "multi_ply_connection" } });
window.dataLayer.push({ event: "calculation_action", solution_found: true });
```

## Decision flow cốt lõi cho variable

Dùng flow ngắn này trong công việc hằng ngày. Phần quản lý nhiều project bên dưới là tài liệu chuẩn chi tiết cho contract, naming, folder, consent, testing, inventory và retirement.

```text
CLASSIFY
Variable là shared, project-specific, temporary hay deprecated?
        ↓
SEARCH
Đã có canonical variable cùng source và meaning chưa?
        ↓
CONTRACT
Định nghĩa source path, business meaning, type, allowed values, missing behavior,
privacy classification, owner và consumers.
        ↓
SELECT TYPE
Dùng GTM native type đơn giản nhất đáp ứng requirement.
        ↓
NAME
Áp dụng [SCOPE] - [TYPE] - [PURPOSE OR SOURCE].
        ↓
CONFIGURE
Áp dụng environment routing, consent, privacy và safe-failure behavior.
        ↓
TEST
Verify Data Layer → Variable → Tag → Network destination.
        ↓
RELEASE AND MAINTAIN
Review, version, inventory, deprecate và retire an toàn.
```

### Chọn type nhanh

| Requirement | GTM variable ưu tiên | Tránh |
| --- | --- | --- |
| Đọc application-owned value | Data Layer Variable | DOM scraping |
| Tái sử dụng fixed value không phải secret | Constant | Lặp literal trong nhiều tag |
| Map exact controlled input | Lookup Table | Nhánh Custom JavaScript |
| Map controlled pattern | RegEx Table | Regex quá rộng hoặc overlap |
| Đọc URL component | URL Variable | Tự parse location |
| Đọc first-party cookie đã approve | First-Party Cookie | Custom code chưa review |
| Thực hiện synchronous transformation không thể tránh | Custom JavaScript | Chuyển business logic vào GTM |

Custom JavaScript là lựa chọn cuối. Code phải synchronous, deterministic, side-effect free, xử lý an toàn input thiếu/sai, privacy-safe, được document, review và test.

### Ba kết quả khi thiếu dữ liệu

| Kết quả | Dùng khi |
| --- | --- |
| Omit | Field optional hoặc không áp dụng |
| Block / fail QA | Value bắt buộc để event hợp lệ |
| Approved default | Business contract định nghĩa fallback có ý nghĩa thật |

Không chuyển missing value thành `unknown`, `N/A` hoặc empty string nếu chưa có semantic meaning được approve. Unknown environment không được resolve về production destination.

## Quản lý variable qua nhiều project

Khi nhiều project dùng chung GTM standard hoặc container, variable nên được quản lý theo lifecycle nhất quán thay vì tạo độc lập mỗi khi có requirement tracking mới.

Luồng quản lý khuyến nghị:

```text
Phân loại requirement
        ↓
Tìm trong variable inventory
        ↓
Reuse hoặc thiết kế variable canonical
        ↓
Chọn variable type phù hợp
        ↓
Áp dụng naming và folder standard
        ↓
Định nghĩa source, type, value và missing-data behavior
        ↓
Cấu hình environment và consent behavior
        ↓
Test trong GTM Preview
        ↓
Review và publish
        ↓
Duy trì inventory
        ↓
Deprecate và retire khi không còn cần
```

### 1. Phân loại variable

Trước khi tạo variable, xác định scope và lifecycle.

#### Shared foundation

Dùng cho value hoặc configuration được nhiều project dùng chung.

Ví dụ:

```text
SHARED - DLV - consent_state
SHARED - LUT - environment
```

Các concept dùng chung thường gồm:

- consent state;
- environment detection;
- campaign information dùng chung;
- shared configuration.

Một variable không nên trở thành shared chỉ vì hai project hiện có tên tương tự. Source, meaning, type, allowed values, fallback và consent behavior cũng phải tương thích.

#### Project-specific

Dùng khi value thuộc Data Layer contract hoặc measurement plan của một application.

Ví dụ:

```text
FD - DLV - inputs - connection_type
WEB - DLV - page_type
```

Ví dụ, `SHARED - DLV - solution_found` là calculation outcome dùng chung vì nhiều project dùng cùng field, Boolean type, meaning và missing-data behavior. Các input riêng như `FD - DLV - inputs - connection_type` phải giữ trong namespace của project sở hữu.

#### Temporary hoặc experimental

Dùng cho variable phục vụ experiment, migration hoặc proof of concept có thời hạn.

Ví dụ:

```text
FD - TEST - normalize_input
```

Variable tạm thời phải có owner và thời điểm review hoặc remove dự kiến.

Không được âm thầm trở thành production dependency lâu dài.

#### Deprecated

Dùng khi variable đã được thay thế nhưng chưa thể xóa an toàn.

Ví dụ:

```text
FD - OLD - calculation_status
```

replacement:

```text
SHARED - DLV - solution_found
```

Deprecation tạo thời gian để migrate consumer trước khi xóa.

### 2. Tìm kiếm trước khi tạo

Trước khi tạo variable mới, hãy tìm trong GTM container và variable inventory hiện có.

Mục tiêu là trả lời:

```text
Variable canonical cho business concept này đã tồn tại chưa?
```

Ví dụ, trước khi tạo:

```text
FD - DLV - print - solution_found
```

hãy tìm variable đã đọc:

```text
solution_found
```

Nếu đã có:

```text
SHARED - DLV - solution_found
```

và contract tương thích, hãy reuse.

Không tạo:

```text
FD - DLV - calculation - solution_found
FD - DLV - print - solution_found
FD - DLV - download - solution_found
```

khi cả ba cùng đọc một source với cùng meaning.

Thiết kế ưu tiên:

```text
               SHARED - DLV - solution_found
                          ↓
             ┌────────────┼────────────┐
             ↓            ↓            ↓
      Calculation Tag   Print Tag   Download Tag
```

Chỉ reuse variable khi các đặc tính sau tương thích:

- source;
- data type;
- business meaning;
- allowed values;
- missing-data behavior;
- environment behavior;
- consent requirements.

Nếu có khác biệt quan trọng ở bất kỳ điểm nào, hãy tạo variable riêng cho project.

### 3. Thiết kế variable contract

Trước khi cấu hình GTM, định nghĩa variable đại diện cho điều gì.

Tối thiểu cần document:

| Property         | Mô tả                                      |
| ---------------- | ------------------------------------------ |
| Name             | Tên GTM variable canonical                 |
| Scope            | Shared, FD, WEB, v.v.                      |
| Business meaning | Value đại diện cho điều gì                 |
| Source           | Data Layer path chính xác hoặc source khác |
| Type             | String, number, boolean, v.v.              |
| Allowed values   | Giá trị được kiểm soát nếu có              |
| Required         | Value có bắt buộc tồn tại không             |
| Missing behavior | Omit, block/fail QA hoặc default được duyệt |
| Environment      | Production, staging, QA hoặc tất cả         |
| Consumers        | Tag, trigger hoặc variable khác             |
| Owner            | Team chịu trách nhiệm                       |
| Privacy          | Phân loại dữ liệu/privacy                   |
| Status           | Active, experimental, deprecated            |
| Replacement      | Variable thay thế khi deprecated             |

Ví dụ:

| Property         | Value                                                                              |
| ---------------- | ---------------------------------------------------------------------------------- |
| Name             | `SHARED - DLV - solution_found`                                                    |
| Scope            | Shared, được nhiều project tương thích sử dụng                                     |
| Business meaning | Cho biết một calculation hoàn tất có trả về ít nhất một output hợp lệ hay không    |
| Source           | `solution_found`                                                                   |
| Type             | Boolean                                                                            |
| Allowed values   | `true`, `false`                                                                    |
| Required         | Có, đối với completed calculation event                                           |
| Missing behavior | Fail QA; không âm thầm đổi thành unknown hoặc fallback khác                       |
| Environment      | Tất cả                                                                             |
| Consumers        | GA4 tag liên quan calculation trong FD và các project tham gia                     |
| Owner            | Shared Analytics / Analytics Governance                                            |
| Status           | Active                                                                             |
| Privacy          | Business data không nhạy cảm                                                       |
| Replacement      | None                                                                              |

Contract phải được định nghĩa trước khi GTM bắt đầu transform hoặc diễn giải value.

### 4. Ưu tiên value do application sở hữu trong Data Layer

Nếu application đã biết business value, hãy expose qua Data Layer thay vì dựng lại trong GTM.

Ưu tiên:

```javascript
window.dataLayer.push({
  event: "calculation_action",
  solution_found: true,
  inputs: {
    connection_type: "multi_ply_connection",
  },
});
```

GTM chỉ đọc:

```text
SHARED - DLV - solution_found
→ solution_found

FD - DLV - inputs - connection_type
→ inputs.connection_type
```

Tránh phụ thuộc vào UI label như:

```text
Multi-Ply Connection
```

khi application có thể cung cấp business value ổn định:

```text
multi_ply_connection
```

Cách này ngăn thay đổi wording, localization hoặc layout làm thay đổi dữ liệu analytics.

Mô hình ownership ưu tiên:

```text
Application
    ↓
Business rules
Stable business values
    ↓
Data Layer contract
    ↓
GTM Variables
    ↓
Tracking configuration
    ↓
GA4
```

GTM không nên trở thành implementation thứ hai của business logic application.

### 5. Chọn variable type phù hợp

Dùng native GTM variable type đơn giản nhất đáp ứng requirement.

| Requirement                       | Type ưu tiên          | Cần tránh                         |
| --------------------------------- | --------------------- | -------------------------------- |
| Đọc value Data Layer đã phê duyệt | Data Layer Variable   | DOM scraping                     |
| Reuse value cố định, không phải secret | Constant          | Lặp value trong nhiều tag        |
| Map input chính xác sang output   | Lookup Table          | Chuỗi `if` JavaScript lớn        |
| Map pattern có kiểm soát          | RegEx Table            | Custom JavaScript phức tạp       |
| Đọc thông tin URL                 | URL Variable           | Tự parse `location`              |
| Đọc first-party cookie            | First-Party Cookie     | Custom JavaScript khi không cần  |
| Đọc page content legacy ổn định   | DOM Element            | Scrape UI không ổn định/localized |
| Transformation không thể tránh   | Custom JavaScript      | Dùng JS làm giải pháp mặc định   |

Luồng quyết định:

```text
Có phải value built-in phù hợp không?
        ↓ Không

Có trong Data Layer contract không?
        ↓ Không

Có phải value cố định dùng chung không?
        ↓ Không

Có phải mapping chính xác không?
        ↓ Không

Có phải pattern có kiểm soát không?
        ↓ Không

URL / Cookie / native type khác có xử lý được không?
        ↓ Không

Cân nhắc Custom JavaScript
```

Custom JavaScript là lựa chọn cuối cùng, không phải đầu tiên.

#### Bảo vệ Lookup Table và RegEx Table

Với mỗi Lookup Table hoặc RegEx Table, document input variable, quy tắc match, output type, default behavior và owner. Test khác biệt về case, input bị thiếu, pattern chồng lấn và value không xác định. RegEx được đánh giá từ trên xuống dưới, vì vậy đặt rule cụ thể nhất lên trước. Không dùng production destination làm default vô tình.

### 6. Áp dụng naming standard

Dùng cấu trúc dễ dự đoán:

```text
[SCOPE] - [TYPE] - [PURPOSE OR SOURCE]
```

Mỗi phần có trách nhiệm riêng:

- **SCOPE** xác định project hoặc group sở hữu variable, như `FD`, `WEB` hoặc `SHARED`.
- **TYPE** xác định GTM variable type, như `DLV`, `CONST` hoặc `LUT`.
- **PURPOSE OR SOURCE** mô tả Data Layer source chính xác hoặc purpose dễ hiểu của variable.

Ví dụ:

```text
SHARED - DLV - solution_found
FD - DLV - inputs - connection_type

FD - LUT - Hostname to Measurement ID
FD - CONST - GA4 Measurement ID - Production

SHARED - DLV - consent_state
```

#### Dùng `snake_case` cho Data Layer field

Khi GTM variable đọc trực tiếp Data Layer, giữ nguyên tên field được định nghĩa trong Data Layer contract.

Ví dụ application gửi:

```javascript
window.dataLayer.push({
  event: "calculation_action",
  solution_found: true,
  inputs: {
    connection_type: "multi_ply_connection",
  },
});
```

Variable tương ứng:

```text
SHARED - DLV - solution_found
FD - DLV - inputs - connection_type
```

Mối liên hệ với source phải dễ trace:

```text
SHARED - DLV - solution_found
               ↓
Data Layer: solution_found
```

và:

```text
FD - DLV - inputs - connection_type
           ↓
Data Layer: inputs.connection_type
```

#### Dùng tên dễ đọc cho concept do GTM quản lý

Khi variable đại diện cho configuration, routing, mapping, transformation hoặc concept khác do GTM tạo và quản lý, dùng mô tả dễ đọc.

Ví dụ:

```text
FD - CONST - GA4 Measurement ID - Production
FD - LUT - Hostname to Measurement ID
WEB - RLT - Page Path to Page Type
SHARED - COOKIE - Consent State
```

Các variable này không trực tiếp đại diện cho Data Layer key.

Ví dụ:

```text
FD - CONST - GA4 Measurement ID - Production
```

chứa value do GTM quản lý:

```text
G-XXXXXXXXXX
```

Không có Data Layer field tương ứng như:

```text
ga4_measurement_id_production
```

Vì vậy tên mô tả dễ đọc sẽ thể hiện purpose tốt hơn.

Tương tự:

```text
FD - LUT - Hostname to Measurement ID
```

mô tả mapping GTM:

```text
app.strongtie.com
→ Production Measurement ID

app-staging.strongtie.com
→ Staging Measurement ID
```

#### Quy tắc quyết định tên

| Variable đại diện cho       | Cách đặt tên                         | Ví dụ                                        |
| --------------------------- | ------------------------------------ | -------------------------------------------- |
| Data Layer field chính xác  | Giữ `snake_case`                     | `SHARED - DLV - solution_found`              |
| Data Layer path lồng nhau   | Giữ đúng các segment của path        | `FD - DLV - inputs - connection_type`        |
| GTM configuration            | Dùng tên dễ đọc                      | `FD - CONST - GA4 Measurement ID - Production` |
| Lookup mapping               | Dùng tên dễ đọc                      | `FD - LUT - Hostname to Measurement ID`      |
| RegEx classification         | Dùng tên dễ đọc                      | `WEB - RLT - Page Path to Page Type`         |
| DOM concept                  | Dùng tên dễ đọc                      | `WEB - DOM - Checkout Total`                 |
| Cookie                       | Tên dễ đọc, trừ khi cần giữ cookie name chính xác | `SHARED - COOKIE - Consent State` |
| Custom transformation        | Purpose dễ đọc                       | `FD - JS - Normalize Method`                 |

Luồng quyết định:

```text
Variable có trực tiếp đại diện cho
Data Layer field/path không?
        │
   ┌────┴────┐
   │         │
  Có         Không
   │         │
   ▼         ▼
Giữ đúng    Dùng purpose
field/path  dễ đọc
   │         │
   ▼         ▼
solution_found
            GA4 Measurement ID - Production

inputs.connection_type
            Hostname to Measurement ID
```

Mục tiêu không phải khiến mọi variable dùng cùng một kiểu chữ, mà là giúp mọi variable dễ trace và dễ hiểu.

#### Prefix type khuyến nghị

| Prefix   | Type                | Ví dụ                                  |
| -------- | ------------------- | -------------------------------------- |
| `DLV`    | Data Layer Variable | `SHARED - DLV - solution_found`        |
| `CONST`  | Constant            | `FD - CONST - GA4 Measurement ID - QA` |
| `LUT`    | Lookup Table        | `FD - LUT - Hostname to Measurement ID` |
| `RLT`    | RegEx Table         | `WEB - RLT - Page Path to Page Type`   |
| `URL`    | URL Variable        | `WEB - URL - Query - campaign_id`      |
| `DOM`    | DOM Element         | `WEB - DOM - Checkout Total`            |
| `COOKIE` | First-Party Cookie  | `SHARED - COOKIE - Consent State`       |
| `JS`     | Custom JavaScript   | `FD - JS - Normalize Method`            |

Tên phải giúp hiểu **scope, type, source và purpose** mà không cần mở cấu hình variable.

Tránh tên mơ hồ như:

```text
Variable 3
New Variable
Connection
Test
GA Value
```

### 7. Tổ chức variable trong folder

Dùng cấu trúc folder nhất quán để variable dễ tìm, review, bảo trì và audit giữa nhiều project.

Folder thể hiện **trách nhiệm hoặc purpose**, trong khi namespace của variable thể hiện **project ownership**.

Ví dụ:

```text
FD - DLV - inputs - connection_type
│
└── FD = project ownership

02 - Project Data Layer
│
└── Data Layer = responsibility
```

#### Cấu trúc folder khuyến nghị

```text
00 - Documentation

01 - Shared Foundations
02 - Project Data Layer
03 - Routing and Environment
04 - Consent and Privacy
05 - Measurement Configuration
06 - Marketing and Attribution
07 - Utilities and Transformations
08 - Third-Party Integrations

90 - Experiments
95 - Migration
99 - Deprecated
```

| Folder                               | Purpose                                                                                          | Ví dụ                                         |
| ------------------------------------ | ------------------------------------------------------------------------------------------------ | --------------------------------------------- |
| `00 - Documentation`                 | Documentation, convention, ownership reference và tài nguyên governance                         | Variable registry hoặc naming standard        |
| `01 - Shared Foundations`            | Concept/configuration dùng chung nhiều project                                                   | `SHARED - DLV - solution_found`               |
| `02 - Project Data Layer`            | Variable đọc Data Layer của từng project                                                        | `FD - DLV - inputs - connection_type`         |
| `03 - Routing and Environment`       | Environment detection, hostname routing hoặc destination selection                              | `FD - LUT - Hostname to Measurement ID`       |
| `04 - Consent and Privacy`           | Consent state, privacy control và collection permission                                          | `SHARED - DLV - consent_state`                |
| `05 - Measurement Configuration`     | Analytics configuration dùng chung như Measurement ID                                             | `FD - CONST - GA4 Measurement ID - Production` |
| `06 - Marketing and Attribution`     | Campaign, attribution và marketing parameter                                                     | `WEB - URL - Query - utm_source`              |
| `07 - Utilities and Transformations` | Helper variable cho mapping, normalization hoặc transformation đơn giản được phê duyệt          | `FD - LUT - Connection Type Mapping`          |
| `08 - Third-Party Integrations`      | Variable cho integration analytics/marketing đã phê duyệt ngoài platform đo lường chính          | Vendor-specific configuration variable        |
| `90 - Experiments`                   | Variable tạm thời cho experiment, proof of concept hoặc test ngắn hạn                            | `FD - TEST - normalize_input`                 |
| `95 - Migration`                     | Variable tạm trong quá trình chuyển contract hoặc implementation cũ sang mới                     | `SHARED - DLV - solution_found - v2`          |
| `99 - Deprecated`                    | Variable đã thay thế và chờ xóa an toàn                                                           | `FD - OLD - calculation_status`               |

#### Ví dụ tổ chức

Một container có thể gồm:

```text
01 - Shared Foundations
└── SHARED - DLV - solution_found

02 - Project Data Layer
├── FD - DLV - inputs - connection_type
├── FD - DLV - inputs - fx
└── FD - DLV - inputs - fy

03 - Routing and Environment
└── FD - LUT - Hostname to Measurement ID

04 - Consent and Privacy
└── SHARED - DLV - consent_state

05 - Measurement Configuration
├── FD - CONST - GA4 Measurement ID - Production
└── FD - CONST - GA4 Measurement ID - Staging

06 - Marketing and Attribution
└── WEB - URL - Query - utm_source

07 - Utilities and Transformations
└── FD - LUT - Connection Type Mapping

90 - Experiments
└── FD - TEST - normalize_input

95 - Migration
└── SHARED - DLV - solution_found - v2

99 - Deprecated
└── FD - OLD - calculation_status
```

#### Trách nhiệm của folder và namespace

Folder và namespace giải quyết hai vấn đề khác nhau.

Namespace cho biết **ai sở hữu variable hoặc variable thuộc đâu**:

```text
SHARED - ...
FD - ...
WEB - ...
```

Folder cho biết **variable phục vụ trách nhiệm nào**:

```text
02 - Project Data Layer
03 - Routing and Environment
05 - Measurement Configuration
```

Ví dụ:

```text
FD - LUT - Hostname to Measurement ID
```

nghĩa là:

```text
FD
→ Owned by FD project

LUT
→ Lookup Table variable

Hostname to Measurement ID
→ Purpose của variable

03 - Routing and Environment
→ Responsibility/category
```

Tách như vậy tránh cấu trúc riêng cho từng project quá sâu và giúp audit các variable có cùng responsibility giữa nhiều project.

#### Quy tắc tạo folder

Không tạo folder chỉ vì nó xuất hiện trong cấu trúc khuyến nghị. Chỉ tạo khi container có variable thuộc responsibility đó.

Ví dụ, nếu container không có marketing hoặc attribution variable thì không cần tạo:

```text
06 - Marketing and Attribution
```

Tương tự, không chuyển variable vào `99 - Deprecated` chỉ vì nhìn như không dùng. Trước hết phải kiểm tra consumer trong tag, trigger, Lookup Table, RegEx Table và Custom JavaScript.

Lifecycle khuyến nghị:

```text
Active variable
      ↓
Đã xác định replacement
      ↓
Đã migrate consumer
      ↓
Chuyển vào 99 - Deprecated
      ↓
Xác minh không còn dependency
      ↓
Xóa qua release đã review
```

Shared standard không tự động làm variable được chia sẻ giữa các GTM container. Nếu nhiều container dùng cùng standard, mỗi container vẫn cần implementation riêng đã được phê duyệt.

#### Quy ước đánh số folder

Số folder không cần liên tục.

Folder `00–89` dành cho category chức năng đang active, `90–99` dành cho trạng thái tạm thời và lifecycle:

```text
00–89 → Active categories
90    → Experiments
95    → Migration
99    → Deprecated
```

### 8. Định nghĩa behavior khi thiếu dữ liệu

Mọi variable phải có behavior rõ ràng khi source value không có.

Dùng một trong ba chiến lược:

| Chiến lược       | Dùng khi                                      | Ví dụ                                                                    |
| ---------------- | --------------------------------------------- | ------------------------------------------------------------------------ |
| Omit             | Value là tùy chọn hoặc không áp dụng           | `inputs.shearPlane` vắng mặt với connection type không dùng nó          |
| Block / fail QA  | Value bắt buộc cho event hợp lệ                | `solution_found` thiếu trong completed calculation event                 |
| Default được duyệt | Business contract định nghĩa fallback thực sự | `language = en` chỉ khi `en` là default được phê duyệt                 |

Với FD input tùy chọn:

```text
Data Layer field không có
        ↓
FD - DLV variable
        ↓
undefined
        ↓
Không tạo fallback nhân tạo
        ↓
Parameter bị bỏ qua trong GA4 hit
```

Không tự động đổi value thiếu thành:

```text
unknown
N/A
empty
```

trừ khi các value đó có business meaning được phê duyệt.

Với value bắt buộc:

```text
Value bắt buộc bị thiếu
        ↓
Tracking contract không hợp lệ
        ↓
Block / fail QA
        ↓
Sửa Data Layer implementation
```

Missing behavior là một phần của variable contract, không phải fallback GTM tùy ý.
Missing behavior là một phần của variable contract, không phải fallback GTM tùy ý.

### 9. Cấu hình variable phụ thuộc environment

Variable có value phụ thuộc environment phải phân biệt rõ production, staging, QA và development.

Ví dụ:

```text
FD - LUT - Hostname to Measurement ID
```

có thể mapping:

```text
app.strongtie.com
→ Production Measurement ID

app-staging.strongtie.com
→ Staging Measurement ID
```

Routing kỳ vọng:

```text
                     FD
                      │
          ┌───────────┴───────────┐
          │                       │
 app.strongtie.com      app-staging.strongtie.com
          │                       │
          ▼                       ▼
 Production ID               Staging ID
          │                       │
          ▼                       ▼
 Production GA4               Test GA4
```

Không dùng production Measurement ID làm default không kiểm soát.

Environment không xác định thông thường nên resolve thành:

```text
Unknown hostname
→ undefined
→ Production tag không fire
```

#### Phát hiện hiện tại của FD

Cấu hình Measurement ID hiện tại nhận diện FD chủ yếu bằng application path:

```text
/fd.*
→ G-VKTPQCSRW3
```

Cả production và staging đều dùng path `/fd/...`:

```text
app.strongtie.com/fd/...
app-staging.strongtie.com/fd/...
```

Testing xác nhận event từ staging FD application được resolve thành:

```text
G-VKTPQCSRW3
```

đây là production FD Measurement ID.

Luồng hiện tại có thể là:

```text
app-staging.strongtie.com/fd/...
        ↓
      /fd.*
        ↓
G-VKTPQCSRW3
        ↓
Production GA4
```

Điều này khiến calculation staging, hoạt động QA và traffic debug làm nhiễu analytics production.

Vì vậy Measurement ID routing phải phân biệt cả application và environment.

#### Cấu hình FD khuyến nghị

Measurement ID mapping phải xét environment, không chỉ application path.

Ví dụ:

```text
app.strongtie.com
+ /fd/*
→ G-VKTPQCSRW3

app-staging.strongtie.com
+ /fd/*
→ G-STAGING123
```

Routing kỳ vọng:

```text
                         FD
                          │
              ┌───────────┴───────────┐
              │                       │
      app.strongtie.com      app-staging.strongtie.com
              │                       │
              ▼                       ▼
       G-VKTPQCSRW3              G-STAGING123
              │                       │
              ▼                       ▼
       Production GA4              Test GA4
```

Nếu staging không cần collection analytics, staging nên resolve thành không có production destination:

```text
app-staging.strongtie.com
+ /fd/*
→ undefined
→ Production GA4 tag không fire
```

Dedicated staging Measurement ID là lựa chọn tốt hơn khi team cần test GA4 event, parameter, Data Layer change và reporting mà không ảnh hưởng production.

### 10. Áp dụng quy tắc consent và privacy

Variable không được expose dữ liệu bị cấm, private hoặc nhạy cảm về bảo mật.

Không tạo variable chứa:

```text
Email address
Password
Access token
Authentication token
Secret
Thông tin thẻ tín dụng
User input không giới hạn
```

Variable liên quan consent có thể hỗ trợ tag behavior, nhưng không được bypass consent configuration kiểm soát việc collection. Consent Initialization phải thiết lập default consent trước các tag khác, và consent update phải được xử lý trước event phụ thuộc. Review built-in consent check và additional consent setting của từng tag; chỉ có consent variable không có nghĩa là đã triển khai Consent Mode.

Mỗi inventory entry của variable nên có privacy classification khi phù hợp.

Web GTM còn cung cấp các built-in/utility variable như Event, Auto-Event, Element Visibility, Environment Name, Container ID/version, Debug Mode, Analytics Storage, Google tag Event Settings và User-provided Data. Chỉ enable hoặc document khi measurement requirement trong scope cần chúng. Không áp dụng nguyên xi hướng dẫn web variable này cho server-side GTM hoặc mobile container.

Không bao giờ lưu secret trong GTM Constant. Cấu hình GTM đã publish có thể được truy cập từ browser.

### 11. Chính sách Custom JavaScript

Chỉ dùng Custom JavaScript khi native GTM variable type không thể đáp ứng requirement an toàn.

Trước khi tạo Custom JavaScript, kiểm tra requirement có thể xử lý bằng:

- Data Layer Variable;
- Constant;
- Lookup Table;
- RegEx Table;
- URL Variable;
- First-Party Cookie;
- hoặc built-in GTM variable type khác.

Ví dụ:

```text
Hostname → Measurement ID
```

thông thường nên dùng **Lookup Table**, không dùng Custom JavaScript.

#### Requirement cho Custom JavaScript

Khi Custom JavaScript là cần thiết, phải tuân theo:

| Requirement                  | Ý nghĩa                                                                                         |
| ---------------------------- | ------------------------------------------------------------------------------------------------ |
| **Nhỏ và tập trung**         | Chỉ thực hiện một transformation hoặc calculation rõ ràng; tránh business logic lớn.            |
| **Deterministic**             | Cùng input phải luôn cho cùng output.                                                            |
| **Synchronous**               | Trả value ngay; không phụ thuộc asynchronous API call.                                           |
| **Không side effect**         | Không sửa DOM, application state, cookie, local storage hoặc push Data Layer event mới.         |
| **Xử lý input an toàn**       | Xử lý `undefined`, `null`, value sai và data type bất ngờ mà không throw error.                  |
| **Return type rõ ràng**       | Document rõ variable có thể trả về type và value nào.                                           |
| **Failure behavior rõ ràng**  | Quy định khi transformation thất bại, ví dụ trả về `undefined`.                                  |
| **An toàn privacy**           | Không đọc hoặc expose PII, secret, token, user input không giới hạn hay dữ liệu bị cấm.          |
| **Có documentation**          | Nêu purpose, input, output, fallback và lý do không dùng native GTM variable được.               |
| **Có owner**                  | Giao cho project hoặc team chịu trách nhiệm bảo trì.                                            |
| **Đã review**                 | Code review trước publish hoặc thay đổi quan trọng.                                              |
| **Đã test**                   | Test input bình thường, thiếu, sai và edge case trong GTM Preview.                              |

Tối thiểu cần test:

```text
normal value
undefined
null
empty value
unexpected type
invalid value
```

Ví dụ, tránh code không an toàn:

```javascript
function () {
  return {{FD - DLV - inputs - connection_type}}.toLowerCase();
}
```

Nếu Data Layer value là `undefined`, code có thể fail.

Ưu tiên xử lý phòng thủ:

```javascript
function () {
  var value = {{FD - DLV - inputs - connection_type}};

  if (typeof value !== "string" || value === "") {
    return undefined;
  }

  return value.toLowerCase();
}
```

Behavior lúc này rõ ràng:

```text
String hợp lệ
→ value đã transform

undefined / null / sai type
→ undefined
→ thực hiện missing-data behavior đã định nghĩa
```

#### Không chuyển application business logic vào GTM

Custom JavaScript không nên trở thành workaround lâu dài cho Data Layer contract kém.

Ví dụ application gửi UI label:

```text
"Multi-Ply Connection"
```

trong khi analytics cần:

```text
multi_ply_connection
```

GTM có thể tạm transform trong giai đoạn migration.

Tuy nhiên, giải pháp dài hạn ưu tiên là application cung cấp trực tiếp analytics value ổn định:

```javascript
dataLayer.push({
  inputs: {
    connection_type: "multi_ply_connection",
  },
});
```

GTM sau đó chỉ đọc:

```text
inputs.connection_type
```

Luồng ưu tiên:

```text
Application
    ↓
Business logic và stable values
    ↓
Data Layer
    ↓
Native GTM Variables
    ↓
GA4
```

thay vì:

```text
Application
    ↓
Display/raw values
    ↓
Custom JavaScript lớn trong GTM
    ↓
GA4
```

Nếu Custom JavaScript được đưa vào như giải pháp migration tạm thời, document:

```text
Owner
Reason
Replacement plan
Expected removal condition
```

### 12. Test trước khi publish

Mọi variable mới hoặc đã chỉnh sửa phải được test trong GTM Preview.

Tối thiểu xác minh:

- value bình thường;
- value bị thiếu;
- value tùy chọn/không áp dụng;
- value không hợp lệ;
- edge value;
- staging environment;
- production environment;
- consent behavior;
- tag consumer;
- GA4 payload cuối cùng.

Với FD calculation event, kiểm tra:

```text
Data Layer message
        ↓
DLV values
        ↓
Environment routing
        ↓
Trigger
        ↓
GA4 Event Tag
        ↓
g/collect payload
        ↓
GA4 Measurement ID đúng
```

Không dừng test ở:

```text
Tag Fired ✓
```

Hãy xác minh request cuối cùng.

Ví dụ:

```text
Staging
→ tid = Staging Measurement ID

Production
→ tid = Production Measurement ID
```

Với variable tùy chọn:

```text
DLV = undefined
→ parameter không có trong GA4 hit cuối cùng
```

Với variable bắt buộc:

```text
DLV = undefined
→ QA failure / event bị block
```

### 13. Review và publish

Thay đổi ảnh hưởng shared variable cần được review bởi mọi project hoặc owner bị ảnh hưởng.

Trước khi publish:

1. Xác nhận variable contract.
2. Xác nhận consumer hiện có.
3. Test mọi environment bị ảnh hưởng.
4. Xác minh consent và privacy behavior.
5. Review shared dependency.
6. Ghi nhận test evidence.
7. Publish thay đổi GTM container theo version.
8. Thêm release note có ý nghĩa.

Ví dụ release note:

```text
FD: Tách routing GA4 Measurement ID staging và production theo hostname.
```

Điều này giúp audit và rollback về sau dễ hơn.

### 14. Duy trì variable inventory

Duy trì một registry có thể tìm kiếm cho các variable đã được phê duyệt.

Field khuyến nghị:

| Field            | Purpose                          |
| ---------------- | -------------------------------- |
| Variable         | Tên canonical                    |
| Scope            | Shared, FD, WEB, v.v.            |
| Type             | DLV, LUT, Constant, v.v.         |
| Exact source     | Data Layer path hoặc input       |
| Business meaning | Value đại diện cho điều gì       |
| Expected type    | String, boolean, number, v.v.    |
| Allowed values   | Giá trị được kiểm soát           |
| Required         | Yes/No                           |
| Missing behavior | Omit, block, default             |
| Consumers        | Tag/trigger/variable             |
| Owner            | Team chịu trách nhiệm            |
| Environment      | All, production, staging, v.v.   |
| Privacy          | Phân loại dữ liệu                |
| Status           | Active, test, deprecated         |
| Review date      | Lần review gần nhất              |
| Replacement      | Replacement canonical nếu retire |

Ví dụ:

| Variable                         | Scope  | Source              | Consumers           | Missing behavior     | Owner        | Status     |
| -------------------------------- | ------ | ------------------- | ------------------- | -------------------- | ------------ | ---------- |
| `SHARED - DLV - consent_state`   | Shared | `consent_state`     | Shared tags         | Block khi cần        | Governance   | Active     |
| `SHARED - DLV - solution_found`  | Shared | `solution_found`    | FD calculation tags | Fail QA              | Governance   | Active     |
| `FD - DLV - inputs - shearPlane` | FD     | `inputs.shearPlane` | FD calculation tag  | Omit                 | FD Analytics | Active     |
| `FD - OLD - calculation_status`  | FD     | Legacy              | None                | N/A                  | FD Analytics | Deprecated |

Hãy tìm trong inventory trước khi tạo variable mới.

### 15. Deprecate và retire an toàn

Không xóa variable chỉ vì nhìn như không dùng.

Trước hết kiểm tra variable có được consume bởi:

```text
Tags
Triggers
Lookup Tables
RegEx Tables
Custom Templates
Custom JavaScript
```

Luồng retirement:

```text
Xác định replacement
        ↓
Đánh dấu variable cũ là deprecated
        ↓
Tìm mọi consumer
        ↓
Migrate consumer
        ↓
Test trong Preview
        ↓
Publish thay đổi đã review
        ↓
Monitor
        ↓
Xóa variable cũ
```

Ví dụ:

```text
FD - OLD - calculation_status
```

có thể được thay bằng:

```text
SHARED - DLV - solution_found
```

Variable cũ nên giữ trong Deprecated folder cho đến khi mọi consumer đã migrate và replacement đã được xác minh.

## Các case audit nhiều project

Khi review container hiện có, tìm các vấn đề sau:

| Audit case               | Ví dụ                                                | Hành động                                |
| ------------------------ | ---------------------------------------------------- | ---------------------------------------- |
| Duplicate                | Nhiều DLV đọc cùng `solution_found` contract        | Gom về một variable canonical             |
| Phụ thuộc DOM            | Variable đọc UI text đã localized                     | Đưa value vào Data Layer                  |
| Environment leak         | Staging resolve tới production Measurement ID       | Thêm environment routing rõ ràng          |
| Che giấu dữ liệu thiếu   | `undefined` thành `unknown` không được phê duyệt    | Khôi phục omit/block behavior rõ ràng     |
| Không dùng                | Variable không có consumer đã phê duyệt              | Xác nhận owner và retire                  |
| Custom JS rủi ro          | JS dựng lại application business logic              | Thiết kế lại Data Layer contract          |
| Variable tạm thời         | `TEST` variable thành dependency lâu dài             | Review để promote hoặc retire             |

## Ví dụ: Luồng quản lý variable cho FD Calculation

Giả sử application phát:

```javascript
window.dataLayer.push({
  event: "calculation_action",
  event_schema_version: "1.0",
  app_name: "fd",
  solution_found: true,
  inputs: {
    country: "gb",
    connection_type: "clt_floor_floor_half_lap_joint",
    unit_system: "metric",
    fx: 1,
    fy: 0,
  },
});
```

### Bước 1 — Định nghĩa Data Layer contract

| Field                    | Type    | Required   | Missing behavior          | Source                          |
| ------------------------ | ------- | ---------- | ------------------------- | ------------------------------- |
| `event`                  | string  | Yes        | Block                     | Application calculation handler |
| `event_schema_version`   | string  | Yes        | Fail QA                   | Application constant             |
| `app_name`               | string  | Yes        | Fail QA                   | Application constant             |
| `solution_found`         | boolean | Yes        | Fail QA                   | Calculation response             |
| `inputs.connection_type` | string  | Yes        | Fail QA                   | Calculation input snapshot       |
| `inputs.fx`              | number  | Conditional | Omit khi không áp dụng   | Calculation input snapshot       |
| `inputs.fy`              | number  | Conditional | Omit khi không áp dụng   | Calculation input snapshot       |

### Bước 2 — Tìm trong inventory

Kiểm tra variable canonical đã tồn tại chưa. Reuse khi contract khớp.

### Bước 3 — Chỉ tạo variable canonical còn thiếu

Ví dụ:

```text
SHARED - DLV - solution_found
FD - DLV - inputs - connection_type
FD - DLV - inputs - fx
FD - DLV - inputs - fy
FD - DLV - event_schema_version
```

Dùng đúng Data Layer path và không tạo bản sao theo từng tag.

### Bước 4 — Cấu hình environment routing

Dùng:

```text
FD - LUT - Hostname to Measurement ID
```

để route rõ ràng:

```text
app.strongtie.com
→ Production Measurement ID

app-staging.strongtie.com
→ Staging Measurement ID
```

### Bước 5 — Kết nối variable với GA4 tag

GA4 Event tag dùng các variable canonical:

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

### Bước 6 — Áp dụng missing-data rule

Với value bắt buộc:

```text
solution_found = undefined
→ QA failure
```

Với value chỉ áp dụng trong một số trường hợp:

```text
fx không áp dụng
→ undefined
→ bỏ qua trong GA4 hit
```

Không đổi bất kỳ trường hợp nào thành giá trị tùy ý như `"unknown"`.

### Bước 7 — Test end-to-end

Xác minh:

```text
Application
    ↓
Data Layer contract
    ↓
Canonical GTM Variables
    ↓
Environment routing
    ↓
Trigger
    ↓
GA4 Event Tag
    ↓
Final g/collect payload
    ↓
GA4 destination đúng
```

Test calculation bình thường, response không có solution, input bị thiếu, input tùy chọn, API error, duplicate callback, staging, production và consent state.

### Bước 8 — Publish và maintain

Sau review của development, analytics và QA:

```text
Test evidence
    ↓
Versioned GTM release
    ↓
Release note
    ↓
Cập nhật Variable Registry
    ↓
Monitor
```

Variable bị thay thế phải đi qua deprecation workflow, không xóa ngay.

---

## Tóm tắt

Quy trình quản lý variable có thể mở rộng nên theo lifecycle:

```text
CLASSIFY
Project hoặc scope nào sở hữu variable?
        ↓
SEARCH
Variable canonical đã tồn tại chưa?
        ↓
DESIGN
Source, meaning, type và missing behavior là gì?
        ↓
SELECT TYPE
Native GTM variable type đơn giản nhất là gì?
        ↓
NAME & ORGANIZE
Áp dụng namespace và folder standard.
        ↓
CONFIGURE
Áp dụng environment, consent, privacy và fallback rule.
        ↓
TEST
Xác minh Data Layer → Variable → Tag → GA4 payload.
        ↓
PUBLISH
Review và release thay đổi GTM theo version.
        ↓
MAINTAIN
Cập nhật owner, consumer và inventory.
        ↓
RETIRE
Deprecate và xóa variable không còn cần một cách an toàn.
```

## Tài liệu tham khảo

- [Tag Manager Help — Variable](https://support.google.com/tagmanager/answer/13355320?hl=en): vai trò của variable trong tag và trigger.
- [Tag Manager Help — User-defined variable types for web](https://support.google.com/tagmanager/answer/7683362?hl=en): các user-defined variable type được hỗ trợ, gồm Data Layer, URL, cookie, DOM và Custom JavaScript.
- [Tag Manager Help — Components of Google Tag Manager](https://support.google.com/tagmanager/answer/6103657?hl=en): mối quan hệ giữa variable, data layer, trigger và tag.
- [Google for Developers — The data layer](https://developers.google.com/tag-platform/tag-manager/datalayer): data layer value, xử lý event, persistence và quy tắc đặt tên.
