# 09 — GA4 Reports, Explorations, Charts và Interpretation

## 1. Tổng quan

### 1.1 Mục tiêu

Biến dữ liệu GA4 đã được kiểm tra thành Report hoặc Exploration có thể tái lập, trả lời một câu hỏi nghiệp vụ rõ ràng và hỗ trợ một quyết định cụ thể. Chart chỉ là lớp trình bày; cần định nghĩa population, grain, scope, field, công thức và giới hạn trước.

### 1.2 Phạm vi

- Report requirement, population, grain, scope, field readiness, dimension, metric, filter, segment, formula, chart, owner và maintenance.
- GA4 Detail/Overview Report, summary card, collection, sharing và export.
- Free-form, Funnel, Path và các Exploration phù hợp với câu hỏi.
- Tách user-level reporting khỏi event-level collection QA.
- Kiểm tra thực tế về data freshness, compatibility, Data quality, thresholding, sampling, identity, consent và cardinality.

### 1.3 Ngoài phạm vi

- Ý nghĩa event và collection contract: xem Section 07 — Measurement Plan.
- Kiểm tra runtime Data Layer, GTM, consent, Network và DebugView: xem Section 08 — Debug/QA.
- Production activation và post-release monitoring: xem Section 10 — Release Monitoring.
- Ads, campaign optimization, attribution operations hoặc external BI dashboard.

### 1.4 Chuỗi phân tích

~~~text
Audience và decision
→ câu hỏi
→ population và grain
→ dimension, metric và formula
→ Report hoặc Exploration
→ chart/table
→ kiểm tra data quality và limitation
→ interpretation và action
~~~

## 2. Khái niệm cốt lõi

### 2.1 Định nghĩa dễ hiểu

| Thuật ngữ | Cách hiểu đơn giản | Ví dụ trong GA4 | Dùng để quyết định |
|---|---|---|---|
| Audience | Người hoặc team sẽ đọc kết quả và đưa ra hành động. Không phải GA4 Audience dùng cho targeting. | Product team xem registration health. | Ai sở hữu quyết định và cần mức chi tiết nào? |
| Population | Toàn bộ user, session, event hoặc item được đưa vào sau khi áp dụng filter và exclusion đã ghi rõ. | User đã phát registration_start trong một khoảng thời gian. | Thực tế đang đếm ai hoặc cái gì? |
| Grain | Đơn vị mà mỗi dòng hoặc mỗi count đại diện. | Một distinct user khác với một sign_up event. | Câu hỏi ở cấp user, session, event hay item? |
| Scope | Cấp độ mà một field thuộc về và tại đó GA4 có thể kết hợp field với metric. | method là event-scoped; user property thuộc user. | Dimension và metric có kết hợp đúng mà không đổi ý nghĩa câu hỏi không? |
| Dimension | Nhãn mô tả dùng để nhóm, tách hoặc filter dữ liệu. | method, event_name hoặc device category. | Category nào sẽ làm rows, columns hoặc breakdown? |
| Metric | Giá trị dạng số được GA4 đếm, cộng hoặc tính. | Users, event count, key events hoặc revenue. | Cần so sánh hoặc báo cáo đại lượng nào? |
| Denominator | Population gốc của một rate; nó quyết định “100%” nghĩa là gì. | User có registration_start với method = X. | Numerator phải chia cho base nào? |
| Cohort | Population ban đầu được chọn bằng start event và quy tắc thời gian trước khi đo event tiếp theo. | User có registration_start trong cohort window. | User nào đủ điều kiện để tính completion? |
| Event sequence | Thứ tự bắt buộc của các event để kết quả được tính hợp lệ. | registration_start → sign_up. | Completion có xảy ra sau start event theo đúng flow không? |
| Completion window | Khoảng thời gian tối đa sau start event mà event tiếp theo vẫn được tính là completion. | sign_up trong 24 giờ sau registration_start. | Khi nào completion còn thuộc về cohort bắt đầu? |
| User metric | Metric đếm user của GA4 dùng ở cả hai phía, ví dụ Total users hoặc Active users. | Cùng một user metric cho registration_start và sign_up. | Hai phía có đang đếm user theo cùng cách không? |
| Rate | Tỷ lệ phần trăm cho biết bao nhiêu phần của denominator đã đạt numerator. | 70 user hoàn tất ÷ 100 user bắt đầu = 70%. | Kết quả là tỷ lệ tương đối hay chỉ là số lượng thô? |
| Field readiness | Field có ý nghĩa đã duyệt, được collect, available sau processing, tương thích, an toàn về privacy và được duyệt cho câu hỏi. | method đã available dưới dạng processed custom dimension sau expected window. | Field đã sẵn sàng dùng trong Report hoặc Exploration chưa? |
| Report | View được lưu và quản lý để theo dõi lặp lại một câu hỏi ổn định. | Registration health theo method hằng tuần. | Asset này có cần publish và maintain không? |
| Exploration | Workspace linh hoạt để điều tra, so sánh hoặc thử một câu hỏi còn thay đổi. | Free-form QA event-level hoặc funnel investigation. | Đây là phân tích khám phá hay Report chính thức? |

Trước khi chọn chart, luôn ghi ba điểm sau:

~~~text
Population = bao gồm ai hoặc cái gì?
Grain = mỗi dòng hoặc mỗi count đại diện cho điều gì?
Scope = mỗi giá trị thuộc cấp độ nào?
~~~

Tóm lại: Audience cho biết ai hành động, Population cho biết phạm vi được đưa vào, Grain cho biết đang đếm đơn vị nào, Scope cho biết field thuộc cấp độ nào, còn Field readiness cho biết kết quả đã an toàn và đủ điều kiện để sử dụng chưa.

### 2.2 Scope và kỷ luật metric

| Scope | Mô tả | Ví dụ | Rủi ro |
|---|---|---|---|
| User | Một user trong toàn bộ hoạt động. | Users hoặc user property. | Đếm cùng user thành nhiều event. |
| Session | Một lần truy cập. | Session source/medium. | Trộn acquisition theo session với user. |
| Event | Một occurrence của event. | Event name hoặc event parameter. | Coi event count là unique users. |
| Item | Một item trong ecommerce. | Item name hoặc category. | Cộng item rows vào event total mà không kiểm tra grain. |

Hai phía của một rate phải dùng cùng đơn vị đếm và quy tắc population có thể so sánh. Với registration completion rate theo method, hãy định nghĩa:

- **Cohort/denominator:** distinct user có `registration_start` trong cohort và method = X.
- **Numerator:** chính các user trong cohort đó hoàn tất `sign_up` với method = X trong completion window đã duyệt.
- **User metric:** dùng cùng một GA4 user metric (ví dụ Total users hoặc Active users) ở cả hai phía.
- **Rate:** numerator chia cho denominator.

Ví dụ: 100 distinct user bắt đầu bằng method = email trong ngày 1–7/9 và 70 user trong chính cohort đó hoàn tất sign_up bằng method = email trong 24 giờ, thì email registration rate là 70 ÷ 100 = 70%. Với cohort và sequence này, numerator là tập con của denominator. User sign_up nhưng bắt đầu trước cohort window không được tính. Không thay denominator bằng toàn bộ user (bao gồm người chưa từng bắt đầu), toàn bộ event (đổi grain và có thể đếm duplicate) hoặc page view (đo một hành động khác).

Nếu Report chỉ đếm độc lập start và completion theo ngày event mà không có cohort và completion window, hãy gọi đó là event-window ratio, không gọi là validated completion rate.

Giá trị method phải có mặt và thuộc danh sách được kiểm soát ở cả hai event. Nếu method thiếu hoặc không hợp lệ, hãy loại khỏi validated rate hoặc báo cáo riêng; không âm thầm gộp vào method khác.

### 2.3 Reports và Explorations

| Surface | Dùng khi | Quy tắc thực tế |
|---|---|---|
| Overview Report | Tóm tắt cấp topic. | Dùng summary card liên kết tới Detail Report có kiểm soát. |
| Detail Report | Câu hỏi ổn định cần theo dõi định kỳ. | Chỉ publish sau khi definition, field và owner ổn định. |
| Free-form Exploration | So sánh hoặc điều tra linh hoạt. | Ghi rõ rows, columns, values, segment, filter và date range. |
| Funnel Exploration | Completion và drop-off qua các bước. | Ghi rõ đếm user hay event và funnel open/closed. |
| Path Exploration | Khám phá hành vi trước/sau. | Chỉ dùng để khám phá, không chứng minh causation. |
| Cohort Exploration | Retention hoặc hành vi lặp lại. | Định nghĩa cohort và observation period trước khi diễn giải. |

Detail Report thường gồm một table và hai chart; Exploration cho phép phân tích linh hoạt hơn. Không biến mọi tab Exploration tạm thời thành Report chính thức.

### 2.4 Filter, comparison và segment

- **Filter:** giới hạn rows hoặc event trong asset hiện tại.
- **Comparison:** hiển thị các nhóm dữ liệu cạnh nhau trong Reports.
- **Segment:** nhóm user, session hoặc event có thể tái sử dụng trong Explorations.

Viết điều kiện bằng ngôn ngữ dễ hiểu và ghi scope. “Users đã hoàn tất sign_up” khác với “events có event_name bằng sign_up”.

## 3. Quy trình tạo Report và Exploration

### Bước 1 — Xác định câu hỏi và quyết định

Ghi một câu hỏi có liên quan trực tiếp đến quyết định:

~~~text
Audience:
Question:
Decision hoặc action:
Cadence:
Owner:
~~~

Đặt tên theo câu hỏi hoặc quyết định. Tránh tên như Dashboard 1, Test hoặc All Events.

### Bước 2 — Xác định population, grain và scope

Ghi lại:

- property, stream, timezone và date range;
- population và exclusion bằng ngôn ngữ rõ ràng;
- grain: user, session, event hoặc item;
- identity/reporting identity khi có liên quan;
- filter, comparison, segment và comparison period;
- numerator, denominator, cohort, sequence, completion window, user metric và key-event definition nếu tính rate;
- freshness và processing status dự kiến.

Không dùng event count để trả lời câu hỏi user-level nếu chưa ghi rõ lý do.

### Bước 3 — Kiểm tra field readiness

Section 07 là nguồn chuẩn cho ý nghĩa event và parameter. Bước này chỉ kiểm tra các field đã duyệt có sẵn sàng và phù hợp với reporting surface đã chọn hay chưa.

Trước khi dựng asset:

1. Xác nhận event và parameter nằm trong Measurement Plan đã duyệt.
2. Ưu tiên standard dimension/metric nếu có đúng ý nghĩa và scope.
3. Xác nhận field đã được collect và nhìn thấy ở GA4 surface phù hợp.
4. Chỉ đăng ký custom dimension/metric khi có nhu cầu phân tích lặp lại đã duyệt.
5. Ghi ngày đăng ký và thời điểm có thể sử dụng.
6. Kiểm tra privacy, allowed values, cardinality, quota và compatibility.
7. Test field sau processing; parameter xuất hiện trong Network chưa đủ để xem là report-ready.

Custom dimensions và metrics được tạo từ custom data đã thu thập. Google lưu ý chúng có thể cần đến 24–48 giờ mới xuất hiện trong Reports hoặc Explorations; vì vậy cần ghi expected availability, không coi field vừa đăng ký là dùng được ngay.

### Bước 4 — Chọn GA4 surface

Dùng Detail Report cho câu hỏi ổn định, lặp lại. Dùng Exploration cho điều tra, so sánh segment linh hoạt, funnel/path hoặc QA event-level. Ghi surface được chọn trong asset record.

### Bước 5 — Chọn dimension, metric và formula

Chọn bộ field nhỏ nhất nhưng tương thích:

~~~text
Question:
Dimensions và scopes:
Metrics và units:
Filter/comparison/segment:
Numerator:
Denominator:
Formula:
~~~

Với mọi percentage, numerator và denominator phải dùng cùng property, stream, date range, population rule, identity và giá trị dimension. Với rate giữa các event, cần ghi thêm cohort, event sequence, completion window và user metric giống nhau. Nếu UI không biểu diễn đúng công thức, dùng Exploration hoặc export đã được duyệt; không âm thầm thay denominator.

Với rate tính giữa nhiều event, cần chọn rõ cách triển khai:

| Nhu cầu | GA4 surface nên dùng | Quy tắc thực tế |
|---|---|---|
| Theo dõi ổn định một metric mà GA4 đã hỗ trợ trực tiếp | Detail Report | Chỉ dùng khi metric và denominator là native, đồng thời công thức có thể nhìn thấy. Không trình bày hai số đếm riêng như một rate đã được tính. |
| Xem completion và drop-off theo user trong giao diện GA4 | Funnel Exploration | Dùng khi step, cách đếm user, filter method và quy tắc open/closed biểu diễn đúng requirement. Lưu cấu hình và ghi rõ các quy tắc. |
| Tính chính xác distinct-user numerator ÷ denominator giữa các event, cần join hoặc extract lặp lại | Export hoặc BigQuery đã được phê duyệt | Dùng khi GA4 UI không biểu diễn chính xác công thức. Ghi rõ identity, logic theo thời gian event, schema, quyền truy cập và processing delay. |

Nếu requirement cần rate chính xác giữa hai event nhưng Detail Report và Funnel Exploration đều làm thay đổi population, hãy dùng phép tính từ export/BigQuery đã được duyệt. Không âm thầm thay bằng event count, total users hoặc page views.

Thứ tự quyết định: dùng Detail Report cho metric native, Funnel Exploration cho completion flow trong UI, và chỉ dùng phép tính từ export/BigQuery đã được duyệt khi logic cross-event chính xác không thể biểu diễn trong GA4.

### Bước 6 — Chọn chart

| Tác vụ phân tích | Chart phù hợp | Cần kiểm tra |
|---|---|---|
| Xu hướng | Line | Date granularity và ngày gần nhất chưa hoàn tất. |
| So sánh category | Bar | Số category có kiểm soát và scope tương thích. |
| Giá trị chính xác | Table | Unit, sorting và denominator vẫn nhìn thấy. |
| Thành phần ít nhóm | Donut/pie | Các phần tạo thành một whole có ý nghĩa. |
| Quan hệ số | Scatterplot | Association không phải causation. |
| Tiến trình bước | Funnel | User/event counting và funnel rule rõ ràng. |
| Khám phá Journey | Path | Loop/noise là bình thường; không suy ra causation. |
| Địa lý | Geo map | Nhóm nhỏ và privacy threshold được kiểm tra. |

Luôn giữ table hoặc formula hiển thị khi exact value hay denominator quan trọng.

### Bước 7 — Dựng, QA và ghi lại asset

Sau khi dựng asset, kiểm tra:

- property, stream, timezone, date range, field, scope và filter;
- Data quality indicator, freshness, thresholding, sampling và cardinality;
- table value so với chart value;
- interpretation, limitation, owner và maintenance trigger.

Dùng evidence từ Section 08 khi cần kiểm tra collection quality. Report hoặc chart không thay thế runtime QA.

## 4. Các record chuẩn

Mỗi record có một mục đích riêng: requirement record định nghĩa câu hỏi, field-readiness record xác nhận field có thể dùng, asset record giúp tái tạo view, còn interpretation note ghi quyết định. Các định nghĩa dùng chung nên được tham chiếu thay vì lặp lại trong nhiều record.

### 4.1 Report Requirement Record

| Field | Giá trị |
|---|---|
| Requirement ID | [stable ID] |
| Audience / owner | [team và owner] |
| Business question | [một câu hỏi] |
| Decision/action | [kết quả có thể thay đổi điều gì] |
| Cadence | [one-off, weekly, release-based] |
| Population/exclusions | [đối tượng và loại trừ] |
| Grain | [user/session/event/item] |
| Dimensions và scopes | [field đã duyệt] |
| Metrics và formula | [unit, numerator, denominator] |
| Filters/comparisons/segments | [logic bao gồm] |
| GA4 surface | [Detail Report, Overview hoặc Exploration] |
| Review trigger | [thay đổi contract, product hoặc data] |

### 4.2 Field Readiness Record

| Field | Giá trị |
|---|---|
| Field-readiness ID | [stable ID] |
| Field và meaning | [event/parameter/property và ý nghĩa] |
| Source | [GA4 event, parameter, user property hoặc export] |
| Scope | [user/session/event/item] |
| Standard/custom | [standard hoặc custom] |
| Collection confirmed | [date/evidence reference] |
| Registration | [not required hoặc custom definition/date] |
| Expected availability | [processing window] |
| Compatibility/privacy | [status và caveat] |
| Cardinality/quota | [risk và decision] |
| Owner/status | [owner và Ready/Pending/Blocked] |

### 4.3 Asset Configuration Record

Dùng một record cho một Detail Report, Overview Report hoặc Exploration đã lưu:

~~~text
Asset ID:
Requirement ID:
Field-readiness IDs:
Name và surface:
GA4 property/stream/timezone:
Date range:
Population và grain:
Dimensions với scope:
Metrics và formulas:
Filters/comparisons/segments:
Chart/table configuration:
Data-quality và limitation notes:
Access/share/export location:
Version/last updated:
Owner và maintenance trigger:
Retirement condition:
~~~

### 4.4 Interpretation và Decision Note

~~~text
Asset ID:
Requirement ID:
Observed result:
Interpretation:
Decision/action:
What the result does not prove:
Date range và freshness:
Thresholding/sampling/cardinality:
Identity/consent/attribution context:
Section 08 evidence hoặc data-quality caveat:
Owner và due date:
Review/retirement trigger:
~~~

## 5. Triển khai thực tế

### 5.1 Tạo Detail Report

Dùng cho câu hỏi ổn định, được theo dõi lặp lại:

1. Mở Reports → Library.
2. Chọn Create new report → Create detail report.
3. Tạo Blank hoặc chọn template phù hợp.
4. Trong Customize report, thêm dimension, metric, filter và hai chart đã sẵn sàng, tương thích.
5. Đặt title, default dimension, sort metric, description, owner và maintenance trigger.
6. Save, mở Report từ Reports và kiểm tra table có trả lời đúng requirement.
7. Ghi cấu hình vào Asset Configuration Record.

Thay đổi dimension của table, sort metric, filter hoặc comparison có thể thay đổi chart. Ghi table và formula, không chỉ ghi loại chart.

### 5.2 Tạo Overview Report và summary card

Dùng Overview Report để tóm tắt một topic:

1. Tạo hoặc mở Detail Report.
2. Chỉ thêm summary card đại diện cho quyết định ổn định.
3. Mở Reports → Library và tạo hoặc sửa Overview Report.
4. Thêm card vào topic, sắp xếp, save và publish collection.
5. Giữ Detail Report liên kết làm nguồn của exact value.

### 5.3 Tạo Free-form Exploration

Dùng cho phân tích điều tra hoặc xem processed event count sau khi đã kiểm tra runtime ở Section 08:

1. Mở Explore → Free form.
2. Chỉ thêm dimension, metric và segment cần thiết.
3. Đặt dimension vào Rows/Columns và metric vào Values.
4. Áp dụng filter chính xác và ghi logic case-sensitive.
5. Chọn chart phù hợp với tác vụ.
6. Đặt tên, save và ghi date range, property, configuration, owner và limitation.

Kiểm tra Data quality indicator trước khi diễn giải. Exploration được share không tự động trở thành Report chính thức.

### 5.4 Share hoặc export

Dùng chính Report hoặc Exploration đã lưu khi share/export. Ghi asset ID, version, date range và nơi nhận/lưu. Không share màn hình Library customization như thể đó là Report hoàn chỉnh.

### 5.5 Quyền truy cập và publish

Dùng mức quyền thấp nhất đủ cho từng thao tác. Tên quyền thực tế có thể bị ảnh hưởng bởi quyền kế thừa ở account/property và data restriction; hãy xác nhận role matrix hiện tại với property administrator.

| Thao tác | Quyền thực tế nên có | Kiểm soát |
|---|---|---|
| Xem Report, Overview hoặc Exploration đã share | Viewer hoặc cao hơn | Owner cấp quyền cho đúng audience. |
| Tạo, sửa và share Exploration | Analyst hoặc cao hơn, tùy restriction của property | Ghi rõ owner và phạm vi chia sẻ. |
| Customize Detail/Overview Report hoặc publish collection | Editor hoặc Administrator | Review trước khi thay đổi navigation hoặc collection dùng chung. |
| Thay custom definition hoặc setting cấp property | Editor/Administrator theo quyền property | Ghi change và processing window dự kiến. |
| Export hoặc share dữ liệu nhạy cảm | Quyền phù hợp và approved data-handling rules | Xác nhận recipient, destination, retention và privacy approval. |

Publish một Report collection sẽ thay đổi nội dung mà người dùng khác nhìn thấy; save một Exploration không tự động publish nó thành Report.

## 6. QA và giới hạn

### 6.1 Checklist cấu hình

- [ ] Đúng property, stream, timezone và date range.
- [ ] Dimension và metric có scope tương thích.
- [ ] Population, filter, comparison, segment, numerator và denominator khớp requirement.
- [ ] Custom definition đã sẵn sàng, không bị duplicate và vẫn được duyệt.
- [ ] Table và chart reconcile ở nơi cần thiết.
- [ ] Title, unit, date granularity, breakdown và legend dễ hiểu.
- [ ] Quyền truy cập phù hợp với audience.

### 6.2 Checklist chất lượng dữ liệu

- [ ] Có collection evidence từ Section 08 khi cần.
- [ ] Đã ghi freshness và processing status.
- [ ] Đã kiểm tra Data quality indicator.
- [ ] Đã ghi thresholding, sampling, cardinality hoặc (other).
- [ ] Không coi ngày gần nhất chưa hoàn tất là kết quả cuối.
- [ ] Đã ghi identity, consent và attribution context khi có liên quan.

### 6.3 Checklist diễn giải

- [ ] Kết luận trả lời đúng câu hỏi ban đầu.
- [ ] Tách observation khỏi interpretation.
- [ ] Ghi rõ numerator, denominator, grain và date range.
- [ ] Không trình bày association như causation.
- [ ] Công khai tracking defect và data limitation.
- [ ] Ghi decision/action, owner và due date.

### 6.4 Giới hạn thường gặp

- **Freshness:** Realtime phản ánh hoạt động gần đây; Reports và Explorations có thể thay đổi khi GA4 xử lý dữ liệu. Không kết luận từ ngày chưa hoàn tất.
- **Thresholding:** Population nhỏ hoặc nhạy cảm có thể bị ẩn hoặc giảm. Kết quả trống không chứng minh event không xảy ra.
- **Sampling:** Truy vấn lớn hoặc phức tạp có thể được lấy mẫu. Ghi Data quality indicator và không trình bày sampled count như exact count.
- **Cardinality:** Dimension có nhiều giá trị có thể tạo dòng (other) và khó diễn giải. Tránh custom dimension cho unique ID, timestamp hoặc free text.
- **Identity và consent:** User count, attribution và cross-device phụ thuộc reporting identity, consent và cấu hình.
- **Khác biệt surface:** Reports và Explorations có thể khác do field hỗ trợ, filter, segment/comparison, date range, low-user handling và processing. So sánh cấu hình trước khi mở defect collection.

### 6.5 Giá trị thiếu và không hợp lệ

- **`(not set)`:** GA4 không có giá trị dimension cho row hoặc event đó. Giữ riêng để điều tra nguồn dữ liệu; không tính như một method hợp lệ.
- **`Unassigned`:** GA4 không map được giá trị vào grouping hoặc classification đã chọn. Xem đây là vấn đề phân loại/attribution, không đồng nhất với `(not set)`.
- **Giá trị invalid hoặc ngoài controlled list:** Giá trị sai type, rỗng, khác casing quy định hoặc không nằm trong danh sách được duyệt là vi phạm field contract. Loại khỏi validated rate hoặc map vào category đã được phê duyệt; không tự ý đổi tên.
- **Cách xử lý trong Report:** Đếm và báo cáo riêng các row này, liên kết issue với collection evidence ở Section 08 và ghi ảnh hưởng trong Interpretation and Decision Note.

## 7. Bản đồ tham chiếu chéo

| Section | Dùng cho |
|---|---|
| 01 — Data Layer Design | Payload event và nguồn field đã duyệt. |
| 02 — Variable Management | Giá trị ổn định expose cho GTM. |
| 03 — Trigger Management | Chọn event authoritative. |
| 04 — Tag Management | Destination và mapping parameter. |
| 05 — Consent | Collection được phép và denied behavior. |
| 06 — Template Governance | Owner và lifecycle của template dùng lại. |
| 07 — Measurement Plan | Business question, event contract, scope và field approval. |
| 08 — Debug/QA | Runtime evidence cho collection và consent. |
| 10 — Release Monitoring | Kiểm tra sau publish và ownership. |

## 8. Ví dụ hoàn chỉnh — Registration Reporting Journey

Phần này minh họa cách chuyển một Registration requirement thành User-level Report và Event-level QA Exploration. Giá trị trong ngoặc vuông là placeholder cho property, stream, ngày, owner và evidence đã duyệt. Setup và evidence runtime được quản lý ở Section 08 và tham chiếu bằng ID.

### 8.1 Yêu cầu báo cáo Registration

Loại record: Report Requirement Record.

| Field | Giá trị ghi nhận |
|---|---|
| Requirement ID | REQ-REG-001 |
| Audience / owner | Product team / [owner cụ thể] |
| Business question | Phương thức đăng ký nào có completion rate được xác nhận thấp nhất? |
| Decision/action | Điều tra method có rate thấp kéo dài; không suy ra quan hệ nhân quả chỉ từ rate. |
| Cadence | Hàng tuần và sau release có thay đổi flow registration hoặc tracking. |
| Population/exclusions | User production đã được phê duyệt; cohort bắt đầu bằng `registration_start` và `method` thuộc controlled list; loại test traffic chưa duyệt. |
| Grain | Một distinct user cho mỗi method và reporting period. |
| Cohort/sequence/completion window | User có `registration_start` trong [cohort window] và method = X; tính `sign_up` tiếp theo với method = X trong [completion window]. |
| Dimensions and scopes | `method` (event-scoped); field bắt buộc của event và parameter theo contract đã duyệt ở Section 07; chỉ thêm breakdown tương thích khi đã ghi rõ. |
| Metrics and formula | User metric: [Total users hoặc Active users], dùng ở cả hai phía. Numerator: user trong cohort có `sign_up`, `method = X`; denominator: user trong cohort có `registration_start`, `method = X`; rate = numerator ÷ denominator. |
| Filters/comparisons/segments | Hai phía của rate dùng cùng property, stream, date range, reporting identity, cohort, completion window, method value và exclusions. |
| GA4 surface | Saved Detail Report hoặc user-level Funnel Exploration đã duyệt. |
| Review trigger | Thay đổi event contract, method values, field approval, consent behavior hoặc product flow. |
| Section 08 reference | QA run `[QA-REG-RUN-001]` và evidence IDs được quản lý tại Section 08. |

### 8.2 Mức sẵn sàng của các field Registration

Loại record: Field Readiness Record, được duy trì riêng cho từng field mà asset sử dụng.

| Field-readiness ID | Field và ý nghĩa | Source | Scope | Standard/custom | Collection confirmed | Registration | Expected availability | Compatibility/privacy | Cardinality/quota | Owner/status |
|---|---|---|---|---|---|---|---|---|---|---|
| FR-REG-001 | `registration_start` — bắt đầu flow đăng ký | Application data layer → GTM → GA4 event | Event | Custom event | Section 08 evidence: [ID] | Không áp dụng | [processing window] | Event name và consent behavior đã duyệt | Cardinality thấp, có kiểm soát | [owner] / Ready |
| FR-REG-002 | `sign_up` — server xác nhận tạo account | Application data layer → GTM → GA4 event | Event | Recommended GA4 event | Section 08 evidence: [ID] | Không áp dụng | [processing window] | Chỉ phát sau server confirmation | Cardinality thấp, có kiểm soát | [owner] / Ready |
| FR-REG-003 | `method` — phương thức đăng ký thuộc controlled list | Application data layer → GTM → GA4 parameter | Event | Recommended/custom parameter; đăng ký custom dimension khi cần report | Section 08 evidence: [ID] | [custom definition ID/date, nếu cần] | [processing window] | Giá trị có kiểm soát; không free text hoặc identifier | Controlled list | [owner] / Ready |
| FR-REG-004 | `form_id` — identifier của form đã duyệt | Application data layer → GTM → GA4 parameter | Event | Custom parameter; chỉ đăng ký khi cần | Section 08 evidence: [ID] | [custom definition ID/date, nếu cần] | [processing window] | Chỉ dùng giá trị không chứa PII đã duyệt | Giữ danh sách hữu hạn | [owner] / Ready hoặc Pending |

### 8.3 Cấu hình User-level Registration Report

Loại record: Asset Configuration Record.

~~~text
Asset ID: R-REG-001
Requirement ID: REQ-REG-001
Field-readiness IDs: FR-REG-001, FR-REG-002, FR-REG-003, FR-REG-004
Name and surface: Registration completion by method — Detail Report (hoặc user-level Funnel Exploration đã duyệt)
GA4 property/stream/timezone: [property] / [web stream] / [timezone]
Date range: [start] → [end]
Population and grain: User đã duyệt có registration_start trong [cohort window]; một distinct user cho mỗi method và reporting period; loại test traffic
Dimensions with scope: method (event-scoped)
Metrics and formulas: [Total users hoặc Active users] trong cùng cohort; users(sign_up, method = X trong [completion window]) ÷ users(registration_start, method = X); implementation path [Funnel Exploration / export hoặc BigQuery đã duyệt]
Filters/comparisons/segments: Numerator và denominator dùng cùng method, property, stream, date range, identity, cohort, completion window và exclusions
Chart/table configuration: Table hoặc bar chart theo method; giữ numerator, denominator và công thức hiển thị được
Data-quality and limitation notes: [freshness, thresholding, sampling, (other), cardinality, identity, consent]
Access/share/export location: [link hoặc vị trí]
Version/last updated: v1.0 / [date]
Owner and maintenance trigger: [owner] / review sau thay đổi contract, field, consent hoặc flow
Retirement condition: Requirement hoặc registration flow bị loại bỏ
~~~

Chọn implementation path theo thứ tự quyết định ở Bước 5: Funnel Exploration khi UI giữ đúng cohort và sequence; nếu không, dùng phép tính từ export/BigQuery đã được duyệt. Detail Report chỉ phù hợp với metric được GA4 hỗ trợ trực tiếp và không được ngầm hiểu là cross-event rate nếu Report không tự tính công thức đó.

### 8.4 Event-level Registration QA Exploration

Loại record: Asset Configuration Record.

~~~text
Asset ID: EX-REG-QA-001
Requirement ID: REQ-REG-001 (runtime acceptance hỗ trợ business question)
Field-readiness IDs: FR-REG-001, FR-REG-002, FR-REG-003, FR-REG-004
Name and surface: Registration event QA — Explore → Free form
GA4 property/stream/timezone: [giống R-REG-001]
Date range: [QA window có kiểm soát]
Population and grain: Controlled QA traffic; một event occurrence
Dimensions with scope: event_name, method, form_id (event-scoped)
Metrics and formulas: event count; không tính completion rate theo user
Filters/comparisons/segments: QA window, registration event family đã duyệt, test/build identifier
Chart/table configuration: Table với event_name ở rows và method/form_id ở columns
Data-quality and limitation notes: Expected một registration_start khi bắt đầu journey và một sign_up cho mỗi account được confirm; processed-count và runtime evidence tham chiếu Section 08 QA run [ID]
Access/share/export location: [link hoặc vị trí]
Version/last updated: v1.0 / [date]
Owner and maintenance trigger: [owner] / review sau thay đổi event contract, mapping hoặc consent
Retirement condition: QA workflow hoặc registration contract bị loại bỏ
~~~

Exploration này kiểm tra event duplicate, missing, sai timing, sai destination và thiếu parameter. Nó không chứng minh user-level rate hoặc request count.

### 8.5 Diễn giải và quyết định cho Registration

Loại record: Interpretation and Decision Note. Cấu hình chart nằm trong từng Asset Configuration Record; kết quả được ghi một lần ở note này.

~~~text
Asset ID: R-REG-001; EX-REG-QA-001
Requirement ID: REQ-REG-001
Observed result: [user metric]; cohort [range]; completion window [window]; [numerator] / [denominator] = [rate] theo method; event-level count [value]
Interpretation: [mô tả chênh lệch hoặc collection defect quan sát được, không khẳng định nhân quả]
Decision/action: [investigate, accept, rollback hoặc monitor] / [owner] / [due date]
What the result does not prove: method gây ra chênh lệch; event-level QA chứng minh user-level rate; processed report chứng minh request count
Date range and freshness: [range] / [freshness và processing status]
Thresholding/sampling/cardinality: [status và impact]
Identity/consent/attribution context: [reporting identity, consent state, attribution caveat]
Section 08 evidence or data-quality caveat: [QA run/evidence IDs và gap chưa giải quyết]
Owner and due date: [owner] / [date]
Review/retirement trigger: thay đổi contract, field, consent, release hoặc reporting surface
~~~

Setup kiểm thử runtime, kết quả scenario và evidence được quản lý ở Section 08. Tham chiếu ID của chúng từ Report Requirement Record hoặc Interpretation and Decision Note.

## Tài liệu tham khảo chính thức

- [GA4 detail reports](https://support.google.com/analytics/answer/10659476)
- [Overview reports](https://support.google.com/analytics/answer/10659551)
- [Create a detail report](https://support.google.com/analytics/answer/13844077)
- [Customize detail reports](https://support.google.com/analytics/answer/10445879)
- [Create an overview report](https://support.google.com/analytics/answer/13823841)
- [Create a summary card](https://support.google.com/analytics/answer/13819308)
- [Customize report navigation](https://support.google.com/analytics/answer/10460557)
- [Free-form Exploration](https://support.google.com/analytics/answer/9327972)
- [Get started with Explorations](https://support.google.com/analytics/answer/7579450)
- [Custom dimensions and metrics](https://support.google.com/analytics/answer/14240153)
- [GA4 data freshness](https://support.google.com/analytics/answer/11198161)
- [Data quality](https://support.google.com/analytics/answer/12856703)
- [Data sampling](https://support.google.com/analytics/answer/13331292)
- [GA4 cardinality](https://support.google.com/analytics/answer/12226705)
- [Data differences between Reports and Explorations](https://support.google.com/analytics/answer/9371379)
