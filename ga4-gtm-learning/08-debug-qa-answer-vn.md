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

## Mỗi công cụ chứng minh điều gì?

| Tool/layer | Cần inspect | Nó chứng minh điều gì? | Nó chưa chứng minh điều gì? |
| --- | --- | --- | --- |
| Application | Authoritative state và event call | Product có thể expose signal cần đo | GTM đã nhận hoặc routing signal đó |
| Data Layer | Event name, value, type, order, count | GTM có message để evaluate | Tag đã fired hoặc request đã được gửi |
| GTM Preview/Tag Assistant | Event timeline, variables, fired/not-fired tags, consent, order | Container trong preview đã evaluate draft đúng | Production đang dùng cùng version hoặc GA4 đã accept request |
| Browser Network | Request URL, Measurement ID, event name, parameters, count, status | Browser đã cố gắng gửi collection request đúng | GA4 đã process event vào mọi report |
| GA4 DebugView | Debug device, event, parameters, timing | GA4 đã nhận một debuggable event | Normal report đã đầy đủ hoặc attribution đã final |
| Realtime | User/event hiện tại và collection cơ bản | Property đang nhận activity gần đây | Historical processing và final attribution |
| Standard report/Exploration | Processed dimensions, metrics, filters, scope, freshness | Data có thể dùng cho analysis đã định nghĩa | Implementation đúng nếu chưa có upstream evidence |

GTM Preview và debug mode cho phép tester inspect tag nào fired, tag nào không fired, thứ tự event và dữ liệu được previewed container xử lý. Xem [Preview and debug containers](https://support.google.com/tagmanager/answer/6107056). Tag Assistant cũng cung cấp diagnostic cho Google tag bị thiếu, duplicate hoặc cấu hình sai; xem [Tag Assistant](https://support.google.com/tagmanager/answer/13355721).

## Chuẩn bị test an toàn

Ghi nhận trước khi test:

| Field | Ví dụ |
| --- | --- |
| Test ID | TC-REG-001 |
| Environment | QA/staging, không dùng production |
| URL và application version | `[sanitized URL]`, `[commit/build]` |
| GTM container/workspace/version | `[container]`, `[workspace]`, `[version]` |
| GA4 property và web stream | `[property]`, `[stream]` |
| Measurement ID | `G-XXXXXXX` hoặc evidence đã sanitize |
| Browser/device | Chrome, desktop, version/date |
| Account/data | Synthetic account và safe values |
| Consent state | Granted, denied hoặc unresolved theo category |
| Expected event | `sign_up` một lần với `method` và `form_id` |
| Tester/reviewer | `[name]` |

Dùng synthetic value. Không đưa tên thật, email, số điện thoại, địa chỉ, authentication token, payment data hoặc free-form user content vào test hay evidence.

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
8. Xác nhận Google tag và GA4 Event tag trỏ tới destination đúng.

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

| Symptom | First checks | Likely layer | Evidence cần capture |
| --- | --- | --- | --- |
| Không có Data Layer event | Business success, app callback, event push | Application/Data Layer | Console/app log và test state |
| Data Layer event có, tag không fire | Exact event name, trigger filter, variable, exception | GTM | Tag Assistant event và not-fired reason |
| Tag fire nhưng không có request | Google tag/config, consent, blocker, tag error | GTM/browser | Tag detail, console, network log |
| Request sai ID | Environment lookup, Google tag, stream selection | Routing | Redacted request URL/payload |
| Request sai parameter | DLV path, variable timing, type conversion | Data contract/GTM | Data Layer và request comparison |
| Một action tạo hai request | Duplicate push, overlapping tag, SPA remount, retry | Application/GTM | Timeline và request count |
| Request có nhưng DebugView trống | Sai property/device, debug mode, consent/privacy, delay | GA4/debug setup | Property, device, consent, timestamp |
| DebugView đúng nhưng report sai | Processing, custom-definition delay, filter, scope, threshold | GA4 reporting | Report setting và date range |
| QA data vào production | Environment routing hoặc sai stream | Release/routing | Request destination, version, hostname |

## Required Test Matrix

Dùng test ID ổn định qua các release.

| ID | Case | Action | Expected |
| --- | --- | --- | --- |
| TC-01 | Happy path | Hoàn thành flow hợp lệ | Một canonical event, mọi required parameter hợp lệ |
| TC-02 | Validation failure | Submit input không hợp lệ | Không có success/key event |
| TC-03 | Server failure | Tạo response lỗi | Không có success/key event |
| TC-04 | Double submit | Click/submit nhanh | Một event cho một business occurrence |
| TC-05 | Refresh/back | Refresh hoặc quay lại confirmation | Không duplicate ngoài ý muốn |
| TC-06 | SPA route | Vào, rời và quay lại route | Page/route event theo plan; không duplicate business event |
| TC-07 | Missing optional | Bỏ optional value | Omit hoặc fallback đúng document |
| TC-08 | Missing required | Ép required value bị thiếu | QA fail; không có success payload gây hiểu nhầm |
| TC-09 | Consent denied | Deny consent liên quan | Behavior đúng consent design; không có prohibited request |
| TC-10 | Consent granted | Grant consent liên quan | Collection bắt đầu/update đúng |
| TC-11 | Routing | Chạy trên QA hostname | Request chỉ đi tới QA destination |
| TC-12 | Privacy | Inspect Data Layer và request | Không PII, secret, raw form value hoặc unsafe URL |
| TC-13 | Browser | Test browser/device được hỗ trợ | Không có khác biệt implementation đáng kể |
| TC-14 | Regression | Chạy journey liên quan | Event cũ vẫn đúng và không duplicate |

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

| Test ID | Layer | Expected | Actual | Evidence | Result | Defect |
| --- | --- | --- | --- | --- | --- | --- |
| TC-01 | Application | Confirmed success | `[actual]` | `[link]` | Pass/Fail | `[ID]` |
| TC-01 | Data Layer | Một `sign_up` với value hợp lệ | `[actual]` | `[screenshot/log]` | Pass/Fail | `[ID]` |
| TC-01 | GTM | Tag đúng fire một lần | `[actual]` | `[Tag Assistant]` | Pass/Fail | `[ID]` |
| TC-01 | Network | Một request tới QA ID | `[actual]` | `[redacted HAR/screenshot]` | Pass/Fail | `[ID]` |
| TC-01 | Consent | Approved state | `[actual]` | `[screenshot]` | Pass/Fail | `[ID]` |
| TC-01 | DebugView | Event hiển thị một lần | `[actual]` | `[screenshot]` | Pass/Fail | `[ID]` |
| TC-01 | Report | Processed field/count hợp lý | `[actual]` | `[report link/screenshot]` | Pass/Fail | `[ID]` |

Evidence phải xác định date, environment, version, property/stream, tester, browser, result và known limitation. Redact sensitive value và không lưu credential trong evidence.

## Bộ Template Debug/QA

Các template này biến một debug session thành record có thể lặp lại. Dùng Evidence Template để chứng minh theo từng layer, Debug Session Record để ghi context tổng thể, và Defect and Retest Record khi expected result không đạt. Giữ release approval và monitoring record trong [Release & Monitoring](11-release-monitoring-answer.md).

| Template | Mục đích | Dùng khi |
| --- | --- | --- |
| Required Test Matrix | Xác định các scenario bắt buộc phải test. | Lập coverage trước implementation hoặc release. |
| Evidence Template | Kết nối một test case qua application, Data Layer, GTM, Network, consent, DebugView và report. | Ghi bằng chứng cho kết quả Pass/Fail. |
| Debug Session Record | Ghi test context, expected behavior, các layer đã kiểm tra và conclusion. | Chạy focused investigation hoặc release smoke test. |
| Defect and Retest Record | Ghi first failing layer, impact, containment, fix và retest result. | Khi test fail hoặc production behavior đáng nghi. |

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

| Field | Cần ghi nhận |
| --- | --- |
| Defect ID và severity | ID ổn định và phân loại Critical/High/Medium/Low. |
| First failing layer | Application, Data Layer, GTM, Network, consent, GA4 setup hoặc reporting. |
| Expected versus actual | Contract expectation và behavior quan sát được. |
| Reproduction | Test ID, URL, browser/device, consent state, steps và frequency. |
| Impact và affected period | Event/user/report/environment bị ảnh hưởng và first/last known time. |
| Evidence | Preview, Network, DebugView, report hoặc application evidence đã sanitize. |
| Containment | Tạm block, sửa routing, quyết định filter hoặc monitoring action. |
| Root cause và fix | Cause đã xác nhận, change/ticket, owner và target version. |
| Retest result | Test ID, date, evidence, residual impact và reviewer decision. |

Không đóng defect chỉ vì tag hiện ở **Tags Fired**. Chỉ đóng sau khi downstream evidence và affected-period assessment liên quan đã được ghi nhận.

## Defect Severity

| Severity | Definition | Ví dụ |
| --- | --- | --- |
| Critical | Privacy/security breach hoặc production corruption trên diện rộng | Gửi PII; production route tới test/unauthorized destination |
| High | Business outcome quan trọng bị thiếu, duplicate hoặc sai đáng kể | Purchase bị đếm đôi; sign-up thiếu trên diện rộng |
| Medium | Context quan trọng sai hoặc scenario/browser bị ảnh hưởng đáng kể | Sai method; SPA route bỏ sót event |
| Low | Documentation hoặc maintainability issue với data impact hiện tại thấp | Thiếu description; naming không nhất quán |

Severity cần xét impact, volume bị ảnh hưởng, duration, privacy risk, detectability và recoverability. PII leak volume thấp vẫn là high/critical vì privacy risk không chỉ phụ thuộc event volume.

## Pre-Publish Checklist

- [ ] Test dùng đúng environment, property, stream và GTM workspace/version.
- [ ] Measurement Plan và Data Layer contract đã được phê duyệt.
- [ ] Positive, negative, duplicate, boundary, consent, routing và privacy case đã pass.
- [ ] Mỗi event mong đợi có name, timing, value, type và count đúng.
- [ ] Mọi event/value bị cấm đều không xuất hiện.
- [ ] Network request dùng destination đúng và không expose PII/secret.
- [ ] Consent default, update và blocked behavior có evidence.
- [ ] Không có overlapping tag hoặc enhanced-measurement path tạo duplicate.
- [ ] Workspace chỉ chứa thay đổi dự kiến hoặc thay đổi ngoài scope đã được ghi rõ.
- [ ] Version name, description, owner, reviewer, rollback version và release window đã sẵn sàng.

## Post-Publish Checklist

- [ ] Xác nhận published container version và environment.
- [ ] Chạy production smoke test đã được phê duyệt với safe data.
- [ ] Kiểm tra destination thực tế và request count.
- [ ] Kiểm tra Realtime/DebugView phù hợp nhưng không xem là historical reporting cuối cùng.
- [ ] Validate processed report sau processing window dự kiến.
- [ ] So sánh event volume, missingness, duplicate, parameter quality và key event với baseline.
- [ ] Ghi release time, timezone, evidence, result và follow-up defect.
- [ ] Quyết định rollback, remediation hoặc tiếp tục monitoring.

## Definition of Done

- [ ] Có thể xác định layer đầu tiên bị lỗi đối với event thiếu hoặc duplicate.
- [ ] Test matrix gồm positive, negative, duplicate, boundary, SPA, consent, routing và privacy case.
- [ ] Evidence kết nối cùng test case qua Data Layer, GTM, network, DebugView và reporting.
- [ ] Đúng property, stream, environment và version được ghi nhận.
- [ ] Consent và privacy behavior được test, không chỉ giả định.
- [ ] DebugView và Realtime được phân biệt rõ với processed report.
- [ ] Defect có severity, owner, evidence và retest status.
- [ ] Reviewer sign-off hoặc accepted exception được ghi nhận.

## Tài liệu tham khảo

- [Preview and debug GTM containers](https://support.google.com/tagmanager/answer/6107056)
- [Tag Assistant](https://support.google.com/tagmanager/answer/13355721)
- [Monitor events in DebugView](https://support.google.com/analytics/answer/7201382)
- [Troubleshoot tag setup on your website](https://support.google.com/analytics/answer/9311124)
- [Unblock Google tags when using consent mode](https://support.google.com/tagmanager/answer/12962079)
- [Consent mode implementation](https://developers.google.com/tag-platform/security/guides/consent)
- [Avoid sending personally identifiable information](https://support.google.com/analytics/answer/6366371)
- [GA4 data freshness](https://support.google.com/analytics/answer/11198161)
