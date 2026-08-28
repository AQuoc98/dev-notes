# 09 — GA4 Reports, Explorations, Charts và Interpretation

## Mục đích

Report không phải là một tập hợp chart đẹp mắt. Report là câu trả lời có thể lặp lại cho một business question. Hãy xác định question và decision trước, sau đó mới chọn GA4 surface, dimension, metric, filter, segment, chart, phương pháp validation và owner.

Dùng chuỗi sau:

```text
Report audience
  → business question
  → decision
  → population và scope
  → dimensions + metrics
  → Report hoặc Exploration
  → chart/table
  → QA và limitations
  → interpretation và action
```

## Thuật ngữ dùng chung

Dùng các định nghĩa đơn giản dưới đây khi trao đổi report với Product, Marketing, Engineering, QA hoặc leadership. Trong tài liệu này, **audience** là những người đọc và sử dụng report; không phải GA4 Audience dùng cho targeting.

| Thuật ngữ | Ý nghĩa dễ hiểu | Ví dụ trong Registration |
| --- | --- | --- |
| Audience | Những người dùng report và decision họ cần đưa ra. | Product team quyết định method đăng ký nào cần điều tra. |
| Population | Chính xác những user, session, event hoặc item nào được đưa vào analysis. | Những user đã bắt đầu registration journey. |
| Grain | Một row hoặc một đơn vị được đếm đại diện cho điều gì. | Một user, một session, một event hoặc một ecommerce item. |
| Scope | Cấp độ mà một value thuộc về. | `method` thuộc event; scope của `device category` cần được kiểm tra theo GA4 surface. |
| Dimension | Label hoặc category dùng để chia dữ liệu thành các nhóm. | `method`, `device category` hoặc `event_name`. |
| Metric | Con số được đếm, cộng hoặc tính toán. | Users, event count, revenue hoặc completion rate. |
| Denominator | Mẫu số dùng làm cơ sở tính rate. | Số user có `registration_start` khi tính completion rate. |
| Field readiness (mức sẵn sàng của field) | Field đã an toàn và sẵn sàng về kỹ thuật để dùng trong report hay chưa; không chỉ là đã xuất hiện trong request. | `method` đã được collect, register nếu cần, process, compatible và được approve cho reporting. |
| GA4 surface | Khu vực/công cụ trong GA4 nơi analysis được tạo hoặc xem. | Reports để monitor định kỳ; Explorations để investigation. |

Phân biệt thực tế:

```text
Population = đưa ai hoặc cái gì vào analysis?
Grain      = một row hoặc một count đại diện cho điều gì?
Scope      = value thuộc cấp độ nào?
Readiness  = field đã dùng đúng và an toàn được chưa?
```

## Khái niệm cốt lõi về reporting

### Dimension, metric và scope

| Khái niệm | Giải thích dễ hiểu | Ví dụ | Sai lầm thường gặp |
| --- | --- | --- | --- |
| Dimension | Label dùng để chia data thành các nhóm. | `method`, `device category`, `event_name` | Xem label như một con số. |
| Metric | Con số cho biết có bao nhiêu hoặc mức độ bao nhiêu. | Users, event count, key events, revenue | So sánh các số có cơ sở khác nhau. |
| Scope | Cấp độ mà value thuộc về. | User, session, event hoặc item | Dùng event value để trả lời câu hỏi ở user level. |
| User scope | Value mô tả user xuyên suốt hoạt động của họ. | User property | Xem nó như value thay đổi ở mỗi event. |
| Session scope | Value mô tả một visit/session. | Session source/medium | Trộn acquisition ở session scope với user scope. |
| Event scope | Value mô tả một event occurrence. | Event name hoặc event parameter | Cho rằng event count bằng unique users. |
| Item scope | Value mô tả một product/item bên trong ecommerce data. | Item name hoặc item category | Cộng item row với event total mà chưa kiểm tra grain. |

Luôn ghi grain của question:

```text
Có bao nhiêu user hoàn thành registration?
→ user-level denominator; key event count có thể khác user count.

Có bao nhiêu registration event được gửi?
→ event-level count và duplicate behavior là điều cần quan tâm.
```

Nếu metric và dimension không tương thích, GA4 có thể disable combination hoặc trả về kết quả không trả lời đúng question. Không cố tạo chart khi scope nền tảng sai.

### Reports và Explorations

| Surface | Dùng tốt nhất cho | Điểm mạnh | Lưu ý |
| --- | --- | --- | --- |
| Reports snapshot/overview | Monitoring cấp cao | Summary dễ tìm cho audience rộng | Ít detail và ít flexibility phân tích |
| Detail report | Câu hỏi vận hành lặp lại, đã được quản lý | Lưu dimension, metric, chart và table | Cần configuration phù hợp và quyền publish |
| Free-form Exploration | So sánh và investigation linh hoạt | Rows, columns, values, segments, filters và visualization | Dễ bị đọc sai; cần document configuration |
| Funnel exploration | Phân tích tiến trình theo bước | So sánh completion/drop-off qua các bước | User/event counting và open/closed funnel setting rất quan trọng |
| Path exploration | Tìm behavior trước/sau một hành động | Hiển thị journey và loop | Là exploratory, không phải bằng chứng về causation |
| Cohort exploration | Retention hoặc behavior lặp lại | So sánh group theo thời gian | Cần cohort definition có ý nghĩa và đủ data |

Google mô tả detail report là report có hai chart và một table; Explorations cung cấp kỹ thuật phân tích nâng cao và flexible analysis. Xem [GA4 detail reports](https://support.google.com/analytics/answer/10659476) và [get started with Explorations](https://support.google.com/analytics/answer/7579450).

### Filter, comparison và segment

- **Filter:** giới hạn data được hiển thị bằng một condition.
- **Comparison:** hiển thị các subset của report cạnh nhau trong Reports workspace.
- **Segment:** định nghĩa subset có thể tái sử dụng của user, session hoặc event trong Explorations.

Viết condition bằng ngôn ngữ đơn giản và ghi rõ scope:

```text
Include users who completed sign_up
versus
Include events where event_name = sign_up
```

Hai cách này có thể cho kết quả khác nhau. Không dùng user segment cho question về event delivery volume, hoặc event filter cho decision cần unique user, nếu chưa document khác biệt.

## Quy trình thiết kế report

### Bước 1 — Xác định question và decision

Ví dụ:

```text
Report audience: Product team
Question: Registration method nào có completion rate thấp nhất?
Decision: Ưu tiên investigation UX theo từng method.
Cadence: Weekly
Owner: Product analytics
```

Report title phải làm rõ mục đích:

```text
Product — Registration health by method
Analytics — Registration event delivery QA
```

Tránh các title như `Dashboard 1`, `Test` hoặc `All Events`.

### Bước 2 — Xác định population và grain

**Population** trả lời: “Ai hoặc cái gì được đưa vào?” **Grain** trả lời: “Một row hoặc một đơn vị được đếm đại diện cho điều gì?” Hai thông tin này phải được ghi trước khi chọn chart.

Ví dụ:

```text
Population: User đã bắt đầu registration journey
Grain:     Mỗi user chỉ được đếm một lần
Question:  Bao nhiêu phần trăm user hoàn thành registration?

Population: Tất cả sign_up event được gửi trong QA period
Grain:     Một event occurrence
Question:  Mỗi account được confirm có gửi đúng một request không?
```

Ghi nhận:

- date range và property timezone;
- population bằng ngôn ngữ dễ hiểu;
- grain: user, session, event hoặc item;
- điều kiện include/exclude;
- comparison period hoặc segment;
- identity setting/reporting identity nếu liên quan;
- attribution model và traffic-source scope nếu liên quan;
- key-event definition và deduplication assumption;
- expected data freshness.

Không tính user-level rate bằng event count, trừ khi sự khác biệt này là có chủ đích và đã được document.

### Bước 3 — Xác nhận field readiness (field đã dùng được chưa?)

**Field readiness** nghĩa là field đã qua các kiểm tra cần thiết: đã được collect, register nếu cần, process và available, compatible với report đã chọn, an toàn và được approve cho question tương ứng. Parameter xuất hiện trong Network request chưa có nghĩa là field đã sẵn sàng cho reporting.

Trước khi build report:

1. Verify event và parameter có trong Measurement Plan và DebugView.
2. Ưu tiên standard dimension/metric khi nó có đúng meaning và scope.
3. Xác nhận event parameter thực sự đang được collect.
4. Chỉ register custom dimension hoặc metric khi cần cho approved analysis.
5. Ghi registration date và expected availability.
6. Kiểm tra cardinality, privacy, allowed values và quota.
7. Test field trong report hoặc Exploration phù hợp sau processing.

Google ghi chú custom dimension và metric được tạo từ collected custom data và có thể cần 24–48 giờ mới available cho reporting/advertising. Cần kiểm tra behavior và limit hiện hành trong property và official documentation. Xem [custom dimensions and metrics](https://support.google.com/analytics/answer/14240153).

### Field-readiness inventory

**Mục đích:** Dùng inventory này để theo dõi một dimension hoặc metric đã sẵn sàng cho reporting chưa. Nó tách “parameter đang được collect” khỏi “field đã register, process, tương thích và an toàn để dùng trong report”.

| Field | Meaning | Source | Scope (cấp độ dữ liệu) | Standard/custom | Registration date | Expected availability | Risk/notes |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `event_name` | Canonical event | GA4 event | Event | Standard | N/A | Standard processing | Stable |
| `method` | Registration method | `sign_up` parameter | Event | Custom if required | YYYY-MM-DD | After processing delay | Controlled values |
| `form_id` | Stable form identifier | `sign_up` parameter | Event | Custom if required | YYYY-MM-DD | After processing delay | Avoid free text |
| `device_category` | Device category | GA4 collection | User/session/event context | Standard | N/A | Standard processing | Scope must be checked |

### Bước 4 — Chọn GA4 area (surface)

Dùng **detail report** khi cùng một audience sẽ monitor một question ổn định lặp lại. Dùng **Exploration** khi analyst cần compare segment, test hypothesis, inspect funnel/path hoặc explore question chưa ổn định.

Không biến mọi Exploration ad hoc thành published report. Chỉ promote sau khi question, definition, filter và ownership đã ổn định.

### Bước 5 — Chọn dimension và metric

Chọn tập nhỏ nhất có thể trả lời question:

```text
Question: Sign-up có được ghi nhận một lần theo method và device không?
Dimensions: method, device category, date
Metrics: users, event count, key events
Filter: approved registration event family
```

Với rate, document numerator và denominator. Ví dụ:

```text
Completion rate = users with sign_up / users with registration_start
```

Không gọi `sign_up event count / page views` là completion rate nếu đó không phải business definition đã thống nhất. Tránh trộn event count với user count mà không giải thích grain.

### Bước 6 — Chọn chart theo analytical task

| Task | Visualization ưu tiên | Vì sao | Lưu ý |
| --- | --- | --- | --- |
| Trend theo thời gian | Line chart | Thể hiện direction và change | Recent/incomplete data có thể thấp giả tạo |
| So sánh category | Bar chart | Hỗ trợ ranking và side-by-side comparison | Quá nhiều category làm khó đọc |
| Giá trị chính xác | Table | Giữ value và nhiều field | Pattern khó nhìn hơn |
| Composition với ít category | Donut/pie | Hiển thị các phần của một whole có ý nghĩa | Tránh nhiều slice hoặc total không liên quan |
| Quan hệ giữa numeric field | Scatterplot | Hiển thị association | Correlation không phải causation |
| Tiến trình theo bước | Funnel | Hiển thị movement qua các step | Kiểm tra user/event count và funnel rule |
| Khám phá journey | Path | Hiển thị next/previous action phổ biến | Exploratory, có thể có loop/noise |
| Địa lý | Geo map | So sánh geographic group đã phê duyệt | Privacy threshold và group nhỏ cần chú ý |

Chart là communication layer. Giữ table hoặc calculation visible khi exact value hay denominator quan trọng.

### Bước 7 — Build, publish và document asset

#### A. Tạo detail report

Cần role **Editor** hoặc **Administrator** để tạo hoặc customize report. Dùng detail report khi question đã ổn định và cần được theo dõi lặp lại.

1. Trong GA4, mở **Reports → Library**.
2. Trong phần **Reports**, chọn **+ Create new report → Create detail report**.
3. Chọn **Blank** hoặc template phù hợp. Report dựa trên template có thể nhận update từ template trong tương lai; hãy ghi rõ report còn linked hay đã unlinked.
4. Trong **Customize report**, cấu hình dimension picker, metric, report filter và hai chart. Chỉ thêm field đã ready và compatible.
5. Đặt default dimension, default sort metric, chart type, filter/comparison, title, description, owner và maintenance trigger.
6. Bấm **Save**, nhập report name rõ ràng rồi lưu lại.
7. Mở report đã lưu từ **Reports**, không chỉ từ Library, và kiểm tra report có trả lời question ban đầu hay không.

Detail report có hai chart và một table. Chart hiển thị theo data của table, nên thay dimension, filter, comparison hoặc default sort có thể làm thay đổi chart data. Hãy document table configuration và mục đích của chart, không chỉ ghi loại visual. Xem [create a detail report](https://support.google.com/analytics/answer/13844077) và [customize detail reports](https://support.google.com/analytics/answer/10445879).

#### B. Tạo summary card và overview report

Overview report là trang tổng quan được tạo từ các summary card. Summary card được tạo từ detail report và có thể dẫn người dùng tới detail report gốc.

Để tạo summary card:

1. Mở detail report liên quan và bấm **Customize report**.
2. Trong **SUMMARY CARDS**, chọn **+ Create new card**.
3. Chọn dimension dropdown, metric dropdown, visualization và card filter nếu cần.
4. Bấm **Apply**, sau đó **Save → Save changes to current report**.

Để tạo overview report:

1. Vào **Reports → Library**.
2. Chọn **+ Create new report → Create overview report**.
3. Chọn **+ Add cards**, chọn card cần dùng và sắp xếp thứ tự. Một overview report có thể có tối đa 16 summary card.
4. Lưu và đặt tên report.

Custom summary card có thể chỉ xuất hiện trong tab **Summary Cards** của overview report sau khi detail report nguồn được thêm vào ít nhất một report collection. Xem [create a summary card](https://support.google.com/analytics/answer/13819308) và [create an overview report](https://support.google.com/analytics/answer/13823841).

#### C. Đưa report vào left navigation

Lưu report trong Library không tự động làm report xuất hiện ở left navigation của property. Editor hoặc Administrator cần thêm report vào collection:

1. Trong **Reports → Library**, tạo collection mới hoặc edit collection hiện có.
2. Tạo hoặc chọn topic.
3. Kéo detail report hoặc overview report vào topic.
4. Bấm **Save**, sau đó dùng **More → Publish** của collection.

Dùng collection cho report mà một audience xác định cần tìm và sử dụng thường xuyên. Giữ exploratory work ở trạng thái private hoặc shared Exploration cho tới khi question, definition và owner ổn định. Xem [customize report navigation](https://support.google.com/analytics/answer/10460557).

#### D. Tạo chart trong free-form Exploration

Dùng Exploration cho investigation, comparison linh hoạt hoặc question chưa ổn định.

1. Mở **Explore → Free form** hoặc bắt đầu từ Exploration template.
2. Trong **Variables**, chỉ thêm dimension, metric và segment cần thiết.
3. Trong **Tab Settings**, đặt dimension vào **Rows/Columns**, metric vào **Values**, rồi áp dụng filter hoặc segment comparison cần thiết.
4. Trong **Visualization**, chọn table, bar, line, donut, scatterplot hoặc geo map theo analytical task.
5. Chỉ thêm tab mới cho một follow-up question riêng; không trộn các question không liên quan trong cùng một tab.
6. Đặt tên rõ ràng, lưu Exploration và ghi date range, configuration, limitation và owner.

Kiểm tra **Data quality** indicator trước khi diễn giải kết quả. Exploration có thể được share và export, nhưng shared Exploration là view-only với user khác; muốn edit cần duplicate. Xem [free-form Exploration](https://support.google.com/analytics/answer/9327972) và [get started with Explorations](https://support.google.com/analytics/answer/7579450).

#### E. Share hoặc export kết quả

Với report đã lưu, mở report từ **Reports**, chọn **Share this report** rồi chọn **Share Link** hoặc **Download File**. Report có thể export thành PDF, CSV hoặc Google Sheets. Với Exploration, dùng **Share exploration** hoặc **Export data** và chọn format phù hợp. Không chia sẻ màn hình Library đang customize như thể đó là saved report. Xem [share and export reports](https://support.google.com/analytics/answer/9317657).

## Chart và Report QA

### Configuration checks

- [ ] Đúng property, stream, timezone và date range.
- [ ] Đúng dimension, metric, scope và compatible combination.
- [ ] Filter/comparison/segment logic khớp written requirement.
- [ ] Key-event definition và metric name hiện hành.
- [ ] Custom definition đã register, available và không duplicate không cần thiết.
- [ ] Chart title, unit, date granularity, breakdown và legend dễ hiểu.
- [ ] Table value và chart value reconcile ở nơi cần reconcile.
- [ ] Report không expose restricted hoặc unnecessary data.
- [ ] Intended audience có access cần thiết.

### Data-quality checks

- [ ] So sánh controlled test period với Data Layer, network và DebugView evidence.
- [ ] Kiểm tra processing delay và recent date chưa hoàn chỉnh.
- [ ] Kiểm tra thresholding indicator và low-volume data có bị ẩn không.
- [ ] Kiểm tra sampling indicator trong Exploration khi applicable.
- [ ] Kiểm tra `(other)` row và high-cardinality dimension.
- [ ] Kiểm tra report identity và attribution context.
- [ ] Kiểm tra event count, users và key-event count có quan hệ hợp lý không.
- [ ] Giải thích discrepancy thay vì âm thầm đổi filter hoặc date.

### Interpretation checks

- [ ] Conclusion trả lời original question.
- [ ] Conclusion tách observation khỏi interpretation.
- [ ] Association không được trình bày như causation.
- [ ] Denominator và time period được ghi rõ.
- [ ] Tracking defect và data limitation đã biết được disclose.
- [ ] Decision/action và owner được ghi nhận.

Mở **Data quality** indicator cạnh title của report hoặc Exploration trước khi chốt analysis. Ghi rõ kết quả là unsampled, thresholded hay sampled và giải thích ảnh hưởng của trạng thái đó tới decision. Xem [GA4 data quality](https://support.google.com/analytics/answer/12856703).

## Limitations cần ghi nhận

### Freshness và processing

Realtime và DebugView phục vụ activity gần đây và diagnostics. Standard report và Exploration có thể dùng processed data với delay. Không đánh giá release từ ngày chưa complete hoặc so sánh current period mới process một phần với historical period đã complete nếu không có note.

### Thresholding và privacy

GA4 có thể giới hạn hoặc ẩn data khi report có nguy cơ expose individual user, đặc biệt với population nhỏ hoặc sensitive dimension. Kết quả trống hoặc giảm không chứng minh rằng event không xảy ra.

### Sampling và data volume

Phân tích lớn hoặc phức tạp có thể bị sampling tùy surface và property configuration. Ghi lại sampling indicator và không trình bày sampled result như exact count.

### Cardinality

High-cardinality dimension có thể khiến value bị gom vào `(other)` hoặc giảm khả năng diễn giải. Không dùng unique user ID, session ID, timestamp, raw URL hoặc free text làm routine custom dimension. Dùng controlled ID hoặc analysis surface khác khi phù hợp.

### Attribution và identity

Acquisition result phụ thuộc attribution setting, reporting identity, lookback window, consent, cross-domain setup và data freshness. DebugView không phải final attribution. Ghi context attribution/identity khi decision phụ thuộc source, medium, campaign hoặc cross-device behavior.

### Reports và Explorations có thể khác nhau một cách hợp lệ

Không nên xem mọi khác biệt giữa Report và Exploration là implementation defect. Hãy so sánh field, filter, comparison/segment, date range, data-retention window, low-user thresholding, behavioral modeling và processing time. Reports và Explorations có thể hỗ trợ field khác nhau và dùng cách filter khác nhau; hãy ghi rõ surface nào là source of truth cho decision. Xem [data differences between Reports and Explorations](https://support.google.com/analytics/answer/9371379) và [data compatibility](https://support.google.com/analytics/answer/11608978).

## Report Requirements Template

**Mục đích:** Dùng template này trước khi build report hoặc Exploration. Nó định nghĩa business question, decision, ai/cái gì được đưa vào, một row hoặc count đại diện cho gì, các field, GA4 area, cadence và owner để output vẫn hữu ích sau khi analyst ban đầu không còn phụ trách.

| ID | Report audience | Business question | Decision | Cadence | Population (ai/cái gì được đưa vào) | Grain (mỗi row/count đại diện cho) | Dimensions | Metrics | Filter/segment | GA4 area | Owner |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| R-01 | Product | Method nào có registration completion thấp hơn? | Ưu tiên UX work | Weekly | User trong registration journey | Một user | Method, device, date | Users, key events, rate | Registration events | Detail report | `[name]` |
| R-02 | Analytics/QA | Confirmed registration có được gửi một lần không? | Approve/fix release | Per release | Event trong test period | Một event occurrence | Event name, method, form ID | Event count | QA traffic | Exploration | `[name]` |

## Interpretation Note Template

**Mục đích:** Dùng note này để giải thích report hoặc Exploration có ý nghĩa gì, quan sát được gì, hỗ trợ decision nào và không thể chứng minh điều gì. Nó ngăn chart được reuse mà thiếu denominator, scope, limitation hoặc attribution context.

```text
Business question:
Decision supported:
Report audience và owner:
GA4 property / stream:
Report hoặc Exploration:
Date range và property timezone:
Population (ai/cái gì được đưa vào):
Grain (mỗi row/count đại diện cho):
Filters, comparisons và segments:
Dimensions và metrics với scope:
Calculation/denominator:
Observed result:
Interpretation:
What this does not prove:
Freshness/processing notes:
Thresholding/sampling/cardinality notes:
Identity/attribution notes:
Tracking hoặc data-quality caveats:
Action, owner và due date:
Review/retirement trigger:
```

## Chart và Report Template Set

Các template này quản lý report sau khi Measurement Plan đã định nghĩa event và parameter contract. Dùng Report Requirements cho request, Field-readiness Inventory để kiểm tra input đã dùng được chưa, Configuration Record để lưu asset đã build, Chart Specification để giải thích lựa chọn visual và Interpretation Note để ghi conclusion được publish.

| Template | Mục đích | Dùng khi |
| --- | --- | --- |
| Report Requirements Template | Định nghĩa question, decision, ai/cái gì được đưa vào, một row/count đại diện cho gì, field, GA4 area, cadence và owner. | Trước khi tạo report hoặc Exploration. |
| Field-readiness Inventory | Xác nhận dimension/metric đã collect, register, process, tương thích và an toàn. | Trước khi dùng field trong report. |
| Report Configuration Record | Ghi configuration chính xác của report/Exploration đã lưu và thông tin maintenance. | Sau khi build hoặc thay đổi đáng kể một report. |
| Chart Specification Record | Giải thích vì sao chọn chart type, breakdown, metric và date granularity. | Khi chart hỗ trợ recurring decision hoặc được chia sẻ với stakeholder. |
| Interpretation Note Template | Ghi observed result, interpretation, limitation và action. | Khi publish hoặc review analysis. |

### Report configuration record template

**Mục đích:** Dùng một record cho một detail report hoặc Exploration đã lưu. Nó giúp asset có thể reproduce và cung cấp đủ thông tin để owner tiếp theo review hoặc rebuild.

```text
Report/Exploration ID:
Name và surface:
GA4 property/stream:
Collection/topic hoặc Exploration:
Business question:
Decision và owner:
Population (ai/cái gì được đưa vào):
Grain (mỗi row/count đại diện cho):
Date range/timezone:
Dimensions với scope (cấp độ dữ liệu):
Metrics và formulas:
Filters/comparisons/segments:
Chart/table configuration:
Custom definitions required:
Field-readiness record (field đã sẵn sàng để reporting chưa?):
Access/sharing:
Report URL hoặc saved location:
Version/last updated:
Maintenance trigger:
Retirement condition:
Reviewer:
```

### Chart specification record template

**Mục đích:** Dùng record này để làm rõ lý do chọn visual. Chart cần truyền đạt một analytical task, không chỉ làm report đẹp hơn.

| Field | Cần ghi nhận |
| --- | --- |
| Chart ID và report ID | ID ổn định của chart và saved report/Exploration. |
| Analytical task | Trend, category comparison, composition, relationship, funnel, path hoặc exact values. |
| Chart type | Line, bar, table, donut/pie, scatterplot, funnel, path hoặc map. |
| Dimension/breakdown | Field trên axis, row, series hoặc slice, gồm cả scope. |
| Metric và denominator | Value được hiển thị và denominator của rate/percentage. |
| Date granularity | Day, week, month hoặc period đã document. |
| Included population/filter | Logic include, exclude, comparison hoặc segment chính xác. |
| Reason và caveat | Vì sao chart trả lời task và điều gì chart không được suy diễn. |
| Owner/review date | Team/người chịu trách nhiệm maintenance và next review. |

Không dùng chart specification thay cho interpretation note. Specification mô tả cách build chart; interpretation note mô tả data có ý nghĩa gì đối với decision.

## Ví dụ: Registration Report và Exploration

### Reusable detail report

```text
Title: Product — Registration health
Primary dimension: Event name hoặc approved registration dimension
Secondary dimensions: Method, form ID, device category
Metrics: Users, event count, key events
Filter: Approved registration event family
Charts: Line trend + bar comparison
Table: Exact values và percentage với denominator đã document
```

### Free-form Exploration

```text
Question: Registration outcome khác nhau như thế nào theo method và device?
Rows: Method
Columns: Device category
Values: Users, event count, key events
Filter: Registration event family
Visualization: Table/heat map cho exact comparison; line tab cho trend
```

Output phải nói rõ quan sát được gì, action nào được hỗ trợ và điều gì không thể chứng minh. Ví dụ, completion rate thấp hơn theo method có thể justify investigation; tự nó không chứng minh method đó gây ra drop.

## Tài liệu tham khảo

- [Reports in the Analytics app](https://support.google.com/analytics/answer/9924671)
- [Create a detail report](https://support.google.com/analytics/answer/13844077)
- [GA4 detail report](https://support.google.com/analytics/answer/10659476)
- [Customize detail reports](https://support.google.com/analytics/answer/10445879)
- [Create a summary card](https://support.google.com/analytics/answer/13819308)
- [Create an overview report](https://support.google.com/analytics/answer/13823841)
- [Customize report navigation](https://support.google.com/analytics/answer/10460557)
- [Share and export reports](https://support.google.com/analytics/answer/9317657)
- [Get started with Explorations](https://support.google.com/analytics/answer/7579450)
- [Free-form Exploration](https://support.google.com/analytics/answer/9327972)
- [Path Exploration](https://support.google.com/analytics/answer/9317498)
- [Custom dimensions and metrics](https://support.google.com/analytics/answer/14240153)
- [GA4 data freshness](https://support.google.com/analytics/answer/11198161)
- [Data quality](https://support.google.com/analytics/answer/12856703)
- [Data compatibility](https://support.google.com/analytics/answer/11608978)
- [Data differences between Reports and Explorations](https://support.google.com/analytics/answer/9371379)
- [About data thresholds](https://support.google.com/analytics/answer/9383630)
- [GA4 cardinality](https://support.google.com/analytics/answer/12226705)
