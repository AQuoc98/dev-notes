# 04 — Quản lý Tag trong GTM

## 1. Mục tiêu, phạm vi và đầu ra

Tài liệu này hướng dẫn cách thiết kế, cấu hình, test, publish và retire Google Tag Manager (GTM) Tag cho quy trình đo lường GA4 ổn định.

Tag là action mà GTM thực hiện sau khi Trigger khớp và consent/setting cho phép. Với FD calculation, Tag gửi event `calculation_action` cùng các parameter đã được duyệt tới đúng GA4 destination. Tag chỉ transport contract; không tự quyết định business result.

### Trong phạm vi

- Thiết kế Google tag và GA4 Event tag.
- Map parameter, Variable, Trigger, consent, sequencing, environment routing và request count.
- Naming, description, inventory, QA, publish, monitoring và retirement.
- `calculation_action` của FD làm implementation tham chiếu.

### Ngoài phạm vi

- Thiết kế Data Layer contract; xem Section 01.
- Thiết kế source/type của Variable; xem Section 02.
- Chọn Trigger và thiết kế filter; xem Section 03.
- Consent implementation; xem Section 05.
- Custom-template governance; xem Section 06.
- Measurement plan, Debug/QA, Reports và Release Monitoring; xem Sections 07–10.
- Tag phục vụ quảng cáo hoặc campaign.

### Đầu ra cần có

Mỗi Tag active phải có:

1. Purpose, event/action, destination và owner đã được phê duyệt.
2. Parameter allowlist có source và missing-data behavior.
3. Trigger có thẩm quyền, consent behavior, environment routing và expected count.
4. Evidence từ Preview, Network và downstream.
5. Publication record có version và lifecycle status.

## 2. Tổng quan: Tag làm gì?

### 2.1 Định nghĩa đơn giản

| Thành phần GTM | Cách hiểu thực tế |
| --- | --- |
| Tag | Action đã cấu hình để gửi dữ liệu tới GA4 hoặc destination được phê duyệt. |
| Google tag | Cấu hình đo lường Google dùng chung cho GA4 destination của environment. Đây là một loại Tag, không phải tên gọi của mọi GTM Tag. |
| GA4 Event tag | Tag có sẵn dùng để gửi một event name cùng các parameter tới GA4. |
| Trigger | Rule giúp Tag đủ điều kiện chạy; xem Section 03. |
| Variable | Source cung cấp giá trị cho Trigger hoặc Tag; xem Section 02. |
| Consent setting | Kiểm tra quyền cho phép collection; có thể chặn Tag gửi dữ liệu. |
| Destination | GA4 property/stream và Measurement ID nhận request. |

Flow thông thường:

```text
Application xác nhận business fact
        ↓
Data Layer message
        ↓
Variable cung cấp giá trị đã duyệt
        ↓
Trigger khớp
        ↓
Consent và exception cho phép
        ↓
Tag chạy
        ↓
GA4 destination an toàn theo environment nhận request
```

Application sở hữu business truth. Tag chỉ map và gửi dữ liệu đã duyệt; không suy luận result từ click, DOM text hoặc payload thiếu.

### 2.2 Google tag và GA4 Event tag khác nhau thế nào?

Dùng một Google tag dùng chung để cấu hình Google measurement cho environment, và một GA4 Event tag tập trung cho từng business event đã được duyệt. Google tag thiết lập destination/configuration; GA4 Event tag gửi event name cụ thể. Không tạo Tag event trùng nhau chỉ vì hostname hoặc Measurement ID khác nhau.

### 2.3 Kiểm tra Tag trước khi release

Trước khi đưa Tag lên production, team phải trả lời được:

```text
Tag gửi điều gì?
Khi nào Tag được phép chạy?
Tag lấy những giá trị nào?
Tag gửi tới đâu?
Consent nào là bắt buộc?
Một business occurrence tương ứng với bao nhiêu request?
Ai sở hữu và có thể retire Tag?
```

## 3. Thiết kế Tag

### 3.1 Tag contract

Ghi các thông tin sau trước khi tạo hoặc sửa Tag:

| Thuộc tính | Cần ghi rõ |
| --- | --- |
| Purpose | Requirement nghiệp vụ hoặc vận hành. |
| Tag type/template | Google tag, GA4 Event tag hoặc template đã review. |
| Event/action | Event name hoặc action chính xác. |
| Parameters | Tên, source Variable, type và required/optional status. |
| Trigger | Trigger có thẩm quyền và condition. |
| Consent | State bắt buộc và behavior khi denied/update. |
| Destination | Environment, GA4 property/stream và nguồn Measurement ID. |
| Expected count | Số request cho mỗi business occurrence hợp lệ. |
| Sequencing | Dependency setup/cleanup thật sự, nếu có. |
| Consumers | Report, QA record hoặc downstream system sử dụng dữ liệu. |
| Owner/lifecycle | Owner chịu trách nhiệm, status và điều kiện retire. |

### 3.2 Quy tắc thiết kế

- Chỉ tạo Tag cho requirement đã được phê duyệt.
- Ưu tiên Google tag/GA4 Event tag có sẵn. Chỉ dùng reviewed custom template khi native type không đáp ứng; Custom HTML chỉ là exception đã được review.
- Map dữ liệu từ canonical Variable, ưu tiên Data Layer do Application sở hữu. Không scrape DOM text hoặc dựng lại business result trong Tag.
- Chỉ gửi parameter nằm trong Measurement Plan allowlist. Required, optional, type, privacy và missing-data behavior phải rõ ràng.
- Dùng một Trigger authoritative cho event: Trigger chính thức duy nhất đại diện cho business moment. Với FD, đó là Custom Event `calculation_action` do Application push, kèm chỉ những filter bắt buộc. Không thêm click/page rule rộng để gửi cùng event; chỉ dùng Trigger và event khác khi contract chủ đích đo một business fact khác.
- Cấu hình consent theo design đã duyệt; không tạo “consent=true” để bypass.
- Route destination qua environment configuration đã review. Hostname không xác định phải fail closed, không fallback về production.
- Định nghĩa một expected request cho mỗi occurrence hợp lệ và loại bỏ path legacy chồng lấn.
- Chỉ dùng tag sequencing khi giữa các Tag có dependency thật; không dùng nó để tạo workflow của Application.

### 3.3 Chọn native Tag type

| Requirement | Cách làm nên dùng |
| --- | --- |
| Cấu hình GA4 destination dùng chung | Google tag với Measurement ID routing an toàn theo environment. |
| Gửi event GA4 có tên cụ thể | GA4 Event tag có sẵn. |
| Gửi action của third-party đã hỗ trợ | Reviewed template có owner và permission rõ ràng. |
| Hành vi chưa được hỗ trợ | Chỉ dùng Custom HTML sau khi review security, privacy và maintenance. |

## 4. Cấu hình Tag trong GTM

### 4.1 Tìm và reuse trước

Tìm trong container và inventory các Google tag, GA4 Event tag, Trigger, Variable, exception, consent setting, sequencing và destination mapping hiện có. Chỉ reuse khi purpose, event, parameter, consent, destination và expected count vẫn tương thích. Nếu không, tạo Tag mới có phạm vi rõ ràng và ghi lý do.

### 4.2 Google tag và environment routing

Dùng một Google tag đã review cho mô hình environment. Route Measurement ID bằng Lookup Table hoặc configuration tương đương:

```text
QA hostname đã biết         → QA Measurement ID
Staging hostname đã biết    → staging/test Measurement ID
Production hostname đã biết → production Measurement ID
Hostname không xác định     → blank/blocked
```

Không hard-code production Measurement ID vào event Tag khi đã có routing được kiểm soát. Kiểm tra Measurement ID/`tid` thực tế trong Network request; chỉ xem GTM Preview không đảm bảo destination đúng.

### 4.3 GA4 Event tag

Một GA4 Event tag có sẵn cần được cấu hình với:

```text
Event name: đúng name và casing trong contract
Google tag: Google tag/configuration dùng chung đã duyệt
Trigger: một Custom Event authoritative hoặc Trigger đã duyệt
Parameters: chỉ scalar allowlist đã duyệt
Consent: analytics behavior đã duyệt
Expected count: request dự kiến cho mỗi occurrence
```

Nested Data Layer path được đọc qua Data Layer Variable Version 2 (Section 02), sau đó map thành scalar GA4 parameter. Không gửi cả object như `inputs` thành một parameter trừ khi contract ghi rõ.

### 4.4 Parameter allowlist và type

Với mỗi parameter, kiểm tra:

- name và casing;
- source Variable và Data Layer path;
- string/number/Boolean type và unit;
- required hay optional;
- allowed value/cardinality;
- privacy classification;
- behavior khi thiếu: omit, block hoặc approved default.

Không gửi PII, credential, token, secret, raw user text hoặc toàn bộ API response. Chỉ tạo Custom Definition khi field cần cho report đã được duyệt (Sections 07 và 09).

### 4.5 Consent, exception và sequencing

Đây là ba lớp kiểm soát khác nhau:

- **Consent:** quyền cho phép collection. Tag có thể không được gửi dù Trigger đã match nếu analytics consent bắt buộc chưa được cấp.
- **Exception:** điều kiện chặn đã được ghi rõ, chẳng hạn environment không được hỗ trợ hoặc internal traffic rule. Nó chỉ block Tag, không phải một cách khác để định nghĩa business event.
- **Tag sequencing:** thứ tự chạy giữa các Tag có dependency, chẳng hạn chạy setup trước event Tag. Nó không chờ API response, không tạo Data Layer value bị thiếu và không thay thế Application event.

Tuân theo consent design đã duyệt ở Section 05. Test riêng từng lớp kiểm soát và ghi rõ kết quả mong đợi.

### 4.6 Firing behavior và overlap

Nhiều firing Trigger trên cùng một Tag là các lựa chọn thay thế (OR). Tránh các Tag chồng lấn cùng gửi một event cho một business occurrence. Kiểm tra duplicate container installation, generic/event-specific path, SPA remount, retry và nhiều analytics library.

## 5. Test và validation

### 5.1 GTM Preview

Dùng GTM Preview/Tag Assistant để kiểm tra:

1. Data Layer event và value.
2. Variable mà Trigger/Tag sử dụng.
3. Trigger match và exception.
4. Consent state.
5. Tags Fired và Tags Not Fired.
6. Firing count cho một occurrence có kiểm soát.

### 5.2 Network và GA4 validation

Kiểm tra từng lớp theo thứ tự:

1. **Network request:** xác nhận browser thực sự gửi request. Kiểm tra request count, event name/casing, parameter name/type, required và optional value, Measurement ID/destination, consent behavior và không có prohibited data.
2. **GA4 diagnostic view:** dùng DebugView hoặc Realtime để kiểm tra GA4 nhìn thấy event trong thời gian test. Đây là diagnostic evidence, chưa chứng minh Reports đã được cập nhật.
3. **Processed data:** chờ processing window được ghi trong Section 09 trước khi đánh giá Reports hoặc Explorations.

GTM Preview chỉ chứng minh Data Layer, Variable, Trigger và Tag path hoạt động theo cấu hình. Tag xuất hiện trong **Tags Fired** không chứng minh request đúng đã tới GA4. Link toàn bộ evidence vào Section 08.

### 5.3 Test coverage

| Case | Kết quả mong đợi |
| --- | --- |
| Business event hợp lệ | Một Tag fire và một request đúng. |
| Event name/case sai | Tag không fire. |
| Required Variable bị thiếu | Tag bị block hoặc QA fail theo contract. |
| Optional Variable bị thiếu | Parameter được omit. |
| Input invalid/API failure | Không có success-outcome request. |
| Duplicate callback/retry/remount | Không có duplicate ngoài ý muốn. |
| Consent denied/granted/updated | Theo consent design đã duyệt. |
| Environment không xác định | Không có request tới production. |
| Destination sai | Dừng release và sửa routing. |
| Tag chồng lấn đã tồn tại | Xóa hoặc ghi rõ purpose riêng trước release. |

## 6. Review, publish và maintain

### 6.1 Naming và description

Dùng format:

```text
[SCOPE] - [TYPE] - [EVENT OR PURPOSE] - [QUALIFIER]
```

Type label khuyến nghị gồm `Google tag`, `GA4 Event`, `TPL` (reviewed template) và `HTML` (Custom HTML exception đã duyệt). Dùng đúng event spelling canonical. Tránh `New Tag`, `Tag 14`, `Copy`, `Test` hoặc `Temp` cho Tag active.

Description nên ghi purpose, Measurement Plan reference, event name, parameter allowlist, Variable, Trigger, consent, destination, expected count, dependency, owner và điều kiện retire.

### 6.2 Inventory

Duy trì một record cho mỗi Tag:

```text
Tag name và type/template
Purpose và Measurement Plan reference
Event/action và parameter allowlist
Variable và source path
Firing Trigger, exception và sequencing
Consent behavior
Environment và destination/Measurement ID source
Expected request count
Consumer và dependency
Owner, status và replacement
Published version và QA/production evidence
Review date và điều kiện retire
```

### 6.3 Publish và monitor

Trước khi publish:

1. Xác nhận Tag contract và toàn bộ consumer.
2. Test positive, negative, duplicate, consent và destination case.
3. Kiểm tra Network request thực tế và request count.
4. Xác nhận environment routing và privacy behavior.
5. Publish GTM change có version, release note, owner, evidence và rollback point.
6. Cập nhật inventory và monitor sau release (Section 10).

### 6.4 Lifecycle và retirement

Dùng lifecycle `Proposed → Development → QA → Active → Deprecated → Retired`. Chỉ retire Tag sau khi consumer đã xóa/thay thế, không còn shared/sequencing dependency, replacement đã pass QA và một version có thể khôi phục được giữ lại.

## 7. Bản đồ tham chiếu chéo

- [Section 01 — Data Layer Design](01-data-layer-design-answer-vn.md): event timing, payload và business truth do Application sở hữu.
- [Section 02 — Variable Management](02-variable-management-answer-vn.md): nguồn Variable, type và missing-data behavior.
- [Section 03 — Trigger Management](03-trigger-management-answer-vn.md): Trigger authoritative, filter, exception và frequency.
- [Section 05 — Consent Management](05-consent-answer-vn.md): consent default, update và denied-state behavior.
- [Section 06 — Template Governance](06-template-governance-answer-vn.md): sử dụng và quản lý reviewed template.
- [Section 07 — Measurement Plan](07-measurement-plan-answer-vn.md): purpose event, parameter approval và owner.
- [Section 08 — Debug/QA](08-debug-qa-answer-vn.md): Preview, Network, evidence, defect và retest.
- [Section 09 — Reports and Charts](09-reports-charts-answer-vn.md): field readiness và processed-data interpretation.
- [Section 10 — Release Monitoring](10-release-monitoring-answer-vn.md): release gate, monitoring, incident và rollback.

## 8. Journey hoàn chỉnh: FD `calculation_action`

Đây là walkthrough cụ thể duy nhất. Thay identifier bằng giá trị của project đã được phê duyệt.

### 8.1 Google tag

```text
Name: FD - Google tag - Primary
Type: Google tag
Measurement ID: {{LUT - Shared - GA4 Measurement ID by Hostname}}
Environment map:
  app-staging.example.com → QA Measurement ID
  app.example.com         → Production Measurement ID
  unknown host            → blank/blocked
```

### 8.2 GA4 Event tag

```text
Name: FD - GA4 Event - calculation_action
Type: GA4 Event
Google tag: FD - Google tag - Primary
Event name: calculation_action
Trigger: FD - CE - calculation_action - Approved
Consent: analytics consent behavior đã duyệt
Expected count: một request cho mỗi calculation occurrence được chấp nhận
```

Parameter mapping:

```text
event_schema_version → {{FD - DLV - event_schema_version}}
app_name             → {{FD - DLV - app_name}}
solution_found       → {{SHARED - DLV - solution_found}}
connection_type      → {{FD - DLV - inputs - connection_type}}
fx                   → {{FD - DLV - inputs - fx}}
fy                   → {{FD - DLV - inputs - fy}}
```

### 8.3 Trigger

```text
Name: FD - CE - calculation_action - Approved
Type: Custom Event
Event name: calculation_action
Conditions:
  app_name equals fd
  event_schema_version equals 1.0
```

### 8.4 Application message

```javascript
window.dataLayer.push({
  event: "calculation_action",
  event_schema_version: "1.0",
  app_name: "fd",
  solution_found: "Yes",
  inputs: {
    connection_type: "clt_floor_floor_half_lap_joint",
    unit_system: "metric",
    fx: 1,
    fy: 0,
  },
});
```

### 8.5 Quyết định validation

```text
Response hợp lệ có output
    → một Data Layer message
    → Trigger match một lần
    → một GA4 Event Tag fire
    → một request tới QA/production Measurement ID đúng

Response hợp lệ nhưng không có output
    → cùng flow với solution_found = "No"

Input invalid, API failure, response stale, duplicate callback,
hostname không xác định hoặc consent denied
    → xử lý theo contract và test record của Section 08
```

## Tài liệu tham khảo

- [Tag Manager Help — Components of Google Tag Manager](https://support.google.com/tagmanager/answer/6103657?hl=en)
- [Google for Developers — The data layer](https://developers.google.com/tag-platform/tag-manager/datalayer)
- [Google Analytics — Set up events](https://developers.google.com/analytics/devguides/collection/ga4/events)
- [Tag Manager Help — Google tag](https://support.google.com/tagmanager/answer/11994839?hl=en)
- [Tag Manager Help — GA4 Event tag](https://support.google.com/tagmanager/answer/9442095?hl=en)
