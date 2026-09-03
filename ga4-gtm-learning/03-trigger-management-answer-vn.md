# 03 — Quản lý Trigger trong GTM

## 1. Mục tiêu, phạm vi và đầu ra

Tài liệu này hướng dẫn cách chọn, cấu hình, test, publish và retire Google Tag Manager (GTM) Trigger cho quy trình đo lường GA4 ổn định.

Trigger là một rule lắng nghe event và quyết định Tag có đủ điều kiện chạy hay không. Trigger không tự gửi dữ liệu. Tag, consent setting, exception, sequencing, browser và destination vẫn quyết định request có thực sự được tạo hay không.

### Trong phạm vi

- Chọn event source có thẩm quyền và Trigger Type phù hợp.
- Firing logic, exception, filter, timing, Trigger Group và environment scope.
- Naming, reuse, inventory, QA, release và retirement.
- `calculation_action` của FD làm pattern Custom Event tham chiếu.

### Ngoài phạm vi

- Thiết kế source/type của Variable; xem Section 02.
- Cấu hình Tag và GA4 destination; xem Section 04.
- Consent implementation; xem Section 05.
- Custom-template governance; xem Section 06.
- Measurement plan, Debug/QA evidence, Reports và Release Monitoring; xem Sections 07–10.
- Trigger phục vụ quảng cáo hoặc campaign.

### Đầu ra cần có

Mỗi Trigger đang active phải có:

1. Business event và source có thẩm quyền.
2. Firing rule, filter, exception, consent và expected frequency được ghi rõ.
3. Owner, consumer, environment scope và lifecycle status.
4. Evidence từ Preview, Network và downstream ở mức phù hợp với Tag.
5. Publication record có version và khả năng khôi phục.

## 2. Tổng quan: Trigger hoạt động như thế nào?

### 2.1 Định nghĩa đơn giản

| Khái niệm trong GTM | Cách hiểu thực tế |
| --- | --- |
| Firing Trigger | Điều kiện giúp Tag đủ điều kiện chạy. Một Tag có thể có nhiều firing Trigger; chúng hoạt động theo kiểu lựa chọn thay thế (OR). |
| Trigger condition | Một phép kiểm tra bên trong Trigger. Tất cả condition phải đúng (AND). |
| Exception/blocking Trigger | Điều kiện chặn Tag. Chỉ cần một exception khớp là Tag bị chặn. |
| Consent setting | Kiểm tra quyền riêng tư riêng biệt; có thể chặn Tag dù Trigger đã khớp. |
| Trigger Group | Cổng chờ tất cả Trigger thành viên xảy ra. Nó chỉ xác nhận các Trigger đã xảy ra, không đảm bảo thứ tự và không nên dựng lại workflow của Application. |

Với một Tag có firing Trigger `F1`, `F2`, exception `B1`, `B2` và yêu cầu consent:

```text
Tag đủ điều kiện khi:
(F1 khớp OR F2 khớp)
AND KHÔNG (B1 khớp OR B2 khớp)
AND consent/setting cho phép
AND đáp ứng yêu cầu sequencing
```

Quy tắc thực tế: dùng Trigger có phạm vi nhỏ nhất nhưng vẫn khớp đúng business moment đã được phê duyệt. Ví dụ, FD calculation nên dùng Custom Event `calculation_action` do Application push, kèm chỉ những filter cần thiết như application và schema version. Không thay application event bị thiếu bằng click, page hoặc DOM rule quá rộng; các tín hiệu đó không chứng minh được kết quả calculation.

## 3. Chọn Trigger

### 3.1 Xác định business moment trước

Trước khi mở GTM, ghi lại:

```text
Business event và định nghĩa:
Source và thời điểm có thẩm quyền:
Event name và casing chính xác:
Filter và Variable bắt buộc:
Giá trị bắt buộc của event:
Expected frequency:
Yêu cầu consent và timing:
Environment scope:
Tag sử dụng Trigger:
Owner và điều kiện retire:
```

Input change, click, request start hoặc component render không tự động là business outcome đã xác nhận. Với FD, Application chỉ nên push `calculation_action` sau khi đã phân loại response API tương ứng (Section 01).

### 3.2 Chọn Trigger Type

| Requirement | Trigger nên dùng | Dùng khi |
| --- | --- | --- |
| Thiết lập consent trước tracking | Consent Initialization | Consent default/update phải sẵn sàng trước các Tag khác. |
| Chạy cấu hình thật sớm | Initialization | Setup phải chạy trước page Trigger thông thường; không dùng mặc định cho business event. |
| Đo lúc page bắt đầu load | Page View | Chính page load là thời điểm cần đo. |
| Đọc element sau khi HTML parse | DOM Ready | DOM cần đọc chưa có ở Page View. |
| Chờ toàn bộ resource của page | Window Loaded | Requirement thực sự phụ thuộc image/script/resource load xong. |
| Đo kết quả Application đã xác nhận | Custom Event | Application push một Data Layer event có tên sau khi biết kết quả. |
| Đo click hoặc UI intent | Click: All Elements | Câu hỏi là click; click không chứng minh action thành công. |
| Đo click vào link | Click: Just Links | Câu hỏi là click trên thẻ `<a>`. |
| Đo native form submit | Form Submission | Website dùng native HTML form. React/AJAX form thường nên dùng Custom Event từ Application. |
| Đo SPA navigation | History Change | Browser history đổi nhưng không có router event đáng tin cậy hơn. |
| Đo element xuất hiện | Element Visibility | Câu hỏi là một element cụ thể đã visible. |
| Đo scroll/video engagement | Scroll Depth hoặc YouTube Video | Câu hỏi là engagement, không phải business result đã xác nhận. |
| Đo điều kiện theo thời gian | Timer | Có interval và stop condition rõ ràng. |
| Chờ nhiều điều kiện độc lập | Trigger Group | Tất cả member Trigger phải xảy ra; thứ tự không được đảm bảo. |

### 3.3 Timing

Dùng Trigger sớm nhất tại thời điểm mọi dữ liệu bắt buộc đã có:

```text
Consent Initialization → consent setup
Initialization         → early setup
Page View              → page bắt đầu load
DOM Ready              → HTML đã parse
Window Loaded          → resource của page đã load xong
Application event      → business result đã xác nhận
```

Chọn stage sớm nhất tại đó mọi giá trị bắt buộc đã có. Nếu Trigger chạy quá sớm, dữ liệu có thể chưa tồn tại. Nếu chạy quá muộn, tracking bị chậm và event có thể mất khi người dùng rời trang. Khi Application đã phát authoritative business event, không thay nó bằng Trigger DOM Ready hoặc Window Loaded muộn hơn.

## 4. Cấu hình Trigger

### 4.1 Tìm và reuse

Tìm trong GTM container và tracking inventory trước khi tạo Trigger. Kiểm tra event name giống nhau, page/click rule tương tự, Tag hiện có, Variable, exception, Trigger Group, sequencing và consent requirement.

Chỉ reuse Trigger khi consumer cần cùng event, scope, timing, filter, consent behavior, missing-data behavior và expected frequency. Shared Trigger có thể ảnh hưởng nhiều Tag; phải review tất cả consumer trước khi sửa.

### 4.2 Tạo Trigger trong GTM

1. Mở **Triggers → New**.
2. Nhập Trigger name đã được duyệt.
3. Mở **Trigger Configuration** và chọn type phù hợp.
4. Chọn **All** chỉ khi mọi occurrence khớp đều thuộc scope.
5. Chọn **Some** và thêm condition giới hạn khi scope hẹp hơn.
6. Save, chỉ attach Trigger vào Tag đã được duyệt, rồi review exception/consent.

### 4.3 Filter và operator

Một filter có dạng:

```text
Variable + Operator + Expected value
```

Dùng condition nhỏ nhất đáp ứng requirement:

| Nhu cầu | Nên dùng |
| --- | --- |
| Một giá trị chính xác | `equals` |
| Text xuất hiện ở bất kỳ vị trí nào | `contains` |
| Prefix/suffix đã biết | `starts with` / `ends with` |
| Pattern có kiểm soát | `matches RegEx` |
| Pattern không phân biệt hoa thường | `matches RegEx (ignore case)` chỉ khi contract cho phép |
| Element cụ thể | `matches CSS selector` |
| So sánh số | `less than`, `greater than`, v.v. |

Trong một Trigger, các condition là AND. Trên một Tag, các firing Trigger riêng biệt là OR. Không thêm `All Pages` và page-specific Trigger với kỳ vọng AND; `All Pages` đã làm Tag đủ điều kiện trên mọi page.

### 4.4 URL, RegEx và CSS rule

- Dùng `Page Path` để match route, `Page URL` cho full URL, URL Variable riêng cho query parameter và `Click URL` cho link destination.
- Anchor RegEx khi boundary quan trọng, ví dụ `^/products(?:/|$)`, và test cả match đúng lẫn near-miss.
- Ưu tiên stable ID hoặc attribute do team sở hữu cho CSS selector. Tránh class do framework sinh ra, layout class và visible text.
- Ghi rõ case sensitivity và test `undefined`, empty, invalid và unexpected value.

### 4.5 Missing và invalid value

Với value bắt buộc, hãy fail closed:

```text
Variable bắt buộc bị thiếu hoặc sai
        ↓
Trigger không khớp
        ↓
Tag không fire
        ↓
QA ghi nhận contract defect
```

Không đổi missing value thành `unknown`, empty string hoặc value cũ nếu contract chưa định nghĩa ý nghĩa đó.

### 4.6 Trigger Group và tag sequencing

Đây là hai cơ chế khác nhau:

- **Trigger Group:** chờ mọi Trigger thành viên khớp ít nhất một lần rồi mới cho Tag đủ điều kiện. Nó không đảm bảo thứ tự và không chứng minh các Trigger thành viên thuộc cùng một business transaction.
- **Tag sequencing:** kiểm soát thứ tự chạy của các Tag, chẳng hạn chạy Tag setup trước event Tag. Nó không làm Trigger chờ API response.

Chỉ dùng Trigger Group khi thực sự cần nhiều tín hiệu độc lập cùng có mặt. Chỉ dùng tag sequencing khi có dependency giữa các Tag đã được ghi rõ. Không cơ chế nào được dùng để dựng lại workflow của Application hoặc ghép API request với response; với FD, một Custom Event do Application phát ra vẫn là pattern ưu tiên.

## 5. Test và validation

### 5.1 GTM Preview

Dùng GTM Preview/Tag Assistant để xem event timeline, Data Layer value, Variable, Trigger match, Tags Fired, Tags Not Fired, exception và consent state:

1. Kết nối với QA/staging URL đã được duyệt.
2. Thực hiện một action có kiểm soát.
3. Chọn event tương ứng trong timeline.
4. Xác nhận event name và value bắt buộc.
5. Kiểm tra từng Variable mà Trigger/Tag sử dụng.
6. Xác nhận filter đã match và điều kiện block.
7. Lặp lại với negative case, duplicate và edge case.

### 5.2 Network và downstream validation

Preview chứng minh GTM path; không chứng minh GA4 đã nhận đúng request. Khi Trigger dẫn tới một outbound Tag, kiểm tra Browser Network panel hoặc hit details tương đương:

- request có tồn tại và count đúng contract;
- event name, parameter name và type chính xác;
- required value có mặt, optional value tuân theo contract;
- Measurement ID/destination đúng với environment;
- consent behavior đúng;
- không có PII, credential, token, secret hoặc unrestricted user input.

Dùng GA4 DebugView/Realtime làm downstream diagnostic evidence. Chỉ thấy Tag Fired chưa đủ evidence. Link kết quả với Evidence Template của Section 08.

### 5.3 Test coverage bắt buộc

| Case | Trigger phải làm gì |
| --- | --- |
| Event hợp lệ | Match một lần tại business moment có thẩm quyền. |
| Event name/case sai | Không match. |
| URL, selector hoặc action tương tự | Không match nhầm case khác. |
| Required value thiếu/sai format | Không match hoặc block theo rule đã ghi. |
| Input invalid hoặc server response fail | Không tạo success match. |
| Double click/retry/duplicate callback | Không tạo duplicate ngoài ý muốn. |
| SPA route/reload/back-forward/revisit | Theo route contract và không tạo duplicate page view. |
| Consent denied/granted/updated | Match hoặc block theo consent behavior đã duyệt. |
| Exception condition | Tag cần chặn bị block; Tag không liên quan không đổi. |
| Trigger Group chưa đủ/đã đủ | Chỉ fire sau khi đủ member bắt buộc. |
| Browser/navigation được hỗ trợ | Tracking không làm hỏng submit hoặc navigation. |

## 6. Publish, inventory và retire

### 6.1 Naming và description

Dùng format:

```text
[SCOPE] - [TYPE] - [BUSINESS EVENT OR PURPOSE] - [QUALIFIER]
```

Prefix khuyến nghị gồm `CI` (Consent Initialization), `INIT`, `PV`, `DOM`, `WL`, `CE` (Custom Event), `CLK`, `LINK`, `FORM`, `HC` (History Change), `VIS`, `TMR`, `GRP` và `EXC` (Exception). Không dùng `Trigger 1`, `New Trigger`, `Test` hoặc `Temp` cho item active.

Description nên ghi business purpose, event/source, filter, Tag sử dụng, exception, consent, expected frequency, owner, environment và điều kiện retire.

### 6.2 Inventory

Duy trì một record cho mỗi Trigger:

```text
Trigger name và type
Exact event hoặc page-load source
Toàn bộ filter, operator, value, regex flag và selector scope
Tag sử dụng và event được gửi
Exception, Trigger Group và sequencing
Consent behavior và timing risk
Expected frequency
Owner/reviewer và environment
Workspace/published version
Status và điều kiện retire
```

### 6.3 Review và publish

Trước khi publish Trigger shared hoặc phụ thuộc environment:

1. Xác nhận Measurement Plan và Trigger contract.
2. Review toàn bộ consumer và duplicate path.
3. Test environment bị ảnh hưởng, consent state, negative case và request count.
4. Đính kèm evidence từ Preview/Network/DebugView.
5. Publish GTM change có version, owner, release note và rollback point.
6. Cập nhật inventory và thông báo owner bị ảnh hưởng.

### 6.4 Retirement

Chỉ retire sau khi Tag sử dụng đã được xóa/thay thế, không còn dependency trong Trigger Group/sequencing, replacement đã pass positive/negative/duplicate/consent/downstream test và version có thể khôi phục đã được giữ lại.

## 7. Bản đồ tham chiếu chéo

- [Section 01 — Data Layer Design](01-data-layer-design-answer-vn.md): application-owned event timing và business truth.
- [Section 02 — Variable Management](02-variable-management-answer-vn.md): Variable source, nested path và missing-data behavior.
- [Section 04 — Tag Management](04-tag-management-answer-vn.md): attach Trigger vào Google/GA4 Tag và kiểm tra destination.
- [Section 05 — Consent Management](05-consent-answer-vn.md): consent default, update và denied-state behavior.
- [Section 06 — Template Governance](06-template-governance-answer-vn.md): governance cho custom template.
- [Section 07 — Measurement Plan](07-measurement-plan-answer-vn.md): business definition, expected frequency và owner.
- [Section 08 — Debug/QA](08-debug-qa-answer-vn.md): Preview, evidence, defect và retest.
- [Section 09 — Reports and Charts](09-reports-charts-answer-vn.md): downstream interpretation sau collection.
- [Section 10 — Release Monitoring](10-release-monitoring-answer-vn.md): release gate, observation và rollback.

## 8. Journey hoàn chỉnh: FD `calculation_action`

Đây là walkthrough cụ thể duy nhất. Thay các project identifier bằng giá trị đã được phê duyệt.

### 8.1 Contract

```text
Business event: calculation_action
Thời điểm có thẩm quyền: response API FD tương ứng đã được phân loại
Expected frequency: một event cho mỗi lần tính hợp lệ được chấp nhận
Required values: event_schema_version, app_name, solution_found, input đã duyệt
Trigger: FD - CE - calculation_action - Approved
Consumer: FD - GA4 Event - calculation_action
Environment: QA/staging/production theo routing
```

### 8.2 Application message

```javascript
window.dataLayer.push({
  event: "calculation_action",
  event_schema_version: "1.0",
  app_name: "fd",
  solution_found: true,
  inputs: {
    connection_type: "clt_floor_floor_half_lap_joint",
    unit_system: "metric",
    fx: 1,
    fy: 0,
  },
});
```

### 8.3 Trigger configuration

```text
Name: FD - CE - calculation_action - Approved
Type: Custom Event
Event name: calculation_action
Conditions:
  app_name equals fd
  event_schema_version equals 1.0
Expected: một match cho mỗi lần tính hợp lệ được chấp nhận
```

### 8.4 Test decision

```text
Response có output hợp lệ
    → một Data Layer event
    → Trigger match một lần
    → một GA4 Tag fire/request

Response hợp lệ nhưng không có output
    → một Data Layer event với solution_found = false
    → cùng expected count

Input invalid, timeout, server failure, response stale, duplicate callback,
environment không xác định hoặc consent denied
    → xử lý theo contract và test record của Section 08
```

## Tài liệu tham khảo

- [Tag Manager Help — About triggers](https://support.google.com/tagmanager/answer/7679316?hl=en)
- [Tag Manager Help — Custom event trigger](https://support.google.com/tagmanager/answer/7679219?hl=en)
- [Tag Manager Help — Best practices for trigger configuration](https://support.google.com/tagmanager/answer/7679102?hl=en)
- [Tag Manager Help — Preview and debug containers](https://support.google.com/tagmanager/answer/6107056?hl=en)
