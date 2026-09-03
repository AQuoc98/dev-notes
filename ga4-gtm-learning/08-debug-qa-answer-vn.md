# 08 — Quy trình Debug và Analytics QA

## 1. Tổng quan

### 1.1 Mục tiêu

Kiểm tra một thay đổi GA4/GTM đã được phê duyệt, bắt đầu từ kết quả authoritative của Application đến request trong trình duyệt và kết quả chẩn đoán hoặc dữ liệu đã xử lý trong GA4 khi cần. Mục tiêu là tìm layer lỗi đầu tiên và chỉ lưu evidence cần thiết cho mức rủi ro.

### 1.2 Phạm vi

- Thu thập dữ liệu web ổn định bằng GTM và GA4.
- Application/Data Layer, Variables, Triggers, Tags, Google tag routing, consent và collection-source ownership.
- GTM Preview/Tag Assistant, Browser Network, GA4 DebugView/Realtime và kiểm tra dữ liệu đã xử lý khi business decision phụ thuộc vào dữ liệu đó.
- Case positive, negative, duplicate, consent, privacy, routing, SPA/navigation, browser và regression.
- Record cho test setup, data safety, scenario, evidence, defect, retest và handoff.

### 1.3 Ngoài phạm vi

- Thiết kế measurement hoặc định nghĩa event: xem Section 07.
- Cấu hình chi tiết Data Layer, Variable, Trigger, Tag, Consent hoặc Template: xem Sections 01–06.
- Thiết kế report hoặc chart: xem Section 09.
- Phê duyệt release production và theo dõi sau release: xem Section 10.
- Media buying, campaign optimization, attribution strategy và Google Ads operations.

### 1.4 Chuỗi validation

~~~text
Authoritative application state
→ Data Layer message
→ GTM Trigger và Variable evaluation
→ consent decision
→ GA4 Network request
→ DebugView/Realtime diagnostic
→ processed result nếu cần
~~~

### 1.5 Quy tắc Pass cho material event

Một material event chỉ được **Pass** khi có evidence cho tất cả kiểm tra áp dụng:

1. Business state authoritative đã xảy ra.
2. Event và request count khớp contract đã duyệt.
3. Payload chỉ chứa field, type và value đã duyệt.
4. Request dùng đúng environment và destination.
5. Consent behavior khớp state đã được phê duyệt.
6. Downstream result liên quan đã được quan sát.

Chỉ đánh dấu downstream là **Pending** khi collection đã hoàn tất nhưng GA4 chưa hết processing window thông thường. Phải ghi owner, ngày follow-up, property, stream và kiểm tra cần thực hiện. Pending chưa phải Pass hoàn tất.

### 1.6 Mỗi layer chứng minh điều gì?

| Layer/tool | Dùng để chứng minh | Không chứng minh |
|---|---|---|
| Application | Business state và outcome thực sự đã đạt. | GTM đã nhận hoặc gửi event. |
| Data Layer | Message, value, type, order và count dự kiến đã được push. | Tag đã fire hoặc request thành công. |
| GTM Preview / Tag Assistant | Container đang preview evaluate đúng event, consent, Variable, Trigger và Tag. | Production đang dùng draft đó hoặc GA4 đã process event. |
| Browser Network | Browser đã cố gửi request với destination, event, parameter và count đúng. | Event có mặt trong mọi GA4 report. |
| GA4 DebugView / Realtime | GA4 đã nhận diagnostic hoặc recent signal từ device được chọn. | Historical processing hoặc report đầy đủ. |
| Processed Report / Exploration | Field, scope, filter và count đã xử lý đáp ứng mục đích phân tích. | Implementation upstream đúng nếu thiếu evidence từ Application/GTM/Network. |

Sử dụng [GTM Preview và debug mode](https://support.google.com/tagmanager/answer/6107056), [Tag Assistant](https://support.google.com/tagmanager/answer/13355721) và [GA4 DebugView](https://support.google.com/analytics/answer/7201382) cho layer tương ứng.

## 2. QA record và thứ tự áp dụng

### 2.1 Mức ưu tiên của record

QA là một gói record. Không tạo một template mới cho từng scenario.

| Ưu tiên | Record | Áp dụng khi nào? | Đầu ra |
|---|---|---|---|
| P0 | Test Run Setup Record | Mọi test run. | Environment, version, browser, consent, reset state, tester. |
| P0 | Data Safety Check | Trước khi thu thập hoặc export evidence. | Xác nhận dữ liệu giả, redaction, destination an toàn và cleanup. |
| P0 | Required Test Matrix | Mọi thay đổi behavior hoặc configuration. | Scenario, action và expected outcome. |
| P0 | Scenario Execution Summary | Mọi scenario được chạy trong material run. | Kết quả ngắn, evidence link và trạng thái follow-up. |
| P1 | Evidence Template | Material event hoặc boundary bị thay đổi. | Bằng chứng theo từng layer, gắn với test ID. |
| P2 | Debug Session Record | Không rõ layer lỗi đầu tiên hoặc behavior không ổn định. | State được giữ, đường điều tra và kết luận. |
| P2 | Defect and Retest Record | Có failure, production risk hoặc cần retest. | Reproduction, containment, fix, retest và residual impact. |

P0 là bắt buộc. P1 bắt buộc với thay đổi material hoặc nhạy cảm ở boundary. P2 dùng theo điều kiện.

### 2.2 Thứ tự sử dụng

~~~text
Test Run Setup
→ Data Safety Check
→ Required Test Matrix
→ Scenario Execution Summary
→ Evidence rows cho boundary material
→ Debug Session hoặc Defect/Retest nếu cần
~~~

Expectation block của scenario nằm trong Required Test Matrix, không phải template độc lập. Nếu yêu cầu bị từ chối khi review contract, dừng lại và ghi quyết định; không tạo GTM asset.

### 2.3 Test Run Setup Record

Điền một lần cho mỗi run trước khi mở Preview:

| Field | Giá trị |
|---|---|
| Test run ID | [run ID] |
| Environment và URL | [QA/staging URL] |
| Application/build | [commit hoặc build] |
| GTM container/workspace/version | [container] / [workspace] / [version] |
| GA4 property/stream/Measurement ID | [property] / [stream] / [sanitized ID] |
| Browser/device/date | [browser] / [device] / [date] |
| Test account/data | Chỉ synthetic account và safe values |
| Consent state | [granted/denied/unresolved theo category] |
| Reset method | [controlled profile, stored state, Preserve log] |
| Tester/reviewer | [name] |

Chỉ reset state cần cho scenario. Ghi rõ cookies/storage, consent, application state, Preview, Network log và extension được giữ hay reset.

### 2.4 Data Safety Check

Hoàn thành trước khi thực hiện action hoặc chia sẻ session:

| Kiểm tra | Expected |
|---|---|
| Environment | QA hoặc staging hostname; không có production destination. |
| Test data | Synthetic account, safe values, không dùng dữ liệu khách hàng thật. |
| Payload | Không PII, credential, payment data, unrestricted input hoặc secret. |
| URL và log | Không có sensitive query string, token hoặc account identifier. |
| Evidence | Screenshot, export và Network log đã redact, có kiểm soát truy cập. |
| Consent | Test state và category được phép đã được ghi lại. |
| Cleanup | Test-only debug setting, preview link và dữ liệu tạm có owner để xóa. |

Nếu bất kỳ kiểm tra nào fail, dừng run, xóa hoặc redact dữ liệu không an toàn và ghi lại containment action.

### 2.5 Required Test Matrix

Dùng test ID ổn định và lấy expected value từ Section 07:

| ID | Case | Action | Expected outcome |
|---|---|---|---|
| TC-01 | Happy path | Hoàn thành flow hợp lệ một lần. | Một canonical event với đủ required parameter. |
| TC-02 | Validation failure | Submit input không hợp lệ. | Không có success event. |
| TC-03 | Server failure | Tạo hoặc mô phỏng response lỗi. | Không có success event; failure classification đúng. |
| TC-04 | Valid no-output | Dùng response hợp lệ không tạo output nếu contract có định nghĩa. | Một event theo contract với giá trị no-output. |
| TC-05 | Duplicate/retry | Double-submit hoặc retry nhanh. | Một event cho mỗi business occurrence. |
| TC-06 | Refresh/back/remount | Refresh, back hoặc remount component. | Không duplicate ngoài ý muốn. |
| TC-07 | Missing required | Bỏ required field. | QA fail; không có success payload gây hiểu nhầm. |
| TC-08 | Missing optional | Bỏ optional field. | Omit hoặc fallback đúng tài liệu. |
| TC-09 | Consent denied/granted | Chạy cả hai state và update sau load. | Behavior blocked, reduced hoặc collected đúng phê duyệt. |
| TC-10 | Routing/privacy | Dùng QA hostname và inspect payload. | Chỉ QA destination; không có dữ liệu bị cấm. |
| TC-11 | Browser/SPA | Test browser được hỗ trợ, navigation và route restoration. | Không khác biệt đáng kể hoặc duplicate. |
| TC-12 | Regression/source ownership | Kiểm tra event lân cận và mọi collection path đã biết. | Baseline vẫn đúng; một source chuẩn hoặc deduplication được ghi rõ. |

Chỉ thêm case ecommerce, key event, User-ID hoặc shared tag khi thay đổi có ảnh hưởng đến chúng.

### 2.6 Scenario Execution Summary

Dùng một dòng cho mỗi scenario đã chạy. Đây là run summary, không phải evidence chi tiết:

| Test ID | Started | Tóm tắt action/kết quả | Expected count | Actual count | Status | Evidence/defect/follow-up |
|---|---|---|---:|---:|---|---|
| [ID] | [timestamp] | [một câu] | [n] | [n] | Pass/Fail/Pending | [ID và owner/date] |

Summary phải nêu business state có xảy ra hay không, không chỉ nêu Tag xuất hiện dưới Tags Fired.

### 2.7 Evidence Template

Dùng một dòng cho mỗi layer liên quan của scenario:

| Test ID | Layer | Expected | Actual | Evidence | Result | Defect |
|---|---|---|---|---|---|---|
| [ID] | Application | Authoritative state đã xảy ra | [actual] | [sanitized log] | Pass/Fail/Pending | [ID/—] |
| [ID] | Data Layer | Một message self-contained đã duyệt | [actual] | [capture] | Pass/Fail/Pending | [ID/—] |
| [ID] | GTM | Trigger/Tag đúng evaluate một lần | [actual] | [Tag Assistant] | Pass/Fail/Pending | [ID/—] |
| [ID] | Network | Request count và destination đúng | [actual] | [redacted request] | Pass/Fail/Pending | [ID/—] |
| [ID] | Consent | Behavior đúng với state đang test | [actual] | [consent evidence] | Pass/Fail/Pending | [ID/—] |
| [ID] | DebugView/Realtime | Event diagnostic/recent đúng expected | [actual] | [capture] | Pass/Fail/Pending | [ID/—] |
| [ID] | Processed data | Kết quả có sau processing nếu cần | [actual] | [report/follow-up] | Pass/Fail/Pending | [ID/—] |

Evidence nên có run ID, ngày, environment, version, property/stream, browser, tester, result và limitation đã biết. Redact identifier và sensitive value.

### 2.8 Record dùng theo điều kiện

#### Debug Session Record

Chỉ tạo sau khi đã giữ nguyên state lỗi hoặc behavior intermittent:

~~~text
Debug session ID:
Test run/scenario ID:
Environment và URL:
Application/build và GTM version:
GA4 property/stream/Measurement ID:
Browser/device và consent state:
Business moment, event và count mong đợi:
Canonical và duplicate source đã kiểm tra:
Các layer đã kiểm tra:
Observed result và first failing layer:
Evidence links:
Tester/date, reviewer/status:
Follow-up defect hoặc decision:
~~~

#### Defect and Retest Record

Dùng khi cần theo dõi failure hoặc retest:

| Field | Nội dung |
|---|---|
| Defect/severity | Stable ID và Critical/High/Medium/Low. |
| First failing layer | Application, Data Layer, GTM, consent, Network, GA4 setup hoặc processed data. |
| Expected versus actual | Contract expectation và kết quả thực tế. |
| Reproduction | Test ID, URL, browser/device, consent, steps và frequency. |
| Impact/containment | Event, user, environment, period bị ảnh hưởng và action tức thời. |
| Root cause/fix | Cause đã xác nhận, change/ticket, owner và target version. |
| Retest | Attempt timestamp mới, evidence, residual impact và reviewer decision. |

## 3. Thực thi QA theo từng bước

### 3.1 Trước khi mở Preview

1. Mở Measurement Plan đã duyệt và xác định event, business moment, required parameter, count, destination, consent và negative case.
2. Hoàn thành Test Run Setup và Data Safety Check.
3. Chọn matrix nhỏ nhất nhưng đủ với risk của thay đổi.
4. Với material event, điền expectation block:

~~~text
Action:
Business moment:
Expected Data Layer event:
Expected request count:
Required parameters:
Expected destination:
Expected consent:
Negative cases:
~~~

5. Chạy frontend contract test trước nếu Application sở hữu business result. GTM Preview không thể chứng minh API callback, deduplication guard hoặc component lifecycle rule.

### 3.2 Chạy một scenario

Thực hiện một action có kiểm soát rồi dừng. Xác nhận Application đạt authoritative state và push Data Layer message đúng một lần. Không coi click, render, route change hoặc request start là business success nếu contract không quy định như vậy.

### 3.3 Kiểm tra GTM Preview / Tag Assistant

Tại đúng event:

1. So khớp Custom Event name, bao gồm cả chữ hoa/chữ thường.
2. Inspect mọi Variable được Trigger và Tag dùng.
3. Xác nhận Trigger authoritative match và Tag fire một lần.
4. Kiểm tra not-fired reason, blocking Trigger, exception, consent requirement, sequencing và Tag setting.
5. Tìm Tag khác, plugin, hardcoded gtag, server-side path hoặc Measurement Protocol call có thể gửi cùng event.
6. Xác nhận Google tag và event Tag dùng đúng environment và Measurement ID.

Preview chỉ chứng minh draft đang được evaluate, không chứng minh production hoặc GA4 processing.

### 3.4 Kiểm tra Network request

Filter GA4 collection request và xác nhận:

- request count bằng contract;
- Measurement ID và destination là giá trị QA/test;
- event name, required parameter, type và value chính xác;
- optional field tuân theo contract;
- không có extra, prohibited hoặc sensitive field;
- consent signal khớp approved design;
- blocker, CSP, redirect, browser privacy hoặc network error không làm sai kết quả.

Chỉ lưu request sau khi đã redact identifier và payload value.

### 3.5 Kiểm tra DebugView / Realtime

Chọn đúng property và debug device. Xác nhận event và required parameter xuất hiện đúng count. DebugView và Realtime là công cụ diagnostic/recent activity; không chứng minh historical processing hoặc report đầy đủ. Consent và client-side privacy control có thể làm event không hiển thị.

Dùng Preview hoặc Tag Assistant để bật debug mode cho device của tester. Gỡ test-only setting sau khi test; không để debug configuration áp dụng cho toàn bộ user trong production.

### 3.6 Kiểm tra processed data khi cần

Chỉ làm bước này khi business decision phụ thuộc vào dữ liệu GA4 đã xử lý:

1. Chờ processing/data-freshness window được ghi trong plan hoặc follow-up.
2. Chọn đúng property, stream, timezone, date range, dimension, metric và filter.
3. Xác nhận event và custom definition đã đăng ký có thể dùng.
4. Kiểm tra scope, thresholding, sampling, cardinality và ngày gần nhất chưa hoàn tất.
5. Reconcile kết quả với evidence của test.
6. Ghi discrepancy hoặc Pending follow-up; không tự ý đổi expected result.

### 3.7 Các case consent

Với thay đổi có consent, thêm các case:

- default state trước khi user chọn banner;
- analytics denied;
- analytics granted;
- update sau khi user chọn;
- stored consent ở lần quay lại;
- SPA navigation sau mỗi state;
- CMP unresolved hoặc failed.

Với mỗi case, ghi Tag behavior, request và signal, storage behavior, destination và DebugView visibility dự kiến. Ưu tiên built-in consent behavior đã duyệt; không bypass bằng Trigger tự phát.

## 4. Chẩn đoán, guardrail và handoff

### 4.1 Chẩn đoán theo layer lỗi đầu tiên

| Dấu hiệu | Kiểm tra đầu tiên | Layer có khả năng lỗi | Evidence cần lưu |
|---|---|---|---|
| Không có Data Layer event | Business state, callback và application push | Application/Data Layer | App log và Data Layer capture |
| Có Data Layer nhưng không có Tag | Event name, filter, Variable, exception | GTM | Tag Assistant timeline và not-fired reason |
| Tag fire nhưng không có request | Google tag/config, consent, blocker, Tag error | GTM/browser | Tag detail và Network |
| Sai destination hoặc Measurement ID | Environment lookup, stream, Google tag | Routing | Redacted request |
| Sai parameter | Data Layer path, timing, type conversion | Contract/GTM | So sánh Data Layer và request |
| Một action có hai request | Duplicate push, Tag chồng, remount, retry, source thứ hai | Application/GTM | Timeline và request count |
| Có request nhưng DebugView trống | Property/device, debug mode, consent, delay | GA4/debug setup | Property, device, consent, timestamp |
| DebugView đúng nhưng processed result sai | Processing, definition delay, scope, filter, thresholding | GA4 processed data | Report setting và follow-up |

### 4.2 Guardrail vận hành

- Giữ nguyên state và evidence trước khi đổi cấu hình.
- Đổi từng layer một; không refresh rồi sửa nhiều setting trước khi capture.
- Mỗi business event chỉ nên có một collection source chuẩn, hoặc phải document ownership và deduplication.
- Business logic nằm ở Application; GTM chỉ transport field đã duyệt.
- Dùng dữ liệu giả và redact Tag Assistant export, screenshot và Network log.
- Phân biệt rõ bằng chứng từ Network, DebugView và processed report.
- Chỉ đóng defect sau khi scenario ban đầu và regression liên quan đều pass.

### 4.3 Handoff

Frontend handoff gồm event name, business moment, payload schema, occurrence/deduplication rule, source, consent assumption, build và test ID. GTM handoff gồm Variable/Trigger/Tag mapping, environment, destination và expected count. QA handoff gồm matrix, scenario summary, evidence link, Pending owner/date và open defect.

Dùng [Release Monitoring](10-release-monitoring-answer-vn.md) cho production activation, rollback ownership và post-release observation.

## 5. Ví dụ hoàn chỉnh — Registration Journey

Đây là ví dụ non-production. Thay toàn bộ ID, value, ngày và evidence placeholder bằng dữ liệu project. Ví dụ chỉ minh họa cách nối các record, không tạo thêm template độc lập.

### 5.1 Context của test run

| Field | Giá trị ghi nhận |
|---|---|
| Test run ID | QA-REG-RUN-001 |
| Measurement Plan | MP-REG-001 / v1.0 |
| Journey | J-REG-001 — Registration |
| Environment | Chỉ QA/staging |
| Application/GTM/GA4 | [build] / [GTM version] / [QA property và stream] |
| Browser/data | Controlled profile; synthetic account; safe values |
| Consent | Analytics granted; test không cần advertising consent |
| Canonical source | Backend xác nhận account creation → Application Data Layer → GTM → GA4 |
| Duplicate source đã kiểm tra | Không có hardcoded gtag, plugin, GTM Tag thứ hai, server-side path hoặc Measurement Protocol source |

### 5.2 Data Safety Check

| Kiểm tra | Kết quả |
|---|---|
| QA hostname và Measurement ID | Pass — [redacted QA ID] |
| Synthetic account và safe values | Pass |
| PII, credential, token và raw form content | Pass — không có |
| Evidence redaction và quyền truy cập | Pass |
| Owner cleanup test-only debug setting | [name/date] |

### 5.3 Expectation của scenario

~~~text
Action: Hoàn thành registration hợp lệ
Business moment: Server xác nhận account creation
Expected Data Layer event: một sign_up
Expected request count: một
Required parameters: method, form_id
Expected destination: chỉ QA stream
Expected consent: analytics behavior đã được phê duyệt
Negative cases: invalid input, server failure, double submit, refresh
~~~

### 5.4 Scenario Execution Summary

| Test ID | Scenario | Expected | Actual | Status | Evidence/follow-up |
|---|---|---|---|---|---|
| TC-REG-01 | Registration hợp lệ | Một sign_up có method và form_id | Backend confirm account; một message/request | Pass | EV-REG-01 |
| TC-REG-02 | Input không hợp lệ | Không có sign_up | Validation error; không có sign_up request | Pass | EV-REG-02 |
| TC-REG-03 | Server failure | Không có sign_up | Server error; không có sign_up request | Pass | EV-REG-03 |
| TC-REG-04 | Double submit/retry | Một event cho mỗi account | Một account; một request | Pass | EV-REG-04 |
| TC-REG-05 | Refresh/back/remount | Không duplicate sign_up | Không duplicate | Pass | EV-REG-05 |
| TC-REG-06 | Consent denied | Behavior denied đã duyệt | Không có prohibited collection | Pass | EV-REG-06 |
| TC-REG-07 | QA routing/privacy | QA destination; không PII | QA Measurement ID; payload an toàn | Pass | EV-REG-07 |
| TC-REG-08 | Processed result | Có sau freshness window | Chưa có | Pending | Owner [name], [date] |

### 5.5 Evidence cho TC-REG-01

| Layer | Expected | Actual | Evidence | Result |
|---|---|---|---|---|
| Application | Server xác nhận account creation | [success response] | [sanitized app log] | Pass |
| Data Layer | Một sign_up self-contained | [event và payload] | [Data Layer capture] | Pass |
| GTM | Một Trigger/Tag authoritative evaluate | [timeline] | [Tag Assistant] | Pass |
| Network | Một request tới QA Measurement ID | [count và redacted payload] | [Network log] | Pass |
| Consent | Analytics behavior đã duyệt | [state/signals] | [consent capture] | Pass |
| DebugView/Realtime | Một event có method và form_id | [event] | [DebugView capture] | Pass |
| Processed data | Kết quả sau processing window | [chưa có] | [follow-up] | Pending |

### 5.6 Kết luận

Runtime collection đạt cho QA stream vì server xác nhận account creation, Application push một event, GTM evaluate một đường authoritative, consent khớp state đã duyệt và một request đi đúng destination. Processed-data validation vẫn Pending cho đến khi freshness window được ghi nhận hoàn tất.

## Tài liệu tham khảo chính thức

- [Preview và debug GTM container](https://support.google.com/tagmanager/answer/6107056)
- [Tag Assistant troubleshooting](https://support.google.com/tagmanager/answer/10039345)
- [Chia sẻ hoặc export Tag Assistant session](https://support.google.com/tagmanager/answer/15212893)
- [Theo dõi event trong GA4 DebugView](https://support.google.com/analytics/answer/7201382)
- [GA4 data freshness](https://support.google.com/analytics/answer/11198161)
- [Consent mode implementation](https://developers.google.com/tag-platform/security/guides/consent)
- [Unblock Google tags khi dùng consent mode](https://support.google.com/tagmanager/answer/12962079)
- [Tránh gửi personally identifiable information](https://support.google.com/analytics/answer/6366371)
