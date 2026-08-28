# 07 — Measurement Plan cho GA4/GTM

## Mục đích

Measurement Plan là hợp đồng giữa business question và dữ liệu sẽ được thu thập, định tuyến, validate, đưa vào report và sử dụng để ra quyết định. Đây không phải là danh sách của mọi cú click. Tài liệu này xác định cần đo gì, vì sao cần đo, khi nào event được xem là hợp lệ, dữ liệu nào được phép thu thập và người khác có thể kiểm tra kết quả như thế nào.

Không bắt đầu từ GTM tag. Hãy bắt đầu từ business state cần được quan sát.

## Project Context và Baseline

**Mục đích:** Dùng record này để xác định property, stream, platform, environment, owner và các mốc thời gian mà plan áp dụng. Nó ngăn cùng một event name bị hiểu khác nhau giữa các product hoặc environment.

Trước khi định nghĩa từng event, cần cố định context mà measurement plan áp dụng. Cùng một event name có thể có owner, destination, consent behavior hoặc ý nghĩa reporting khác nhau giữa các product và environment.

| Field                         | Giá trị cần có                                                     |
| ----------------------------- | ------------------------------------------------------------------ |
| Plan ID và version            | `MP-REG-001 / v1.0`                                                |
| Business/product area         | `[product hoặc journey]`                                           |
| GA4 account/property          | `[account] / [property]`                                           |
| GA4 data stream               | `[web/app stream name và ID]`                                      |
| Google tag / Measurement ID   | `[tag ID hoặc reference đã sanitize]`                              |
| GTM account/container         | `[container name và ID]`                                           |
| Platform và collection source | Web client-side qua GTM, app SDK, server hoặc Measurement Protocol |
| Environments                  | Local, QA, staging, production                                     |
| Property timezone/currency    | `[timezone] / [currency]` nếu phù hợp                              |
| Business owner                | `[name/team]`                                                      |
| Analytics owner               | `[name/team]`                                                      |
| Technical owner               | `[name/team]`                                                      |
| Privacy/consent reviewer      | `[name/team hoặc N/A]`                                             |
| Effective date                | `YYYY-MM-DD`                                                       |
| Next review/expiry            | `YYYY-MM-DD`                                                       |
| Status                        | Proposed, approved, QA, active, deprecated hoặc retired            |

Plan phải nêu rõ tài liệu chỉ áp dụng cho web client-side collection hay còn bao gồm app, server-side, offline hoặc Measurement Protocol events. Nếu một source nằm ngoài scope, cần ghi rõ thay vì để người đọc tự suy đoán.

## Khái niệm cốt lõi

### Mô hình dễ hiểu nhất

Measurement Plan biến một business question thành dữ liệu mà GA4 có thể collect và một report mà team có thể sử dụng. Cách dễ nhất để hiểu các thuật ngữ chính là xem một event như một câu:

- **Event = hành động hoặc kết quả.** Trả lời câu hỏi: “Điều gì đã xảy ra?” Ví dụ: `sign_up`, `purchase`.
- **Event parameter = chi tiết của hành động đó.** Trả lời câu hỏi: “Cần biết thêm điều gì?” Ví dụ: `method`, `form_id`, `value`.
- **User property = thông tin về user.** Mô tả user và thường thay đổi ít hơn event. Ví dụ: `account_type = business`.
- **Dimension = field dùng để group hoặc filter report.** Trả lời câu hỏi: “Muốn chia dữ liệu theo cách nào?” Ví dụ: event name, device category hoặc `form_id`.
- **Metric = một con số có thể đếm, cộng hoặc tính toán.** Ví dụ: users, event count và revenue.
- **Key event = event được đánh dấu là quan trọng đối với business.** Ví dụ: `sign_up` hoặc `purchase` khi các outcome này quan trọng với business.

Ví dụ:

```text
Event:             purchase
Event parameters:  transaction_id = ORD-123, value = 49.90, currency = USD
User property:     account_type = business
Report dimension:  account_type
Report metrics:    purchase count, revenue
Business outcome:  purchase có thể được đánh dấu là key event
```

Điểm này quan trọng vì việc collect dữ liệu và việc dùng dữ liệu trong report là hai việc khác nhau. Event parameter là dữ liệu được gửi cùng event; nó không tự động trở thành custom dimension hoặc metric có thể dùng lại. Trước khi đăng ký custom definition, hãy kiểm tra xem standard field của GA4 đã trả lời được câu hỏi chưa, scope có đúng không và report surface thực sự có cần field đó không.

### Các loại event

Khi planning một event, hãy chọn category cụ thể nhất đã phù hợp. Có thể đi theo thứ tự sau:

1. Kiểm tra xem GA4 đã tự collect event đó chưa.
2. Kiểm tra xem Enhanced Measurement có thể collect event đó không.
3. Kiểm tra xem có Recommended event nào đúng với business meaning không.
4. Chỉ tạo Custom event khi các lựa chọn trên không phù hợp.

| Category                | Hiểu đơn giản là                                                            | Ví dụ                                                   | Quy tắc planning                                                                           |
| ----------------------- | --------------------------------------------------------------------------- | ------------------------------------------------------- | ------------------------------------------------------------------------------------------ |
| Automatically collected | GA4 tự collect khi basic setup đã có.                                       | `page_view`, `session_start`                            | Kiểm tra dữ liệu hiện có trước để không tạo event trùng lặp.                               |
| Enhanced Measurement    | GA4 có thể collect các website interaction phổ biến khi setting được bật.   | Scrolls, outbound clicks, site search, video engagement | Review setting và test SPA/DOM behavior trước khi tạo lại interaction bằng GTM.            |
| Recommended event       | Google cung cấp tên và bộ parameter chuẩn cho một business action phổ biến. | `login`, `sign_up`, `generate_lead`, `purchase`         | Ưu tiên tên và parameter được quy định sẵn vì chúng hỗ trợ standard report và integration. |
| Custom event            | Business action có ý nghĩa nhưng không thuộc các lựa chọn trên.             | `calculation_complete`, `quote_requested`               | Chỉ dùng khi business meaning thực sự cần và không có Recommended event phù hợp.           |

Khi có thể, Recommended event nên dùng các prescribed parameter tương ứng. Ví dụ, dùng `sign_up` thay vì tự tạo `signup_done` nếu cả hai cùng mô tả một hành động. Cách này giúp schema nhất quán và tránh chia cùng một business meaning thành nhiều event name. Xem [recommended events](https://support.google.com/analytics/answer/9267735) và [about events](https://support.google.com/analytics/answer/9322688).

### Collection truth và reporting truth

Measurement Plan nên phân biệt mỗi checkpoint chứng minh được điều gì:

| Checkpoint              | Nó chứng minh điều gì?                                    | Nó chưa chứng minh điều gì?                                              |
| ----------------------- | --------------------------------------------------------- | ------------------------------------------------------------------------ |
| Application             | Product biết business action đã xảy ra.                   | Chưa chứng minh analytics đã nhận dữ liệu.                               |
| Data Layer              | Action và payload đã được expose cho GTM.                 | Chưa chứng minh GTM đã gửi request đúng.                                 |
| GTM                     | Trigger và tag đã evaluate và cố gắng gửi dữ liệu.        | Chưa chứng minh GA4 đã xử lý mọi field để dùng trong report.             |
| GA4 DebugView           | GA4 đã nhận event gần đây và các parameter đang hiển thị. | Chưa bảo đảm field đã được đăng ký, đúng scope hoặc có trong mọi report. |
| Reports và Explorations | GA4 đã xử lý dữ liệu thành reporting view có thể sử dụng. | Chưa chứng minh business interpretation là đúng nếu chưa validation.     |

Vì vậy, flow đầy đủ là:

```text
Business action xảy ra
        ↓
Application expose action một lần trong Data Layer
        ↓
GTM đọc và gửi request đúng
        ↓
GA4 nhận và xử lý dữ liệu
        ↓
Report trả lời được business question
```

Một event có thể xuất hiện trong Data Layer và DebugView nhưng vẫn không dùng được cho recurring report vì parameter bị thiếu, sai scope, chưa đăng ký, chưa xử lý xong, có cardinality cao hoặc vi phạm privacy rule.

## Cardinality và High-Cardinality Dimensions

### Cardinality là gì?

Cardinality là số lượng value duy nhất được gán cho một dimension. Một số dimension có số lượng unique value cố định. Ví dụ, Device dimension có thể có tối đa ba value desktop, tablet và mobile nên cardinality trong ví dụ này là ba. Những dimension khác như Item ID, Page path và Page location có thể có rất nhiều unique value. Website ecommerce có thể có hàng trăm nghìn item, còn website có thể có hàng trăm nghìn page duy nhất; các dimension này được xem là có cardinality cao.

### Cách đọc “dimension/value pattern”

Trong bảng này:

- **Dimension** là tiêu chí hoặc field dùng để chia dữ liệu trong report, ví dụ `method` hoặc `item_id`.
- **Value** là giá trị cụ thể được lưu trong field đó, ví dụ `email`, `google` hoặc `SKU-123`.
- **Dimension/value pattern** mô tả kiểu value mà field đó thường chứa và mức độ các value có thể thay đổi nhiều hay ít.

Ví dụ, `method = email` nghĩa là `method` là dimension còn `email` là một value của dimension đó. Một danh sách value nhỏ và đã được phê duyệt thường dễ dùng cho recurring report hơn một field nhận value mới cho từng page, product, user hoặc transaction.

| Pattern nói đơn giản                                                                      | Ví dụ                                           | Cardinality thường gặp      | Cách ghi trong Measurement Plan                                                                                                                      |
| ----------------------------------------------------------------------------------------- | ----------------------------------------------- | --------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Category nhỏ, có kiểm soát** — chỉ có một vài value được phép                           | `device_category = desktop/mobile/tablet`       | Thấp                        | Phù hợp để breakdown report thường xuyên. Cần document danh sách value được phép.                                                                    |
| **Danh sách nhỏ đã phê duyệt** — team duy trì một danh sách ngắn                          | `method = email/google/apple`                   | Thấp                        | Phù hợp cho recurring report và comparison. Không trộn nhiều cách viết hoặc label ngoài danh sách.                                                   |
| **Business ID có danh sách giới hạn** — ID được dùng lại cho một số ít đối tượng business | `form_id = registration/newsletter`             | Thấp đến trung bình         | Chỉ dùng ID đã được phê duyệt và theo dõi xem danh sách có tăng nhanh không.                                                                         |
| **ID của page hoặc product** — dùng để nhận diện nhiều page hoặc product                  | `page_location`, `page_path`, `item_id`         | Trung bình đến cao          | Chỉ dùng khi có business question cụ thể cần đến. Không đăng ký thêm custom dimension nếu không cần thiết.                                           |
| **ID của từng occurrence** — thường unique cho một order, request hoặc transaction        | `transaction_id`, `order_id`, request ID        | Cao                         | Chỉ collect cho nhu cầu ecommerce hoặc operational đã được phê duyệt. Không đưa vào custom dimension dùng thường xuyên nếu chưa có use case rõ ràng. |
| **ID của user hoặc session** — nhận diện một người, browser hoặc session                  | `user_id`, session ID, client ID                | Rất cao                     | Không đăng ký làm custom dimension. Dùng identity, export hoặc source-system mechanism phù hợp.                                                      |
| **Free text hoặc value được generate** — có thể thay đổi ở mọi occurrence                 | Search text, raw error message, UUID, timestamp | Rất cao hoặc không giới hạn | Không dùng làm report dimension thường xuyên. Hãy chuẩn hóa, gom nhóm thành category, redact hoặc không gửi vào GA4.                                 |

### High-cardinality dimension là gì?

Google định nghĩa high-cardinality dimension là dimension có hơn 500 unique value trong một ngày. High-cardinality dimension làm tăng số lượng row trong report, khiến report dễ đạt row limit hơn. Khi đạt giới hạn, dữ liệu vượt quá giới hạn có thể được gom vào row `(other)`. Google cũng nêu rằng GA4 có cardinality limit là 50.000 value; sau giới hạn này, cardinality control sẽ được áp dụng. Property vẫn có thể có dimension với bất kỳ số lượng value nào, nhưng chỉ nên dùng high-cardinality dimension khi thông tin đó thực sự cần thiết cho business. Xem [GA4 cardinality](https://support.google.com/analytics/answer/12226705?hl=en).

Các con số 500 value và 50.000 value mô tả behavior của Google Analytics; đây không phải là rule chung để cấm collect dữ liệu. Hãy kiểm tra property, report surface và official documentation hiện hành trước khi implementation.

High-cardinality không có nghĩa là “tuyệt đối không được collect value này”. Điều đó có nghĩa value cần có purpose, destination, access policy và analysis method rõ ràng. Product ID hoặc transaction ID có thể cần cho reconciliation nhưng vẫn không phù hợp làm recurring custom report dimension.

### Vì sao nên hạn chế high-cardinality dimensions?

1. **Report detail có thể bị gom nhóm.** High-cardinality dimension làm tăng số row và khiến report dễ đạt row limit hơn. Dữ liệu vượt quá giới hạn có thể bị gom vào row `(other)`, khiến detail theo từng value không còn đầy đủ.
2. **Analysis trở nên nhiều noise.** Một table có hàng nghìn URL, ID, timestamp hoặc error string chỉ xuất hiện một lần sẽ khó đọc và hiếm khi hỗ trợ một business decision ổn định.
3. **Tốn custom-definition quota.** Đăng ký unique identifier làm custom dimension sẽ sử dụng một resource giới hạn của property nhưng không tạo ra breakdown hữu ích cho recurring report. Google khuyến nghị ưu tiên predefined field và tránh custom dimension high-cardinality không cần thiết.
4. **Tăng privacy và access risk.** Một value duy nhất có thể trở thành linkable identifier, còn free text có thể chứa PII hoặc secret. High cardinality tự nó không phải privacy violation, nhưng làm tăng yêu cầu về data minimization và review.
5. **Giảm tính nhất quán giữa các report.** High-cardinality field có thể hoạt động khác nhau giữa Reports, Explorations, exports và APIs. Plan phải nêu rõ surface nào là authoritative cho analysis cần thực hiện.

### Decision framework

Trước khi thêm dimension hoặc đăng ký custom definition, hãy hỏi:

1. Field này có cần thiết cho một business question và decision đã được document không?
2. Đây là category hay là identifier của một user/item/session/occurrence?
3. Có thể rút gọn value thành một controlled category mà vẫn giữ được signal phục vụ decision không?
4. Standard dimension, recommended parameter, ecommerce field, User-ID mechanism hoặc source-system report đã giải quyết nhu cầu này chưa?
5. Report chỉ cần aggregate value, còn reconciliation mới cần raw identifier phải không?
6. Dự kiến có bao nhiêu distinct value mỗi ngày và trong toàn bộ retention period?
7. Value có an toàn theo privacy, consent, access và retention policy không?

Sử dụng các pattern sau:

| Nhu cầu                     | Nên tránh                                         | Nên dùng                                                                                    |
| --------------------------- | ------------------------------------------------- | ------------------------------------------------------------------------------------------- |
| Phân tích nguyên nhân error | Raw error message hoặc stack trace                | Controlled `error_type`, `error_code` và `error_category`                                   |
| So sánh nhóm page           | Full URL có query/user content                    | Approved `page_type`, route group hoặc content group                                        |
| So sánh product             | Free-text product label không giới hạn            | Stable approved `item_id`, category hoặc product group ở đúng scope                         |
| Reconcile purchase          | Đăng ký mọi `transaction_id` làm report dimension | Giữ recommended transaction field cho đúng mục đích và reconcile với commerce source/export |
| Nhận diện signed-in user    | Custom dimension chứa user ID                     | Dùng User-ID mechanism đã được phê duyệt; không expose làm routine report dimension         |
| Phân tích duration/amount   | Timestamp hoặc raw numeric string làm category    | Dùng metric hoặc bucket đã document như `0–10s`, `11–30s`, `31s+`                           |

Nếu raw detail thực sự cần thiết, hãy ghi rõ destination, access, retention, cost và owner đã được phê duyệt. Tùy use case, governed export hoặc source-system analysis có thể phù hợp hơn custom dimension trong GA4. Xem [custom dimensions and metrics](https://support.google.com/analytics/answer/14240153).

## Quy trình xây dựng Measurement Plan

Quy trình này đi từ business decision đến một report đáng tin cậy. Mỗi bước nên trả lời một câu hỏi thực tế: cần hỗ trợ quyết định nào, điều gì đã xảy ra, cần thêm chi tiết gì, tín hiệu đến từ đâu và sẽ kiểm tra cũng như sử dụng dữ liệu như thế nào?

### 1. Xác định decision

Viết business question theo cách có thể dẫn đến một action:

```text
Decision owner: Product team
Question: Method đăng ký nào có completion rate cao nhất?
Decision: Ưu tiên cải thiện UX cho method có completion thấp đáng kể.
Success criterion: Report phân biệt được valid start và confirmed completion theo method.
```

Tránh những câu hỏi chỉ yêu cầu một vanity count, ví dụ “Có bao nhiêu button được click?”, trừ khi count đó có mục đích vận hành rõ ràng.

### 2. Xác định business moment có tính quyết định

Với mỗi event, mô tả state transition khiến event trở thành sự thật. Nói đơn giản, đây là thời điểm application hoặc backend có đủ bằng chứng để xác nhận business action thực sự đã xảy ra. Nó không phải lúc nào cũng là thời điểm user click button.

```text
User click Submit
  → client validate input
  → server xác nhận tạo account
  → application push sign_up
```

Event `sign_up` chính thức phải xảy ra sau khi thành công được xác nhận, không phải ngay khi button được click. Application hoặc backend response nên là nguồn business truth khi phù hợp; GTM chỉ routing signal đã được phê duyệt thay vì suy đoán success từ phần hiển thị của page.

Ghi nhận cả behavior cho negative case và duplicate case:

- Form không hợp lệ: không có `sign_up`.
- Server failure: không có `sign_up`.
- Double click nhanh: một event cho một account được tạo thành công.
- Refresh confirmation page: không tạo event mới, trừ khi business định nghĩa đó là một occurrence mới.

### 3. Chọn event type và event name

Áp dụng thứ tự sau:

1. Interaction đã được automatically collected hoặc enhanced measurement hỗ trợ chưa?
2. Có recommended event nào có ý nghĩa phù hợp không?
3. Nếu không, định nghĩa custom event có business meaning ổn định.
4. Kiểm tra event name theo naming rules và collection limits hiện hành của GA4.

Sử dụng lowercase `snake_case` như convention của team. Event name của GA4 phân biệt hoa thường, phải bắt đầu bằng chữ cái và không được chứa khoảng trắng; cần tránh reserved names và reserved prefixes. Xem [event naming rules](https://support.google.com/analytics/answer/13316687) và [collection limits](https://support.google.com/analytics/answer/9267744) như nguồn thông tin chính thức hiện hành.

Không encode các business value thay đổi vào event name:

```text
Đúng:  search với parameter search_term = "shoes"
Sai:   search_shoes

Đúng:  sign_up với parameter method = "email"
Sai:   sign_up_email
```

### 4. Định nghĩa parameters và types

Chỉ thêm parameter khi nó giúp trả lời một question đã được phê duyệt. Mỗi parameter cần có:

**Định nghĩa cốt lõi**

- canonical name và meaning;
- source và owner có tính quyết định;
- type, scope và trạng thái required/optional;
- allowed values;
- behavior khi value bị thiếu hoặc không hợp lệ.

**Quality và reporting checks**

- privacy và consent classification;
- cardinality và volume dự kiến;
- reporting destination hoặc quyết định về custom definition.

Ưu tiên controlled vocabulary:

```text
method: "email" | "google" | "apple"
form_id: "registration" | "newsletter"
result: "success" | "validation_error" | "server_error"
```

Không dùng `unknown` để che giấu một required value đang bị lỗi. Với optional value, dùng policy omit/null đã được ghi rõ; với required value bị thiếu, phải fail QA.

Không gửi name, email, phone number, address, free-form text, access token, password, payment details hoặc dữ liệu cá nhân bị cấm khác tới GA4. Tránh để chúng xuất hiện trong URL, log, screenshot hoặc QA evidence. Nếu GTM tạm thời đọc một value để validation hoặc consent logic, value đó không được forward tới GA4. Một controlled identifier ổn định vẫn có thể nhạy cảm; cần ghi rõ mục đích, quyền truy cập, retention và privacy approval.

### 5. Quy tắc về parameter name, limits và schema

Parameter dictionary phải được kiểm tra theo naming rules và collection limits hiện hành của GA4 trước khi implementation. Ngoài event name, cần review reserved event-parameter names, reserved user-property names, reserved item-parameter names và prohibited prefixes. Không tạo custom parameter xung đột với system field hoặc bắt đầu bằng reserved prefix.

Ghi nhận các constraint sau nhưng không coi chúng là product constant vĩnh viễn:

- độ dài tối đa của event name, parameter name và value;
- số lượng event parameters và user properties tối đa;
- số lượng items và item-scoped custom parameters tối đa cho ecommerce;
- primitive type được phép và behavior khi GA4 convert type;
- cấu trúc array/object nếu source là ecommerce hoặc server-side;
- field sẽ được omit, gửi `null` nếu implementation hỗ trợ rõ ràng, hay reject khi không có dữ liệu;
- value có an toàn khi xuất hiện trong URL, log, export và screenshot không.

Dùng [event naming rules](https://support.google.com/analytics/answer/13316687) và [event collection limits](https://support.google.com/analytics/answer/9267744) làm nguồn thông tin chính thức. Kiểm tra lại trước mỗi implementation vì limits và reserved names có thể thay đổi. Không mặc định gửi `null`; hãy dùng behavior được implementation đã chọn hỗ trợ.

Thêm schema version khi event contract có khả năng thay đổi:

```text
event: "purchase"
schema_version: "2"
transaction_id: "ORDER-123"
currency: "USD"
value: 99.00
```

Chỉ sử dụng schema version khi team có thể vận hành và diễn giải nó. Nếu không, version hóa measurement-plan record và ghi lại deployed contract trong inventory.

### 6. Định nghĩa Data Layer và GTM mapping

Mapping phải rõ ràng và có thể trace:

| Measurement field | Application/Data Layer     | GTM object                 | GA4 destination                       |
| ----------------- | -------------------------- | -------------------------- | ------------------------------------- |
| Event name        | `event: "sign_up"`         | Custom Event trigger       | GA4 Event name `sign_up`              |
| Method            | `method: "email"`          | DLV `method`               | Event parameter `method`              |
| Form ID           | `form_id: "registration"`  | DLV `form_id`              | Event parameter `form_id`             |
| Consent           | CMP/approved consent state | Consent settings/variables | Behavior collection đã được phê duyệt |
| Destination       | Environment configuration  | Google tag/stream mapping  | QA hoặc production stream             |

Application nên push event và các value đi cùng nhau khi có thể:

```javascript
window.dataLayer = window.dataLayer || [];
window.dataLayer.push({
  event: "sign_up",
  method: "email",
  form_id: "registration",
});
```

Khi application có thể cung cấp authoritative signal, GTM không nên tự dựng business event từ DOM text, button label hoặc raw form value. Custom Event trigger lắng nghe event value được push vào Data Layer; xem [GTM Custom Event triggers](https://support.google.com/tagmanager/answer/7679219).

### 6. Quyết định key event

Chỉ đánh dấu event là key event khi event đại diện cho business outcome có ý nghĩa và tổ chức đã thống nhất cách sử dụng. Không đánh dấu mọi interaction là key event. Với mỗi candidate, ghi nhận:

- business owner và decision được hỗ trợ;
- success condition chính xác;
- volume và frequency dự kiến;
- có sử dụng cho advertising hoặc bidding không;
- deduplication rule;
- consent/privacy dependency;
- downstream reports, audiences hoặc exports;
- approval và change owner.

#### Validate collection trước khi đánh dấu key event

Đánh dấu một event là key event chỉ cho GA4 biết event đó quan trọng đối với business. Nó không sửa được một event đã bị collect sai.

Ví dụ, flow `sign_up` đúng là:

```text
User click Submit
  → input hợp lệ
  → application/backend xác nhận account creation
  → gửi một event sign_up
  → đánh dấu sign_up là key event
```

Flow sau đây không an toàn:

```text
User click Submit
  → gửi sign_up ngay lập tức
  → server trả về lỗi
```

Flow này có thể ghi nhận một registration chưa bao giờ thành công. Double click nhanh, retry hoặc refresh cũng có thể gửi cùng một success event nhiều lần. Nếu event đó là key event, GA4 có thể báo cáo business outcome cao hơn thực tế; khi event được dùng cho Google Ads, dữ liệu sai còn có thể ảnh hưởng đến quyết định quảng cáo hoặc bidding.

Trước khi đánh dấu event là key event, hãy kiểm tra:

- một business occurrence đã được xác nhận chỉ tạo ra một event;
- event chỉ fire sau khi success condition được xác nhận;
- input không hợp lệ, server failure, retry và refresh không tạo success giả hoặc duplicate;
- required parameter, consent behavior và destination đều chính xác.

Một event được collect đúng nhưng chưa được đánh dấu key event chủ yếu là vấn đề phân loại. Một key event bị duplicate hoặc fire quá sớm lại là vấn đề data quality, có thể làm sai report, business decision và downstream advertising.

### User-ID và user-property contract

Nếu product có authenticated user, cần có một identity section riêng trong plan. Không xem `user_id` như event parameter thông thường và không tạo high-cardinality custom dimension từ `user_id`.

Ghi nhận:

- identifier non-PII đã được phê duyệt và source of truth;
- thời điểm identifier trở nên available, thường là sau khi sign-in được phê duyệt;
- identifier được set ở configuration level hay qua Google tag implementation đã được phê duyệt;
- behavior trước sign-in, sau logout, khi đổi account và khi session tiếp tục;
- consent và privacy requirements;
- access, retention, export và deletion implications;
- reporting identity và cross-device expectations;
- test cases cho sign-in giữa session, logout, multi-tab behavior và account switching.

Với user property, ghi rõ property name, meaning, allowed values, update timing, unset behavior, scope, retention và reportability. User property mô tả user; event parameter mô tả event occurrence. Xem [send User-IDs](https://developers.google.com/analytics/devguides/collection/ga4/user-id).

### 7. Quyết định reporting readiness

Collect parameter và report parameter là hai bước khác nhau. Trước tiên cần xác nhận value đang được collect đúng; sau đó mới quyết định GA4 cần standard field, custom definition, export hay không cần report field nào cả.

Với mỗi parameter, chọn một outcome sau:

| Outcome                           | Khi nào dùng                                                                                       |
| --------------------------------- | -------------------------------------------------------------------------------------------------- |
| Standard dimension/metric         | GA4 đã expose đúng meaning và scope cần thiết.                                                     |
| Recommended event parameter       | Google có prescribed parameter hỗ trợ standard reporting.                                          |
| Event-scoped custom dimension     | Cần một descriptive parameter có controlled values trong Reports/Explorations.                     |
| Event-scoped custom metric        | Cần một numeric quantity và không có standard metric phù hợp.                                      |
| Keep collected but not reportable | Hữu ích cho debugging hoặc downstream processing nhưng không đáng tạo custom definition trong GA4. |
| Do not collect                    | Không có business use được phê duyệt, risk quá cao, là PII hoặc cardinality không kiểm soát.       |

Chỉ đăng ký custom definition sau khi parameter đang được collect và đã được validate. Hướng dẫn hiện tại của Google cho biết custom definition mới cần thời gian mới có thể report và field có cardinality cao có thể bị gom vào `(other)` hoặc làm giảm chất lượng analysis. Xem [custom dimensions and metrics](https://support.google.com/analytics/answer/14240153).

## Template cho Measurement Plan

Bộ template này là hồ sơ làm việc để biến business requirement thành các event contract có thể vận hành và maintain. Dùng một row cho mỗi event contract; không gom các business moment khác nhau vào một row mơ hồ. Mỗi template có một mục đích riêng:

- **Journey coverage matrix** cho biết journey cần những event nào.
- **Event contract** định nghĩa một event và business meaning của nó.
- **Parameter dictionary** định nghĩa các field được gửi cùng event.
- **Traceability matrix** kết nối application, Data Layer, GTM, GA4, report và QA evidence.
- Các record về decision và lifecycle giải thích vì sao field được report và cách quản lý thay đổi.

Các section derived view và handoff trong completed example chỉ nhằm giúp team implementation, QA và reporting đọc plan dễ hơn; chúng không phải template canonical bổ sung. Canonical record vẫn là nguồn chuẩn cho event, parameter, consent, traceability, decision và lifecycle.

### Journey và event coverage matrix

**Mục đích:** Dùng matrix này để quản lý một journey hoàn chỉnh có nhiều event. Nó giúp product, analytics, development, QA và reporting owner nhìn được coverage trong một nơi thay vì phải đọc từng event row riêng lẻ.

| Journey ID | Journey      | Business question                      | Event sequence                                          | Primary key event | Report ID | QA/Evidence ID | Owner        | Status |
| ---------- | ------------ | -------------------------------------- | ------------------------------------------------------- | ----------------- | --------- | -------------- | ------------ | ------ |
| J-REG-001  | Registration | User abandon ở đâu trong registration? | `registration_start` → `registration_error` → `sign_up` | `sign_up`         | R-REG-001 | TC-REG-001     | Product team | Active |

### Event contract template

**Mục đích:** Dùng một record cho một canonical event. Đây là source of truth về ý nghĩa event, thời điểm event đúng, dữ liệu được gửi, destination và owner.

| Field                               | Ví dụ                                                                                |
| ----------------------------------- | ------------------------------------------------------------------------------------ |
| Plan ID / version                   | MP-REG-001 / v1.2                                                                    |
| Requirement ID / journey            | REQ-REG-001 / J-REG-001                                                              |
| Business area                       | Registration                                                                         |
| Business question                   | Method đăng ký nào có completion rate thấp nhất?                                     |
| Decision owner                      | Product team                                                                         |
| Decision                            | Ưu tiên cải thiện UX cho method có completion thấp đáng kể.                          |
| GA4 property / stream               | `[property] / [web stream]`                                                          |
| GTM container / environment         | `[container] / QA → production`                                                      |
| Platform / collection source        | Web client-side qua GTM                                                              |
| Collection-source ownership         | GTM là client-side source chuẩn; không có source thứ hai chưa được document          |
| Event name                          | `sign_up`                                                                            |
| Schema version                      | `v1`                                                                                 |
| Event type                          | Recommended                                                                          |
| Business definition                 | Account creation được server xác nhận                                                |
| Authoritative business moment       | Backend/application xác nhận account creation                                        |
| Source system / source of truth     | Registration application / account-creation response                                 |
| Source owner                        | Registration application                                                             |
| Data Layer signal                   | `event: "sign_up"`                                                                   |
| GTM trigger                         | `CE - Web - sign_up`                                                                 |
| GA4 tag                             | `GA4 Event - Web - sign_up`                                                          |
| Environment / routing               | QA stream; production stream sau approval                                            |
| Expected occurrence/frequency       | Một event cho mỗi account creation được xác nhận; daily volume dự kiến `[range]`     |
| Deduplication/idempotency           | Account creation ID hoặc confirmed occurrence từ server; không duplicate khi refresh |
| Key event                           | Có, chờ product approval                                                             |
| Google Ads conversion               | Không / chờ advertising approval riêng                                               |
| Consent requirement/denied behavior | Analytics behavior đã được phê duyệt; ghi rõ denied behavior                         |
| Required parameters                 | `method`, `form_id`                                                                  |
| Optional parameters                 | `account_type` nếu được phê duyệt                                                    |
| Negative cases                      | Invalid input, server failure, duplicate submit, refresh                             |
| Data classification/privacy status  | Internal; không PII; chỉ controlled vocabulary                                       |
| Downstream consumers                | Reports, Exploration, audience, Ads import, exports                                  |
| Report / custom-definition ID       | `R-REG-001` / `[CD-ID hoặc N/A]`                                                     |
| QA / evidence ID                    | `TC-REG-001` / `[evidence link]`                                                     |
| Release / monitoring ID             | `[release ID]` / `[monitor ID]`                                                      |
| Owner / reviewer                    | `[name/team]`                                                                        |
| Lifecycle/status/review date        | QA / YYYY-MM-DD / `[next review]`                                                    |

### Template cho parameter dictionary

**Mục đích:** Dùng dictionary này để định nghĩa mọi event parameter đã được phê duyệt trước implementation. Nó ngăn các team dùng cùng một field name nhưng khác meaning, type, value hoặc privacy behavior.

Đối với reporting readiness, hãy liên kết tới [Field-readiness inventory](09-reports-charts-answer.md) thay vì copy toàn bộ report template vào tài liệu này. Ghi report field hoặc custom-definition ID ở dưới.

| Event     | Parameter      | Meaning                   | Type   | Scope | Required? | Allowed values             | Missing/invalid behavior | Source of truth | Schema | Validation rule             | Report/custom-definition ID | Privacy          | Consent            | Cardinality/volume |
| --------- | -------------- | ------------------------- | ------ | ----- | --------- | -------------------------- | ------------------------ | --------------- | ------ | --------------------------- | --------------------------- | ---------------- | ------------------ | ------------------ |
| `sign_up` | `method`       | Registration method       | string | Event | Yes       | `email`, `google`, `apple` | QA failure               | Application     | `v1`   | Controlled vocabulary       | Standard/review             | Internal         | Analytics approved | Low / `[range]`    |
| `sign_up` | `form_id`      | Stable form identifier    | string | Event | Yes       | Approved IDs               | QA failure               | Application     | `v1`   | Approved ID, bounded length | `[CD-ID if needed]`         | Internal         | Analytics approved | Low / `[range]`    |
| `sign_up` | `account_type` | Approved account category | string | Event | No        | Controlled list            | Omit                     | Application     | `v1`   | Controlled vocabulary       | `[CD-ID if needed]`         | Sensitive review | `[consent]`        | `[range]`          |

### Template cho traceability matrix

**Mục đích:** Dùng matrix này để chứng minh một business requirement được kết nối end to end. Đây là index giúp reviewer đi từ requirement tới implementation, request evidence, reporting, QA, consent và release record.

| Requirement/Event ID    | Application state | Data Layer               | GTM                  | Consent behavior            | Request evidence     | GA4/report field      | QA/Evidence ID | Release ID     | Status | Owner    |
| ----------------------- | ----------------- | ------------------------ | -------------------- | --------------------------- | -------------------- | --------------------- | -------------- | -------------- | ------ | -------- |
| REQ-REG-001 / `sign_up` | Server success    | `sign_up` pushed một lần | CE trigger + GA4 tag | Approved analytics behavior | Một request, đúng ID | Event count/key event | TC-01/TC-03    | `[release ID]` | QA     | `[name]` |

### Key-event và custom-definition decision record

**Mục đích:** Dùng record này để giải thích vì sao event được đánh dấu là key event hoặc vì sao parameter cần custom dimension/metric. Nó ngăn các setting quan trọng của GA4 bị thay đổi mà không có business reason, owner hoặc privacy review.

```text
Decision ID:
Event/parameter:
Requirement/journey ID:
Business question:
Decision owner:
Business purpose:
Success condition:
Expected occurrence và volume:
Deduplication rule:
Mark as GA4 key event? [Yes/No]
Reason:
Use for Google Ads conversion/bidding? [Yes/No/Pending]
Standard field checked:
Custom dimension/metric required? [Yes/No]
Custom definition ID/status:
Cardinality và quota review:
Consent/privacy impact:
Report/audience/export consumers:
QA/Evidence ID:
Approval và change owner:
Effective/review date:
```

### Schema change và lifecycle register

**Mục đích:** Dùng register này để quản lý thay đổi về event meaning, parameter type/scope, allowed values hoặc downstream behavior. Nó khác release record: register mô tả contract change, còn release record mô tả deployment.

| Change ID  | Event/parameter  | Current version | Proposed version | Change type          | Affected consumers       | Migration plan                  | Approval owner  | Effective date | Status   |
| ---------- | ---------------- | --------------- | ---------------- | -------------------- | ------------------------ | ------------------------------- | --------------- | -------------- | -------- |
| CH-REG-001 | `sign_up.method` | `v1`            | `v2`             | Allowed-value change | Report, Exploration, Ads | Dual-write và migrate consumers | Analytics owner | YYYY-MM-DD     | Proposed |

### Consent và data classification matrix

**Mục đích:** Dùng matrix này để ghi nhận cách phân loại event/parameter và behavior khi consent liên quan bị denied. Giữ implementation và test case chi tiết trong [Consent Management](05-consent-answer.md).

| Event/parameter  | Data classification | Consent requirement | Denied behavior                          | Destination    | Retention       | Privacy owner | Evidence/approval | Status   |
| ---------------- | ------------------- | ------------------- | ---------------------------------------- | -------------- | --------------- | ------------- | ----------------- | -------- |
| `sign_up.method` | Internal            | Analytics consent   | Omit hoặc block theo design đã phê duyệt | GA4 web stream | Property policy | Privacy team  | `[link]`          | Approved |

## Ví dụ hoàn chỉnh — Registration Journey

Ví dụ này thể hiện một Measurement Plan hoàn chỉnh, sẵn sàng cho implementation. Đây là sample để học, không phải production contract có thể copy nguyên trạng. Hãy thay property, stream, owner, URL, allowed values, evidence link và tên người phê duyệt bằng thông tin đã được project approve.

### Lựa chọn template cho Registration example này

Registration Journey này có ba event, một business outcome đã được approve, client-side web collection path và một reporting use case. Nó **không cần dùng toàn bộ template** trong learning set. Hãy chọn như sau:

| Mức độ | Template hoặc record | Nguồn chuẩn / quan hệ | Vì sao áp dụng cho ví dụ này |
| --- | --- | --- | --- |
| **Bắt buộc cho tracking design và implementation** | Project Context và Baseline | Canonical context record ở cấp plan | Cố định property, stream, environment, owner, source và version mà contract áp dụng. |
| **Bắt buộc cho tracking design và implementation** | Journey và event coverage matrix | Canonical Measurement Plan template | Registration là journey nhiều event; matrix xác nhận đã cover start, error và confirmed completion. |
| **Bắt buộc cho tracking design và implementation** | Event contract, một record cho mỗi event | Canonical Event contract template | Định nghĩa business meaning, authoritative moment, timing, parameter, deduplication, destination và owner. |
| **Bắt buộc cho tracking design và implementation** | Parameter dictionary | Canonical Parameter dictionary template | Định nghĩa `form_id`, `method` và `error_type`, gồm type, scope, allowed value, privacy, consent và invalid behavior. |
| **Bắt buộc cho tracking design và implementation** | Traceability matrix | Canonical Traceability matrix; mapping view bên dưới được suy ra từ đây | Kết nối application state, Data Layer, GTM, consent, request destination, GA4 field, QA evidence và release ID. |
| **Derived view tùy chọn** | Event coverage summary / Event plan | Suy ra từ Journey coverage matrix và Event contract record | Cung cấp summary dễ đọc; không phải source of truth thứ hai. |
| **Derived view tùy chọn** | Data Layer, GTM và destination mapping | Suy ra từ Traceability matrix và routing field trong Event contract | Giúp đọc routing implementation; không được định nghĩa lại business meaning. |
| **Bắt buộc trước production collection** | Consent và data classification matrix | Canonical Consent và data classification matrix | Ghi data nào được collect, consent denied sẽ xử lý thế nào và data nào bị cấm. |
| **Bắt buộc trước production collection** | Required Test Matrix và Evidence Template trong Section 08 | QA record chuẩn trong [Debug/QA](08-debug-qa-answer.md) | Chứng minh positive, negative, duplicate, consent, privacy và routing behavior qua các collection layer. |
| **Bắt buộc trong ví dụ này vì `sign_up` là key event hoặc có custom field** | Key-event và custom-definition decision record | Canonical decision record trong Measurement Plan này | Giải thích riêng việc đánh dấu GA4 key event và register `method`/`error_type`; đánh dấu key event không validate collection. |
| **Chỉ bắt buộc nếu report nằm trong deliverable** | Report Requirements và Field-readiness template trong Section 09 | Reporting record chuẩn trong [Reports và Charts](09-reports-charts-answer.md) | Định nghĩa completion-rate question, denominator, report field và availability. Có thể implement tracking trước khi build report cuối cùng. |
| **Bắt buộc trước production activation khi change có ý nghĩa** | Approval record và Release Record trong Section 10 | Project governance record và [Release & Monitoring](10-release-monitoring-answer.md) | Ghi accountable approval, deployment version, smoke test, observation window và rollback/monitoring decision. |

Minimum tracking packet cho ví dụ này là: **context → journey coverage → event contracts → parameter dictionary → traceability → consent/privacy → QA evidence**. Thêm key-event/custom-definition record vì ví dụ dùng các decision đó. Chỉ thêm reporting, release và monitoring record khi deliverable hoặc production change tương ứng nằm trong scope.

#### Không cần cho Registration example này

| Template hoặc record | Vì sao nằm ngoài scope hoặc được deferred |
| --- | --- |
| Ecommerce event và item schema addendum | Journey không đo product, cart, purchase, refund, item hoặc revenue. |
| Server/offline event addendum | Ví dụ dùng browser → Data Layer → GTM → GA4 web-stream path. Chỉ thêm khi `sign_up` cũng được gửi từ backend/offline source. |
| User-ID và user-property contract | Registration completion được đo mà không cần authenticated identity field. Chỉ thêm khi feature có sign-in identity behavior đã approve. |
| Report Configuration, Chart Specification và Interpretation Note | Chỉ cần sau khi report/chart thực sự được build hoặc publish, không cần chỉ để định nghĩa tracking contract. |
| Schema change và lifecycle register | Không cần cho contract `v1` ban đầu; dùng khi đổi event meaning, parameter type/scope, allowed value hoặc downstream consumer. |
| GA4 Operations scenario/change/governance template | Không cần trừ khi feature thay đổi property setting, access, filter, attribution, identity, integration hoặc operational state khác. |

Các section bên dưới cố ý hiển thị một số derived view hoặc phần sẽ thực hiện ở lifecycle tiếp theo để người đọc thấy toàn bộ quy trình. Không nên hiểu chúng là các template bắt buộc bổ sung. Hãy duy trì một canonical record và link tới nó thay vì copy cùng một decision vào nhiều bảng.

Ví dụ này cố ý tách riêng:

- **business truth**: account thực sự đã được tạo;
- **collection contract**: event và parameter nào được gửi, tại thời điểm nào và một lần cho mỗi occurrence;
- **reporting contract**: field nào đã collect được register và dùng trong report;
- **governance decision**: có đánh dấu `sign_up` là GA4 key event hay import vào Google Ads hay không.

Đánh dấu event là key event không sửa được collection kém. Contract phải đúng và event phải pass QA trước khi được dùng cho business hoặc advertising decision.

### 1. Context, decision và scope

```text
Plan ID/version: MP-REG-001 / v1.0
Journey ID: J-REG-001
Business area: Product growth và account creation
Business owner: Product Growth team
Analytics owner: Product Analytics
Technical owner: Registration application team
GTM owner: Web Analytics Engineering
QA owner: Analytics QA

Business question: User abandon ở đâu trong registration, và confirmed account creation có được ghi nhận một lần không?
Decision được hỗ trợ: Ưu tiên registration step có user drop-off lớn nhất sau khi validate và điều tra vấn đề UX hoặc technical liên quan.
Population: User đi vào registration journey trong QA hoặc production web property đã được approve.
Grain: User-level completion rate và event-level delivery/duplicate checks.
Source of truth: Registration application và account-creation service response.
Authoritative success moment: Backend xác nhận account đã được tạo.
Collection path: Web application → Data Layer → GTM → GA4 web stream.
Environment: Bắt đầu bằng QA stream; chỉ dùng production stream sau approval và release validation.
Key event: sign_up, sau khi business và QA approval hoàn tất.
Google Ads conversion: Không import trong ví dụ này; cần một advertising decision riêng.
```

Source of truth là account-creation response, không phải submit click, button state, URL, thank-you component hoặc front-end success message xuất hiện trước khi backend xác nhận account. Phân biệt này bảo vệ completion rate khỏi premature success event.

### 2. Journey và event coverage matrix — bắt buộc

| Journey ID | Step        | Business state                                                 | Event                | Event classification | Expected occurrence                                       | Primary consumer                       |
| ---------- | ----------- | -------------------------------------------------------------- | -------------------- | -------------------- | --------------------------------------------------------- | -------------------------------------- |
| J-REG-001  | 1. Start    | Registration form được mở và sẵn sàng cho user                 | `registration_start` | Custom event         | Một lần cho mỗi journey start; không fire khi SPA remount | Funnel entry và start-volume QA        |
| J-REG-001  | 2. Error    | Validation hoặc server error đã plan được hiển thị rõ cho user | `registration_error` | Custom event         | Một lần cho mỗi displayed error occurrence đã approve     | Error breakdown và troubleshooting     |
| J-REG-001  | 3. Complete | Account-creation service xác nhận account mới                  | `sign_up`            | Recommended event    | Một lần cho mỗi account được xác nhận                     | Completion rate và key-event reporting |

Không tạo một success event riêng cho submit button. Nếu user click Submit nhưng validation fail hoặc server reject request, journey chưa đạt đến `sign_up`.

### 3. Event coverage summary — derived view tùy chọn

Đây là view rút gọn của Journey và event coverage matrix ở trên và các Event contract record chi tiết bên dưới. Dùng nó để định hướng nhanh; detailed contract vẫn là source of truth.

| Event                | Business definition                                             | Authoritative moment/source                         | Required parameters     | Expected occurrence và deduplication                                                                           | Key event decision | Report use                                 |
| -------------------- | --------------------------------------------------------------- | --------------------------------------------------- | ----------------------- | -------------------------------------------------------------------------------------------------------------- | ------------------ | ------------------------------------------ |
| `registration_start` | User đã vào registration journey và form sẵn sàng để sử dụng.   | Registration application state; form-ready callback | `form_id`               | Một lần cho mỗi journey start; suppress duplicate push do remount                                              | No                 | Funnel entry và start-user denominator     |
| `registration_error` | Validation hoặc server error đã approve được hiển thị cho user. | Registration application error state                | `form_id`, `error_type` | Một lần cho mỗi displayed error occurrence; không gửi hidden/raw error text                                    | No                 | Error count và controlled error breakdown  |
| `sign_up`            | Account-creation service xác nhận account mới tồn tại.          | Backend/application success response                | `method`, `form_id`     | Một lần cho mỗi confirmed account; không event khi failure, retry trước success hoặc refresh confirmation page | Yes, sau approval  | Completion rate và GA4 key-event reporting |

Ba row dùng event classification khác nhau có chủ đích: `sign_up` theo GA4 recommended event taxonomy, còn start và error của journey là custom event. Chỉ dùng custom event vì các business moment này không được thay thế bởi automatic hoặc recommended event phù hợp.

### 4. Event contract records — bắt buộc, một record cho mỗi event

#### `registration_start`

```text
Event type: Custom
Business meaning: User đã vào registration journey và form có thể sử dụng.
Authoritative source: Registration application form-ready state.
Trigger condition: Form sẵn sàng sau khi user chủ động vào registration.
Không trigger khi: Generic page load, SPA component remount, hidden pre-render hoặc mỗi lần field được focus.
Expected frequency: Một lần cho mỗi intended journey start.
Required parameter: form_id = registration.
Negative cases: Page view nhưng chưa vào form; component remount; back/forward không tạo một journey mới có chủ đích.
Downstream use: Funnel entry, start-user denominator và start-volume QA.
Owner: Registration application team.
```

#### `registration_error`

```text
Event type: Custom
Business meaning: Planned validation hoặc server error được hiển thị cho user.
Authoritative source: Registration application error state và approved error taxonomy.
Trigger condition: Error đã approve được render hoặc announce cho user.
Không trigger khi: Hidden error, developer log, raw server message, stack trace, email hoặc password.
Expected frequency: Một lần cho mỗi displayed error occurrence đã approve.
Required parameters: form_id = registration; error_type lấy từ controlled list.
Negative cases: Không hiển thị error; duplicate rendering cùng một error; error_type chưa được approve.
Downstream use: Error breakdown, QA diagnosis và product investigation.
Owner: Registration application team.
```

#### `sign_up`

```text
Event type: Recommended event
Business meaning: Account mới đã được account-creation service xác nhận.
Authoritative source: Successful backend/application account-creation response.
Trigger condition: Application nhận confirmed success state và chưa emit event cho account creation đó.
Không trigger khi: Submit click, client-side validation pass, loading state, optimistic UI, payment/auth redirect trước confirmation hoặc page refresh.
Expected frequency: Một lần cho mỗi confirmed account creation.
Required parameters: method từ controlled list; form_id = registration.
Deduplication: Application-level idempotency và một client emission cho mỗi confirmed business occurrence; refresh không emit lại.
Downstream use: Completion rate và tùy chọn GA4 key event.
Owner: Registration application team với Analytics review.
```

Nếu sau này tổ chức gửi `sign_up` từ server/offline source, phải chọn một authoritative collection path hoặc document deduplication design trước release. Không gửi cùng một confirmed account từ browser và server nếu chưa chứng minh được cách ngăn double count.

### 5. Parameter dictionary và data minimization — bắt buộc

| Event                                                 | Parameter        | Meaning                                       | Type/scope                       | Required?            | Allowed values                                  | Missing/invalid behavior                                             | Source of truth                        | GA4 reporting decision                                                                      | Privacy/cardinality                            |
| ----------------------------------------------------- | ---------------- | --------------------------------------------- | -------------------------------- | -------------------- | ----------------------------------------------- | -------------------------------------------------------------------- | -------------------------------------- | ------------------------------------------------------------------------------------------- | ---------------------------------------------- |
| `registration_start`, `registration_error`, `sign_up` | `form_id`        | Stable identifier của registration form       | String / event                   | Yes                  | `registration` và các bounded ID đã approve     | Block hoặc fail QA; không dùng arbitrary text                        | Registration application               | Collect cho QA/routing; chỉ register custom dimension nếu nhiều form cần recurring analysis | Internal, non-PII, low cardinality             |
| `registration_error`                                  | `error_type`     | Controlled reason category của error hiển thị | String / event                   | Yes                  | `validation`, `duplicate_email`, `server_error` | Chỉ dùng taxonomy đã approve; omit hoặc fail QA nếu value không biết | Application error mapping              | Register event-scoped custom dimension nếu error breakdown là recurring use case đã approve | Internal, non-PII, bounded vocabulary          |
| `sign_up`                                             | `method`         | Method dùng để tạo account                    | String / event                   | Yes                  | `email`, `google`, `apple`                      | Không gửi event với value chưa approve; fail QA                      | Registration application/auth response | Register event-scoped custom dimension cho registration health report                       | Internal, non-PII, low cardinality             |
| Data Layer only                                       | `schema_version` | Version của application/Data Layer contract   | String / implementation metadata | Yes trong Data Layer | `v1`                                            | Block release nếu thiếu hoặc unexpected                              | Application contract                   | Không gửi vào GA4 nếu chưa có reporting use riêng được approve                              | Internal metadata; không phải GA4 report field |

Không collect email, phone number, password, raw error message, stack trace, account name, raw database ID, session ID, timestamp-as-a-dimension hoặc free-text form value cho journey này. QA correlation ID có thể tồn tại trong internal log, nhưng không được gửi vào GA4 nếu chưa được review và justify riêng.

`schema_version` xuất hiện trong Data Layer example để hỗ trợ implementation và QA. Nó không tự động là GA4 event parameter hoặc custom dimension. Collection truth và reporting truth là hai decision riêng.

### 6. Data Layer, GTM và destination mapping — derived implementation view

Đây là routing view tùy chọn được tạo từ [Traceability matrix template](#template-cho-traceability-matrix) và routing field trong từng Event contract. Nó kết nối application signal đã approve với routing và destination; không định nghĩa lại business event và không được duy trì như source of truth thứ hai.

```javascript
// schema_version là implementation metadata và mặc định không gửi vào GA4.
window.dataLayer.push({
  event: "registration_start",
  form_id: "registration",
  schema_version: "v1",
});

window.dataLayer.push({
  event: "registration_error",
  form_id: "registration",
  error_type: "validation",
  schema_version: "v1",
});

window.dataLayer.push({
  event: "sign_up",
  method: "email",
  form_id: "registration",
  schema_version: "v1",
});
```

| Event                | Data Layer signal            | GTM trigger/tag                                                          | Destination                   | Routing rule                                                          |
| -------------------- | ---------------------------- | ------------------------------------------------------------------------ | ----------------------------- | --------------------------------------------------------------------- |
| `registration_start` | `event = registration_start` | `CE - Web - registration_start` → `GA4 Event - Web - registration_start` | QA hoặc production web stream | Fire một lần cho intended journey start                               |
| `registration_error` | `event = registration_error` | `CE - Web - registration_error` → `GA4 Event - Web - registration_error` | QA hoặc production web stream | Chỉ fire cho visible error đã approve và `error_type` controlled      |
| `sign_up`            | `event = sign_up`            | `CE - Web - sign_up` → `GA4 Event - Web - sign_up`                       | QA hoặc production web stream | Chỉ fire sau confirmed account creation và một lần cho mỗi occurrence |

GTM trigger chỉ route application signal; nó không quyết định button click có nghĩa là account đã được tạo. GA4 Event tag chỉ map các parameter đã approve và không được forward toàn bộ Data Layer object.

### 7. Consent và data classification matrix — bắt buộc trước production

| Data                                                   | Classification                | Consent requirement                    | Behavior khi consent denied                                                   | Destination                | Retention/owner                 |
| ------------------------------------------------------ | ----------------------------- | -------------------------------------- | ----------------------------------------------------------------------------- | -------------------------- | ------------------------------- |
| `form_id`, `method`, `error_type`                      | Internal, controlled, non-PII | Theo approved analytics consent design | Block, omit hoặc dùng Consent Mode behavior đã approve; không bypass decision | GA4 web stream             | Property policy / Privacy owner |
| Email, phone, password, raw error text, raw account ID | Prohibited trong contract này | Not applicable                         | Không collect hoặc forward                                                    | None                       | Privacy owner                   |
| User-ID sau authenticated sign-in                      | Separate identity contract    | Separate approved conditions           | Clear hoặc omit theo identity contract                                        | GA4 identity configuration | Identity owner                  |

Denied behavior cụ thể phải lấy từ Consent Management design đã approve cho property. “Consent denied” không phải lý do để gửi prohibited parameter qua tag hoặc destination khác.

### 8. Key-event và custom-definition decision record — bắt buộc trong ví dụ này

```text
Decision ID: DEC-REG-001
Event: sign_up
Business purpose: Đo confirmed account creation và registration completion.
Mark as GA4 key event: Yes, sau khi collection QA pass và Product approve.
Reason: Confirmed account creation là outcome business muốn monitor; start và error là supporting diagnostics, không phải outcome.
Google Ads conversion/bidding: No trong ví dụ này; advertising owner riêng phải approve import hoặc bidding use.
Deduplication rule: Một event cho mỗi confirmed account creation; không event khi validation fail, server failure, retry trước success hoặc refresh.
Required evidence: Application success state, Data Layer, GTM, Network, consent, DebugView và processed report evidence.
```

Key-event label là downstream configuration. Nó không validate event name, timing, parameter, consent, destination hoặc duplicate behavior. `sign_up` bị duplicate hoặc fire quá sớm có thể gây hại hơn việc chưa đánh dấu một event hợp lệ vì nó làm sai product decision và advertising outcome.

### 9. Reporting readiness và report requirements — conditional handoff tới Section 09

Section này là reporting handoff, không copy lại toàn bộ reporting template set. Report configuration, chart specification và interpretation note chuẩn nằm trong [Reports và Charts](09-reports-charts-answer.md); Measurement Plan chỉ lưu các ID và contract decision liên quan.

#### Field-readiness decision

| Field            | Collection status                    | Scope          | Reporting status                                          | Rationale/limitation                                                            |
| ---------------- | ------------------------------------ | -------------- | --------------------------------------------------------- | ------------------------------------------------------------------------------- |
| `event_name`     | Collected như standard GA4 field     | Event          | Ready                                                     | Dùng standard event name thay vì tạo event-category parameter riêng             |
| `method`         | Collected trên `sign_up`             | Event          | Register event-scoped custom dimension sau QA             | Cần cho recurring completion breakdown; value có low cardinality và controlled  |
| `error_type`     | Collected trên `registration_error`  | Event          | Chỉ register nếu Product approve recurring error analysis | Hữu ích cho diagnosis; giữ vocabulary bounded                                   |
| `form_id`        | Collected trên toàn bộ journey event | Event          | Giữ cho QA/routing; custom dimension là tùy chọn          | Một form hiện tại chưa justify report field mới; đánh giá lại khi có nhiều form |
| `schema_version` | Data Layer metadata                  | Implementation | Mặc định không reportable                                 | Dùng nhận diện contract version trong QA, không phải business dimension         |

Chỉ register custom definition sau khi collection pass QA. Ghi registration date, expected availability delay, scope, cardinality review và report sử dụng definition đó. Không tạo custom dimension cho mọi parameter chỉ vì parameter xuất hiện trong request.

#### Report requirements

| Report ID | Audience       | Business question                                                      | Decision                                | Population/grain                              | Dimensions                                      | Metrics/formula                                        | Surface                                        | Owner             |
| --------- | -------------- | ---------------------------------------------------------------------- | --------------------------------------- | --------------------------------------------- | ----------------------------------------------- | ------------------------------------------------------ | ---------------------------------------------- | ----------------- |
| R-REG-001 | Product Growth | Registration method nào có completion rate thấp nhất sau validation?   | Ưu tiên UX hoặc technical investigation | User vào registration; user-level rate        | `method`, `device category`, date               | Users with `sign_up` / users with `registration_start` | Detail report + funnel Exploration             | Product Analytics |
| R-REG-002 | Analytics/QA   | Confirmed account creation có được gửi một lần với value hợp lệ không? | Approve hoặc fix release                | Event occurrence trong controlled test period | `event_name`, `method`, `form_id`, `error_type` | Event count và duplicate review                        | Free-form Exploration + processed event report | Analytics QA      |

Với R-REG-001, “completion rate” có nghĩa là user-level calculation đã document:

```text
Registration completion rate
  = users có ít nhất một sign_up hợp lệ
    / users có ít nhất một registration_start hợp lệ
```

Numerator và denominator phải dùng cùng date range, population, identity context và registration journey đã approve. `sign_up` event count không bằng số completed user; rate thấp hơn theo method là lý do để điều tra, không tự nó chứng minh method gây ra drop.

### 10. QA và evidence matrix — bắt buộc trước production; canonical record nằm trong Section 08

Section này tóm tắt QA contract cho journey. Test case, debug session record, defect record và evidence chuẩn được quản lý trong [Debug/QA](08-debug-qa-answer.md); Measurement Plan liên kết các ID và expected outcome.

| Test ID    | Scenario                         | Expected application/Data Layer result                           | Expected tag/request result                                     | Report/decision evidence                            | Status    |
| ---------- | -------------------------------- | ---------------------------------------------------------------- | --------------------------------------------------------------- | --------------------------------------------------- | --------- |
| TC-REG-001 | Form mở và sẵn sàng              | Một `registration_start`; `form_id=registration`; không PII      | Một request tới QA Measurement ID                               | Event xuất hiện một lần trong DebugView/test report | Pass/Fail |
| TC-REG-002 | Input không hợp lệ               | Không `sign_up`; chỉ `registration_error` đã approve nếu visible | Không success request; error request có controlled `error_type` | Error breakdown giải thích được                     | Pass/Fail |
| TC-REG-003 | Server failure                   | Không `sign_up`; controlled server error nếu đã plan             | Không success request                                           | Failure được ghi nhận mà không false completion     | Pass/Fail |
| TC-REG-004 | Account creation được xác nhận   | Một `sign_up` hợp lệ với `method` và `form_id` đã approve        | Một request tới QA/production destination đúng                  | Completion/key-event data hợp lý sau processing     | Pass/Fail |
| TC-REG-005 | Double submit/retry nhanh        | Một business occurrence đã confirm và một `sign_up`              | Không duplicate success request                                 | Event count reconcile với application result        | Pass/Fail |
| TC-REG-006 | Refresh/back/SPA remount         | Không duplicate `sign_up`; không extra start ngoài ý muốn        | Không unexpected duplicate request                              | Duplicate review được ghi nhận                      | Pass/Fail |
| TC-REG-007 | Consent denied                   | Behavior theo consent design; không prohibited data              | Không unauthorized request hoặc parameter                       | Consent evidence được đính kèm                      | Pass/Fail |
| TC-REG-008 | Sai environment hoặc destination | Test bị block hoặc route tới stream đúng                         | Request chỉ chứa Measurement ID đã approve                      | Routing evidence được đính kèm                      | Pass/Fail |
| TC-REG-009 | Kiểm tra User-ID ngoài scope     | Registration tracking không tự thêm identity field               | Không email/phone/raw identifier hoặc User-ID chưa approve      | Identity tracking vẫn nằm ở scope riêng              | Pass/Fail/Out of scope |
| TC-REG-010 | Collection source ownership     | Chỉ dùng application → Data Layer path chuẩn; không có duplicate source chưa được document | Một request hoặc deduplication behavior đã document | Đính kèm source map và request timeline | Pass/Fail |

Với mỗi test, dùng cùng `Test ID` trong application evidence, Data Layer inspection, GTM Preview, Network request, consent state, DebugView và processed reporting. Ghi nhận first failing layer thay vì đánh dấu Pass chỉ vì GTM tag đã fire.

### 11. Traceability matrix và consumers — canonical record

Đây là traceability record chuẩn của ví dụ. Section 6 chỉ là routing view được suy ra từ record này; không duy trì hai record độc lập.

| Requirement/event                  | Application/source                 | Data Layer                                  | GTM                                  | Consent                     | Request/destination                 | GA4/report field                | QA/release evidence                    | Owner/status     |
| ---------------------------------- | ---------------------------------- | ------------------------------------------- | ------------------------------------ | --------------------------- | ----------------------------------- | ------------------------------- | -------------------------------------- | ---------------- |
| REQ-REG-001 / `registration_start` | Form-ready state                   | `registration_start` một lần                | Custom Event trigger + GA4 Event tag | Approved analytics behavior | Một request tới approved web stream | Event name, users, funnel entry | TC-REG-001/006/007; `[release ID]`     | Product app / QA |
| REQ-REG-001 / `registration_error` | Visible approved error             | `registration_error` một lần mỗi occurrence | Custom Event trigger + GA4 Event tag | Approved analytics behavior | Chỉ controlled `error_type`         | Event name, `error_type`        | TC-REG-002/003/007; `[release ID]`     | Product app / QA |
| REQ-REG-001 / `sign_up`            | Backend-confirmed account creation | `sign_up` một lần                           | Custom Event trigger + GA4 Event tag | Approved analytics behavior | Một request tới approved web stream | Event name, `method`, key event | TC-REG-004/005/006/007; `[release ID]` | Product app / QA |

Consumers được giới hạn rõ ở registration detail report, funnel Exploration, DebugView/processed QA report và optional GA4 key-event reporting đã approve. Google Ads import không phải hidden consumer; nó cần approval record riêng.

### 12. Approval record và schema lifecycle — conditional

#### Ví dụ approval record

| Gate                  | Reviewer                          | Evidence hoặc decision                                                                         | Status                             |
| --------------------- | --------------------------------- | ---------------------------------------------------------------------------------------------- | ---------------------------------- |
| Business scope        | Product Growth + Analytics        | Question, decision, journey, population và owner đã approve                                    | Pass                               |
| Event semantics       | Product + Application + Analytics | Backend-confirmed success, controlled error taxonomy và deduplication rule đã approve          | Pass                               |
| Technical feasibility | Application + GTM                 | Data Layer signal, environment routing và parameter mapping đã xác nhận                        | Pass                               |
| Privacy/consent       | Privacy + Analytics               | Không PII; denied behavior link tới Consent Management design đã approve                       | Pass                               |
| Reporting readiness   | Reporting + Analytics             | `method` và nếu được approve thì `error_type` custom definition; user-level rate đã định nghĩa | Pass with registration pending     |
| QA/release readiness  | QA + Implementation               | Positive, negative, duplicate, boundary, consent, privacy và routing case đã sẵn sàng          | Pass for QA; production pending    |
| Final plan decision   | Final plan owner                  | Approved cho QA implementation; production activation theo release evidence                    | Approved with controlled exception |

```text
Plan ID/version: MP-REG-001 / v1.0
Approval outcome: Approved with controlled exception
Exception: Chỉ register custom definition sau khi QA evidence pass; production activation cần release approval.
Exception owner/due date: Product Analytics / YYYY-MM-DD
Evidence location: [sanitized ticket, QA evidence, report configuration, release record]
Next review: Khi schema, consent hoặc report thay đổi; hoặc quarterly review theo lịch.
```

“Approved with controlled exception” không có nghĩa là được implementation một business truth chưa rõ. Exception chỉ giới hạn ở delayed reporting registration và production activation; event contract đã hoàn chỉnh và có thể test.

#### Ví dụ schema lifecycle register

| Change ID  | Event/parameter  | Current version | Proposed version | Change type                   | Affected consumers                            | Migration/approval action                                                                           | Status   |
| ---------- | ---------------- | --------------- | ---------------- | ----------------------------- | --------------------------------------------- | --------------------------------------------------------------------------------------------------- | -------- |
| CH-REG-001 | `sign_up.method` | `v1`            | `v2`             | Add approved method `passkey` | Registration report, Exploration, QA taxonomy | Update allowed values, dual-test, review cardinality, update report description rồi approve release | Proposed |

Thêm một method mới không chỉ là code change. Nó thay đổi parameter contract, QA case, cách diễn giải report và có thể ảnh hưởng key-event hoặc advertising audience. Dùng schema version mới và cập nhật consumer bị ảnh hưởng trước khi release value mới.

## Common Anti-Patterns

| Anti-pattern                                             | Vì sao sai                                         | Cách tốt hơn                                                        |
| -------------------------------------------------------- | -------------------------------------------------- | ------------------------------------------------------------------- |
| Track mọi click mà không có decision                     | Tạo noise và maintenance cost.                     | Bắt đầu từ decision và measurable outcome.                          |
| Fire success event khi button được click                 | Click không chứng minh business success.           | Dùng application/backend state đã được xác nhận.                    |
| Encode value vào event name                              | Làm phân mảnh reporting và schema không ổn định.   | Dùng một event với controlled parameters.                           |
| Gửi raw form fields hoặc tạo dimension cho mọi parameter | Tạo privacy risk, tốn quota và làm report clutter. | Chỉ gửi field đã được phê duyệt và register field cần cho analysis. |
| Duplicate Enhanced Measurement bằng GTM                  | Một interaction bị đếm nhiều lần.                  | Kiểm tra automatic collection trước custom tagging.                 |
| Tùy tiện rename event                                    | Làm hỏng report, key event, audience và baseline.  | Dùng versioned migration và consumer-impact review.                 |

## Official References

- [About events](https://support.google.com/analytics/answer/9322688)
- [Enhanced measurement events](https://support.google.com/analytics/answer/9216061?hl=en)
- [Recommended events](https://support.google.com/analytics/answer/9267735)
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
