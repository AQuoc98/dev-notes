# 10 — Quản lý Release GTM và Monitoring GA4

## Mục đích

Mọi thay đổi tracking đều là thay đổi production. Một tag có thể fire đúng trong Preview nhưng vẫn gửi nhầm property, gửi trùng event, vi phạm consent/privacy hoặc làm thay đổi reporting sau khi publish. Release management kết nối measurement contract đã được phê duyệt, implementation, bằng chứng QA, approval, publication, rollback, smoke test và monitoring.

Mô hình thực tế:

```text
Thay đổi nhỏ, có phạm vi rõ
  → workspace
  → Preview/QA
  → evidence và review
  → version
  → publish vào đúng environment
  → production smoke test
  → monitoring window
  → close, remediate hoặc rollback
```

Rollback GTM version là cơ chế khôi phục configuration của container. Nó không xóa hoặc sửa các event đã được GA4 process, và cũng không tự động đảo ngược GA4 property settings hoặc permanent data filters.

## Cách sử dụng quy trình này

Dùng quy trình này cho mọi thay đổi material có thể ảnh hưởng đến tracking behavior, consent/privacy, routing, key events, destinations, schemas, reports hoặc data quality. Tạo release record trước khi implementation và cập nhật record khi có thêm evidence.

### Bộ hồ sơ release tối thiểu

Mỗi material change nên có:

- release record gồm owner, scope, environment, change type và các ID liên quan;
- các Measurement Plan, implementation, Debug/QA, report và monitoring records có liên quan;
- version/publish record và production smoke-test evidence;
- observation result, final outcome hoặc incident record.

Không cần tạo mọi template cho mọi change. Nếu một layer hoặc downstream asset không bị ảnh hưởng, ghi `N/A` và lý do. Report-only change thông thường chỉ cần configuration/review records của Section 09, không cần GTM version hoặc GTM rollback.

### Quy trình change thông thường

1. **Phân loại change.** Ghi business purpose, journey bị ảnh hưởng, change type, owner, environment và downstream impact dự kiến.
2. **Kiểm tra Gate 0.** Xác nhận [Measurement Plan](07-measurement-plan-answer-vn.md) đã approve, event contract, schema/lifecycle decision, consent/privacy decision và reporting requirement.
3. **Implementation trong workspace có phạm vi rõ.** Hoàn tất Gate 1 và xác định mọi variable, trigger, tag, template, destination, report, key event và consumer bị ảnh hưởng.
4. **Chạy Debug/QA.** Hoàn tất Gate 2 bằng [Debug/QA test matrix](08-debug-qa-answer-vn.md). Bảo toàn first failing layer, evidence, defect status và retest result.
5. **Chuẩn bị release.** Resolve workspace conflict, lưu version có tên rõ ràng, attach QA/report/monitoring records và hoàn tất Gate 3 approval.
6. **Publish và smoke test.** Chỉ publish vào environment đã định, sau đó chạy production smoke test nhỏ nhất đã được phê duyệt.
7. **Observe và validate.** Kiểm tra immediate signals, so sánh với baseline và validate processed reports hoặc Explorations sau expected processing delay.
8. **Close hoặc xử lý incident.** Hoàn tất Gate 4 và ghi outcome. Nếu change không an toàn hoặc sai material, contain hoặc rollback, mở incident và ghi affected period.

### Handoff map

| Giai đoạn | Source/owner chính | Evidence bàn giao cho giai đoạn tiếp theo |
| --- | --- | --- |
| Requirement | Measurement Plan / Product và Analytics | Plan, event contract, schema/lifecycle decision, consent/privacy decision |
| Implementation | Sections 01–06 / Developer và GTM implementer | Application/Data Layer change, workspace diff, affected consumers, expected request count |
| QA | Debug/QA / QA reviewer | Test matrix, layer evidence, defect/retest status, release recommendation |
| Reporting | Reports và Charts / Analytics owner | Field readiness, report configuration, interpretation hoặc impact note `N/A` |
| Release | Section 10 / Publisher hoặc approver | Release record, named version, target environment, approval, rollback path |
| Monitoring | Section 10 / Monitoring owner | Baseline, thresholds, observation result, incident hoặc closure decision |

### Các trường hợp ngoại lệ

- **Không có contract hoặc implementation change:** không tạo release giả; ghi rõ lý do và dùng report hoặc operations workflow phù hợp.
- **QA fail trước khi publish:** hold release, bảo toàn evidence, sửa first failing layer và chạy lại đúng test case đó.
- **Production fail:** contain surface nhỏ nhất và an toàn nhất trước; sau đó quyết định fix forward hay rollback. Không chờ processed report nếu đã thấy rõ privacy, routing hoặc duplicate key-event risk.
- **Report-only change:** dùng report requirements, configuration, QA và interpretation records của Section 09. Không dùng GTM rollback làm recovery mechanism.

## Nguyên tắc vận hành

- Giữ thay đổi nhỏ, liên quan với nhau và có thể test độc lập.
- Tách environment development/QA khỏi production destination.
- Mọi thay đổi material về event/tag phải có reference tới Measurement Plan.
- Duy trì một chuỗi truy vết từ Measurement Plan và schema version tới GTM objects, QA evidence, report/configuration IDs, release version và monitoring record.
- Dùng Preview và network evidence thực tế, không chỉ dựa vào trạng thái “Tag Fired”.
- Publish một version có tên, mô tả và owner rõ ràng.
- Ưu tiên release có thể đảo ngược; nếu không thể thì phải ghi rõ rủi ro và cách xử lý.
- Xác định baseline metrics và observation window trước khi publish.
- Xem consent, privacy, routing, duplication và key-event integrity là release gates.
- Ghi nhận affected period ngay cả khi rollback thành công.
- Validate processed reporting data sau expected delay; Realtime và DebugView là bằng chứng chẩn đoán, không thay thế reporting validation.
- Không tùy tiện tạo data filter hoặc quyết định xóa data; các hành động này có thể không thể đảo ngược hoặc chỉ có tác dụng từ thời điểm kích hoạt trở đi.

## Vai trò và trách nhiệm

| Vai trò | Trách nhiệm |
| --- | --- |
| Requester/product owner | Xác định lý do kinh doanh và success criterion. |
| Developer | Implement application/Data Layer contract và cung cấp build context. |
| GTM implementer | Cấu hình tags, triggers, variables, consent, routing và version. |
| Analytics owner | Review event meaning, destinations, reports, key events và baselines. |
| QA reviewer | Chạy test matrix và xác nhận evidence. |
| Privacy/security reviewer | Review PII, consent, access và destination risk khi phù hợp. |
| Publisher/approver | Xác nhận gates, publish và quyết định rollback. |
| Incident owner | Điều phối impact assessment, mitigation, communication và follow-up. |

Trong team nhỏ, một người có thể đảm nhiệm nhiều vai trò. Tuy nhiên, release record vẫn phải ghi rõ từng trách nhiệm.

## Change Traceability (truy vết thay đổi)

Với mọi material change, duy trì một chuỗi reference. Nếu một layer không bị ảnh hưởng, ghi `N/A` thay vì để không rõ impact:

```text
Requirement / Measurement Plan
  → schema hoặc lifecycle change
  → Data Layer và GTM implementation
  → consent/privacy decision
  → Debug/QA evidence
  → GA4 report, custom definition hoặc Exploration impact
  → GTM release version
  → monitoring record và affected-period assessment
```

[Measurement Plan](07-measurement-plan-answer-vn.md) là source of truth cho event meaning và schema. [Debug/QA](08-debug-qa-answer-vn.md) là source of truth cho test evidence và defect status. [Reports và Charts](09-reports-charts-answer-vn.md) là source of truth cho report configuration, field readiness và interpretation.

## GTM Workspaces, Environments và Versions

### Workspaces

Dùng một workspace cho một nhóm thay đổi liên quan sẽ được test và version cùng nhau. Tách các initiative không liên quan để reviewer hiểu được diff và rollback không vô tình hoàn tác thay đổi khác.

Phạm vi workspace phù hợp:

- một measurement journey cùng các variables/triggers/tags cần thiết;
- một consent hoặc routing change cùng configuration hỗ trợ;
- một vendor/template update có phạm vi hẹp;
- một bug fix có kiểm soát.

Tránh gom tag cleanup, ecommerce implementation mới, consent change và production hotfix không liên quan vào cùng workspace. Cách này khó test và không an toàn khi rollback.

Theo [Workspaces guidance](https://support.google.com/tagmanager/answer/7059647) của Google, workspace là nơi gom một nhóm thay đổi để trở thành version; thay đổi nên nhỏ, liên quan và có tên rõ ràng.

### Environments

Dùng các environment Dev/QA/Staging/Live rõ ràng nếu website deployment của tổ chức hỗ trợ. Mỗi environment phải có:

- URL/hostname được ghi nhận;
- container snippet hoặc preview method chính xác;
- GA4 destination/Measurement ID đã được phê duyệt;
- routing rule an toàn theo environment;
- quy định về access và data handling;
- chính sách test account/test data.

Hostname không xác định không được âm thầm fallback về production. QA environment gửi data vào production là release-blocking defect.

Xem [GTM environments](https://support.google.com/tagmanager/answer/6311518).

### Versions và approvals

Trước khi publish:

1. Review toàn bộ thay đổi trong workspace.
2. Chạy Preview và test matrix bắt buộc.
3. Xác nhận network destination và payload thực tế.
4. Ghi version name, description, owner, ticket và release window.
5. Lấy đủ approvals.
6. Chỉ publish vào environment đã định.
7. Lưu published version và publish history làm evidence.

GTM version là snapshot và có thể dùng version trước làm latest để recovery. Native approval request chỉ có trong Tag Manager 360; với account thường, dùng peer-approval có document và yêu cầu cùng bộ evidence, đồng thời tách người implement khỏi người review/publish. Xem [Publishing, versions, and approvals](https://support.google.com/tagmanager/answer/6107163).

## Release Gates

### Gate 0 — Requirement readiness

- [ ] Business question và decision đã được document.
- [ ] Measurement Plan ID/version, event contract, schema version và schema/lifecycle change ID (nếu có) đã được ghi nhận.
- [ ] Data Layer source và authoritative business moment đã rõ.
- [ ] Population/grain hoặc reporting requirement khác đã được ghi nhận nếu release ảnh hưởng reporting.
- [ ] Privacy, consent, destination, key-event và custom-definition decisions đã hoàn tất.
- [ ] Đã có phương án rollback/mitigation.

### Gate 1 — Implementation readiness

- [ ] Naming, variables, triggers, tags, templates, folders và descriptions tuân theo standards.
- [ ] Environment routing rõ ràng và fail safely.
- [ ] Expected request count đã được document.
- [ ] Enhanced measurement và legacy paths đã được kiểm tra overlap.
- [ ] Custom definitions và report requirements đã được xác định.
- [ ] Shared variables, triggers, tags, templates và downstream consumers bị ảnh hưởng đã được xác định.
- [ ] Khi template thay đổi, source, sandbox permissions, owner và update impact đã được review.
- [ ] Environment và destination routing fail safely với context không xác định hoặc không được hỗ trợ.

### Gate 2 — QA readiness

- [ ] Positive, negative, duplicate, boundary, SPA/navigation, consent, privacy và routing tests đều pass.
- [ ] Data Layer, GTM, network, DebugView và report evidence đã được capture khi phù hợp.
- [ ] Processed report validation đã được schedule nếu chưa thể có trong release window.
- [ ] Consent denied/granted behavior khớp design đã được phê duyệt; không mặc định rằng denied nghĩa là không có network request.
- [ ] Tất cả defect đã được fix, được accept kèm owner/date, hoặc block release theo severity.
- [ ] Test property/stream và browser/device đã được ghi nhận.

### Gate 3 — Publish readiness

- [ ] Workspace chỉ chứa các thay đổi dự kiến.
- [ ] Workspace đang current; conflict hoặc out-of-date changes đã được resolve.
- [ ] Version name và description giải thích rõ thay đổi.
- [ ] Environment và publisher chính xác đã được xác nhận.
- [ ] Release window và observation period đã được thống nhất.
- [ ] Rollback version và incident contact đã sẵn sàng.
- [ ] QA, report và monitoring records liên quan đã được attach.

### Gate 4 — Post-publish readiness

- [ ] Production smoke test pass với safe data.
- [ ] Container version và destination chính xác đã được xác nhận.
- [ ] Không quan sát thấy request duplicate/missing bất thường.
- [ ] Realtime/DebugView checks đã hoàn tất.
- [ ] Processed report validation đã được schedule.
- [ ] Monitoring owner chấp nhận kết quả hoặc mở incident.

## Release Record Template

```text
Release ID / Mã release:
Change title / Tên thay đổi:
Business purpose / Mục đích kinh doanh:
Change type / Loại thay đổi: New / fix / schema / consent / routing / template / report impact
Measurement Plan ID/version / Mã và version Measurement Plan:
Requirement/event IDs / Mã requirement và event:
Schema/lifecycle change ID / Mã thay đổi schema/lifecycle:
Traceability ID / Mã truy vết:
Affected journey and downstream consumers / Journey và consumer bị ảnh hưởng:
GTM account/container / Tài khoản và container GTM:
Workspace / Workspace:
Version / Version:
Source/build/application version / Version source/build/application:
Target environment / Environment đích:
GA4 property/stream/Measurement ID / GA4 property/stream/Measurement ID:
Consent/privacy impact / Ảnh hưởng consent/privacy:
Tags/triggers/variables/templates changed / Tags/triggers/variables/templates thay đổi:
Expected new/changed events / Event mới hoặc thay đổi dự kiến:
Expected request count / Số request dự kiến:
QA evidence / Bằng chứng QA:
Report/custom-definition/configuration IDs / Mã report/custom definition/configuration:
Monitoring specification/record ID / Mã monitoring specification/record:
Known limitations / Giới hạn đã biết:
Approvers / Người approve:
Publisher / Người publish:
Release date/timezone / Ngày release/timezone:
Observation window / Observation window:
Rollback version/mitigation / Version rollback/biện pháp giảm thiểu:
Monitoring owner / Owner monitoring:
Final outcome / Kết quả cuối:
Affected period if incident / Khoảng thời gian bị ảnh hưởng nếu có incident:
```

## Production Smoke Test

Chạy bài test nhỏ nhất nhưng đủ exercise changed path:

1. Xác nhận production hostname và published container version.
2. Xác nhận test identity/data và consent state đã được phê duyệt.
3. Thực hiện một business action có kiểm soát.
4. Xác nhận application outcome và business moment dự kiến.
5. Kiểm tra Data Layer event count và payload.
6. Kiểm tra GTM/Tag Assistant khi session cho phép.
7. Kiểm tra network request, payload, destination và request count.
8. Kiểm tra Realtime/DebugView khi phù hợp.
9. Dừng test nếu phát hiện PII, wrong destination, duplicate key event hoặc severe error.
10. Ghi evidence, timestamp, browser/device, consent state, result và next check.

Không tạo production key event hoặc Google Ads conversion chỉ để chứng minh release nếu business owner hoặc privacy owner chưa phê duyệt test method an toàn.

## Monitoring Specification

Monitoring Specification là operating plan để theo dõi một release sau khi publish. Nó trả lời 5 câu hỏi: **theo dõi điều gì, đo bằng cách nào, kiểm tra khi nào, ai chịu trách nhiệm và phải làm gì khi kết quả bất thường**.

Nó không tự nó là một release gate. Các gate sử dụng Monitoring Specification và evidence của nó để ra quyết định:

```text
Monitoring Specification = kế hoạch quan sát thay đổi
Release Gate             = điểm ra quyết định dựa trên kế hoạch và evidence
```

Không cần tạo đầy đủ mọi field cho mọi thay đổi nhỏ. Hãy tạo specification hoàn chỉnh cho material change có thể ảnh hưởng event delivery, key events, consent/privacy, routing, ecommerce, reporting hoặc critical user journey. Với layer không bị ảnh hưởng, ghi `N/A` và lý do.

### Cách sử dụng specification

1. **Trước implementation:** xác định event, report, destination, business outcome, downstream consumer và source of truth bị ảnh hưởng.
2. **Trước publish:** chọn signals, định nghĩa population/grain, ghi baseline và thresholds, chỉ định monitoring owner và đặt observation window.
3. **Trong immediate release window:** kiểm tra destination, request count, consent, duplicate/missing behavior và journey đã thay đổi.
4. **Sau normal processing:** validate processed Report hoặc Exploration, gồm population, grain, scope, filters, Data quality state và các indicator `(other)`/thresholding/sampling.
5. **Khi close:** ghi kết quả vào release record. Chọn `Close`, `Incident`, `Contain`, `Rollback` hoặc `Accept exception`.

### Monitoring record template

Dùng một record cho mỗi material release hoặc monitored asset. Liên kết `Monitoring ID` vào Release Record.

```text
Monitoring ID:
Release ID:
Event/report/journey được monitor:
Business outcome:
Signal và metric definition:
Population và grain:
Baseline period và mức độ complete:
Expected range:
Warning threshold:
Release-blocking threshold:
Observation window:
Check frequency:
Source of truth:
Monitoring owner:
Escalation owner:
Response action:
Final observation result:
Evidence links:
```

Ví dụ cho registration release:

```text
Signal: sign_up events trên mỗi confirmed account creation
Population: confirmed account creations trong observation window
Grain: một confirmed account / một sign_up dự kiến
Source of truth: registration service và processed GA4 report
Action: contain hoặc rollback nếu xác nhận duplicate key event hoặc material drop
```

### Mối liên hệ với release gates

| Gate | Monitoring Specification được sử dụng như thế nào |
| --- | --- |
| Gate 0 — Requirement readiness | Quyết định có cần monitoring hay không, đồng thời định nghĩa business outcome và source of truth. |
| Gate 1 — Implementation readiness | Xác định signals, destinations, reports, key events và downstream consumers bị thay đổi. |
| Gate 2 — QA readiness | Kiểm tra expected request count, duplicate/missing behavior, consent behavior và evidence sẽ dùng cho monitoring. |
| Gate 3 — Publish readiness | Xác nhận monitoring record, baseline, thresholds, owner, escalation path và observation window đã sẵn sàng. |
| Gate 4 — Post-publish readiness | Dùng monitoring result và processed report validation để close release hoặc mở incident. |

Monitoring phải phát hiện cả transport failure và semantic failure. Request vẫn có thể tiếp tục đến trong khi business meaning đã sai.

### Signals

| Signal | Ví dụ phép đo | Ý nghĩa | Tần suất |
| --- | --- | --- | --- |
| Collection volume | Event count theo canonical event | Phát hiện event bị thiếu hoặc fire quá nhiều | Daily/release window |
| Unique users | Users/key-event users | Phát hiện thay đổi lớn về delivery | Daily/weekly |
| Key events | Key-event count/rate | Bảo vệ business outcome reporting | Release + daily |
| Duplicate rate | Events trên mỗi confirmed business occurrence | Phát hiện double tag, retry hoặc remount | Release + daily |
| Missingness | Tỷ lệ required parameter bị null/omitted | Phát hiện schema regression | Release + daily |
| Vocabulary | Parameter values ngoài danh sách dự kiến | Phát hiện app/GTM contract drift | Daily/weekly |
| Destination | QA so với production ID và hostname | Phát hiện routing error | Every release |
| Consent behavior | Tag/request behavior khi granted/denied | Phát hiện privacy regression | Every consent change |
| Report freshness | Data delay và incomplete period | Ngăn decision quá sớm | Daily |
| Data quality | Thresholding, sampling, `(other)` và field availability indicators | Phân biệt platform/report limitation với collection defect | Release + daily |
| Report configuration | Population, grain, scope, filters và source-of-truth surface | Phát hiện semantic drift sau thay đổi | Every report change |
| Source/medium | Thay đổi direct/referral/campaign | Phát hiện attribution hoặc linker change | Daily/weekly |
| Ecommerce | Transactions/revenue so với source of truth | Bảo vệ financial reporting | Daily |

### Baselines và thresholds

Không dùng một percentage threshold chung cho mọi trường hợp nếu chưa có baseline. Xác định giá trị bình thường theo event, environment, day-of-week, release cadence và traffic mix.

Ghi nhận:

- baseline period và mức độ complete;
- metric definition, population, grain, scope và source-of-truth surface;
- expected range và seasonality;
- warning threshold;
- release-blocking threshold;
- alert owner và response time;
- campaign hoặc product change đã biết có thể giải thích biến động;
- source of truth dùng để reconciliation.

Policy khởi điểm, cần được calibrate theo dữ liệu thực tế:

```text
Critical: Có PII hoặc unauthorized destination; dừng và escalate ngay.
High: Key event bị thiếu, duplicate ở quy mô lớn hoặc bị đếm sai material.
Medium: Required parameter bị thiếu ở một nhóm đáng kể hoặc chỉ ảnh hưởng một browser/route.
Low: Vocabulary/documentation drift nhỏ, chưa ảnh hưởng decision hiện tại.
```

Statistical anomaly detection có thể hỗ trợ review nhưng không thay thế measurement contract hoặc incident owner. Google mô tả anomaly detection là phương pháp thống kê cho time-series data; xem [Anomaly detection](https://support.google.com/analytics/answer/9517187).

## Monitoring Workflow

### Immediate release window

- Xác nhận version, hostname, stream và timestamp.
- Kiểm tra event đã thay đổi và các event liền kề.
- Xác nhận không có duplicate request hoặc tag ngoài dự kiến.
- Xác nhận consent và privacy behavior.
- Xác nhận key-event và ecommerce behavior nếu bị ảnh hưởng.

### Short observation window

- So sánh volume với pre-release baseline.
- Kiểm tra required parameters bị null/missing và values ngoài dự kiến.
- Kiểm tra khác biệt theo route/browser/device.
- Kiểm tra source/medium/referral change nếu navigation hoặc domain thay đổi.
- Validate processed report hoặc Exploration sau normal processing; kiểm tra population, grain, scope, filters, Data quality indicator và trạng thái `(other)`/thresholding/sampling.
- So sánh business outcomes quan trọng với approved source system nếu có.

### Periodic operations

- Review event và parameter inventory so với configuration đang active.
- Review tags, triggers, variables, templates, reports, audiences và exports đã obsolete.
- Review access, owners, integrations, consent/privacy decisions và data filters.
- Reconcile ecommerce/key-event quan trọng với source system.
- Review report owners, field readiness, report configuration, monitoring thresholds và retirement triggers.

## Incident Response

### Detect và classify

Dùng cách xác định first failing layer và severity guidance trong [Debug/QA](08-debug-qa-answer-vn.md). Monitoring alert là tín hiệu cần điều tra, không tự chứng minh GTM là root cause.

Ghi nhận:

- first observed time và timezone;
- last known good version/configuration;
- property, stream, environment, event, report và user journey bị ảnh hưởng;
- vấn đề là missing, duplicated, misrouted, malformed, privacy-related hay attribution-related;
- estimated affected volume và period;
- evidence ở application, Data Layer, GTM, network, DebugView và report layers.

### Contain

Chọn hành động an toàn và nhỏ nhất:

- pause hoặc block faulty tag;
- publish last known-good GTM version;
- sửa environment routing;
- disable broken enhancement nếu an toàn;
- dừng server/offline sender hoặc retry loop;
- ngăn việc tiếp tục collect PII;
- không kích hoạt permanent data filter như emergency shortcut nếu chưa có approval của owner phù hợp.

### Recover và communicate

- chạy lại smoke test;
- validate event bị ảnh hưởng và các flow liền kề;
- nói rõ data nào không thể sửa hồi tố;
- thông báo cho report/marketing/product owners bị ảnh hưởng;
- document affected period, decision impact và follow-up fix;
- thêm regression test hoặc monitoring signal để giảm khả năng lặp lại.

### Không mặc định rollback sẽ sửa được data

Rollback GTM container chỉ dừng hoặc thay đổi client-side behavior trong tương lai. Nó không xóa event đã process, không sửa key-event count sai và không khôi phục data đã bị loại bởi permanent GA4 filter. Hãy định lượng affected period và annotate các downstream decision.

## Data Filters và Developer Traffic

GA4 data filter tác động lên incoming event từ thời điểm activation trở đi và có thể permanently remove excluded data khỏi Analytics và BigQuery. Hãy test filter trước khi activate và phân biệt developer/internal traffic handling với report filtering thông thường. Xem [Data filters](https://support.google.com/analytics/answer/13296761).

Trước khi activate filter:

- định nghĩa traffic classification;
- validate Testing mode;
- xác nhận debug/QA access vẫn hoạt động;
- ghi nhận environment bị ảnh hưởng và IP/hostname rules;
- lấy approval;
- ghi activation time và irreversible impact.

## Rollback Runbook

1. Xác nhận incident và severity; không rollback chỉ vì một fluctuation nhỏ chưa giải thích được.
2. Xác định last known-good GTM version và nội dung của version đó.
3. Kiểm tra root cause nằm ở GTM, application/Data Layer, GA4 property configuration, consent hay external system.
4. Đánh giá rollback sẽ đồng thời hoàn tác những thay đổi nào khác.
5. Lấy quyết định của publisher/incident owner theo severity.
6. Set/publish approved previous version vào đúng environment.
7. Chạy production smoke test cho changed path và các critical path liền kề.
8. Kiểm tra destination, request count, consent, DebugView/Realtime và report sau đó.
9. Ghi rollback time, version, evidence, affected period và remaining data-quality impact.
10. Tạo corrective change và regression tests trước khi re-release.

Nếu root cause ở application-side, GA4 property-side hoặc server-side, GTM rollback có thể không giúp ích và còn làm trạng thái khó phân tích hơn.

## Release decision và closure

- **Go:** Gates 0–3 pass, QA evidence đã link, version/environment/destination chính xác đã được xác nhận và đã có owner cho rollback hoặc containment.
- **Hold:** Business moment chưa rõ, chưa xác định được first failing layer, routing sai, có prohibited data hoặc duplicate/missing key-event behavior chưa được xử lý.
- **Accept exception:** Risk còn lại có phạm vi rõ, không block, và có accountable owner, mitigation, due date, reviewer cùng monitoring action.
- **Close:** Gate 4 chỉ hoàn tất sau observation window, processed report validation, affected-period assessment và final release outcome.

## Tài liệu tham khảo chính thức

- [GTM Workspaces](https://support.google.com/tagmanager/answer/7059647)
- [GTM Environments](https://support.google.com/tagmanager/answer/6311518)
- [Publishing, versions, and approvals](https://support.google.com/tagmanager/answer/6107163)
- [Preview and debug containers](https://support.google.com/tagmanager/answer/6107056)
- [Data filters](https://support.google.com/analytics/answer/13296761)
- [Monitor events in DebugView](https://support.google.com/analytics/answer/7201382)
- [Realtime report](https://support.google.com/analytics/answer/9271392)
- [Anomaly detection](https://support.google.com/analytics/answer/9517187)
- [GA4 data freshness](https://support.google.com/analytics/answer/11198161)
- [Data quality](https://support.google.com/analytics/answer/12856703)
- [Data compatibility](https://support.google.com/analytics/answer/11608978)
- [Data differences between Reports and Explorations](https://support.google.com/analytics/answer/9371379)
- [Custom dimensions and metrics](https://support.google.com/analytics/answer/14240153)
