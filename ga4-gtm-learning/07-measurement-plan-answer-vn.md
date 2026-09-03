# 07 — Measurement Plan cho GA4/GTM

## 1. Mục tiêu, phạm vi và đầu ra

### 1.1 Mục tiêu

Measurement Plan (kế hoạch đo lường) quy định team sẽ đo điều gì, đo để làm gì, khi nào một event được xem là hợp lệ và bàn giao cho việc triển khai ra sao. Tài liệu biến yêu cầu nghiệp vụ thành một Event Contract (hợp đồng event) và Parameter Dictionary (từ điển tham số) đã được phê duyệt, để Application, Data Layer, GTM, consent và GA4 dùng cùng một cách hiểu.

Đây là tài liệu thiết kế và phê duyệt. Nó không phải nhật ký debug lúc chạy thật và cũng không phải hướng dẫn dựng GA4 Report, Exploration hay chart.

### 1.2 Phạm vi

- Thu thập dữ liệu web ổn định ở phía client bằng Google Tag Manager (GTM) và Google Analytics 4 (GA4).
- Business question, business moment chuẩn, tên event, parameter, quy tắc occurrence, chống trùng, consent, privacy, destination, owner và version của schema.
- Quyết định về key event, custom definition, identity, cardinality và data minimization khi yêu cầu có liên quan.
- Các record chuẩn và phần bàn giao cho Sections 01–06.
- Một Registration Journey mẫu ở cuối tài liệu.

### 1.3 Ngoài phạm vi

- Chạy test, thu thập evidence và quyết định pass/fail: xem Section 08 — Debug/QA.
- Phân tích dữ liệu đã được GA4 xử lý, GA4 Reports, Explorations và thiết kế chart: xem Section 09 — Reports/Charts.
- Theo dõi sau khi release production và rollback: xem Section 10 — Release Monitoring.
- Thiết lập ads, campaign, attribution hoặc vận hành Google Ads.

### 1.4 Đầu ra cần có

1. Một plan đã được duyệt cho từng Journey hoặc yêu cầu đo lường.
2. Một Event Contract và Parameter Dictionary cho mỗi event chuẩn.
3. Tham chiếu rõ đến Data Layer, GTM, consent, destination, owner và vòng đời schema.
4. Quyết định về key event, custom definition, identity và phân loại dữ liệu khi cần.
5. Bản bàn giao có version để Sections 08–10 sử dụng mà không tự định nghĩa lại ý nghĩa event.

## 2. Tổng quan: plan kiểm soát điều gì

### 2.1 Chuỗi đo lường

~~~text
Câu hỏi nghiệp vụ
→ business moment chuẩn
→ hợp đồng event và parameter
→ Application push một message Data Layer đầy đủ
→ GTM map field đã duyệt, kiểm tra consent và chọn destination
→ GA4 nhận và xử lý event
→ Debug/QA và Reports/Charts dùng đúng contract đã duyệt
~~~

Application chịu trách nhiệm về sự thật nghiệp vụ. Data Layer mang dữ liệu có cấu trúc để GTM đọc. GTM định tuyến và map những field đã được duyệt. GA4 nhận và xử lý request. Plan ghi lại các quyết định và cách bàn giao; nó không thay thế việc kiểm tra runtime ở Section 08 hay công việc phân tích ở Section 09.

### 2.2 Thuật ngữ cốt lõi

| Thuật ngữ | Ý nghĩa thực tế |
|---|---|
| Event | Một tương tác hoặc trạng thái nghiệp vụ có tên, được gửi đến GA4. |
| Event parameter | Field mô tả event, ví dụ method hoặc form_id. |
| Occurrence | Một lần phát sinh nghiệp vụ được tính là hợp lệ theo rule của event; click hoặc retry không tự động là occurrence mới. |
| Contract | Bộ quy tắc thống nhất cho event và các field: ý nghĩa, kiểu dữ liệu, giá trị được phép, cách xử lý khi thiếu, privacy, owner và version. |
| User property | Thuộc tính tương đối ổn định của user, không phải giá trị phát sinh cho từng event. |
| Key event | Event mà business xác định là một kết quả quan trọng. |
| Custom definition | Đăng ký một parameter thành dimension hoặc metric trong GA4 để có thể phân tích. |
| Schema version | Version của contract event và parameter; chỉ đổi sau khi được review. |

### 2.3 Chọn loại và tên event

Chọn giải pháp ít tùy biến nhất nhưng vẫn mô tả đúng yêu cầu:

1. Dùng event tự động thu thập nếu GA4 đã ghi nhận hành vi đó.
2. Kiểm tra Enhanced Measurement cho những tương tác web được hỗ trợ.
3. Dùng recommended event của Google nếu nó khớp với business moment.
4. Chỉ tạo custom event khi ba lựa chọn trên không mô tả đúng yêu cầu.

Tên event nên viết thường, ổn định và dễ hiểu, ví dụ sign_up hoặc calculation_action. Không nhét giá trị vào tên event, ví dụ sign_up_email; giá trị thay đổi phải nằm trong parameter. Trước khi duyệt, kiểm tra quy tắc đặt tên, tên dành riêng và giới hạn thu thập của Google.

## 3. Quy trình lập Measurement Plan

### 3.1 Xác định quyết định cần hỗ trợ

Ghi lại:

- Câu hỏi hoặc quyết định nghiệp vụ mà việc đo lường phải trả lời.
- Hành động của user hoặc trạng thái nghiệp vụ cần đo.
- Đối tượng hoặc Journey nằm trong phạm vi.
- Điều kiện thành công và người sẽ sử dụng dữ liệu.
- Business owner và technical owner.

Nếu không ai nói rõ event hỗ trợ quyết định nào, chưa nên thêm event.

### 3.2 Xác định business moment chuẩn

Business moment chuẩn là trạng thái của Application hoặc backend chứng minh hành động đã xảy ra. Ưu tiên application event hoặc kết quả server đã xác nhận thay vì click, page view, DOM selector hoặc route change.

Với mỗi event, phải nêu:

- State transition nào tạo ra event.
- Nguồn nào là authoritative: Application hay server.
- Thế nào là một occurrence hợp lệ.
- Retry, refresh, remount, double-submit, cancellation, timeout và server failure được xử lý thế nào.
- Quy tắc idempotency hoặc deduplication nào ngăn một occurrence nghiệp vụ bị gửi lặp.

Phải tách “không có kết quả” hợp lệ khỏi validation failure, timeout, cancellation hoặc server failure. Thiếu state bắt buộc không được coi là occurrence hợp lệ.

### 3.3 Định nghĩa Event Contract và parameter

Với mỗi event, thống nhất:

- Tên chuẩn và loại event.
- Ý nghĩa bằng ngôn ngữ dễ hiểu và occurrence hợp lệ.
- Parameter bắt buộc và không bắt buộc.
- Ý nghĩa, kiểu dữ liệu, scope, giá trị được phép, nguồn và volume dự kiến.
- Cách xử lý khi thiếu, sai hoặc không áp dụng.
- Consent category, privacy classification, destination, owner và schema version.

Field bắt buộc phải có trước khi bàn giao. Field tùy chọn chỉ được bỏ qua khi contract cho phép. Không dùng một giá trị chung như unknown để che giấu lỗi triển khai.

### 3.4 Định nghĩa bàn giao Data Layer và GTM

Plan phải chỉ rõ application event, field trong Data Layer, GTM Variable, Trigger, Tag, environment, consent behavior và destination.

Application push một message hoàn chỉnh tại business moment chuẩn. GTM đọc message đó và chỉ chuyển những scalar field đã được duyệt. Application có thể giữ snapshot nội bộ hoặc request token trong log để correlation; không gửi chúng đến GA4 nếu chưa có phê duyệt riêng.

### 3.5 Quyết định key event, custom definition và identity

Ghi lại quyết định và lý do, không chỉ ghi kết quả mong muốn:

- Vì sao event là hoặc không là key event.
- Field chuẩn hoặc recommended của GA4 đã đáp ứng nhu cầu chưa.
- Có cần custom definition cho câu hỏi phân tích lặp lại hay không.
- Parameter có phù hợp để đăng ký không: giá trị được kiểm soát, scope hữu ích và cardinality chấp nhận được.
- Có cần User-ID theo một identity contract riêng hay không.
- Owner, người duyệt, trạng thái và ngày hiệu lực.

Không đăng ký tất cả parameter theo mặc định. Một parameter vẫn có thể được thu thập mà không cần đăng ký thành custom definition.

### 3.6 Áp dụng privacy và data minimization

Phân loại từng event và parameter trước khi triển khai. Xác định destination được phép và hành vi khi user từ chối consent.

Không gửi email, số điện thoại, password, dữ liệu thanh toán, free text, input form không giới hạn, request token nội bộ hoặc raw account identifier đến GA4 nếu chưa có contract riêng được duyệt. Ưu tiên category có kiểm soát và ID ổn định không nhận diện cá nhân.

### 3.7 Review, phê duyệt và versioning

Trước khi triển khai hoặc thay đổi có thể phá vỡ contract, cần review với business owner, application/frontend owner, analytics owner, privacy/consent reviewer và GTM owner.

Ghi lại:

- Version của plan và schema.
- Event, parameter bị ảnh hưởng.
- Environment và stream đích.
- Ngày hiệu lực, ngày review tiếp theo và trạng thái.
- Những consumer cần migration.

Mọi thay đổi về ý nghĩa phải cập nhật Event Contract, Parameter Dictionary, Data Layer/GTM mapping, consent decision và handoff. Field cũ phải được retire có chủ đích; không âm thầm dùng lại với ý nghĩa mới.

## 4. Các record chuẩn của plan

Các record dưới đây là nguồn sự thật cho giai đoạn planning. Section 08 tham chiếu chúng khi chạy test; Section 09 dùng chúng để suy ra yêu cầu phân tích. Hai section đó không được tự định nghĩa lại Event Contract.

### Thứ tự ưu tiên và thời điểm sử dụng record

Không cần điền tất cả record cùng một lúc. Hãy dùng bộ record nhỏ nhất để phê duyệt yêu cầu trước, sau đó bổ sung record triển khai và lifecycle khi công việc tiến triển.

| Ưu tiên | Record | Dùng khi nào? | Bắt buộc? |
|---|---|---|---|
| P0 | Project Context / Baseline | Bắt đầu product, Journey, environment hoặc phạm vi đo lường mới. | Luôn luôn |
| P0 | Journey / Event Coverage Matrix | Chuyển business question thành danh sách event cần cân nhắc. | Luôn luôn |
| P0 | Event Contract | Phê duyệt ý nghĩa, business moment authoritative, occurrence và destination của event. | Luôn có cho mỗi event |
| P0 | Parameter Dictionary | Phê duyệt field, type, giá trị, nguồn, privacy và cách xử lý khi thiếu. | Luôn có nếu event có parameter |
| P1 | Consent / Data Classification | Trước khi field được expose cho GTM hoặc gửi tới destination. | Luôn có cho dữ liệu được thu thập |
| P1 | Key-Event / Custom-Definition Decision Record | Khi event có thể là key event hoặc parameter có thể cần đăng ký trong GA4. | Theo điều kiện; nếu không cần vẫn ghi “Not required” |
| P1 | Traceability Matrix | Sau khi đã biết mapping Application/Data Layer/GTM và trước khi handoff. | Bắt buộc trước handoff triển khai |
| P2 | Schema Lifecycle Register | Khi thêm, sửa, deprecate hoặc retire event/parameter. | Bắt buộc cho mọi thay đổi schema |

Thứ tự khuyến nghị khi tạo event mới:

~~~text
Project Context
→ Journey/Event Coverage
→ Event Contract
→ Parameter Dictionary
→ Consent/Data Classification
→ Quyết định Key-Event/Custom-Definition (nếu có)
→ Traceability
→ Ghi Schema Lifecycle
~~~

Khi sửa event đã có, mở Schema Lifecycle hiện tại trước, đánh giá consumer bị ảnh hưởng, sau đó cập nhật Event Contract, Parameter Dictionary, consent decision và Traceability Matrix trước khi duyệt version mới. Nếu yêu cầu bị từ chối, dừng sau khi ghi quyết định; không tạo GTM asset.

### 4.1 Project Context / Baseline

| Field | Nội dung cần ghi |
|---|---|
| Plan ID / version | Mã ổn định và version hiện tại. |
| Product / Journey | Khu vực sản phẩm và Journey được bao phủ. |
| GA4 property / stream | Property và web stream dùng cho environment. |
| Google tag / Measurement ID | Destination thu thập đã cấu hình. |
| GTM container | Container và workspace dùng để triển khai. |
| Platform / source | Ứng dụng web và nguồn dữ liệu. |
| Environments | Quy tắc cho local, QA, staging và production. |
| Timezone / currency | Mặc định của ngữ cảnh đo lường. |
| Owners | Business, application, analytics, GTM và privacy owner. |
| Effective / next review | Ngày phê duyệt và ngày cần xem xét lại. |
| Status | Draft, approved, deprecated hoặc retired. |

### 4.2 Journey / Event Coverage Matrix

| Journey ID | Journey | Câu hỏi nghiệp vụ | Chuỗi event dự kiến | Kết quả chính | Owner | Status |
|---|---|---|---|---|---|---|
| J-… | … | … | … → … | … | … | Draft/Approved |

Mỗi dòng đại diện cho một Journey hoặc một yêu cầu. Chỉ thêm event khi có câu hỏi cụ thể cần trả lời.

### 4.3 Event Contract

| Field | Quyết định bắt buộc |
|---|---|
| Requirement / Journey ID | Yêu cầu làm căn cứ cho event. |
| Business question / decision | Event sẽ trả lời điều gì. |
| Event name / type | Tên chuẩn và loại automatic, Enhanced Measurement, recommended hoặc custom. |
| Definition | Ý nghĩa bằng ngôn ngữ dễ hiểu. |
| Authoritative moment / source | State của Application hoặc server chứng minh occurrence. |
| Valid occurrence / deduplication | Quy tắc count, retry, refresh, cancellation và idempotency. |
| Data Layer signal | Tên event và đường dẫn payload chính xác. |
| GTM mapping | Variable, Trigger authoritative và Tag đã duyệt. |
| Environment / destination | Stream và quy tắc routing. |
| Required / optional parameters | Danh sách field theo contract. |
| Consent / privacy | Hành vi được phép và phân loại dữ liệu. |
| Key-event status | Yes, No hoặc Pending kèm lý do. |
| Custom-definition status | Required, Not required hoặc Pending kèm lý do. |
| Owner / reviewer / version | Người chịu trách nhiệm và kiểm soát thay đổi. |

### 4.4 Parameter Dictionary

| Event | Parameter | Ý nghĩa | Type / scope | Bắt buộc? | Giá trị được phép | Khi thiếu hoặc sai | Nguồn | Privacy / consent | Cardinality / volume | GA4 registration |
|---|---|---|---|---|---|---|---|---|---|---|
| … | … | … | … | Yes/No | Danh sách kiểm soát | Bỏ qua, reject hoặc handoff fail | Application/Data Layer | … | Low/Medium/High | Standard/Custom/Not registered |

“GA4 registration” là quyết định trong planning; nó không mô tả cách dựng report.

### 4.5 Traceability Matrix

| Requirement / event | Application state | Data Layer | GTM | Consent | Destination | Owner / status |
|---|---|---|---|---|---|---|
| … | … | … | … | … | … | … |

### 4.6 Key-Event / Custom-Definition Decision Record

~~~text
Decision ID:
Event hoặc parameter:
Requirement / Journey ID:
Business question:
Điều kiện thành công và occurrence hợp lệ:
Quy tắc deduplication:
Key event: Yes / No / Pending
Đã kiểm tra field standard hoặc recommended:
Custom definition: Required / Not required / Pending
Đã xem xét cardinality và quota:
Ảnh hưởng consent và privacy:
Owner / approver:
Ngày hiệu lực và status:
~~~

Record này dùng để phân loại và phê duyệt. Kiểm tra runtime thuộc Section 08; thiết kế report hoặc Exploration thuộc Section 09.

### 4.7 Consent / Data Classification

| Event / parameter | Classification | Consent requirement | Hành vi khi bị từ chối | Destination | Owner / status |
|---|---|---|---|---|---|
| … | Analytics / Sensitive / Restricted | … | Suppress, giảm dữ liệu hoặc alternative đã duyệt | QA/Production stream | … |

Chi tiết triển khai consent xem Section 05.

### 4.8 Schema Lifecycle Register

| Change ID | Event / parameter | Version hiện tại | Version đề xuất | Loại thay đổi | Consumer bị ảnh hưởng | Hành động migration / handoff | Approval / ngày hiệu lực | Status |
|---|---|---|---|---|---|---|---|---|
| … | … | v… | v… | Add/Modify/Deprecate | Application, GTM, GA4, analysis | … | … | Proposed/Approved/Retired |

## 5. Bàn giao triển khai và lưu ý thực tế

### 5.1 Bàn giao tối thiểu

| Hạng mục plan | Application / Data Layer | GTM object | Destination |
|---|---|---|---|
| Event name | Push message hoàn chỉnh đã duyệt | Một Custom Event Trigger authoritative và một GA4 Event Tag | QA hoặc production stream được duyệt |
| Parameter | Dùng đúng type và value set | Data Layer Variable chỉ map field đã duyệt | Event parameter trong GA4 |
| Consent | Không lộ dữ liệu restricted khi bị từ chối | Consent check theo Section 05 | Hành vi thu thập được phép |
| Version | Theo contract đang active | Workspace/change tham chiếu đúng version plan | Cùng semantic version ở handoff |

### 5.2 Cardinality và data minimization

Cardinality là số lượng giá trị khác nhau mà một field có thể tạo ra. Free text có cardinality cao thường khó phân tích và làm dữ liệu GA4 nhiễu. Ưu tiên category ngắn, được kiểm soát; bỏ qua giá trị không bắt buộc. Chỉ tạo custom definition khi business có nhu cầu phân tích lặp lại và tập giá trị ổn định.

### 5.3 Bổ sung cho ecommerce

Với ecommerce, cần định nghĩa business moment, transaction ID authoritative, quy tắc retry và deduplication, value số, currency, schema của item và owner đối soát. Ngữ nghĩa ecommerce nằm trong Event Contract; Data Layer dùng schema đã duyệt ở Section 01.

### 5.4 Tách riêng planning, QA và reporting

| Tài liệu | Câu hỏi chính | Đầu ra |
|---|---|---|
| Measurement Plan (Section 07) | Cần đo gì và ý nghĩa là gì? | Contract và handoff đã duyệt. |
| Debug/QA (Section 08) | Runtime có tạo đúng event, payload, consent, destination và count không? | Evidence và kết quả pass/fail. |
| Reports/Charts (Section 09) | Dữ liệu GA4 đã xử lý được dùng thế nào để trả lời câu hỏi? | Report, Exploration hoặc chart specification. |

Plan có thể ghi trạng thái reportability hoặc custom definition, nhưng không nên chứa quy trình QA hay công thức chart. Tách ba tài liệu giúp test scenario hoặc visualization không trở thành một định nghĩa event thứ hai và mâu thuẫn với contract.

### 5.5 Anti-pattern cần từ chối

| Anti-pattern | Quy tắc thay thế |
|---|---|
| Track mọi click hoặc thay đổi DOM | Track business moment đã được duyệt. |
| Bắn success ngay khi click, trước khi confirm | Chờ state của Application hoặc server chứng minh thành công. |
| Nhét giá trị vào tên event | Giữ một tên event ổn định và dùng parameter. |
| Gửi raw form input hoặc PII | Phân loại, tối thiểu hóa và suppress nếu chưa được duyệt riêng. |
| Đăng ký mọi parameter thành custom definition | Chỉ đăng ký field phục vụ phân tích lặp lại và đã duyệt. |
| Thu thập trùng automatic, Enhanced Measurement và custom | Chọn một nguồn authoritative và ghi rõ ngoại lệ. |
| Đổi tên hoặc dùng lại field một cách âm thầm | Version, migration và retire qua lifecycle register. |

## 6. Bản đồ tham chiếu chéo

| Section | Dùng cho |
|---|---|
| 01 — Data Layer Design | Cấu trúc message, payload và ranh giới Application–GTM. |
| 02 — Variable Management | Đặt tên, scope, tái sử dụng và kiểm tra giá trị của Variable. |
| 03 — Trigger Management | Một Trigger hẹp và authoritative cho event đã duyệt. |
| 04 — Tag Management | GA4 Event Tag, configuration, sequencing và destination mapping. |
| 05 — Consent | Consent state, hành vi khi bị từ chối và consent check. |
| 06 — Template Governance | Template dùng lại, owner, review và deployment record. |
| 08 — Debug/QA | Chạy test và thu evidence theo contract này. |
| 09 — Reports/Charts | Dựng view phân tích từ event và parameter đã xử lý. |
| 10 — Release Monitoring | Theo dõi production sau deployment được duyệt. |

## 7. Journey mẫu: Registration

Ví dụ này chỉ minh họa quyết định trong plan. Dùng Section 08 để kiểm tra implementation và Section 09 để thiết kế view phân tích.

### 7.1 Project context

~~~text
Plan ID: REG-MP-001
Version: 1.0
Product: Web registration
Platform: Client-side web application
Environments: QA stream trước; production sau khi được duyệt
Status: Approved for implementation
~~~

### 7.2 Journey / Event Coverage

| Journey ID | Câu hỏi nghiệp vụ | Chuỗi event dự kiến | Kết quả chính | Owner / status |
|---|---|---|---|---|
| J-REG-001 | Phương thức đăng ký nào hoàn tất thành công? | registration_start → registration_error (khi có) → sign_up | sign_up | Product + Analytics / Approved |

### 7.3 Tóm tắt Event Contract

| Event | Type | Business moment authoritative | Occurrence hợp lệ | Parameter bắt buộc |
|---|---|---|---|---|
| registration_start | Custom | Application đã nhận method đăng ký và form sẵn sàng | Một lần cho mỗi attempt dự kiến; không lặp do remount | form_id, method |
| registration_error | Custom | Application phân loại và hiển thị lỗi đăng ký đã được duyệt | Một lần cho mỗi lỗi đang hiển thị; retry có thể là occurrence mới | form_id, method, error_type |
| sign_up | Recommended | Backend xác nhận tạo account cho lần đăng ký đó | Một lần cho mỗi account được tạo; không lặp do retry hoặc refresh | form_id, method |

### 7.4 Parameter Dictionary

| Event | Parameter | Type | Giá trị được phép | Khi thiếu hoặc sai | GA4 registration |
|---|---|---|---|---|---|
| registration_start | form_id | String | Form ID đã duyệt | Handoff fail; không tự bịa giá trị | Chưa đăng ký nếu chưa cần |
| registration_start | method | String | email, google, apple | Handoff fail | Custom dimension chỉ khi được duyệt |
| registration_error | form_id | String | Form ID đã duyệt | Handoff fail | Chưa đăng ký nếu chưa cần |
| registration_error | method | String | email, google, apple | Handoff fail | Custom dimension chỉ khi được duyệt |
| registration_error | error_type | String | validation, server_error, category đã duyệt khác | Bỏ qua hoặc reject theo contract | Custom dimension chỉ khi phân tích lặp lại |
| sign_up | form_id | String | Form ID đã duyệt | Handoff fail | Chưa đăng ký nếu chưa cần |
| sign_up | method | String | email, google, apple | Handoff fail | Custom dimension chỉ khi được duyệt |

Không gửi email, phone, password, raw form content, request token hoặc raw account identifier đến GA4.

### 7.5 Mapping, consent và destination

| Hạng mục plan | Quyết định đã duyệt |
|---|---|
| Data Layer | Application push một message đầy đủ cho mỗi event authoritative. |
| GTM | Một Custom Event Trigger cho mỗi event và một GA4 Event Tag; chỉ map form_id, method và error_type đã duyệt. |
| Consent | Theo analytics behavior đã duyệt ở Section 05; suppress hoặc giảm dữ liệu khi bị từ chối. |
| Destination | QA stream trong giai đoạn validation; chỉ chuyển production sau khi được duyệt. |
| Key event | sign_up: Pending cho đến khi business owner duyệt định nghĩa thành công. |
| Custom definition | method: Pending; chỉ đăng ký nếu có nhu cầu phân tích lặp lại. |
| Identity | Không dùng User-ID nếu chưa có identity contract riêng. |

### 7.6 Registration schema lifecycle

~~~text
Change ID: REG-CHG-001
Event/parameter: sign_up.method
Current version: v1
Proposed version: v1
Change type: Initial approval
Affected consumers: Application, Data Layer, GTM, GA4, analysis
Migration action: None; triển khai đúng value set đã duyệt
Approval owner: Product + Analytics
Effective date: Sau khi QA được duyệt
Status: Approved
~~~

## Tài liệu tham khảo chính thức

- Google Analytics — About events: https://support.google.com/analytics/answer/9322688
- Enhanced Measurement: https://support.google.com/analytics/answer/9216061
- Recommended events: https://developers.google.com/analytics/devguides/collection/ga4/reference/events
- Custom events: https://support.google.com/analytics/answer/12229021
- Event naming rules: https://support.google.com/analytics/answer/13316687
- Event parameters: https://support.google.com/analytics/answer/13594907
- Event collection limits: https://support.google.com/analytics/answer/9267744
- Custom dimensions and metrics: https://support.google.com/analytics/answer/14240153
- User-ID: https://support.google.com/analytics/answer/9213390
- GTM Custom Event trigger: https://support.google.com/tagmanager/answer/7679219
- Tránh gửi thông tin nhận dạng cá nhân: https://support.google.com/analytics/answer/6366371
