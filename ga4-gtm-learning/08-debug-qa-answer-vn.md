# 08 — Quy trình Debug và Analytics QA

## 1. Tổng quan

### Mục tiêu

Dùng section này để kiểm tra một tracking change từ business outcome đến dữ liệu GA4 đã collection và processing. Mục tiêu là tìm **first failing layer**, không chỉ xác nhận GTM tag xuất hiện dưới **Tags Fired**.

Chuỗi validation:

```text
Authoritative application state
  → Data Layer message
  → GTM trigger và variable evaluation
  → consent decision
  → GA4 network request
  → DebugView/Realtime diagnostic
  → processed report
```

Chỉ đánh dấu **Pass** cho một material event khi business outcome đã xảy ra, event được gửi đúng số lần với payload đã approve, request đi đúng destination trong consent state phù hợp và các kiểm tra downstream liên quan đều đạt. Nếu report downstream vẫn đang trong processing window được GA4 document, hãy đánh dấu kiểm tra đó là **Pending**, ghi owner và ngày follow-up, và chưa xem phần đó là hoàn tất.

### Phạm vi

Quy trình này tập trung vào cách vận hành web GTM và GA4 ổn định:

- contract từ application đến Data Layer;
- variable, trigger, tag, Google tag routing và collection-source ownership;
- GTM Preview/Tag Assistant, browser Network, GA4 DebugView/Realtime và processed report;
- kiểm tra positive, negative, duplicate, consent, privacy, routing, SPA/navigation, browser và regression;
- record cho evidence, defect, retest và handoff.

Media buying, campaign optimization, attribution strategy và Google Ads operations nằm ngoài section này. Release approval và post-release monitoring vẫn ở [Release & Monitoring](10-release-monitoring-answer.md).

### Test level và stopping rule

Bắt đầu từ L0 và dừng ở mức thấp nhất đủ trả lời risk. Change chỉ sửa documentation có thể cần contract check và test run tập trung; consent, ecommerce hoặc key business event mới cần toàn bộ sequence.

| Level                       | Cần chứng minh                                                                       | Khi dùng                                           | Evidence kết thúc                                              |
| --------------------------- | ------------------------------------------------------------------------------------ | -------------------------------------------------- | -------------------------------------------------------------- |
| L0 — Contract review        | Event, parameter, count, source, destination, consent và negative case đã định nghĩa | Trước khi mở debugger                              | Measurement Plan/Event Contract và precondition rõ ràng        |
| L1 — Isolated event         | Một action có kiểm soát tạo đúng Data Layer signal và request                        | Mọi event mới hoặc thay đổi                        | Event và request count đúng được quan sát                      |
| L2 — Journey flow           | Thứ tự event và business-state transition đúng                                       | Flow nhiều event                                   | Sequence không thiếu hoặc duplicate business event             |
| L3 — Boundary/privacy       | Invalid input, failure, retry, refresh, consent, PII, routing và browser behavior    | Material release hoặc change risk cao              | Các boundary case khớp contract đã approve                     |
| L4 — Regression             | Event lân cận, shared tag và destination không đổi sai                               | Thay đổi shared GTM/Google tag/consent/application | Baseline event vẫn đúng                                        |
| L5 — Processed data         | Field availability, scope, count, filter và interpretation                           | Khi report hoặc decision phụ thuộc kết quả         | Processed result được reconcile hoặc discrepancy được ghi nhận |
| L6 — Production observation | Version đã publish và impact ban đầu                                                 | Sau production activation                          | Release version, smoke evidence, window và owner được ghi nhận |

### Mỗi tool chứng minh điều gì?

| Tool/layer                  | Inspect                                                      | Chứng minh                                     | Không chứng minh                                         |
| --------------------------- | ------------------------------------------------------------ | ---------------------------------------------- | -------------------------------------------------------- |
| Application                 | Authoritative state và event call                            | Product đã expose signal cần đo                | GTM đã nhận hoặc routing signal                          |
| Data Layer                  | Name, value, type, order và count                            | GTM có message để evaluate                     | Tag đã fire hoặc request thành công                      |
| GTM Preview/Tag Assistant   | Timeline, variable, fired/not-fired tag, consent, order      | Previewed container evaluate draft đúng        | Production dùng version đó hoặc GA4 đã process           |
| Browser Network             | Request URL, Measurement ID, event, parameter, count, status | Browser đã cố gắng gửi collection request đúng | GA4 đã populate mọi report                               |
| GA4 DebugView/Realtime      | Device, event, parameter, timing, recent activity            | GA4 đã nhận diagnostic/recent signal           | Historical processing hoặc final attribution             |
| Standard report/Exploration | Processed field, scope, filter, freshness                    | Data dùng được cho analysis đã định nghĩa      | Upstream implementation đúng nếu thiếu upstream evidence |

Xem [Preview and debug containers](https://support.google.com/tagmanager/answer/6107056), [Tag Assistant](https://support.google.com/tagmanager/answer/13355721) và [Monitor events in DebugView](https://support.google.com/analytics/answer/7201382).

## 2. Bộ record QA cốt lõi

Dùng một bộ record nhỏ. QA report là gói tổng hợp các record này, không phải một template độc lập khác.

| Record                   | Priority                                   | Khi áp dụng                                            | Output                                                           |
| ------------------------ | ------------------------------------------ | ------------------------------------------------------ | ---------------------------------------------------------------- |
| Test Run Setup Record    | P0 — mọi run                               | Trước khi test                                         | Environment, version, consent, data an toàn, reset state, tester |
| Required Test Matrix     | P0 — mọi behavior change                   | Trước khi thực thi                                     | Scenario và expected outcome                                     |
| Evidence Template        | P1 — material event hoặc boundary thay đổi | Trong/sau khi thực thi                                 | Evidence theo layer, gắn với scenario ID                         |
| Debug Session Record     | P2 — conditional                           | First failing layer chưa rõ hoặc behavior intermittent | State được giữ, đường điều tra, conclusion                       |
| Defect and Retest Record | P2 — conditional                           | Case fail, có rủi ro production hoặc cần retest        | Defect, containment, fix, retest evidence                        |

Priority: **P0** bắt buộc, **P1** bắt buộc cho change material/impact cao, **P2** là conditional.

### 2.1 Test Run Setup Record

Hoàn thành record dùng lại này một lần cho mỗi test run trước khi mở GTM Preview:

| Field                              | Value                                                    |
| ---------------------------------- | -------------------------------------------------------- |
| Test run ID                        | `[run ID]`                                               |
| Environment và URL                 | `[QA/staging URL]`                                       |
| Application/build                  | `[commit/build]`                                         |
| GTM container/workspace/version    | `[container]` / `[workspace]` / `[version]`              |
| GA4 property/stream/Measurement ID | `[property]` / `[stream]` / `[sanitized ID]`             |
| Browser/device và date             | `[browser/device/date]`                                  |
| Test account/data                  | Chỉ synthetic account và safe values                     |
| Consent state                      | `[granted/denied/unresolved by category]`                |
| Tester/reviewer                    | `[name]`                                                 |
| Reset method                       | `[controlled profile, stored state, Preserve log, etc.]` |

Không đưa tên thật, email, phone, address, token, payment data hoặc free-form user content vào test data/evidence.

Chỉ reset state cần thiết cho scenario. Ghi nhận cookies/storage, consent, application journey state, Preview session, Network log và browser extensions được giữ hay reset. Không mặc định xoá tất cả vì có thể phá hỏng behavior returning-user, retry, refresh hoặc multi-tab đang cần test.

### 2.2 Required Test Matrix

Dùng test ID ổn định. Scenario được chọn có thể có block expectation L0 chi tiết trong workflow, nhưng block đó là một phần của matrix, không phải template riêng.

| ID    | Case               | Action                               | Expected                                                  |
| ----- | ------------------ | ------------------------------------ | --------------------------------------------------------- |
| TC-01 | Happy path         | Hoàn thành flow hợp lệ               | Một canonical event với required parameter hợp lệ         |
| TC-02 | Validation failure | Submit input không hợp lệ            | Không có success event                                    |
| TC-03 | Server failure     | Tạo response lỗi                     | Không có success event                                    |
| TC-04 | Duplicate/retry    | Submit hoặc retry nhanh              | Một event cho mỗi business occurrence                     |
| TC-05 | Refresh/back       | Refresh hoặc quay lại result         | Không duplicate ngoài ý muốn                              |
| TC-06 | SPA navigation     | Vào, rời và quay lại route           | Route event theo plan; không duplicate business event     |
| TC-07 | Missing optional   | Bỏ optional value                    | Omit hoặc fallback đúng tài liệu                          |
| TC-08 | Missing required   | Bỏ required value                    | QA fail; không tạo success payload gây hiểu nhầm          |
| TC-09 | Consent denied     | Deny consent liên quan               | Behavior đúng consent design; không có prohibited request |
| TC-10 | Consent granted    | Grant consent liên quan              | Collection bắt đầu/cập nhật đúng                          |
| TC-11 | Routing            | Chạy trên QA hostname                | Request chỉ đến QA destination                            |
| TC-12 | Privacy            | Inspect Data Layer và request        | Không PII, secret, raw form value hoặc unsafe URL         |
| TC-13 | Browser            | Test browser/device được hỗ trợ      | Không có khác biệt implementation đáng kể                 |
| TC-14 | Regression         | Chạy journey lân cận                 | Event cũ vẫn đúng và không duplicate                      |
| TC-15 | Collection source  | Kiểm tra mọi collection path đã biết | Một canonical source hoặc deduplication đã document       |

### 2.3 Evidence Template

Dùng một dòng cho mỗi layer của một test case được chọn. `Pending` chỉ hợp lệ khi runtime collection đã hoàn tất nhưng GA4 chưa hết processing window; phải ghi owner và ngày follow-up.

| Test ID | Layer              | Expected                                            | Actual     | Evidence                 | Result            | Defect   |
| ------- | ------------------ | --------------------------------------------------- | ---------- | ------------------------ | ----------------- | -------- |
| `[ID]`  | Application        | Business state đã xác nhận                          | `[actual]` | `[application log/link]` | Pass/Fail/Pending | `[ID/—]` |
| `[ID]`  | Data Layer         | Một event self-contained với value đã approve       | `[actual]` | `[capture/log]`          | Pass/Fail/Pending | `[ID/—]` |
| `[ID]`  | GTM                | Trigger/tag đúng được evaluate một lần              | `[actual]` | `[Tag Assistant]`        | Pass/Fail/Pending | `[ID/—]` |
| `[ID]`  | Collection source  | Một canonical source hoặc deduplication đã document | `[actual]` | `[source map/timeline]`  | Pass/Fail/Pending | `[ID/—]` |
| `[ID]`  | Network            | Request count và destination đúng expected          | `[actual]` | `[redacted request]`     | Pass/Fail/Pending | `[ID/—]` |
| `[ID]`  | Consent            | Behavior đúng state đang test                       | `[actual]` | `[consent evidence]`     | Pass/Fail/Pending | `[ID/—]` |
| `[ID]`  | DebugView/Realtime | Diagnostic/recent activity đúng expected            | `[actual]` | `[capture]`              | Pass/Fail/Pending | `[ID/—]` |
| `[ID]`  | Report             | Processed result reconcile với test                 | `[actual]` | `[report/follow-up]`     | Pass/Fail/Pending | `[ID/—]` |

Evidence phải có date, environment, version, property/stream, tester, browser, result và known limitation. Redact identifier và sensitive value.

### 2.4 Conditional records

#### Debug Session Record

Chỉ tạo sau khi giữ nguyên failing state hoặc khi behavior intermittent:

```text
Debug session ID:
Test case/journey ID:
Business question hoặc release:
Environment và URL:
Application/build và GTM version:
GA4 property/stream/Measurement ID:
Browser/device và consent state:
Expected business moment và event/count:
Canonical source và duplicate source đã kiểm tra:
Các layer đã kiểm tra:
Observed result:
First failing layer:
Evidence links:
Tester/date và reviewer/status:
Follow-up defect hoặc decision:
```

#### Defect and Retest Record

Dùng khi test fail hoặc cần theo dõi production risk:

| Field                     | Cần ghi nhận                                                                     |
| ------------------------- | -------------------------------------------------------------------------------- |
| Defect và severity        | Stable ID và phân loại Critical/High/Medium/Low                                  |
| First failing layer       | Application, Data Layer, GTM, consent, browser/network, GA4 setup hoặc reporting |
| Expected versus actual    | Contract expectation và behavior quan sát được                                   |
| Reproduction              | Test ID, URL, browser/device, consent state, steps, frequency                    |
| Impact và affected period | Event/user/report/environment và time range đã biết                              |
| Evidence                  | Application, Preview, Network, DebugView hoặc report evidence đã sanitize        |
| Containment               | Block, routing correction, pause, filter hoặc monitoring action                  |
| Root cause và fix         | Cause đã xác nhận, change/ticket, owner, target version                          |
| Retest result             | Test ID, date, evidence, residual impact, reviewer decision                      |

## 3. Thứ tự áp dụng và quy trình debug

### 3.1 Thứ tự và mức ưu tiên

1. **Setup run (P0):** hoàn thành Test Run Setup Record và kiểm soát browser/application state.
2. **Define coverage (P0):** tạo hoặc cập nhật Required Test Matrix từ Measurement Plan.
3. **Define expectation được chọn (L0):** ghi business moment, event/count, required parameter, destination, consent và negative case cho scenario đang test. Đây là chi tiết của matrix, không phải template mới.
4. **Execute và summarize (P0):** chạy từng scenario và cập nhật Scenario Execution Summary.
5. **Capture proof có chọn lọc (P1):** hoàn thành các dòng Evidence Template cho boundary material hoặc boundary đã thay đổi.
6. **Escalate khi cần (P2):** tạo Debug Session Record cho behavior chưa giải thích được và Defect and Retest Record cho failure/retest cần theo dõi.

Shortcut:

- Tracking change material thông thường dùng Bước 1–5.
- Change chỉ sửa documentation dùng Bước 1–2 và evidence của boundary bị ảnh hưởng.
- Happy path không có mismatch không cần Debug Session hoặc Defect record.
- Change consent, routing, key event, ecommerce hoặc shared tag cần đủ positive, negative, duplicate và regression coverage.
- Sau fix, chạy lại cùng scenario ID với attempt timestamp mới; chỉ đóng defect sau khi regression evidence liên quan đã pass.

### 3.2 Frontend checks trước GTM Preview

GTM Preview kiểm tra integration, không thay thế application test. Test layer sớm nhất sở hữu failure:

| Test level          | Cần chứng minh                                                                   | Cách tiếp cận                    |
| ------------------- | -------------------------------------------------------------------------------- | -------------------------------- |
| Unit                | Analytics adapter chỉ nhận approved event contract                               | TypeScript/project test runner   |
| Service/integration | API result đã confirm emit một lần; validation/failure/cancel không emit success | Mocked API hoặc integration test |
| Component/SPA       | Strict Mode, remount, route transition, double click và retry không duplicate    | Component/browser test           |
| Browser contract    | Trang thật push đúng Data Layer message trước khi GTM evaluate                   | E2E test với Data Layer capture  |

Assert final Data Layer message, occurrence count, value type và absence của prohibited field. Không chỉ assert adapter function đã được gọi.

### 3.3 Các bước debug end to end

#### Bước 1 — Xác nhận behavior mong đợi (L0 Contract Review)

Đọc Measurement Plan trước khi mở debugger và điền block expectation cấp scenario:

```text
Action:
Business moment:
Expected Data Layer event:
Expected request count:
Required parameters:
Expected destination:
Expected consent:
Negative cases:
```

Block này cung cấp các giá trị `Expected` cho Evidence Template. Không debug implementation khi expectation chưa được định nghĩa.

#### Bước 2 — Mở đúng preview session

1. Mở đúng GTM container, workspace, environment và QA URL.
2. Start Preview; xác nhận container, version và hostname đã connect.
3. Bật Preserve Network log khi có navigation hoặc redirect.
4. Xác nhận browser state và consent state khớp Test Run Setup Record.

#### Bước 3 — Thực hiện một action

Thực hiện đúng một action rồi dừng. Xác nhận application đã đạt authoritative business state và Data Layer signal xảy ra một lần, đúng thời điểm. Kiểm tra event name, parameter name, type, value, optional-field behavior và prohibited data.

Nếu business outcome chưa xảy ra, không dùng button click làm success event.

#### Bước 4 — Inspect GTM evaluation

Ở đúng event trong Tag Assistant:

1. So khớp Custom Event name chính xác, bao gồm cả case.
2. Inspect mọi variable được trigger và tag sử dụng.
3. Xác nhận trigger đúng match và tag fire một lần.
4. Kiểm tra tag không fire, blocking trigger, exception, consent requirement, sequencing và tag setting.
5. Tìm tag hoặc implementation khác có thể gửi cùng event.
6. Xác nhận Google tag và event tag trỏ đến đúng destination.

Một business occurrence nên có một canonical collection source. Nếu có nhiều source có chủ đích, phải document ownership, deduplication và expected request count.

#### Bước 5 — Inspect Network request

Filter GA4 collection request trong Developer Tools và xác nhận:

- request count đúng contract;
- Measurement ID và destination là QA/test value;
- event name, required parameter, type và value đúng;
- không có extra, prohibited hoặc sensitive field;
- consent signal đúng approved design;
- blocker, browser privacy, CSP, redirect hoặc network error không làm thay đổi kết quả.

Redact identifier và payload value trước khi chia sẻ evidence.

#### Bước 6 — Check GA4 DebugView và Realtime

Chọn đúng property và debug device. Xác nhận event và required parameter xuất hiện đúng count. DebugView/Realtime chỉ là collection diagnostic, không phải bằng chứng historical reporting đã hoàn chỉnh. Privacy control hoặc consent có thể làm event không hiển thị.

Xoá hoặc giới hạn test-only debug setting sau khi test. Không để `debug_mode` áp dụng cho toàn bộ user trong production.

#### Bước 7 — Validate processed reporting data

Sau documented processing window:

- chọn đúng property, stream, timezone và date range;
- xác nhận event và custom definition đã đăng ký có thể dùng;
- kiểm tra metric/dimension scope, filter, `(other)`, thresholding, sampling và recent date chưa hoàn tất;
- reconcile processed count với test evidence;
- ghi discrepancy thay vì âm thầm đổi conclusion.

### 3.4 Consent debugging

Consent là một runtime dimension. Thêm case cho default state, analytics denied, analytics granted, consent update sau load, SPA navigation sau mỗi state, returning-user có stored consent và CMP unresolved/failed. Với mỗi case, ghi:

- thời điểm default và update;
- tag được expected fire hoặc remain blocked;
- request có được gửi không và mang signal nào;
- behavior có khớp approved privacy design không;
- DebugView visibility có được expected không.

Không bypass consent model bằng ad hoc trigger. Inspect consent requirement và additional consent check trong Tag Assistant. Xem [unblock Google tags when using consent mode](https://support.google.com/tagmanager/answer/12962079).

### 3.5 Chẩn đoán failure

Luôn chẩn đoán first failing layer:

| Symptom                                  | First check                                               | Likely layer           | Evidence cần capture                    |
| ---------------------------------------- | --------------------------------------------------------- | ---------------------- | --------------------------------------- |
| Không có Data Layer event                | Business state, callback, event push                      | Application/Data Layer | Application log và test state           |
| Có Data Layer event nhưng tag không fire | Event name, filter, variable, exception                   | GTM                    | Tag Assistant event và not-fired reason |
| Tag fire nhưng không có request          | Google tag/config, consent, blocker, tag error            | GTM/browser            | Tag detail và Network log               |
| Sai Measurement ID                       | Environment lookup, Google tag, stream selection          | Routing                | Redacted request                        |
| Sai parameter                            | Data Layer path, timing, type conversion                  | Contract/GTM           | Data Layer/request comparison           |
| Một action có hai request                | Duplicate push, overlapping tag, remount, retry           | Application/GTM        | Timeline và request count               |
| Có request nhưng DebugView trống         | Property/device, debug mode, consent/privacy, delay       | GA4/debug setup        | Property, device, consent, timestamp    |
| DebugView đúng nhưng report sai          | Processing, definition delay, filter, scope, thresholding | GA4 reporting          | Report setting và date range            |

## 4. Lưu ý thực chiến và handoff

- **First failing layer là điểm neo chẩn đoán.** Sửa symptom ở layer sau có thể che giấu nguyên nhân thật.
- **Freeze trước khi đổi.** Giữ nguyên state và evidence ban đầu; không refresh hoặc đổi nhiều setting cùng lúc.
- **Ownership phải rõ.** Hardcoded `gtag`, CMS/plugin, Enhanced Measurement path, server-side path, Measurement Protocol call hoặc GTM tag thứ hai là duplicate source nếu chưa được document và deduplicate.
- **Evidence phải an toàn.** Dùng synthetic data, redact ID/payload value và không lưu credential.
- **Kết luận phải đúng phạm vi.** Network chứng minh browser đã cố gửi collection; DebugView chứng minh GA4 nhận debuggable event; processed report chứng minh reporting availability sau processing. Không gộp các kết luận này.
- **Handoff tối thiểu.** Frontend cần event name, business moment, payload schema, occurrence rule, source, consent assumption và test ID. GTM owner cần variable/trigger/tag mapping và destination. QA owner cần expected matrix và evidence link.
- **Ranh giới release.** Dùng [Release & Monitoring](10-release-monitoring-answer.md) cho approval, production activation, rollback ownership và post-release observation.

## 5. Ví dụ hoàn chỉnh — Registration Journey

Đây là ví dụ duy nhất trong tài liệu. Đây là minh họa non-production; thay toàn bộ sample ID, value và evidence placeholder bằng dữ liệu của project.

### 5.1 L0 expectation block đã điền

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

### 5.2 Context của test run

| Field                        | Giá trị ghi nhận                                                                                                    |
| ---------------------------- | ------------------------------------------------------------------------------------------------------------------- |
| QA report ID                 | `QA-REG-001`                                                                                                        |
| Measurement Plan             | `MP-REG-001 / v1.0`                                                                                                 |
| Journey                      | `J-REG-001` — Registration                                                                                          |
| Environment                  | Chỉ QA/staging                                                                                                      |
| Application/GTM/GA4          | `[build]` / `[GTM version]` / `[QA property và stream]`                                                             |
| Browser và data              | Controlled profile; synthetic account; safe values                                                                  |
| Consent state                | Analytics granted; test không cần advertising consent                                                               |
| Canonical source             | Backend xác nhận account creation → application Data Layer → GTM → GA4                                              |
| Duplicate source đã kiểm tra | Không có hardcoded `gtag`, plugin, GTM tag thứ hai, server-side path hoặc Measurement Protocol source cho event này |
| Expected result              | Một `sign_up` với `method=email` và `form_id=registration`                                                          |

### 5.3 Scenario execution summary

| Test ID      | Scenario                       | Kết quả quan sát                                             | Evidence                               | Status |
| ------------ | ------------------------------ | ------------------------------------------------------------ | -------------------------------------- | ------ |
| `TC-REG-001` | Form ready                     | Một `registration_start`; không có PII                       | `[application + Data Layer]`           | Pass   |
| `TC-REG-002` | Invalid input                  | Validation error; không có `sign_up`                         | `[Preview + request]`                  | Pass   |
| `TC-REG-003` | Server failure                 | Server error; không có `sign_up`                             | `[application + request]`              | Pass   |
| `TC-REG-004` | Account creation được xác nhận | Backend success; một `sign_up` đã approve                    | `[application + Data Layer + Preview]` | Pass   |
| `TC-REG-005` | Rapid double submit/retry      | Một confirmed account; một request                           | `[timeline + Network]`                 | Pass   |
| `TC-REG-006` | Refresh/back/SPA remount       | Không duplicate `sign_up`                                    | `[navigation timeline]`                | Pass   |
| `TC-REG-007` | Consent denied                 | Behavior đúng denied-state; không có prohibited data         | `[consent + storage + Network]`        | Pass   |
| `TC-REG-008` | Sai environment/destination    | Chỉ QA Measurement ID                                        | `[redacted request]`                   | Pass   |
| `TC-REG-009` | User-ID ngoài scope            | Không email, phone, raw account ID hoặc User-ID chưa approve | `[redacted payload]`                   | Pass   |
| `TC-REG-010` | Collection-source ownership    | Chỉ canonical GTM path gửi `sign_up`                         | `[source map + timeline]`              | Pass   |

### 5.4 Evidence Template chi tiết — `TC-REG-004`

| Test ID      | Layer             | Expected                                    | Actual               | Evidence               | Result  | Defect |
| ------------ | ----------------- | ------------------------------------------- | -------------------- | ---------------------- | ------- | ------ |
| `TC-REG-004` | Application       | Backend xác nhận account creation           | `[success response]` | `[application log]`    | Pass    | `—`    |
| `TC-REG-004` | Data Layer        | Một `sign_up` với parameter đã approve      | `[payload]`          | `[Data Layer capture]` | Pass    | `—`    |
| `TC-REG-004` | GTM               | Trigger/tag đúng fire một lần               | `[timeline]`         | `[Tag Assistant]`      | Pass    | `—`    |
| `TC-REG-004` | Collection source | Một canonical source                        | `[source map]`       | `[source timeline]`    | Pass    | `—`    |
| `TC-REG-004` | Network           | Một request tới QA Measurement ID           | `[count/payload]`    | `[redacted request]`   | Pass    | `—`    |
| `TC-REG-004` | Consent           | Analytics behavior đã approve               | `[state/signals]`    | `[consent evidence]`   | Pass    | `—`    |
| `TC-REG-004` | DebugView         | Một debuggable event với required parameter | `[event]`            | `[DebugView capture]`  | Pass    | `—`    |
| `TC-REG-004` | Report            | Processed result reconcile với test         | `[chưa có]`          | `[follow-up record]`   | Pending | `—`    |

### 5.5 Frontend contract example

```typescript
window.dataLayer = [];

await completeConfirmedRegistration();

expect(window.dataLayer).toEqual([
  expect.objectContaining({
    event: "sign_up",
    event_schema_version: "1.0",
    method: "email",
    form_id: "registration",
  }),
]);
```

Application test cũng cần cover API failure, duplicate callback, invalid required value và SPA remount. Event chỉ được emit sau authoritative account-creation result.

### 5.6 Kết luận

Runtime collection QA đã pass: backend xác nhận account creation, application emit một `sign_up` self-contained, GTM chọn đúng tag một lần, consent khớp approved design và một request đi tới QA Measurement ID. Processed reporting vẫn là follow-up cho đến khi documented GA4 processing window hoàn tất.

## Tài liệu tham khảo chính thức

- [Preview and debug GTM containers](https://support.google.com/tagmanager/answer/6107056)
- [Tag Assistant](https://support.google.com/tagmanager/answer/13355721)
- [Monitor events in DebugView](https://support.google.com/analytics/answer/7201382)
- [Troubleshoot tag setup on your website](https://support.google.com/analytics/answer/9311124)
- [Unblock Google tags when using consent mode](https://support.google.com/tagmanager/answer/12962079)
- [Consent mode implementation](https://developers.google.com/tag-platform/security/guides/consent)
- [Avoid sending personally identifiable information](https://support.google.com/analytics/answer/6366371)
- [GA4 data freshness](https://support.google.com/analytics/answer/11198161)
