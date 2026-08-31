# 07 — Measurement Plan cho GA4/GTM

## 1. Tổng quan

### Mục tiêu

Measurement Plan là contract giữa business question và dữ liệu team sẽ thu thập, định tuyến, kiểm tra và đưa vào report. Plan xác định cần đo gì, khi nào event được xem là đúng, field nào được phép gửi, ai sở hữu signal và người khác sẽ kiểm tra nó như thế nào.

Bắt đầu từ business outcome, không bắt đầu từ GTM tag hoặc danh sách mọi click.

Với một tracking change thông thường ở frontend/GTM, contract tối thiểu cần có:

- requirement ID và journey ID;
- business meaning và authoritative moment;
- event name, required/optional parameter, type và allowed value;
- occurrence rule và deduplication rule;
- Data Layer source và GTM mapping;
- consent/privacy behavior và destination;
- reporting/custom-definition decision;
- QA, owner, version và review date.

### Phạm vi

Section này tập trung vào web client-side collection qua GTM và GA4 theo các pattern ổn định. Đối tượng sử dụng là frontend developer, GTM owner, analytics reviewer và QA. Advertising activation, campaign optimization, attribution strategy và Google Ads operations nằm ngoài measurement plan cốt lõi.

### Chuỗi đo lường

```text
Business question
  → authoritative business moment
  → event và parameter contract
  → application/Data Layer signal
  → GTM mapping và destination
  → consent/privacy decision
  → QA evidence
  → report hoặc downstream consumer
```

### Thuật ngữ cốt lõi

| Thuật ngữ | Ý nghĩa thực tế | Quyết định cần ghi trong plan |
| --- | --- | --- |
| Event | Business action hoặc outcome | Khi nào event trở thành đúng và được phép xuất hiện bao nhiêu lần |
| Event parameter | Chi tiết gắn với một event occurrence | Meaning, type, allowed value, scope và missing-value behavior |
| User property | Attribute tương đối ổn định của user | Source, update/unset behavior, consent và scope |
| Dimension | Field dùng để group hoặc filter data | Controlled value set và có cần đăng ký report hay không |
| Metric | Con số có thể count, sum hoặc calculate | Unit, source, aggregation và cách validation |
| Key event | Event được đánh dấu là quan trọng trong GA4 | Chỉ đánh dấu sau khi contract và collection QA đúng |

Collection và reporting là hai việc khác nhau. Một parameter có thể được collect nhưng không cần đăng ký custom definition; custom definition cũng không sửa được payload sai.

### Chọn event type

Trước khi tạo event mới trong GTM, đi theo thứ tự:

1. Kiểm tra GA4 đã tự động thu thập interaction đó chưa.
2. Kiểm tra Enhanced Measurement của web stream hiện tại có bao phủ không.
3. Kiểm tra recommended event của GA4 có đúng business meaning không.
4. Chỉ tạo custom event khi ba lựa chọn trên không phù hợp.

Dùng [event naming rules](https://support.google.com/analytics/answer/13316687), [recommended events](https://support.google.com/analytics/answer/9267735) và [collection limits](https://support.google.com/analytics/answer/9267744) làm nguồn chuẩn hiện tại. Giữ event name ở dạng lowercase `snake_case`, ổn định và không phụ thuộc vào business value thay đổi.

### Collection truth và reporting truth

| Checkpoint | Chứng minh | Không chứng minh |
| --- | --- | --- |
| Application | Product biết business state đã xảy ra | Analytics đã nhận data |
| Data Layer | State và payload đã expose cho GTM | GTM gửi request đúng |
| GTM | Trigger/tag đã evaluate và thử collection | GA4 đã process mọi field cho report |
| DebugView/Realtime | GA4 nhận diagnostic signal gần đây | Historical processing hoặc report đã sẵn sàng |
| Report/Exploration | GA4 process data thành view dùng được | Implementation ban đầu đúng nếu thiếu upstream evidence |

## 2. Quy trình xây dựng Measurement Plan

Thực hiện theo thứ tự. Mỗi bước phải tạo ra một quyết định mà owner tiếp theo có thể dùng mà không cần đoán.

### Bước 1 — Xác định decision

Viết question có thể dẫn đến hành động product, technical hoặc operational. Ghi decision owner, population, success criterion và cách dùng kết quả. Không tạo event chỉ để có vanity count.

### Bước 2 — Xác định authoritative business moment

Mô tả state transition làm cho event trở thành đúng. Ưu tiên application/backend result thay vì click, DOM label, route hoặc visual confirmation. Ghi success condition, invalid/failure behavior, retry, refresh và deduplication rule.

### Bước 3 — Chọn event name và type

Dùng automatic, Enhanced Measurement hoặc recommended event nếu meaning phù hợp. Nếu không, định nghĩa một custom event có business definition ổn định. Không encode business value thay đổi vào event name; giữ value đó ở parameter có kiểm soát.

### Bước 4 — Định nghĩa parameter và schema

Với mỗi parameter, ghi:

- canonical name và meaning;
- source of truth và owner;
- type và scope;
- required/optional;
- allowed value và normalization;
- missing/invalid behavior;
- consent/privacy classification;
- cardinality và volume dự kiến;
- reporting hoặc export destination.

Thiếu required value phải làm QA fail. Với optional value, ghi rõ omit hay fallback nào được approve. Không dùng giá trị chung chung `unknown` để che giấu source bị hỏng.

Kiểm tra reserved name, prefix, length, số lượng parameter, item limit và primitive type theo [event naming rules](https://support.google.com/analytics/answer/13316687) và [collection limits](https://support.google.com/analytics/answer/9267744). Kiểm tra lại trước implementation vì giới hạn nền tảng có thể thay đổi.

Chỉ thêm schema version khi team có thể vận hành và diễn giải nó. Nếu không, version hóa Measurement Plan và inventory thay vì thêm metadata không được dùng vào mọi event.

### Bước 5 — Định nghĩa Data Layer và GTM mapping

Ghi rõ application signal, Data Layer path, GTM variable/trigger/tag, Google tag configuration, environment routing và destination. Application nên publish event cùng các value trong một message khi có thể. GTM chỉ route signal đã approve, không suy đoán business success từ DOM state mong manh hoặc raw form field.

### Bước 6 — Quyết định key-event và custom-definition

Với key event hoặc custom dimension/metric, ghi business reason, owner, success condition, consent/privacy impact, expected volume và consumer. Phải validate collection trước khi đánh dấu key event. Chỉ đăng ký custom definition sau khi parameter đã pass QA và recurring report thực sự cần nó.

### Bước 7 — Định nghĩa identity và user property khi cần

Identity của user đã authenticated phải được xử lý riêng với event parameter. Ghi approved non-PII identifier, source, thời điểm có giá trị, behavior trước sign-in/sau logout/account switch, consent, access, retention và deletion implication. Với user property, ghi name, meaning, allowed value, update/unset behavior, scope và reportability. Xem [send User-IDs](https://developers.google.com/analytics/devguides/collection/ga4/user-id).

### Bước 8 — Xác định reporting readiness

Với mọi collected field, chọn một outcome:

| Outcome | Khi dùng |
| --- | --- |
| Standard field/metric | GA4 đã có meaning và scope cần thiết |
| Recommended parameter | Prescribed parameter hỗ trợ reporting cần thiết |
| Event-scoped custom dimension | Cần field mô tả có kiểm soát trong recurring report |
| Event-scoped custom metric | Cần numeric quantity và không có standard metric phù hợp |
| Collected nhưng không reportable | Hữu ích cho QA/routing/export nhưng không đáng tạo custom definition |
| Do not collect | Không có use case được approve, risk cao, PII hoặc cardinality không kiểm soát |

Ghi processing delay, scope, cardinality và report/export sử dụng field. Xem [Custom dimensions and metrics](https://support.google.com/analytics/answer/14240153) và Section 09 về report readiness.

### Bước 9 — Review, approve và version

Trước implementation, xác nhận business owner, technical owner, analytics reviewer, privacy/consent reviewer, target environment, version, effective date, next review date và consumer bị ảnh hưởng. Schema change phải cập nhật event contract, parameter dictionary, Data Layer/GTM mapping, QA case, report và handoff record cùng lúc.

## 3. Record và template chuẩn của plan

Giữ các record dưới đây làm source of truth. Derived summary hoặc routing view chỉ giúp đọc dễ hơn, không được định nghĩa lại contract.

### 3.1 Project Context và Baseline

Dùng một lần cho mỗi plan/version:

| Field | Value |
| --- | --- |
| Plan ID/version | `[plan ID] / [version]` |
| Product/business area | `[product hoặc journey]` |
| GA4 account/property/stream | `[account] / [property] / [stream]` |
| Google tag/Measurement ID | `[tag hoặc sanitized ID]` |
| GTM account/container | `[container]` |
| Platform/source | Web client-side qua GTM; ghi rõ mọi source khác nếu có |
| Environments | Local, QA/staging, production |
| Timezone/currency | `[timezone] / [currency nếu liên quan]` |
| Business/analytics/technical owner | `[teams]` |
| Privacy/consent reviewer | `[team hoặc N/A]` |
| Effective/next review date | `[YYYY-MM-DD] / [YYYY-MM-DD]` |
| Status | Proposed, approved, QA, active, deprecated hoặc retired |

### 3.2 Journey và Event Coverage Matrix

Dùng cho flow nhiều event. Matrix xác định coverage, không thay thế payload detail:

| Journey ID | Journey | Business question | Planned event sequence | Primary outcome | Report ID | QA ID | Owner | Status |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `[ID]` | `[journey]` | `[question]` | `[event A] → [event B] → [event C]` | `[outcome event]` | `[report]` | `[QA]` | `[owner]` | `[status]` |

### 3.3 Event Contract

Dùng một record cho mỗi canonical event:

| Field | Value |
| --- | --- |
| Requirement/journey ID | `[requirement] / [journey]` |
| Business question/decision | `[question và action]` |
| Event name/type | `[event_name] / automatic, enhanced, recommended hoặc custom` |
| Business definition | `[event có ý nghĩa gì]` |
| Authoritative moment/source | `[application/backend state]` |
| Expected occurrence/deduplication | `[count, retry, refresh, idempotency rule]` |
| Data Layer signal | `[event path và payload owner]` |
| GTM trigger/tag | `[trigger] / [tag]` |
| Environment/destination | `[QA và production routing]` |
| Required/optional parameters | `[names] / [names]` |
| Consent/privacy behavior | `[approved state và denied behavior]` |
| Key event status | `[yes/no/pending và reason]` |
| Reporting/custom-definition status | `[standard/custom/not reportable]` |
| QA/evidence ID | `[test IDs/evidence link]` |
| Owner/reviewer/version | `[teams] / [version/date]` |

### 3.4 Parameter Dictionary

Dùng một dòng cho mỗi event parameter được approve:

| Event | Parameter | Meaning | Type | Scope | Required? | Allowed values | Missing/invalid behavior | Source | Privacy/consent | Cardinality/volume | Report field |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `[event_name]` | `[parameter]` | `[meaning]` | `[string/number]` | `[event/item/user]` | `[yes/no]` | `[controlled list]` | `[omit/fail/fallback]` | `[application/system]` | `[classification/state]` | `[estimate]` | `[standard/custom/N/A]` |

### 3.5 Traceability Matrix

Dùng làm index từ requirement tới implementation và evidence:

| Requirement/event | Application state | Data Layer | GTM | Consent | Request/destination | GA4/report field | QA/evidence | Release | Owner/status |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `[requirement] / [event]` | `[state]` | `[signal]` | `[trigger/tag]` | `[behavior]` | `[request]` | `[field/report]` | `[IDs]` | `[release]` | `[owner/status]` |

### 3.6 Key-Event và Custom-Definition Decision Record

Chỉ dùng khi đề xuất key event hoặc custom definition:

```text
Decision ID:
Event/parameter:
Requirement/journey ID:
Business question và decision:
Success condition và expected occurrence:
Deduplication rule:
Mark as GA4 key event? [Yes/No/Pending]
Standard field checked:
Custom dimension/metric required? [Yes/No]
Cardinality/quota review:
Consent/privacy impact:
Report/export consumers:
QA/evidence ID:
Owner, approval, effective date:
```

Record này quản lý classification, không thay thế collection QA.

### 3.7 Consent và Data Classification Matrix

Dùng trước production collection:

| Event/parameter | Classification | Consent requirement | Denied behavior | Destination | Retention/owner | Evidence/status |
| --- | --- | --- | --- | --- | --- | --- |
| `[event/parameter]` | `[internal/sensitive/prohibited]` | `[category]` | `[block/omit/approved Consent Mode behavior]` | `[stream/none]` | `[policy/owner]` | `[link/status]` |

Giữ implementation detail và test case trong [Consent Management](05-consent-answer.md). Không gửi PII, secret, password, payment data, raw form value hoặc free text không kiểm soát đến GA4.

### 3.8 Schema Lifecycle Register

Dùng khi event meaning, parameter type/scope, allowed value hoặc downstream interpretation thay đổi:

| Trường | Giá trị template | Cách sử dụng |
| --- | --- | --- |
| Change ID | `[change ID]` | Mã ổn định cho schema change |
| Event/parameter | `[event.parameter]` | Event hoặc parameter bị ảnh hưởng |
| Current version | `[v1]` | Version đang được implementation và document |
| Proposed version | `[v2]` | Version mới sẽ đưa vào sau approval |
| Change type | `[type/value/meaning]` | Thay đổi type, allowed value, meaning, scope hoặc schema khác |
| Affected consumers | `[reports/GTM/app]` | Report, export, GTM object, application code hoặc consumer liên quan |
| Migration/QA action | `[migration và test plan]` | Migration, compatibility và QA cần thực hiện |
| Approval owner | `[owner]` | Người hoặc team chịu trách nhiệm approval |
| Effective date | `[date]` | Ngày version mới bắt đầu có hiệu lực |
| Status | `[status]` | Proposed, approved, migrating, active, deprecated hoặc retired |

## 4. Handoff implementation và lưu ý thực chiến

### Handoff Data Layer và GTM

Implementation handoff phải có mapping chính xác, không chỉ là screenshot thiếu tên:

| Plan field | Application/Data Layer | GTM object | GA4 destination |
| --- | --- | --- | --- |
| Event | `[event value]` | Custom Event trigger | Event name |
| Parameter | `[path và type]` | Data Layer Variable | Event parameter |
| Consent | `[approved state]` | Consent settings/variables | Approved collection behavior |
| Destination | `[environment]` | Google tag/stream mapping | QA hoặc production stream |

Link plan với [Debug/QA](08-debug-qa-answer.md) cho test ID/evidence và [Reports and Charts](09-reports-charts-answer.md) cho field readiness. Không duy trì một event contract thứ hai mâu thuẫn trong GTM.

### Cardinality và data minimization

Cardinality là số lượng value khác nhau trong một dimension. Controlled category thường phù hợp cho recurring report; value duy nhất cho user, session, request hoặc occurrence thường không phù hợp. Trước khi collect hoặc register high-cardinality field, hỏi:

1. Decision nào được document cần field này?
2. Có thể rút gọn thành controlled category không?
3. Standard field, ecommerce field, User-ID mechanism, export hoặc source-system report có phù hợp hơn không?
4. Daily value, privacy risk, retention và access policy dự kiến là gì?

Ưu tiên error category có kiểm soát thay cho raw error text, route group thay cho full URL value và report dimension thay cho operational identifier duy nhất. Nếu raw detail thực sự cần, document destination và governance thay vì biến nó thành GA4 custom dimension thông thường.

### Ecommerce addendum

Với ecommerce, mở rộng contract thông thường bằng:

| Area | Quyết định bắt buộc |
| --- | --- |
| Business moment | Khi product/cart/transaction state trở thành đúng |
| Transaction identity | Authoritative transaction ID, retry/replay behavior và deduplication owner |
| Monetary values | Numeric value, ISO currency, tax/shipping treatment và source of truth |
| Items | Item array đầy đủ, item identity ổn định, price/quantity dạng số và taxonomy được approve |
| Reporting | Dùng standard ecommerce field trước; custom item definition chỉ cho analysis recurring đã approve |
| Reconciliation | So sánh với commerce/order system; GA4 không phải accounting ledger |

Section 01 định nghĩa Data Layer payload, Sections 02–04 mapping và gửi, Section 08 test payload/duplicate, Sections 09–10 reconcile và monitor.

### Anti-pattern thường gặp

| Anti-pattern | Vì sao sai | Cách làm tốt hơn |
| --- | --- | --- |
| Track mọi click không có decision | Tạo noise và maintenance cost | Bắt đầu từ measurable outcome |
| Fire success khi click | Click không chứng minh business success | Dùng authoritative application/backend state |
| Encode value vào event name | Chia nhỏ report và schema không ổn định | Một event với controlled parameter |
| Gửi raw form field hoặc free text | Privacy và data-quality risk | Allowlist field và normalize value |
| Đăng ký mọi parameter thành custom dimension | Tốn quota và làm report rối | Chỉ đăng ký report field recurring đã approve |
| Duplicate automatic/enhanced collection trong GTM | Một interaction bị count nhiều lần | Kiểm tra collection có sẵn trước khi thêm tag |
| Đổi live event name tùy tiện | Làm hỏng report, QA baseline và consumer | Version hóa contract và review impact |

## 5. Ví dụ hoàn chỉnh — Registration Journey

Đây là Journey example duy nhất trong tài liệu. Đây là minh họa non-production; thay sample ID, owner, value, destination và evidence bằng dữ liệu đã được project approve. Mỗi subsection bên dưới tuân theo canonical record tương ứng ở Section 3.

### 5.1 Project Context và Baseline

| Field | Giá trị ghi nhận |
| --- | --- |
| Plan ID/version | `MP-REG-001 / v1.0` |
| Product/business area | Account registration |
| GA4 account/property/stream | `[project account] / [project property] / QA và production web stream` |
| Google tag/Measurement ID | `[project Google tag / Measurement ID]` |
| GTM account/container | `[project GTM account / container]` |
| Platform/source | Web client-side qua application Data Layer → GTM → GA4 |
| Environments | Local, QA/staging, production |
| Timezone/currency | `[project timezone] / N/A` |
| Business/analytics/technical owner | Product team / Analytics QA / Frontend và GTM owner |
| Privacy/consent reviewer | Privacy owner |
| Effective/next review date | `[YYYY-MM-DD] / [YYYY-MM-DD]` |
| Status | Proposed — pending QA và approval |

### 5.2 Journey và Event Coverage Matrix

| Journey ID | Journey | Business question | Planned event sequence | Primary outcome | Report ID | QA ID | Owner | Status |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `J-REG-001` | Registration | User bỏ cuộc ở bước nào? | `registration_start` → `[registration_error]*` → `sign_up` | `sign_up` | `R-REG-001` | `TC-REG-001` | Product team | Proposed |

`registration_error` là nhánh failure tùy chọn và có thể lặp lại. Đây không phải bước bắt buộc trước `sign_up`.

### 5.3 Event Contract

#### `registration_start`

| Field | Giá trị ghi nhận |
| --- | --- |
| Requirement/journey ID | `REQ-REG-001 / J-REG-001` |
| Business question/decision | User bỏ cuộc ở đâu trong registration? / cải thiện funnel từ entry đến completion |
| Event name/type | `registration_start` / custom |
| Business definition | User đã chọn registration method và form sẵn sàng để bắt đầu |
| Authoritative moment/source | Application xác nhận method đã chọn và form ready |
| Expected occurrence/deduplication | Một lần cho mỗi entry hợp lệ; không duplicate khi remount nếu plan không định nghĩa entry mới |
| Data Layer signal | `event: registration_start` cùng `form_id` và `method`; payload do application sở hữu |
| GTM trigger/tag | `CE - Web - registration_start` / GA4 Event tag |
| Environment/destination | QA/staging → QA stream; production sau approval → production stream |
| Required/optional parameters | `form_id`, `method` / none |
| Consent/privacy behavior | Approved analytics consent; khi bị từ chối thì block, omit hoặc dùng Consent Mode behavior đã approve |
| Key event status | No — chỉ là funnel entry |
| Reporting/custom-definition status | Event name và users; chỉ đăng ký `method` khi recurring report cần; `form_id` được collect nhưng không reportable |
| QA/evidence ID | `TC-REG-001 / [evidence link]` |
| Owner/reviewer/version | Frontend và GTM owner / Analytics QA / `v1.0` |

#### `registration_error`

| Field | Giá trị ghi nhận |
| --- | --- |
| Requirement/journey ID | `REQ-REG-001 / J-REG-001` |
| Business question/decision | Failure nào trong registration cần xử lý? / tách remediation cho validation và server error |
| Event name/type | `registration_error` / custom |
| Business definition | Hiển thị validation error hoặc server error đã approve cho user |
| Authoritative moment/source | Application phân loại và hiển thị error category đã approve |
| Expected occurrence/deduplication | Một lần cho mỗi visible error occurrence; không lặp lại cùng display nếu chưa có occurrence mới |
| Data Layer signal | `event: registration_error` cùng `form_id`, `method` và `error_type`; payload do application sở hữu |
| GTM trigger/tag | `CE - Web - registration_error` / GA4 Event tag |
| Environment/destination | QA/staging → QA stream; production sau approval → production stream |
| Required/optional parameters | `form_id`, `method`, `error_type` / none |
| Consent/privacy behavior | Approved analytics consent; khi bị từ chối thì block, omit hoặc dùng Consent Mode behavior đã approve |
| Key event status | No — diagnostic journey event |
| Reporting/custom-definition status | `error_type` được collect; chỉ đăng ký event-scoped custom dimension khi recurring error analysis được approve |
| QA/evidence ID | `TC-REG-001 / [evidence link]` |
| Owner/reviewer/version | Frontend và GTM owner / Analytics QA / `v1.0` |

#### `sign_up`

| Field | Giá trị ghi nhận |
| --- | --- |
| Requirement/journey ID | `REQ-REG-001 / J-REG-001` |
| Business question/decision | Method nào hoàn tất registration? / đo completion đã được confirm và phân loại thành key event |
| Event name/type | `sign_up` / recommended |
| Business definition | Backend xác nhận account creation |
| Authoritative moment/source | Application nhận account-creation result thành công |
| Expected occurrence/deduplication | Một lần cho mỗi confirmed account; không duplicate do retry hoặc refresh; application/backend sở hữu deduplication |
| Data Layer signal | `event: sign_up` cùng `form_id` và `method`; payload do application sở hữu |
| GTM trigger/tag | `CE - Web - sign_up` / GA4 Event tag |
| Environment/destination | QA/staging → QA stream; production sau approval → production stream |
| Required/optional parameters | `method`, `form_id` / none |
| Consent/privacy behavior | Approved analytics consent; khi bị từ chối thì block, omit hoặc dùng Consent Mode behavior đã approve |
| Key event status | Pending — chỉ chuyển thành Yes sau collection QA và Product approval |
| Reporting/custom-definition status | Dùng recommended `sign_up`; đăng ký `method` thành event-scoped custom dimension sau QA nếu recurring report cần; `form_id` được collect nhưng không reportable |
| QA/evidence ID | `TC-REG-001 / [evidence link]` |
| Owner/reviewer/version | Frontend và GTM owner / Analytics QA / `v1.0` |

### 5.4 Parameter Dictionary

| Event | Parameter | Meaning | Type | Scope | Required? | Allowed values | Missing/invalid behavior | Source | Privacy/consent | Cardinality/volume | Report field |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `registration_start` | `form_id` | Stable form identifier | string | Event | Yes | Approved form IDs | Fail QA; không gửi event | Application registration state | Controlled non-PII; approved analytics consent | Low; controlled IDs | Collected, not reportable |
| `registration_start` | `method` | Registration method đã chọn | string | Event | Yes | `email`, `google`, `apple` | Fail QA; không gửi event | Application registration state | Controlled non-PII; approved analytics consent | Low; controlled list | Event-scoped custom dimension nếu recurring report được approve |
| `registration_error` | `form_id` | Form hiển thị error | string | Event | Yes | Approved form IDs | Fail QA; không gửi event | Application registration state | Controlled non-PII; approved analytics consent | Low; controlled IDs | Collected, not reportable |
| `registration_error` | `method` | Registration method tại thời điểm failure | string | Event | Yes | `email`, `google`, `apple` | Fail QA; không gửi event | Application registration state | Controlled non-PII; approved analytics consent | Low; controlled list | Event-scoped custom dimension nếu recurring report được approve |
| `registration_error` | `error_type` | Error category có kiểm soát | string | Event | Yes | `validation`, `server_error` | Fail QA; không gửi event | Application error classification | Controlled non-PII; approved analytics consent | Low; controlled list | Event-scoped custom dimension chỉ khi recurring error analysis được approve |
| `sign_up` | `form_id` | Form dùng cho account creation đã confirm | string | Event | Yes | Approved form IDs | Fail QA; không gửi event | Application/backend registration state | Controlled non-PII; approved analytics consent | Low; controlled IDs | Collected, not reportable |
| `sign_up` | `method` | Registration method dùng cho account đã confirm | string | Event | Yes | `email`, `google`, `apple` | Fail QA; không gửi event | Application/backend registration state | Controlled non-PII; approved analytics consent | Low; controlled list | Event-scoped custom dimension sau QA nếu recurring report được approve |

Không collect email, phone, password, raw account ID, raw error text hoặc free-form form value trong contract này.

### 5.5 Data Layer, GTM và destination mapping

Handoff cụ thể này tuân theo các dòng chuẩn `Event` / `Parameter` / `Consent` / `Destination`. Application sở hữu business truth; GTM route signal đã approve và chỉ map parameter nằm trong allowlist.

| Plan field | Application/Data Layer | GTM object | GA4 destination |
| --- | --- | --- | --- |
| Event | `event: registration_start`, `event: registration_error` hoặc `event: sign_up` | Custom Event trigger tương ứng → GA4 Event tag | Event name trên QA hoặc production web stream |
| Parameter | `form_id`, `method` và `error_type` từ application payload; type và allowlist rõ ràng | Data Layer Variable map theo tên; không forward toàn bộ object | Event parameters |
| Consent | Analytics-consent state đã approve được expose cho tag configuration | Consent settings và trigger/tag có xử lý consent | Approved collection behavior hoặc denied behavior |
| Destination | Local/QA staging dùng QA stream; production dùng production stream sau approval | Google tag và mapping stream theo environment | QA hoặc production web stream |

#### Per-event routing view (derived)

| Event | Data Layer payload | GTM object | Allowed parameters |
| --- | --- | --- | --- |
| `registration_start` | `event` + `form_id` + `method` | `CE - Web - registration_start` → GA4 Event tag | `form_id`, `method` |
| `registration_error` | `event` + `form_id` + `method` + `error_type` | `CE - Web - registration_error` → GA4 Event tag | `form_id`, `method`, `error_type` |
| `sign_up` | `event` + `form_id` + `method` | `CE - Web - sign_up` → GA4 Event tag | `form_id`, `method` |

### 5.6 Consent và Data Classification Matrix

| Event/parameter | Classification | Consent requirement | Denied behavior | Destination | Retention/owner | Evidence/status |
| --- | --- | --- | --- | --- | --- | --- |
| `registration_start.form_id`, `registration_start.method` | Internal, controlled, non-PII | Approved analytics consent | Block, omit hoặc dùng Consent Mode behavior đã approve | QA stream; production stream sau approval | Project retention policy / Privacy owner | `TC-REG-001 / pending QA` |
| `registration_error.form_id`, `registration_error.method`, `registration_error.error_type` | Internal, controlled, non-PII | Approved analytics consent | Block, omit hoặc dùng Consent Mode behavior đã approve | QA stream; production stream sau approval | Project retention policy / Privacy owner | `TC-REG-001 / pending QA` |
| `sign_up.form_id`, `sign_up.method` | Internal, controlled, non-PII | Approved analytics consent | Block, omit hoặc dùng Consent Mode behavior đã approve | QA stream; production stream sau approval | Project retention policy / Privacy owner | `TC-REG-001 / pending QA` |
| Any event / email, phone, password, raw error text, raw account ID | Prohibited | Not applicable | Không collect hoặc forward | None | Không retention / Privacy owner | `PROHIBITED / enforced` |
| User-ID sau authenticated sign-in | Separate identity contract | Separate approved conditions | Clear hoặc omit theo identity contract | Chỉ approved identity configuration | Identity retention policy / Identity owner | `N/A / separate review` |

Giữ implementation detail và test case trong [Consent Management](05-consent-answer.md). Không gửi PII, secret, password, payment data, raw form value hoặc free text không kiểm soát đến GA4.

### 5.7 Key-Event và Custom-Definition Decision Record

```text
Decision ID: DEC-REG-001
Event/parameter: sign_up; sign_up.method
Requirement/journey ID: REQ-REG-001 / J-REG-001
Business question và decision: Method nào hoàn tất registration? Đánh dấu account creation đã confirm là key event sau khi validation.
Success condition và expected occurrence: Backend xác nhận account creation; một occurrence cho mỗi confirmed account.
Deduplication rule: Không event khi validation fail, server failure, retry trước success hoặc refresh; một event cho mỗi confirmed account.
Mark as GA4 key event? [Pending → Yes sau QA và Product approval]
Standard field checked: Recommended sign_up event; đã kiểm tra standard event count và user metrics.
Custom dimension/metric required? [Yes — event-scoped custom dimension cho method sau QA; không có custom metric]
Cardinality/quota review: Method có controlled value set với cardinality thấp; form_id chỉ collect để traceability, không đăng ký thành recurring custom dimension.
Consent/privacy impact: method và form_id là controlled non-PII, cần approved analytics consent.
Report/export consumers: R-REG-001 / Product Analytics; R-REG-002 / Analytics QA.
QA/evidence ID: TC-REG-001 / [evidence link]
Owner, approval, effective date: Product owner + Analytics QA + Privacy owner / pending / [YYYY-MM-DD]
```

`registration_error.error_type` chưa có decision record; chỉ mở record khi recurring error analysis được approve.

### 5.8 Derived reporting requirements

Đây là derived reporting view. Event Contract và Parameter Dictionary vẫn là source of truth.

| Report ID | Question | Population/grain | Dimensions | Metrics/formula | Surface | Owner |
| --- | --- | --- | --- | --- | --- | --- |
| `R-REG-001` | Method nào có validated completion rate thấp nhất? | User đã chọn method và bắt đầu registration | `method`, device category, date | User có `sign_up` / user có `registration_start` | Detail report + funnel Exploration | Product Analytics |
| `R-REG-002` | Account creation đã confirm có gửi một lần với value đúng không? | Controlled test event occurrences | Event name, method, form ID, error type | Event count và duplicate review | Exploration + processed event report | Analytics QA |

Numerator và denominator của completion rate phải dùng cùng date range, population, identity context, journey definition và cùng source `method`. Event count không giống completed-user count.

### 5.9 Traceability Matrix và approval

| Requirement/event | Application state | Data Layer | GTM | Consent | Request/destination | GA4/report field | QA/evidence | Release | Owner/status |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `REQ-REG-001 / registration_start` | Selected method và form-ready state | `event: registration_start` + `form_id` + `method` | `CE - Web - registration_start` → GA4 Event tag | Approved behavior | GA4 request → QA stream; production sau approval | Event name, users, `method`, funnel entry | `TC-REG-001 / [evidence]` | `[release ID]` | Frontend + GTM / Proposed |
| `REQ-REG-001 / registration_error` | Validation/server error đã approve và đang hiển thị | `event: registration_error` + `form_id` + `method` + `error_type` | `CE - Web - registration_error` → GA4 Event tag | Approved behavior | GA4 request → QA stream; production sau approval | Event name, `error_type` | `TC-REG-001 / [evidence]` | `[release ID]` | Frontend + GTM / Proposed |
| `REQ-REG-001 / sign_up` | Backend-confirmed account creation | `event: sign_up` + `form_id` + `method` | `CE - Web - sign_up` → GA4 Event tag | Approved behavior | GA4 request → QA stream; production sau approval | Event name, `method`, pending key event | `TC-REG-001 / [evidence]` | `[release ID]` | Frontend + GTM / Proposed |

Approval yêu cầu business meaning, technical mapping, privacy/consent behavior, QA evidence, report readiness và release reference rõ ràng. Production activation do Section 10 xử lý.

### 5.10 Schema Lifecycle Register

Đây là plan mới ở `v1.0`, chưa có schema migration nào được đề xuất. Giữ entry này để thể hiện rõ trạng thái lifecycle:

| Trường | Giá trị ghi nhận | Ghi chú |
| --- | --- | --- |
| Change ID | `N/A-REG-001` | Chưa đề xuất schema migration cho version này |
| Event/parameter | Tất cả Registration event và parameter | Mở lại record khi có event hoặc parameter bị ảnh hưởng |
| Current version | `v1.0` | Version đang được document |
| Proposed version | — | Chưa đề xuất version mới |
| Change type | No change | Không thay đổi meaning, type, scope hoặc allowed value |
| Affected consumers | — | Chưa xác định consumer cần migration |
| Migration/QA action | Mở lại register trước khi đổi event meaning, parameter type/scope hoặc allowed value | Cập nhật contract, mapping, QA case và report cùng lúc |
| Approval owner | Product + Analytics + Privacy owners | Bắt buộc cho mọi schema change sau này |
| Effective date | — | Không áp dụng khi chưa có change |
| Status | Not applicable | Register vẫn sẵn sàng cho schema change tiếp theo |

## Tài liệu tham khảo chính thức

- [About events](https://support.google.com/analytics/answer/9322688)
- [Enhanced measurement events](https://support.google.com/analytics/answer/9216061?hl=en)
- [Recommended events](https://support.google.com/analytics/answer/9267735)
- [Measure ecommerce](https://developers.google.com/analytics/devguides/collection/ga4/ecommerce)
- [Custom events](https://support.google.com/analytics/answer/12229021?hl=en)
- [Event naming rules](https://support.google.com/analytics/answer/13316687)
- [Event parameters](https://support.google.com/analytics/answer/13675006)
- [Event collection limits](https://support.google.com/analytics/answer/9267744)
- [Custom dimensions and metrics](https://support.google.com/analytics/answer/14240153)
- [Cardinality](https://support.google.com/analytics/answer/12226705?hl=en)
- [About key events](https://support.google.com/analytics/answer/9267568)
- [Send User-IDs](https://developers.google.com/analytics/devguides/collection/ga4/user-id)
- [GTM Custom Event trigger](https://support.google.com/tagmanager/answer/7679219)
- [Avoid sending personally identifiable information](https://support.google.com/analytics/answer/6366371)
