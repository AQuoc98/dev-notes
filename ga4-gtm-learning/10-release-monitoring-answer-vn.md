# 10 — Quản lý Release GTM và Monitoring GA4

## 1. Tổng quan

### 1.1 Mục tiêu

Thiết lập quy trình có thể truy vết để đưa một thay đổi GTM/GA4 từ yêu cầu đã được phê duyệt đến release an toàn, theo dõi sau release và đóng release hoặc xử lý incident.

Mọi thay đổi tracking đều có thể ảnh hưởng production. Preview có thể pass nhưng bản publish vẫn có thể gửi nhầm property, gửi trùng event, vi phạm consent/privacy hoặc làm thay đổi ý nghĩa báo cáo.

### 1.2 Phạm vi

- Phân loại change, phân công owner, quản lý workspace/environment/version, approval, publication, smoke test, monitoring, containment và rollback.
- Các thay đổi material đối với GTM configuration, event routing, consent/privacy, destination, key event, custom definition, report hoặc data quality.
- Release Record và Monitoring Record có liên kết với Sections 01–09.
- Theo dõi thực tế: event volume, duplicate/missingness, chất lượng parameter, destination, consent, processed report và data quality.
- Phân loại risk/status, lịch monitoring, escalation và nơi lưu evidence/quyền truy cập/thời hạn lưu.

### 1.3 Ranh giới với các section khác

| Source of truth | Phạm vi sở hữu | Section 10 sử dụng để |
|---|---|---|
| Section 07 — Measurement Plan | Event meaning, schema, occurrence, consent/privacy và destination decision | Xác nhận requirement và release impact |
| Sections 01–06 | Data Layer, Variables, Triggers, Tags, Consent và Template configuration | Xác định implementation scope và object bị ảnh hưởng |
| Section 08 — Debug/QA | Runtime test matrix, layer evidence và defect status | Xác nhận QA gate và smoke-test evidence |
| Section 09 — Reports/Charts | Field readiness, report configuration và interpretation | Đánh giá report impact và processed-data |
| Section 10 — Release Monitoring | Release decision, observation, incident, rollback và closure | Truy vết đầu-cuối |

Section 10 không định nghĩa lại event contract, test matrix, report formula hoặc template internals. Những nội dung đó được tham chiếu từ section sở hữu và được dùng để quyết định release/closure.

### 1.4 Vòng đời release

```text
Phân loại change
  → tạo Release Record
  → kiểm tra requirement và implementation
  → chạy QA ở Section 08
  → tạo version và approval
  → publish vào đúng environment
  → production smoke test
  → observation và kiểm tra processed data
  → Go, Hold, Accept exception, Close hoặc Incident
```

Rollback khôi phục configuration của GTM cho hành vi phát sinh sau đó. Rollback không xóa/sửa event đã được GA4 xử lý, không hoàn tác property setting và không khôi phục dữ liệu đã bị loại bởi permanent data filter.

## 2. Chuẩn bị release

### 2.1 Khi nào tạo Release Record

Tạo Release Record trước khi implementation cho material change có thể ảnh hưởng tracking behavior, consent/privacy, routing, destination, key event, schema, report hoặc data quality. Nếu một layer hoặc downstream asset không bị ảnh hưởng, ghi `N/A` và lý do.

| Loại change | Cách xử lý release |
|---|---|
| Không có implementation/configuration change | Ghi nhận quyết định và dùng planning/operations workflow phù hợp. |
| Chỉ thay đổi report | Dùng requirement/configuration/interpretation records của Section 09; không cần GTM rollback. |
| Thay đổi GTM Variable, Trigger, Tag, consent, routing hoặc template | Dùng workspace riêng, QA ở Section 08, named version và smoke test. |
| Thay đổi event/schema/key event hoặc GA4 property | Link decision ở Section 07, report bị ảnh hưởng, QA evidence và processed-data follow-up. |
| Production incident hoặc containment khẩn cấp | Mở/cập nhật Release Record, ghi last known-good state và dùng các bước incident của Section 10. |

Dùng risk level dưới đây để xác định mức review và monitoring. Team có thể điều chỉnh ví dụ sau project đầu tiên:

| Risk level | Loại change thường gặp | Xử lý tối thiểu |
|---|---|---|
| Low | Thay đổi layout report, naming hoặc documentation, không ảnh hưởng collection | Owner review và record phù hợp của Section 09. |
| Medium | Thay đổi Variable, Trigger, Tag, routing hoặc parameter không breaking | Section 08 QA, named version, approval, smoke test và observation window ngắn. |
| High | Thay đổi event/schema, consent/privacy, destination, key event, GA4 property hoặc permanent filter | Cross-functional approval, full QA, production smoke test an toàn, escalation rõ và processed-data validation. |

### 2.2 Bộ hồ sơ release tối thiểu

Mỗi material release cần liên kết:

- Release Record có owner, scope, environment, change type và journey bị ảnh hưởng;
- Measurement Plan/schema decision đã approve và các implementation references;
- kết quả/evidence của Section 08;
- report/configuration impact của Section 09 nếu reporting bị ảnh hưởng;
- GTM version, publisher, target environment và rollback/mitigation path;
- Monitoring Record, smoke-test result và final outcome hoặc incident.

### 2.3 Release Record

```text
Release ID:
Project, change hoặc incident ID:
Status: Draft / Review / Approved / In QA / Published / Monitoring / Pending / Closed / Blocked
Risk level: Low / Medium / High
Risk rationale:
Tên change và business purpose:
Change type: New / fix / schema / consent / routing / template / report impact
Measurement Plan và requirement IDs:
Schema/lifecycle decision ID:
Journey bị ảnh hưởng và downstream consumers:
GTM account/container và workspace:
Source/build/application version:
Target environment và GA4 property/stream:
GTM objects hoặc GA4 settings đã thay đổi:
Event dự kiến và request count:
Consent/privacy và destination impact:
Production smoke-test method và approval:
Section 08 QA evidence:
Section 09 report/configuration IDs:
Monitoring ID:
Version, approvers, publisher và release window:
Rollback version hoặc mitigation:
Observation window và monitoring owner:
Evidence location, access restriction và retention period:
Final outcome và affected period nếu có incident:
```

### 2.4 Vai trò và trách nhiệm

| Vai trò | Trách nhiệm trong release |
|---|---|
| Requester/product owner | Nêu business purpose và success criterion. |
| Developer | Thay đổi Application/Data Layer và cung cấp build context. |
| GTM implementer | Cấu hình Variables, Triggers, Tags, consent, routing và workspace version. |
| Analytics owner | Đánh giá event/report impact, field readiness, baseline và interpretation. |
| QA reviewer | Chạy Section 08 và review evidence. |
| Privacy/security reviewer | Review PII, consent, access và destination risk khi phù hợp. |
| Publisher/approver | Quyết định gate, publication và rollback approval. |
| Monitoring/incident owner | Theo dõi, escalate, đánh giá impact và follow-up. |

Một người có thể giữ nhiều vai trò, nhưng Release Record vẫn phải ghi rõ từng trách nhiệm.

### 2.5 Chuỗi truy vết

Giữ một chuỗi reference duy nhất. Nếu layer không bị ảnh hưởng, ghi `N/A` và lý do:

```text
Measurement Plan/schema decision
  → implementation và GTM objects bị ảnh hưởng
  → Section 08 QA evidence
  → Section 09 report/configuration impact
  → named GTM version và target environment
  → Monitoring Record và affected-period assessment
```

## 3. Triển khai release

### 3.1 Release gates

| Gate | Evidence tối thiểu trước khi tiếp tục |
|---|---|
| Gate 0 — Requirement readiness | Measurement Plan/schema decision đã approve, business outcome, report bị ảnh hưởng, consent/privacy/destination decision và rollback/mitigation approach. |
| Gate 1 — Implementation readiness | Workspace có phạm vi rõ, routing an toàn, naming standards, expected count, overlap check, affected consumers và template review khi cần. |
| Gate 2 — QA readiness | Kết quả positive/negative/duplicate/consent/privacy/routing ở Section 08, first-failing-layer status, evidence links và processed-data follow-up khi cần. |
| Gate 3 — Publish readiness | Workspace hiện tại, đúng environment, named version, approval, release/observation window, rollback path và linked records. |
| Gate 4 — Post-publish readiness | Smoke test, version/destination check, immediate signals, processed-data check theo lịch và Monitoring Record outcome hoặc incident. |

Không lặp lại test matrix của Section 08 ở đây. Chỉ link scenario ID và evidence ID vào Release Record.

### 3.2 Quy tắc workspace và environment

- Một workspace chỉ nên chứa một nhóm thay đổi liên quan, có thể test độc lập và version cùng nhau.
- Tách cleanup, ecommerce, consent change và hotfix không liên quan thành workspace khác.
- Ghi hostname/URL, container snippet hoặc preview method, GA4 destination, routing rule, access và test-data policy cho từng environment.
- Hostname không xác định phải fail safely; QA gửi vào production là lỗi blocking.
- Kiểm tra enhanced measurement hoặc legacy path có thể tạo overlap với event đang đổi.

Xem thêm [GTM Workspaces](https://support.google.com/tagmanager/answer/7059647) và [GTM Environments](https://support.google.com/tagmanager/answer/6311518).

### 3.3 Version, approval và publication

Trước khi publish:

1. Review toàn bộ workspace diff và xử lý conflict.
2. Chạy Preview và test matrix đã được approve ở Section 08.
3. Kiểm tra Network destination và payload thực tế khi áp dụng.
4. Lưu named version có description, owner, ticket và release window.
5. Nhận approval đã được ghi nhận; xác nhận publisher và target environment.
6. Chỉ publish vào environment đã định và lưu publish history làm evidence.

GTM version là snapshot có thể khôi phục. Nếu native approval request không khả dụng, dùng peer-approval có ghi nhận evidence và separation of duties tương đương. Xem [Publishing, versions và approvals](https://support.google.com/tagmanager/answer/6107163).

### 3.4 Production smoke test

Chạy test nhỏ nhất nhưng vẫn đi qua đúng flow bị thay đổi. Dùng phương pháp an toàn đã được phê duyệt (ví dụ QA route, synthetic/test account hoặc allowlisted test identity) và không tạo dữ liệu khách hàng thật.

1. Xác nhận hostname, published container version, GA4 property/stream và consent state.
2. Dùng test identity/data đã được approve, thực hiện một business action có kiểm soát.
3. Xác nhận application outcome và business moment dự kiến.
4. Kiểm tra Data Layer/GTM evaluation nếu có; kiểm tra Network request, payload, destination và count.
5. Kiểm tra Realtime/DebugView khi phù hợp. Đây là tín hiệu gần thời gian thực/chẩn đoán, không phải bằng chứng processed report.
6. Dừng và escalate khi có PII, sai destination, duplicate key event hoặc lỗi nghiêm trọng.
7. Ghi timestamp, browser/device, consent, kết quả, evidence và lần kiểm tra tiếp theo.

Không tạo key event hoặc conversion chỉ để kiểm tra release nếu chưa có phương pháp test an toàn được approve.

## 4. Triển khai monitoring

### 4.1 Monitoring Record

Dùng một Monitoring Record cho mỗi material release hoặc asset cần theo dõi. Record phải trả lời: theo dõi gì, đo như thế nào, kiểm tra khi nào, ai chịu trách nhiệm và kết quả bất thường sẽ được xử lý ra sao.

```text
Monitoring ID:
Release ID:
Status: Draft / Review / Monitoring / Pending / Closed / Blocked
Risk level: Low / Medium / High
Event/report/journey được theo dõi:
Business outcome:
Signal và metric definition:
Population, grain và scope:
Baseline period và mức độ đầy đủ:
Expected range hoặc seasonality:
Warning threshold:
Release-blocking threshold:
Observation window và check frequency:
Source of truth:
Monitoring owner và escalation owner:
Escalation channel và response target:
Response action:
Observation result và decision:
Evidence links:
Evidence location, access restriction và retention period:
```

Material change cần record đầy đủ. Signal/layer không bị ảnh hưởng ghi `N/A` kèm lý do.

### 4.2 Các tín hiệu cần theo dõi

Kiểm tra nhóm tối thiểu cho mọi material release. Chỉ thêm tín hiệu tùy chọn khi asset hoặc business outcome bị thay đổi yêu cầu.

#### Tín hiệu tối thiểu

| Signal | Ví dụ measure | Quyết định hỗ trợ |
|---|---|---|
| Collection volume | Event count theo canonical event | Phát hiện thiếu event hoặc fire quá nhiều. |
| Business outcome | Key-event/user count hoặc outcome từ hệ thống nguồn | Phát hiện thay đổi material ở kết quả kinh doanh. |
| Duplicate/missingness | Event trên mỗi occurrence; tỷ lệ thiếu required parameter | Phát hiện duplicate tag, retry, remount hoặc schema regression. |
| Destination | Measurement ID và hostname theo environment | Phát hiện routing sai. |
| Consent/privacy | Hành vi tag/request khi granted hoặc denied | Phát hiện privacy regression. |
| Report freshness | Processing delay và ngày dữ liệu chưa hoàn tất | Tránh quyết định quá sớm. |

#### Tín hiệu tùy chọn

| Signal | Ví dụ measure | Quyết định hỗ trợ |
|---|---|---|
| Vocabulary | Parameter value/casing ngoài danh sách | Phát hiện contract drift giữa Application và GTM. |
| Data quality | Thresholding, sampling, `(other)` và field availability | Phân biệt giới hạn nền tảng/report với lỗi collection. |
| Report semantics | Population, grain, scope, filter và source-of-truth surface | Phát hiện semantic drift sau thay đổi report. |
| Ecommerce hoặc source critical khác | Transaction/outcome so với source of truth đã approve | Reconcile dữ liệu quan trọng khi nằm trong scope. |

Monitoring phải phát hiện cả lỗi vận chuyển và lỗi ý nghĩa. Request đến GA4 chưa có nghĩa là business event đúng.

### 4.3 Baseline và threshold

Không dùng một ngưỡng phần trăm chung cho mọi event. Ghi rõ:

- baseline period và mức độ đầy đủ;
- metric definition, population, grain, scope và source of truth;
- expected range, day-of-week pattern và product change đã biết;
- warning threshold và release-blocking threshold;
- owner, escalation path và response time.

Có thể dùng chính sách severity dưới đây làm điểm bắt đầu, sau đó hiệu chỉnh theo baseline:

```text
Critical: Có PII hoặc destination chưa được phép; dừng và escalate.
High: Key event biến mất, bị duplicate ở quy mô lớn hoặc bị đếm sai material.
Medium: Required parameter thiếu ở một phần đáng kể hoặc chỉ ảnh hưởng một route/browser.
Low: Vocabulary/documentation drift nhỏ, chưa ảnh hưởng quyết định hiện tại.
```

### 4.4 Các observation window

**Immediate release window**

- Xác nhận version, hostname, property/stream, timestamp, event thay đổi, destination, consent và request count.
- Dùng evidence của Section 08 để xác nhận runtime; trạng thái “Tag Fired” một mình không đủ.

**Short observation window**

- So sánh volume và business outcome với baseline trước release.
- Kiểm tra missing/invalid parameter và khác biệt theo route/browser/device.
- Validate processed Report hoặc Exploration sau processing window dự kiến, dùng population, grain, scope, filter và data-quality rule của Section 09.

**Closure**

- Ghi observation result, affected period, decision impact và follow-up còn mở.
- Chỉ close khi processed-data check hoàn tất, hoặc exception đã được approve với owner và due date.

Realtime và DebugView là evidence gần thời gian thực/chẩn đoán; không thay thế validation trên Report/Exploration đã xử lý.

## 5. Incident và khôi phục

### 5.1 Phát hiện và phân loại

Dùng cách chẩn đoán first-failing-layer và severity trong [Debug/QA](08-debug-qa-answer-vn.md). Alert chỉ là tín hiệu điều tra, chưa chứng minh GTM là root cause.

Ghi first observed time, last known-good version, property/stream/environment bị ảnh hưởng, event/report/journey, issue type (missing, duplicate, misrouted, malformed, privacy hoặc semantic), volume/period ước tính và evidence links.

Với impact High hoặc Critical, phải thông báo vào escalation channel và response owner đã ghi trong Monitoring Record; không chỉ nhắn riêng mà không có incident reference.

### 5.2 Contain

Chọn hành động nhỏ nhất nhưng an toàn:

- pause hoặc block Tag lỗi;
- sửa environment routing;
- publish last known-good version nếu lỗi nằm ở GTM;
- dừng server/offline sender hoặc retry loop;
- tắt enhancement hỏng nếu an toàn;
- ngăn PII tiếp tục được thu thập;
- không kích hoạt permanent data filter như biện pháp khẩn cấp nếu chưa có approval phù hợp.

### 5.3 Rollback runbook

1. Xác nhận severity; không rollback chỉ vì một dao động nhỏ chưa giải thích được.
2. Xác định last known-good version và nội dung của version đó.
3. Xác định lỗi nằm ở Application/Data Layer, GTM, consent, GA4 property hay hệ thống khác.
4. Đánh giá các thay đổi khác sẽ bị rollback cùng.
5. Nhận quyết định từ publisher/incident owner.
6. Publish previous version đã approve vào đúng environment.
7. Chạy lại smoke test cho flow thay đổi và các flow critical liền kề.
8. Kiểm tra destination, request count, consent, DebugView/Realtime và processed report về sau.
9. Ghi thời điểm rollback, version, evidence, affected period và data-quality impact còn lại.
10. Tạo corrective change và regression test trước khi release lại.

Nếu root cause ở application, server hoặc GA4 property, GTM rollback có thể không giải quyết được vấn đề.

### 5.4 Data filters và developer traffic

Test traffic classification và GA4 data filter trước khi activate. Ghi rule, environment, kết quả Testing mode, approval, activation time và impact không thể hoàn tác. Tách xử lý developer/internal traffic khỏi report filtering thông thường. Xem [Data filters](https://support.google.com/analytics/answer/13296761).

Rollback GTM không xóa event đã process, không sửa key-event count sai và không khôi phục dữ liệu đã bị permanent filter loại bỏ. Phải xác định affected period và chú thích các quyết định downstream bị ảnh hưởng.

### 5.5 Quyết định release và đóng hồ sơ

Cập nhật status của Release Record và Monitoring Record ở mỗi quyết định; không để change đã publish ở trạng thái `Approved` hoặc `In QA`.

- **Go:** Gates 0–3 pass; đúng version, environment, destination, QA evidence, rollback/containment owner và Monitoring Record đã sẵn sàng.
- **Hold:** Requirement chưa rõ, chưa xác định first failing layer, routing sai, có prohibited data hoặc duplicate/missing key event chưa được giải quyết.
- **Accept exception:** Rủi ro còn lại đã được giới hạn và không blocking, có owner, mitigation, reviewer, due date và monitoring action.
- **Close:** Gate 4 hoàn tất sau observation window, processed-data validation, affected-period assessment và final outcome.

## 6. Lưu ý vận hành và lỗi thường gặp

- Preview pass không chứng minh production routing hoặc processed GA4 reporting.
- Realtime/DebugView chỉ chứng minh receipt gần đây hoặc chẩn đoán, không chứng minh historical completeness.
- Report-only change không nên được recovery bằng GTM rollback.
- Không đưa thay đổi không liên quan vào cùng workspace.
- Không dùng threshold chung khi chưa có baseline đầy đủ.
- Không để layer bị ảnh hưởng trống; ghi `N/A` kèm lý do.
- Luôn lưu affected period, kể cả khi rollback thành công.
- Ads, campaign optimization và attribution operations nằm ngoài phạm vi section này.

## 7. Bản đồ tham chiếu chéo

| Section | Section 10 sử dụng để |
|---|---|
| 01 — Data Layer Design | Xác nhận nguồn payload và handoff từ application. |
| 02 — Variable Management | Xác định Variables bị ảnh hưởng và owner. |
| 03 — Trigger Management | Xác định authoritative Trigger và nguy cơ overlap. |
| 04 — Tag Management | Xác định destination, mapping, sequencing và Tag impact. |
| 05 — Consent | Xác nhận hành vi granted/denied và privacy impact. |
| 06 — Template Governance | Review template version, permissions, consumers và rollback/export path khi cần. |
| 07 — Measurement Plan | Xác nhận requirement, contract, schema, consent và destination đã approve. |
| 08 — Debug/QA | Link test matrix, runtime evidence, defect status và smoke-test proof. |
| 09 — Reports/Charts | Link report configuration, field readiness, interpretation và processed-data impact. |

## 8. Journey minh họa — Release Registration

Các giá trị dưới đây minh họa cách ghi nhận một thay đổi Registration trong toàn bộ vòng đời release. Event contract nằm ở Section 07, runtime test evidence ở Section 08 và report configuration ở Section 09.

### 8.1 Release context

```text
Release ID: REL-REG-001
Project, change hoặc incident ID: [ticket]
Status: Monitoring
Risk level: Medium
Risk rationale: GTM mapping change có report Registration impact; không thay đổi consent hoặc destination
Tên change và business purpose: Cập nhật mapping registration event và registration report
Change type: GTM mapping + report impact
Measurement Plan và requirement IDs: MP-REG-001 / REQ-REG-001
Journey bị ảnh hưởng và downstream consumers: J-REG-001 / registration report và QA Exploration
GTM account/container và workspace: [container] / WS-REG-001
Source/build/application version: [build]
Target environment và GA4 property/stream: QA → [QA property/stream], sau đó Live → [production stream đã approve]
GTM objects hoặc GA4 settings đã thay đổi: [Variable/Trigger/Tag hoặc custom definition IDs]
Event dự kiến và request count: registration_start khi bắt đầu; một sign_up cho mỗi account được xác nhận
Consent/privacy và destination impact: analytics behavior đã approve; dùng QA destination khi validation
Production smoke-test method và approval: [synthetic/test account hoặc allowlisted identity] / [approver]
Section 08 QA evidence: QA-REG-RUN-001 / [evidence IDs]
Section 09 report/configuration IDs: R-REG-001 / EX-REG-QA-001
Monitoring ID: MON-REG-001
Version, approvers, publisher và release window: [version / owners / window]
Rollback version hoặc mitigation: [last known-good version hoặc containment]
Observation window và monitoring owner: [window] / [owner]
Evidence location, access restriction và retention period: [location] / [access] / [period]
Final outcome và affected period nếu có incident: [outcome]
```

### 8.2 Tóm tắt gate

| Gate | Evidence của Registration | Trạng thái |
|---|---|---|
| Gate 0 | Requirement, contract, destination/consent decision ở Section 07 và report impact ở Section 09 | Pass |
| Gate 1 | Workspace riêng, QA routing, GTM objects bị ảnh hưởng, expected count và không có overlap | Pass |
| Gate 2 | Valid/negative/duplicate/consent/routing tests của Section 08 link tới QA-REG-RUN-001 | Pass hoặc [status] |
| Gate 3 | Named version, publisher, target environment, rollback path và MON-REG-001 | Pass hoặc [status] |
| Gate 4 | Smoke test hoàn tất; processed Registration Report validation và observation result đã ghi nhận | Pending đến [date] hoặc [status] |

### 8.3 Monitoring Record

```text
Monitoring ID: MON-REG-001
Release ID: REL-REG-001
Status: Monitoring
Risk level: Medium
Event/report/journey được theo dõi: registration_start → sign_up; R-REG-001 và EX-REG-QA-001
Business outcome: một account được server xác nhận tương ứng với một sign_up
Signal và metric definition: event volume, duplicate/missingness, destination, consent và processed registration completion rate
Population, grain và scope: traffic của release đã approve; một event occurrence cho QA signal; Section 09 cohort/user metric cho rate
Baseline period và mức độ đầy đủ: [pre-release period / completeness]
Expected range hoặc seasonality: [range]
Warning threshold: [calibrated threshold]
Release-blocking threshold: duplicate key event, unauthorized destination, PII hoặc missingness material
Observation window và check frequency: immediate smoke test → [short window] → [processed-data date]
Source of truth: registration service, Section 08 evidence và processed GA4 asset
Monitoring owner và escalation owner: [owners]
Escalation channel và response target: [channel] / [target]
Response action: contain hoặc rollback sau khi xác nhận; mở incident nếu impact material
Observation result và decision: [result / Go, Hold, Accept exception, Close hoặc Incident]
Evidence links: [release, QA, report, smoke-test và processed-data IDs]
Evidence location, access restriction và retention period: [location] / [access] / [period]
```

### 8.4 Closure note

Chỉ close release sau khi smoke test, immediate signal checks, processed Report/Exploration validation và affected-period assessment đã được ghi nhận. Nếu dữ liệu vẫn trong processing window đã tài liệu hóa, giữ Gate 4 và Monitoring Record ở trạng thái `Pending`, đồng thời ghi owner và ngày follow-up.

## Tài liệu tham khảo chính thức

- [GTM Workspaces](https://support.google.com/tagmanager/answer/7059647)
- [GTM Environments](https://support.google.com/tagmanager/answer/6311518)
- [Publishing, versions và approvals](https://support.google.com/tagmanager/answer/6107163)
- [Preview và debug containers](https://support.google.com/tagmanager/answer/6107056)
- [Data filters](https://support.google.com/analytics/answer/13296761)
- [Monitor events in DebugView](https://support.google.com/analytics/answer/7201382)
- [Realtime report](https://support.google.com/analytics/answer/9271392)
- [GA4 data freshness](https://support.google.com/analytics/answer/11198161)
- [Data quality](https://support.google.com/analytics/answer/12856703)
