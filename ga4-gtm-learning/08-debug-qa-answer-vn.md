# 08 — Quy trình Debug và Analytics QA

## Mục đích

Debug cần xác định lớp đầu tiên bị lỗi, không chỉ xác nhận một tag hiển thị là đã fired. Analytics QA kiểm tra occurrence, payload, routing, consent, privacy và behavior trong reporting.

Dùng pipeline sau:

```text
User interaction
  → application state
  → Data Layer message
  → GTM event/variable evaluation
  → tag decision và consent
  → GA4 network request
  → GA4 DebugView/Realtime
  → processed report/Exploration
```

Nếu event không xuất hiện trong DebugView, nguyên nhân có thể nằm ở application, Data Layer, trigger, variable, tag, consent, browser/network, Measurement ID, property, debug mode hoặc processing. Hãy kiểm tra từng boundary theo đúng thứ tự.

## QA Strategy

Analytics QA là quy trình nhiều layer, dựa trên risk. Không xem “tag đã fired” là tiêu chí để release. Một test chỉ pass khi business state, collection payload, consent behavior, destination và reporting outcome liên quan đã được chứng minh hoặc limitation do processing/platform đã được ghi rõ.

Dùng assertion chain này cho mỗi event quan trọng:

```text
Business state là đúng
  → Data Layer signal được emit một lần
  → GTM evaluate đúng trigger và variable
  → Consent cho phép behavior dự kiến
  → Network request có destination và payload đúng
  → DebugView/Realtime nhận activity dự kiến
  → Processed reporting data hỗ trợ interpretation dự kiến
```

### Các test level và thứ tự thực hiện

| Level                             | Mục đích                                                                                        | Khi nào chạy                                                                 | Evidence hoặc điều kiện kết thúc                                                     |
| --------------------------------- | ----------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------- | ------------------------------------------------------------------------------------ |
| L0 — Contract review              | Xác nhận event, parameter, count, source, destination, consent và negative case                 | Trước khi mở debugger                                                        | Measurement Plan/Event Contract được tham chiếu và precondition rõ ràng              |
| L1 — Isolated event               | Chứng minh một event mà không bị nhiễu bởi navigation/action khác                               | Với mọi event mới hoặc thay đổi                                              | Một controlled action tạo đúng Data Layer và request result                          |
| L2 — Journey flow                 | Chứng minh event order, business-state transition và denominator                                | Với flow nhiều event như registration hoặc checkout                          | Journey tạo đúng sequence, không thiếu hoặc duplicate business event                 |
| L3 — Boundary và privacy          | Chứng minh invalid input, server failure, retry, refresh, consent, PII, routing và browser case | Mọi material release; độ sâu tùy risk                                        | Negative case đã biết bị block hoặc behavior đúng tài liệu                           |
| L4 — Regression                   | Xác nhận event và destination cũ không bị thay đổi                                              | Khi shared GTM, Google tag, consent, variable hoặc application code thay đổi | Baseline event vẫn đúng, không có duplicate/misroute mới                             |
| L5 — Processed data               | Xác nhận field availability, scope, count, filter và interpretation sau processing              | Khi kết quả dùng cho report, key event, audience hoặc business decision      | Processed result được reconcile hoặc discrepancy được ghi nhận                       |
| L6 — Production smoke/observation | Xác nhận version đã publish và theo dõi tác động ban đầu                                        | Sau production activation                                                    | Release version, smoke evidence, observation window và follow-up owner được ghi nhận |

Bắt đầu từ L0 và chỉ đi đến mức phù hợp với risk. Copy change nhỏ có thể chỉ cần L0 và L1; purchase hoặc consent flow mới thường cần toàn bộ sequence.

## Mỗi công cụ chứng minh điều gì?

| Tool/layer                  | Cần inspect                                                        | Nó chứng minh điều gì?                         | Nó chưa chứng minh điều gì?                                  |
| --------------------------- | ------------------------------------------------------------------ | ---------------------------------------------- | ------------------------------------------------------------ |
| Application                 | Authoritative state và event call                                  | Product có thể expose signal cần đo            | GTM đã nhận hoặc routing signal đó                           |
| Data Layer                  | Event name, value, type, order, count                              | GTM có message để evaluate                     | Tag đã fired hoặc request đã được gửi                        |
| GTM Preview/Tag Assistant   | Event timeline, variables, fired/not-fired tags, consent, order    | Container trong preview đã evaluate draft đúng | Production đang dùng cùng version hoặc GA4 đã accept request |
| Browser Network             | Request URL, Measurement ID, event name, parameters, count, status | Browser đã cố gắng gửi collection request đúng | GA4 đã process event vào mọi report                          |
| GA4 DebugView               | Debug device, event, parameters, timing                            | GA4 đã nhận một debuggable event               | Normal report đã đầy đủ hoặc attribution đã final            |
| Realtime                    | User/event hiện tại và collection cơ bản                           | Property đang nhận activity gần đây            | Historical processing và final attribution                   |
| Standard report/Exploration | Processed dimensions, metrics, filters, scope, freshness           | Data có thể dùng cho analysis đã định nghĩa    | Implementation đúng nếu chưa có upstream evidence            |

GTM Preview và debug mode cho phép tester inspect tag nào fired, tag nào không fired, thứ tự event và dữ liệu được previewed container xử lý. Xem [Preview and debug containers](https://support.google.com/tagmanager/answer/6107056). Tag Assistant cũng cung cấp diagnostic cho Google tag bị thiếu, duplicate hoặc cấu hình sai; xem [Tag Assistant](https://support.google.com/tagmanager/answer/13355721).

## Test Setup và Data Safety

Ghi nhận trước khi test:

| Field                           | Ví dụ                                         |
| ------------------------------- | --------------------------------------------- |
| Test ID                         | TC-REG-001                                    |
| Environment                     | QA/staging, không dùng production             |
| URL và application version      | `[sanitized URL]`, `[commit/build]`           |
| GTM container/workspace/version | `[container]`, `[workspace]`, `[version]`     |
| GA4 property và web stream      | `[property]`, `[stream]`                      |
| Measurement ID                  | `G-XXXXXXX` hoặc evidence đã sanitize         |
| Browser/device                  | Chrome, desktop, version/date                 |
| Account/data                    | Synthetic account và safe values              |
| Consent state                   | Granted, denied hoặc unresolved theo category |
| Expected event                  | `sign_up` một lần với `method` và `form_id`   |
| Tester/reviewer                 | `[name]`                                      |

Dùng synthetic value. Không đưa tên thật, email, số điện thoại, địa chỉ, authentication token, payment data hoặc free-form user content vào test hay evidence.

### “Reset state liên quan” nghĩa là gì?

Kết quả test phụ thuộc vào context tại thời điểm bắt đầu. Consent đã lưu có thể block request, một lần registration thành công trước đó có thể làm application suppress duplicate event, còn Preview session cũ có thể kết nối tới sai GTM workspace. Vì vậy, trước mỗi attempt, hãy tự hỏi hai câu:

1. **State nào phải ở trạng thái mới cho test này?** Chỉ reset state đó.
2. **State nào phải được giữ nguyên cho test này?** Giữ nguyên và ghi nhận nó.

Không tự động xoá toàn bộ cookie, storage, application data và browser state. Việc đó có thể phá hỏng đúng scenario cần test, ví dụ returning-user consent, refresh behavior, retry behavior hoặc multi-tab behavior. Hãy ghi cách reset nếu tester khác cần reproduce kết quả.

Ví dụ:

- **New-visitor happy path:** dùng browser profile có kiểm soát, connect lại đúng GTM Preview session, tạo synthetic account, đặt consent state theo plan và bật **Preserve log** trước action.
- **Refresh hoặc duplicate test:** giữ account và consent state liên quan, reset application về pre-action state đã document, xoá Network log rồi chỉ thực hiện refresh hoặc retry cần kiểm tra.
- **Returning-user consent test:** không xoá cookie hoặc local storage; ghi consent state đang lưu và xác nhận page xử lý đúng như returning user.

Một reset record đầy đủ có thể rất ngắn: `browser profile mới; GTM Preview connect lại QA workspace v42; analytics consent=denied; synthetic account; Network Preserve log=on`.

| State                            | Vì sao có thể làm thay đổi kết quả                                             | Cách reset hoặc kiểm soát                                                |
| -------------------------------- | ------------------------------------------------------------------------------ | ------------------------------------------------------------------------ |
| GTM Preview session              | Workspace/version đang preview có tính session-specific                        | Connect lại đúng container, workspace, environment và version            |
| Cookie, local storage và consent | Consent hoặc identifier đã lưu có thể block, allow hoặc deduplicate collection | Dùng browser profile có kiểm soát và ghi initial consent state           |
| Application journey state        | Success, retry hoặc cached form trước đó có thể suppress hoặc duplicate event  | Dùng synthetic account mới hoặc reset path đã document                   |
| Network log                      | Navigation có thể làm mất request trước đó khỏi màn hình                       | Bật Preserve log trước redirect hoặc SPA navigation                      |
| Browser extension/privacy tool   | Có thể block request hoặc thay đổi storage                                     | Ghi clean profile hay không và repeat bằng tool đã approve nếu liên quan |

Không trộn nhiều test case trong cùng browser state, trừ khi test đó chủ đích kiểm tra returning user, stored consent, multi-tab hoặc retry behavior.

## Quy trình Debug end to end

### Bước 1 — Xác nhận behavior mong đợi

Đọc Measurement Plan trước khi mở debugger. Ghi rõ:

```text
Action: Hoàn thành registration hợp lệ
Business moment: Server xác nhận account creation
Expected Data Layer event: một sign_up
Expected request count: một
Required parameters: method, form_id
Expected destination: chỉ QA stream
Expected consent: analytics behavior đã được phê duyệt
Negative cases: invalid input, server failure, double submit, refresh
```

Không debug một implementation khi expectation chưa được định nghĩa.

### Bước 2 — Mở đúng preview session

1. Mở đúng GTM container và workspace.
2. Start Preview và connect tới QA URL.
3. Xác nhận page báo đúng container đã connected.
4. Kiểm tra environment, container ID và version đang preview.
5. Giữ network log nếu có navigation hoặc redirect.
6. Dùng browser state sạch hoặc được kiểm soát khi cookie, consent hoặc SPA state có thể ảnh hưởng kết quả.

Preview interface phụ thuộc vào session. Không mặc định rằng colleague đang xem cùng draft nếu chưa tạo shared preview session.

### Bước 3 — Thực hiện một action

Thực hiện đúng một test action rồi dừng. Không click thêm trước khi capture event liên quan. Chỉ lặp lại sau khi reset test state.

Kiểm tra:

- Application có đạt business state mong đợi không?
- Data Layer event có xảy ra một lần không?
- Event có xảy ra đúng thời điểm không?
- Event name, parameter name, type và value có đúng không?
- Optional value có được omit đúng thiết kế không?
- Có dữ liệu bị cấm nào xuất hiện không?

Ví dụ Data Layer message:

```javascript
{
  event: 'sign_up',
  method: 'email',
  form_id: 'registration'
}
```

Nếu business outcome chưa xảy ra, không được xem button click là success event.

### Bước 4 — Inspect GTM evaluation

Tại đúng event trong Tag Assistant:

1. Xác nhận Custom Event name khớp contract, kể cả case.
2. Inspect mọi variable được trigger và tag sử dụng.
3. Xác nhận trigger mong đợi đã match.
4. Xác nhận tag cần fire đã fire một lần.
5. Xác nhận tag không cần fire đang ở not-fired state và hiểu lý do.
6. Kiểm tra blocking trigger, exception, consent requirement, additional consent, sequencing và tag setting.
7. Tìm tag khác có thể gửi cùng event.
8. Xác định canonical collection source của event. Kiểm tra event business này có thể đồng thời được gửi bởi hardcoded `gtag`, Enhanced Measurement, CMS/plugin, server-side tagging, Measurement Protocol hoặc GTM tag khác hay không.
9. Xác nhận Google tag và GA4 Event tag trỏ tới destination đúng.

Một business occurrence nên có một canonical collection source. Nếu nhiều source là chủ ý, phải document ownership, cơ chế deduplication và expected request count. Một source thứ hai không được document là duplicate-collection defect, dù event name và parameter trông vẫn đúng.

Nhớ rằng:

```text
Nhiều firing trigger trên một tag → điều kiện thay thế (OR)
Nhiều condition trong một trigger → tất cả phải match (AND)
Exception/blocking condition match → tag bị block
```

“Tag Fired” chỉ có nghĩa GTM đã chọn tag để execute. Nó không chứng minh network request thành công hoặc GA4 đã process payload đúng.

### Bước 5 — Inspect network request

Dùng Browser Developer Tools và filter GA4 collection request, thường có chứa `collect`. Hãy inspect request thật thay vì chỉ dựa vào GTM UI.

Xác nhận:

- request count bằng expected count;
- destination Measurement ID là QA/test ID;
- event name đúng;
- required parameter có mặt với value và type đúng;
- không có field thừa hoặc bị cấm;
- consent signal khớp approved design;
- request không bị extension, browser privacy setting, CSP hoặc network error block;
- redirect/navigation không làm mất linker hoặc campaign information cần thiết.

Không copy live identifier hoặc sensitive payload vào public ticket. Redact identifier và value nhưng giữ đủ context để chứng minh kết quả.

### Bước 6 — Kiểm tra GA4 DebugView

DebugView hiển thị event và user property từ debug-enabled device gần như theo thời gian thực. Với website, Tag Assistant hoặc GTM Preview có thể bật device debug signal; cũng có thể cấu hình `debug_mode` cho test scope đã được phê duyệt. Chọn đúng property và debug device, sau đó xác nhận event xuất hiện một lần với parameter mong đợi.

Google lưu ý event có thể không xuất hiện trong DebugView khi client-side privacy control hoặc consent mode ngăn Analytics storage/collection. DebugView và Realtime cũng có attribution behavior giới hạn, vì vậy không dùng chúng làm nguồn cuối cùng cho historical acquisition conclusion. Xem [Monitor events in DebugView](https://support.google.com/analytics/answer/7201382).

Sau test, bảo đảm debug traffic được tách hoặc filter theo data-quality design đã phê duyệt. Không để `debug_mode` áp dụng cho toàn bộ user trong production.

### Bước 7 — Validate processed reporting data

Sau khi hết processing window thông thường:

- chọn đúng property, stream, timezone và date range;
- xác nhận event xuất hiện trong Events report;
- xác nhận custom definition chỉ available sau registration/processing delay đã document;
- kiểm tra metric và dimension scope;
- so sánh count dự kiến với test evidence;
- kiểm tra `(other)`, thresholding, sampling, field không tương thích và recent date chưa hoàn chỉnh;
- ghi nhận discrepancy thay vì âm thầm đổi conclusion.

DebugView là collection diagnostic, không phải bằng chứng rằng standard report đã populate ngay lập tức.

## Decision tree cho lỗi phổ biến

```text
Expected event bị thiếu
  ├─ Business state đã xảy ra chưa?
  │   ├─ Chưa → sửa test precondition/application flow
  │   └─ Rồi
  ├─ Data Layer event có được push một lần không?
  │   ├─ Không → sửa application/Data Layer contract
  │   └─ Có
  ├─ GTM custom event name/variable có khớp không?
  │   ├─ Không → sửa trigger/variable mapping
  │   └─ Có
  ├─ Tag đã fire và consent có cho phép không?
  │   ├─ Không → inspect trigger, exception, consent, sequencing
  │   └─ Có
  ├─ Network request có tồn tại và destination có đúng không?
  │   ├─ Không → inspect tag config, Google tag, blocker, CSP, network
  │   └─ Có
  ├─ DebugView device/property/debug mode có đúng không?
  │   ├─ Không → sửa debug selection hoặc test configuration
  │   └─ Có
  └─ Processed report bị thiếu?
      → chờ processing, sau đó kiểm tra registration, scope, filter, threshold và date range
```

### Failure diagnosis matrix

| Symptom                             | First checks                                                  | Likely layer           | Evidence cần capture                    |
| ----------------------------------- | ------------------------------------------------------------- | ---------------------- | --------------------------------------- |
| Không có Data Layer event           | Business success, app callback, event push                    | Application/Data Layer | Console/app log và test state           |
| Data Layer event có, tag không fire | Exact event name, trigger filter, variable, exception         | GTM                    | Tag Assistant event và not-fired reason |
| Tag fire nhưng không có request     | Google tag/config, consent, blocker, tag error                | GTM/browser            | Tag detail, console, network log        |
| Request sai ID                      | Environment lookup, Google tag, stream selection              | Routing                | Redacted request URL/payload            |
| Request sai parameter               | DLV path, variable timing, type conversion                    | Data contract/GTM      | Data Layer và request comparison        |
| Một action tạo hai request          | Duplicate push, overlapping tag, SPA remount, retry           | Application/GTM        | Timeline và request count               |
| Request có nhưng DebugView trống    | Sai property/device, debug mode, consent/privacy, delay       | GA4/debug setup        | Property, device, consent, timestamp    |
| DebugView đúng nhưng report sai     | Processing, custom-definition delay, filter, scope, threshold | GA4 reporting          | Report setting và date range            |
| QA data vào production              | Environment routing hoặc sai stream                           | Release/routing        | Request destination, version, hostname  |

## Required Test Matrix

Dùng test ID ổn định qua các release.

| ID    | Case               | Action                                                       | Expected                                                                    |
| ----- | ------------------ | ------------------------------------------------------------ | --------------------------------------------------------------------------- |
| TC-01 | Happy path         | Hoàn thành flow hợp lệ                                       | Một canonical event, mọi required parameter hợp lệ                          |
| TC-02 | Validation failure | Submit input không hợp lệ                                    | Không có success/key event                                                  |
| TC-03 | Server failure     | Tạo response lỗi                                             | Không có success/key event                                                  |
| TC-04 | Double submit      | Click/submit nhanh                                           | Một event cho một business occurrence                                       |
| TC-05 | Refresh/back       | Refresh hoặc quay lại confirmation                           | Không duplicate ngoài ý muốn                                                |
| TC-06 | SPA route          | Vào, rời và quay lại route                                   | Page/route event theo plan; không duplicate business event                  |
| TC-07 | Missing optional   | Bỏ optional value                                            | Omit hoặc fallback đúng document                                            |
| TC-08 | Missing required   | Ép required value bị thiếu                                   | QA fail; không có success payload gây hiểu nhầm                             |
| TC-09 | Consent denied     | Deny consent liên quan                                       | Behavior đúng consent design; không có prohibited request                   |
| TC-10 | Consent granted    | Grant consent liên quan                                      | Collection bắt đầu/update đúng                                              |
| TC-11 | Routing            | Chạy trên QA hostname                                        | Request chỉ đi tới QA destination                                           |
| TC-12 | Privacy            | Inspect Data Layer và request                                | Không PII, secret, raw form value hoặc unsafe URL                           |
| TC-13 | Browser            | Test browser/device được hỗ trợ                              | Không có khác biệt implementation đáng kể                                   |
| TC-14 | Regression         | Chạy journey liên quan                                       | Event cũ vẫn đúng và không duplicate                                        |
| TC-15 | Collection source  | Thực hiện một action và kiểm tra mọi collection path đã biết | Một canonical source, hoặc deduplication đã document với request count đúng |

## Consent Debugging

Consent là runtime condition và là một debugging dimension. Tối thiểu phải test:

1. default state trước khi user tương tác với banner;
2. analytics denied;
3. analytics granted;
4. advertising denied/granted khi liên quan;
5. consent update sau khi page load;
6. navigation và SPA transition sau mỗi state;
7. returning user có stored consent;
8. CMP state unresolved hoặc failed.

Với mỗi case, ghi nhận:

- consent default và update timing;
- tag nào được expected fire hoặc remain blocked;
- request có được gửi không và mang signal nào;
- behavior có khớp approved privacy design không;
- DebugView visibility có được expected trong state đó không.

Không tạo ad hoc trigger logic để bypass consent model đã phê duyệt. Nếu tag bị block ngoài dự kiến, inspect consent requirement và additional consent check trong Tag Assistant. Xem [unblock Google tags when using consent mode](https://support.google.com/tagmanager/answer/12962079).

## Evidence Template

| Test ID | Layer             | Expected                                            | Actual     | Evidence                    | Result    | Defect |
| ------- | ----------------- | --------------------------------------------------- | ---------- | --------------------------- | --------- | ------ |
| TC-01   | Application       | Confirmed success                                   | `[actual]` | `[link]`                    | Pass/Fail | `[ID]` |
| TC-01   | Data Layer        | Một `sign_up` với value hợp lệ                      | `[actual]` | `[screenshot/log]`          | Pass/Fail | `[ID]` |
| TC-01   | GTM               | Tag đúng fire một lần                               | `[actual]` | `[Tag Assistant]`           | Pass/Fail | `[ID]` |
| TC-01   | Collection source | Một canonical source hoặc deduplication đã document | `[actual]` | `[source map/timeline]`     | Pass/Fail | `[ID]` |
| TC-01   | Network           | Một request tới QA ID                               | `[actual]` | `[redacted HAR/screenshot]` | Pass/Fail | `[ID]` |
| TC-01   | Consent           | Approved state                                      | `[actual]` | `[screenshot]`              | Pass/Fail | `[ID]` |
| TC-01   | DebugView         | Event hiển thị một lần                              | `[actual]` | `[screenshot]`              | Pass/Fail | `[ID]` |
| TC-01   | Report            | Processed field/count hợp lý                        | `[actual]` | `[report link/screenshot]`  | Pass/Fail | `[ID]` |

Evidence phải xác định date, environment, version, property/stream, tester, browser, result và known limitation. Redact sensitive value và không lưu credential trong evidence.

## Ví dụ hoàn chỉnh — QA Report cho Registration Journey

Đây là một QA report mẫu ở non-production, dựa trên contract của Registration Journey trong [Section 07](07-measurement-plan-answer.md). Nó cho thấy một test run kết nối application outcome, Data Layer, GTM, collection source, consent, network request, DebugView và processed reporting như thế nào. Thay sample ID và evidence placeholder bằng giá trị của project; đây không phải production evidence thực tế.

### Context của test run

| Field                        | Giá trị ghi nhận                                                                                                                  |
| ---------------------------- | --------------------------------------------------------------------------------------------------------------------------------- |
| QA report ID                 | `QA-REG-001`                                                                                                                      |
| Measurement Plan             | `MP-REG-001 / v1.0`                                                                                                               |
| Journey                      | `J-REG-001` — Registration                                                                                                        |
| Environment                  | Chỉ QA/staging                                                                                                                    |
| Application/GTM/GA4          | `[build]` / `[GTM version]` / `[QA property và stream]`                                                                           |
| Browser và data              | Clean browser profile; synthetic account; safe test value                                                                         |
| Consent state                | Analytics consent granted; test này không cần advertising consent                                                                 |
| Canonical source             | Backend xác nhận account creation → application Data Layer → GTM → GA4                                                            |
| Duplicate source đã kiểm tra | Không có hardcoded `gtag`, Enhanced Measurement, CMS/plugin, server-side, Measurement Protocol hoặc GTM tag thứ hai cho `sign_up` |
| Expected result              | Một `sign_up` với `method=email` và `form_id=registration`                                                                        |

### Kết quả đã thực hiện

| Test ID      | Scenario                         | Kết quả quan sát                                                      | Evidence                                 | Status |
| ------------ | -------------------------------- | --------------------------------------------------------------------- | ---------------------------------------- | ------ |
| `TC-REG-001` | Form mở và sẵn sàng              | Một `registration_start`; không có PII                                | `[application + Data Layer evidence]`    | Pass   |
| `TC-REG-002` | Input không hợp lệ               | `registration_error` với `error_type=validation`; không có `sign_up`  | `[Preview + request evidence]`           | Pass   |
| `TC-REG-003` | Server failure                   | `registration_error` có `error_type=server_error`; không có `sign_up` | `[application + request evidence]`       | Pass   |
| `TC-REG-004` | Account creation được xác nhận   | Backend success; một `sign_up` với value đã approve                   | `[application + Data Layer + Preview]`   | Pass   |
| `TC-REG-005` | Rapid double submit/retry        | Một confirmed account; một `sign_up` request                          | `[timeline + network evidence]`          | Pass   |
| `TC-REG-006` | Refresh/back/SPA remount         | Không duplicate `sign_up`; không có start ngoài ý muốn                | `[navigation timeline]`                  | Pass   |
| `TC-REG-007` | Consent denied                   | Behavior khớp approved consent design; không có prohibited data       | `[consent + storage + network evidence]` | Pass   |
| `TC-REG-008` | Sai environment hoặc destination | Chỉ có QA Measurement ID; không dùng production destination           | `[redacted request evidence]`            | Pass   |
| `TC-REG-009` | User-ID ngoài scope              | Không có email, phone, raw account ID hoặc User-ID chưa approve       | `[redacted payload evidence]`            | Pass   |
| `TC-REG-010` | Collection source ownership      | Chỉ canonical GTM path gửi `sign_up`; không tìm thấy source thứ hai   | `[source map + request timeline]`        | Pass   |

### Kết luận theo từng layer

| Layer               | Kết luận                                                                                                     |
| ------------------- | ------------------------------------------------------------------------------------------------------------ |
| Application         | Account creation đã được backend xác nhận trước khi gửi `sign_up`.                                           |
| Data Layer          | Một message `sign_up` self-contained với name, value và type đã approve.                                     |
| GTM                 | Custom Event trigger và GA4 Event tag đúng đã fire một lần; không có consumer trùng match.                   |
| Consent             | Behavior quan sát được khớp approved analytics-consent design.                                               |
| Collection source   | Một canonical client-side path; không có duplicate source chưa được document.                                |
| Network             | Một request tới QA Measurement ID, chỉ chứa parameter đã approve.                                            |
| DebugView           | Một `sign_up` có thể debug với `method` và `form_id`.                                                        |
| Processed reporting | Chờ đến documented GA4 processing window; đây là reporting follow-up, không phải runtime collection failure. |

**Decision:** Runtime collection QA đã pass. Report đã sẵn sàng cho production review, nhưng phải hoàn tất processed-report check trước khi kết luận reporting validation nếu Section 09 nằm trong scope. Không mở defect; follow-up là kiểm tra processed data `R-REG-002` trước `[date]`.

## Bộ Template Debug/QA

Các template này biến một debug session thành record có thể lặp lại. Dùng Evidence Template để chứng minh theo từng layer, Debug Session Record để ghi context tổng thể, và Defect and Retest Record khi expected result không đạt. Giữ release approval và monitoring record trong [Release & Monitoring](10-release-monitoring-answer.md).

| Template                 | Mục đích                                                                                       | Dùng khi                                            |
| ------------------------ | ---------------------------------------------------------------------------------------------- | --------------------------------------------------- |
| Required Test Matrix     | Xác định các scenario bắt buộc phải test.                                                      | Lập coverage trước implementation hoặc release.     |
| Evidence Template        | Kết nối một test case qua application, Data Layer, GTM, Network, consent, DebugView và report. | Ghi bằng chứng cho kết quả Pass/Fail.               |
| Debug Session Record     | Ghi test context, expected behavior, các layer đã kiểm tra và conclusion.                      | Chạy focused investigation hoặc release smoke test. |
| Defect and Retest Record | Ghi first failing layer, impact, containment, fix và retest result.                            | Khi test fail hoặc production behavior đáng nghi.   |

### Debug session record template

**Mục đích:** Dùng một record cho một debug session tập trung. Nó ngăn evidence bị mất property, version, consent state hoặc expected request count làm cho kết quả có ý nghĩa.

```text
Debug session ID:
Test case/journey ID:
Business question hoặc release:
Environment và URL:
Application/build version:
GTM container/workspace/version:
GA4 property/stream/Measurement ID:
Browser/device:
Consent state:
Expected business moment:
Expected Data Layer event và count:
Canonical collection source và duplicate source đã kiểm tra:
Expected tag/request và destination:
Các layer đã kiểm tra:
Observed result:
First failing layer:
Evidence links:
Tester/date:
Reviewer/status:
Follow-up defect hoặc decision:
```

### Defect and retest record template

**Mục đích:** Dùng record này để biến test fail thành defect có thể xử lý. Nó giữ original evidence và affected period đi cùng fix thay vì phụ thuộc vào một tin nhắn không chính thức.

| Field                     | Cần ghi nhận                                                               |
| ------------------------- | -------------------------------------------------------------------------- |
| Defect ID và severity     | ID ổn định và phân loại Critical/High/Medium/Low.                          |
| First failing layer       | Application, Data Layer, GTM, Network, consent, GA4 setup hoặc reporting.  |
| Expected versus actual    | Contract expectation và behavior quan sát được.                            |
| Reproduction              | Test ID, URL, browser/device, consent state, steps và frequency.           |
| Impact và affected period | Event/user/report/environment bị ảnh hưởng và first/last known time.       |
| Evidence                  | Preview, Network, DebugView, report hoặc application evidence đã sanitize. |
| Containment               | Tạm block, sửa routing, quyết định filter hoặc monitoring action.          |
| Root cause và fix         | Cause đã xác nhận, change/ticket, owner và target version.                 |
| Retest result             | Test ID, date, evidence, residual impact và reviewer decision.             |

Không đóng defect chỉ vì tag hiện ở **Tags Fired**. Chỉ đóng sau khi downstream evidence và affected-period assessment liên quan đã được ghi nhận.

## Defect Triage và Retest Protocol

Khi test fail, thực hiện theo thứ tự sau:

1. Giữ nguyên failing state và lưu evidence gốc; không lập tức refresh hoặc thay đổi nhiều setting cùng lúc.
2. Xác định **first failing layer** trong assertion chain: application, Data Layer, GTM, consent, browser/network, GA4 setup hoặc processed reporting.
3. Ghi impact, environment/period bị ảnh hưởng, expected so với actual behavior, và phân loại lỗi là missing, duplicate, misnamed, mistimed, blocked, misrouted hoặc privacy-related.
4. Thực hiện containment khi production data hoặc privacy có risk: block tag, sửa routing, tạm dừng change không an toàn hoặc mở incident.
5. Sửa nhỏ nhất tại first failing layer, sau đó chạy lại cùng test case với timestamp của attempt mới.
6. So sánh retest với evidence gốc và chạy các regression case bị ảnh hưởng.
7. Chỉ đóng defect sau khi downstream evidence và affected-period assessment liên quan đã được ghi nhận.

First failing layer là diagnosis anchor. Các layer phía sau có thể cũng trông như bị lỗi, nhưng sửa symptom downstream có thể che mất nguyên nhân thật.

## Defect Severity

| Severity | Definition                                                             | Ví dụ                                                       |
| -------- | ---------------------------------------------------------------------- | ----------------------------------------------------------- |
| Critical | Privacy/security breach hoặc production corruption trên diện rộng      | Gửi PII; production route tới test/unauthorized destination |
| High     | Business outcome quan trọng bị thiếu, duplicate hoặc sai đáng kể       | Purchase bị đếm đôi; sign-up thiếu trên diện rộng           |
| Medium   | Context quan trọng sai hoặc scenario/browser bị ảnh hưởng đáng kể      | Sai method; SPA route bỏ sót event                          |
| Low      | Documentation hoặc maintainability issue với data impact hiện tại thấp | Thiếu description; naming không nhất quán                   |

Severity cần xét impact, volume bị ảnh hưởng, duration, privacy risk, detectability và recoverability. PII leak volume thấp vẫn là high/critical vì privacy risk không chỉ phụ thuộc event volume.

## Release Decision Matrix

Dùng matrix này thay vì xem checklist như một approval tự động. Release owner ghi decision, evidence và exception trong release record.

| Decision point              | Evidence tối thiểu                                                                                                       | Quy tắc quyết định                                                            |
| --------------------------- | ------------------------------------------------------------------------------------------------------------------------ | ----------------------------------------------------------------------------- |
| Ready for QA implementation | Contract, test environment, expected payload và test ID đã rõ                                                            | Chỉ bắt đầu khi target property/stream và GTM version không mơ hồ             |
| Ready for production review | Positive/negative/duplicate/consent/privacy/routing case pass; không còn Critical/High defect chưa xử lý                 | Chỉ tiếp tục khi risk còn lại đã được ghi nhận và accountable owner chấp nhận |
| Go to production            | Published version, destination, smoke test, consent behavior và rollback/containment owner đã rõ                         | Activate change nhỏ nhất đã approve trong đúng environment                    |
| Hold release                | Business moment chưa rõ, chưa biết first failing layer, destination sai, có PII hoặc duplicate key event chưa giải quyết | Không publish; quay lại contract hoặc defect liên quan                        |
| Roll back hoặc contain      | Production collection missing, duplicate, misroute, privacy-unsafe hoặc làm thay đổi key metric đáng kể                  | Dùng containment/rollback path đã approve và ghi affected dates/scope         |
| Accept exception            | Issue có phạm vi giới hạn, không blocking và có mitigation, owner, due date, reviewer                                    | Ghi exception rõ ràng; không ẩn trong test comment                            |

“Ready for production review” không đồng nghĩa với “Go to production”. Production decision còn phụ thuộc change scope, risk, approval và khả năng observe/contain kết quả.

## Post-Release Observation Plan

Sau activation, theo dõi observation window đã thống nhất thay vì chỉ dựa vào smoke test ban đầu. Ghi các thông tin sau trong release hoặc monitoring record:

| Observation                   | So sánh điều gì                                                            | Vì sao quan trọng                                   |
| ----------------------------- | -------------------------------------------------------------------------- | --------------------------------------------------- |
| Destination và request count  | Published version, Measurement ID, event count trên controlled action      | Phát hiện sai environment routing và duplicate      |
| Event volume và missingness   | Current period so với baseline tương đương, kèm release time/timezone      | Phát hiện collection loss hoặc spike bất thường     |
| Parameter quality             | Required-field coverage, allowed-value distribution, invalid/unknown value | Phát hiện schema drift và application mapping error |
| Key event và business outcome | Confirmed business source so với GA4 result sau processing                 | Ngăn kết luận business/advertising quá sớm          |
| Consent/privacy behavior      | Denied/granted path và incident signal                                     | Phát hiện collection không an toàn sau activation   |
| Report và export              | Freshness, scope, filter, `(other)`, thresholding, sampling và discrepancy | Phân biệt reporting issue với collection defect     |

Gán owner, observation end date, escalation threshold và next action. Liên kết kết quả tới [Release & Monitoring](10-release-monitoring-answer.md); không để production monitoring chỉ tồn tại trong một tin nhắn follow-up.

## Tài liệu tham khảo

- [Preview and debug GTM containers](https://support.google.com/tagmanager/answer/6107056)
- [Tag Assistant](https://support.google.com/tagmanager/answer/13355721)
- [Monitor events in DebugView](https://support.google.com/analytics/answer/7201382)
- [Troubleshoot tag setup on your website](https://support.google.com/analytics/answer/9311124)
- [Unblock Google tags when using consent mode](https://support.google.com/tagmanager/answer/12962079)
- [Consent mode implementation](https://developers.google.com/tag-platform/security/guides/consent)
- [Avoid sending personally identifiable information](https://support.google.com/analytics/answer/6366371)
- [GA4 data freshness](https://support.google.com/analytics/answer/11198161)
