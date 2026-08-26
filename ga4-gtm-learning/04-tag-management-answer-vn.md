# GTM Tag Management Standard — Bản dịch tiếng Việt

## Mục đích và phạm vi (Purpose and Scope)

## 1. Lý thuyết (1. Theory)

### Tag là gì? (What is a tag?)

Tag là một cấu hình tracking hoặc operational, được chạy khi các điều kiện của Trigger được thỏa mãn. Một tag thường trả lời ba câu hỏi:

1. **What should happen? — Cần thực hiện việc gì?** Ví dụ: gửi một GA4 event hoặc một Google Ads conversion.
2. **When should it happen? — Khi nào thực hiện?** Trigger và các exception đã được approve quyết định điều này.
3. **Where should the result go? — Kết quả gửi đi đâu?** Destination và cấu hình environment quyết định điều này.

Luồng GTM đơn giản:

```text
Application / Browser
        ↓
Data Layer event and values
        ↓
Variables
        ↓
Triggers and exceptions
        ↓
Consent evaluation
        ↓
Tag
        ↓
Environment-safe destination
        ↓
Network request and downstream platform
```

Application nên sở hữu business fact. GTM chỉ nên route và cấu hình measurement cho business fact đó; không nên tự dựng lại business logic từ click, page markup hoặc raw form field nếu application có thể cung cấp signal do application sở hữu trong Data Layer.

### Google tag là gì? (What is the Google tag?)

Google tag là một cấu hình Google measurement, dùng để kết nối website với các Google destination được hỗ trợ như GA4 và Google Ads. Đây là một loại tag trong GTM, không phải tên gọi chung cho mọi tag của GTM.

```text
GTM tag
├── Google tag                 ← shared Google measurement configuration
├── GA4 Event tag              ← sends a named GA4 event
├── Google Ads conversion tag
├── Approved template tag
└── Custom HTML tag            ← exception requiring review
```

Global site tag cũ (`gtag.js`) và Google tag là các khái niệm Google measurement có liên quan. Tuy nhiên, một GTM container vẫn có nhiều loại tag.

## 2. Các loại tag phổ biến (2. Common Tag Types)

Chọn tag type được hỗ trợ và phù hợp nhất với requirement.

| Requirement                                | Tag type ưu tiên               | Ví dụ                                                           |
| ------------------------------------------ | ------------------------------ | --------------------------------------------------------------- |
| Cấu hình shared Google measurement         | Google tag                     | Cấu hình FD GA4 destination bằng environment routing đã approve |
| Gửi GA4 business event                     | GA4 Event                      | Gửi `calculation_action` với các parameter đã approve           |
| Gửi Google Ads conversion                  | Google Ads Conversion Tracking | Gửi purchase conversion đã được xác nhận                        |
| Hỗ trợ third-party integration đã approve  | Reviewed template              | Dùng Community Template đã review và có owner rõ ràng           |
| Triển khai hành vi custom chưa được hỗ trợ | Custom HTML                    | Ngoại lệ đã qua review về security, privacy và implementation   |

Ưu tiên tag type có sẵn trong GTM hoặc reviewed template. Custom HTML không phải phương án mặc định khi đã có tag type được hỗ trợ đáp ứng requirement.

## 3. Các thành phần của một tag (3. Anatomy of a Tag)

Một production tag phải có thể được hiểu thông qua các thành phần sau:

| Thành phần      | Câu hỏi cần trả lời                                                  |
| --------------- | -------------------------------------------------------------------- |
| Purpose         | Tag tồn tại để làm gì và đáp ứng requirement nào?                    |
| Type/template   | Dùng tag type được hỗ trợ hoặc reviewed template nào?                |
| Event/action    | Tag thực hiện measurement hoặc operational action gì?                |
| Parameters      | Gửi những value nào được định nghĩa trong contract?                  |
| Variables       | Mỗi value lấy từ đâu và xử lý thế nào khi thiếu?                     |
| Trigger         | Điều kiện authoritative nào làm tag đủ điều kiện chạy?               |
| Exceptions      | Khi nào tag không được chạy? Exception có cần thiết và đã test chưa? |
| Consent         | Cần consent state nào và chuyện gì xảy ra khi state thay đổi?        |
| Destination     | Data được gửi tới environment và property/stream nào?                |
| Firing behavior | Một business occurrence dự kiến tạo bao nhiêu request?               |
| Sequencing      | Có dependency setup hoặc cleanup thật sự không?                      |
| Consumers       | Team, report, audience hoặc downstream system nào dùng data?         |
| Owner           | Ai approve thay đổi, trả lời câu hỏi và xác nhận retirement?         |
| Lifecycle       | Tag đang Proposed, QA, Active, Deprecated hay Retired?               |

Nếu không trả lời được bất kỳ câu hỏi nào ở trên, tag chưa sẵn sàng cho production.

## 4. Tiêu chuẩn thiết kế (4. Design Standard)

- **Chỉ tạo tag khi có measurement hoặc operational requirement được ghi nhận.** Mọi production tag phải có purpose và owner rõ ràng.  
  **Ví dụ:** Chỉ tạo `GA4 Event - FD - calculation_action` khi `calculation_action` đã được định nghĩa trong FD measurement plan đã approve.

- **Ưu tiên built-in GTM tag type hoặc reviewed template.** Chỉ dùng Custom HTML khi không có supported option phù hợp và implementation đã được review.  
  **Ví dụ:** Dùng GA4 Event tag để gửi `calculation_action`, thay vì viết custom `gtag()` call trong Custom HTML tag.

- **Map parameter từ approved variable và ưu tiên value do application sở hữu trong Data Layer thay vì DOM hoặc raw form field.** Không bao giờ gửi PII, credentials, secrets hoặc unrestricted user input.  
  **Ví dụ:** Dùng `{{FD - DLV - solution_found}}` thay vì đọc result text trực tiếp từ page.

- **Chỉ gửi parameter được định nghĩa trong measurement contract.** Required và optional parameter phải tuân theo cách xử lý khi thiếu đã được ghi nhận.  
  **Ví dụ:** Bỏ `product_category` nếu optional value không có; nếu required `connection_type` bị thiếu thì fail QA, không gửi `unknown`.

- **Dùng Trigger đơn giản và đáng tin cậy nhất, đại diện duy nhất cho điều kiện measurement.** Chỉ thêm filter hoặc exception khi tracking contract yêu cầu và hành vi của chúng đã được hiểu, test.  
  **Ví dụ:** Nếu application push `event: "calculation_action"`, dùng Custom Event đó thay vì thêm các điều kiện click và DOM không cần thiết.

- **Tránh các tag bị overlap và gửi cùng một measurement cho cùng một business occurrence.** Xác định một canonical tracking path và expected request count.  
  **Ví dụ:** Một calculation hoàn tất phải tạo đúng một `calculation_action` request, không phải một request từ generic FD tag và một request từ calculation-specific tag.

- **Không dùng tag sequencing để thay thế application hoặc Data Layer contract.** Chỉ dùng và ghi lại sequencing khi có dependency setup hoặc cleanup thật sự.  
  **Ví dụ:** Không fire Tag B sau Tag A chỉ để tạo calculation data; application phải cung cấp event và value đầy đủ.

- **Định nghĩa và test consent behavior mà tag yêu cầu.** Không bypass consent design đã approve bằng custom trigger logic.  
  **Ví dụ:** Tôn trọng analytics consent behavior đã approve, thay vì tạo trigger `consent = true` để thay thế.

- **Không coi “Tag Fired” trong GTM Preview là bằng chứng collection thành công.** Phải kiểm tra request thật, destination, event name, parameter, consent state và expected request count.  
  **Ví dụ:** Sau khi `calculation_action` fire, xác nhận một request tới đúng GA4 Measurement ID với payload đã approve.

- **Không nhân bản tag chỉ để xử lý khác biệt giữa các environment.** Khi phù hợp, route configuration phụ thuộc environment, như GA4 Measurement ID, qua shared configuration đã review.  
  **Ví dụ:** Dùng một FD GA4 Event tag với environment-safe configuration variable thay vì bản staging và production riêng.

- **Environment routing phải fail safely.** Environment unknown hoặc non-production không được vô tình gửi data tới production destination.  
  **Ví dụ:** Unknown hostname trả về không có Measurement ID hoặc block tag, thay vì fallback về production.

- **Review trigger, exception, variable, consent setting, sequencing, destination và consumer trước khi sửa hoặc retire tag.**  
  **Ví dụ:** Trước khi xóa `GA4 Event - FD - calculation_action`, kiểm tra mọi reference, downstream consumer, replacement và production dependency.

## 5. Hướng dẫn quyết định tạo tag (5. Tag Decision Guide)

Trước khi tạo tag, trả lời lần lượt các câu hỏi sau:

1. **Có measurement hoặc operational requirement đã được approve không?**
   - **Không:** Không tạo tag.
2. **Đã có tag hiện tại đáp ứng requirement chưa?**
   - **Có:** Chỉ reuse nếu purpose, destination, consent và contract vẫn tương thích.
3. **GTM có built-in tag type phù hợp không?**
   - **Có:** Ưu tiên tag type đó.
4. **Có reviewed template cho requirement không?**
   - **Có:** Ưu tiên reviewed template hơn Custom HTML.
5. **Tất cả required value đã có qua approved variable chưa?**
   - **Chưa:** Sửa application/Data Layer hoặc variable contract trước.
6. **Đã có authoritative trigger chưa?**
   - **Chưa:** Cải thiện application signal nếu có thể; không bù bằng DOM hoặc route logic ngày càng rộng.
7. **Consent behavior đã được định nghĩa chưa?**
   - **Chưa:** Định nghĩa trước release.
8. **Destination có environment-safe không?**
   - **Không:** Sửa routing trước khi tạo hoặc publish tag.
9. **Có test được positive, negative, duplicate, consent và destination case không?**
   - **Không:** Không publish.
10. **Đã có owner được chỉ định và inventory record chưa?**
    - **Chưa:** Phân công owner trước khi tag trở thành production-active.

## 6. Tiêu chuẩn đặt tên (6. Naming Standard)

Dùng format dễ đoán:

```text
[TYPE] - [SCOPE] - [EVENT OR PURPOSE] - [QUALIFIER]
```

Ví dụ:

```text
Google tag - FD - Primary
GA4 Event - FD - calculation_action
Google Ads - Web - purchase
CE - FD - calculation_action
DLV - FD - solution_found
LUT - Shared - GA4 Measurement ID by Environment
```

Quy tắc đặt tên:

- Dùng đúng spelling và casing canonical của event trong measurement contract.
- Thêm scope khi container phục vụ nhiều product hoặc application.
- Đặt tên Custom Event trigger với `CE`; Data Layer Variable với `DLV`; lookup table với `LUT`.
- Tránh các tên như `New Tag`, `Test`, `Copy` hoặc `Tag 14` trong shared container.
- Nếu phải giữ legacy design, thêm `Legacy` vào tên khi cần để phân biệt với preferred path.

Description của tag phải ghi business purpose, owner, requirement hoặc ticket, event name, parameter contract, destination, consent behavior, expected firing count, dependencies và retirement condition.

## 7. Hành vi firing của tag (7. Tag Firing Behavior)

### Events tạo timeline (Events create the timeline)

GTM evaluate trigger trên mỗi event trong Data Layer/event queue. Các page lifecycle event thường đi qua các giai đoạn:

```text
Consent Initialization
        ↓
Initialization
        ↓
Container Loaded / Page View
        ↓
DOM Ready
        ↓
Window Loaded
        ↓
Application and user events
```

Custom Event xảy ra theo thứ tự application push chúng. Với FD, sequence có thể là:

```text
Input updated
        ↓
Calculation completes
        ↓
calculation_action pushed
        ↓
GA4 Event tag evaluated and possibly fired
```

### Triggers không tạo thành sequence (Triggers do not form a sequence)

Nhiều firing trigger trên cùng một tag thường là các điều kiện thay thế nhau:

```text
Trigger A matches OR Trigger B matches
        ↓
Tag may fire
```

Thứ tự trigger hiển thị trong GTM UI không phải execution order. Trigger Group xác nhận các điều kiện thành viên đã xảy ra; không nên dùng nó để tự tạo business process vốn thuộc về application.

### Expected request count là một phần của contract (Expected request count is part of the contract)

Ghi rõ tần suất dự kiến:

```text
One confirmed calculation
        ↓
One calculation_action request
```

Kiểm tra các nguyên nhân có thể tạo duplicate:

- Data Layer push lặp;
- generic trigger và event-specific trigger bị overlap;
- SPA navigation hoặc component remount;
- retry, callback và API completion handler;
- nhiều tag cùng gửi một event;
- tag sequencing vô tình chạy lại tag.

Có thể dùng firing option được GTM hỗ trợ khi phù hợp, nhưng không dùng firing option để sửa một business-event contract bị hỏng. Application hoặc Data Layer phải định nghĩa hai calculation hoàn tất là hai occurrence hợp lệ hay chỉ là một occurrence bị lặp.

## 8. Quản lý environment và destination (8. Environment & Destination Management)

Environment routing phải được tập trung, có thể review và fail-safe.

### Các control bắt buộc (Required controls)

- Giữ Google tag/GA4 configuration ở một nơi dùng chung và đã review khi có thể.
- Route các value phụ thuộc environment qua configuration variable hoặc lookup table đã review, ví dụ `{{LUT - Shared - GA4 Measurement ID by Environment}}`.
- Map rõ các environment đã biết như local, QA, staging và production.
- Không hard-code production Measurement ID trong event tag khi đã có shared configuration có kiểm soát.
- Không tạo tag trùng chỉ vì destination khác nhau giữa các environment.
- Xem hostname không biết, environment bị thiếu hoặc mapping không hợp lệ là trường hợp block hoặc không gửi.
- Kiểm tra Measurement ID thực tế trong outgoing request; GTM Preview tự nó không chứng minh request an toàn cho production.
- Ghi destination, stream/property, region và các giả định data governance trong inventory và tag description.

Routing mẫu:

```text
Known QA hostname         → QA Measurement ID
Known staging hostname    → staging/test Measurement ID
Known production hostname → production Measurement ID
Unknown hostname          → undefined / tag blocked
```

Nếu cần container hoặc workspace riêng cho một environment, phải ghi rõ lý do và giữ configuration contract nhất quán. Tách environment không được tự động tạo ra tracking logic khác nhau mà không qua review.

## 12. Inventory và ownership (12. Inventory & Ownership)

Inventory là operational record của container, không chỉ là danh sách tên tag.

| Field                | Cần ghi lại                                             |
| -------------------- | ------------------------------------------------------- |
| Tag                  | Tên GTM tag chính xác                                   |
| Type/template        | Built-in type hoặc reviewed template                    |
| Purpose/requirement  | Lý do business và tracking-plan reference               |
| Event/action         | Event hoặc operational action chính xác                 |
| Firing triggers      | Tất cả trigger và condition                             |
| Exceptions           | Blocking condition và lý do                             |
| Variables/parameters | Source, type, required/optional behavior                |
| Consent              | State cần có và behavior khi denied/update              |
| Destination          | Environment, property/stream, nguồn Measurement ID      |
| Expected count       | Ví dụ `1 per calculation`                               |
| Environment          | Local, QA, staging, production hoặc controlled routing  |
| Consumers            | Report, audience, export hoặc system phụ thuộc          |
| Owner                | Business/analytics owner và maintainer chịu trách nhiệm |
| Status               | Proposed, Development, QA, Active, Deprecated, Retired  |
| Dependencies         | Google tag, sequencing, template, legacy path           |
| Replacement          | Replacement đã approve nếu có                           |
| Evidence             | Version, ticket, link QA và production validation       |
| Review date          | Lần review tiếp theo hoặc ngày hết hạn                  |

Ownership không chỉ là việc có tên trong spreadsheet. Owner phải approve measurement purpose, parameter contract, destination, consent behavior và quyết định retirement.

## 13. Quy trình review và test (13. Review and Test Workflow)

Dùng workflow này cho tag mới và material change của tag hiện có:

1. Trace business requirement về approved tracking plan và Data Layer contract.
2. Tìm trong container và inventory các tag, trigger, variable, legacy path và consumer đã có.
3. Chọn tag type đã approve và đơn giản nhất; ghi lý do phù hợp.
4. Verify hoặc tạo variable đã approve, đúng scope, type và missing-value behavior.
5. Cấu hình shared Google tag/destination qua environment-safe routing.
6. Review firing trigger, exception, consent setting, sequencing và expected request count.
7. Chạy Preview test cho valid, invalid, repeated, navigation/SPA và consent case.
8. Xác nhận network request thực, destination, payload, consent state và request count.
9. Lấy peer/business/privacy review cần thiết và ghi evidence.
10. Publish focused version kèm release notes và rollback plan.
11. Chạy production smoke test sau publish.
12. Monitor volume, quality, consent behavior và duplicate; cập nhật inventory.

Hãy xem “Tag Fired” là một diagnostic signal, không phải bằng chứng collection end-to-end.

## 14. Vòng đời tag (14. Tag Lifecycle)

### Các status được khuyến nghị (Recommended statuses)

```text
Proposed
    ↓
Development
    ↓
QA
    ↓
Active
    ↓
Deprecated
    ↓
Retired
```

- **Proposed:** Có requirement nhưng implementation chưa được approve.
- **Development:** Đang build hoặc thay đổi configuration.
- **QA:** Đang validation và chưa được approve cho production.
- **Active:** Đã approve, publish, ghi inventory và monitor.
- **Deprecated:** Tạm thời còn tồn tại nhưng đã có replacement hoặc retirement plan được approve.
- **Retired:** Không còn requirement hoặc consumer active; có thể remove sau dependency review.

### Quy tắc lifecycle (Lifecycle rules)

- Chỉ pause như một biện pháp chẩn đoán hoặc rollback ngắn hạn có ghi nhận; không để paused tag trở thành clutter vĩnh viễn.
- Trước khi delete, kiểm tra toàn bộ dependency của trigger, variable, sequencing, template, destination và consumer.
- Giữ versioned recovery point và evidence trước khi remove.
- Ghi owner, replacement, retirement reason, date và approval.
- Chỉ deprecate legacy `webApps` consumer sau khi verify authoritative event path và actual production request count.
- Kiểm tra lại report, audience, export và downstream use trước khi retire event.

## 15. Các anti-pattern phổ biến (15. Common Anti-patterns)

| Anti-pattern                                                              | Rủi ro                                         | Cách ưu tiên                                                |
| ------------------------------------------------------------------------- | ---------------------------------------------- | ----------------------------------------------------------- |
| Custom HTML cho GA4 event đã được hỗ trợ                                  | Code không cần thiết, khó review               | Dùng built-in GA4 Event tag                                 |
| DOM scraping hoặc raw form collection                                     | Fragile, có privacy và data-quality risk       | Dùng application-owned Data Layer value đã approve          |
| Hard-code production Measurement ID                                       | Environment leakage                            | Dùng shared routing có kiểm soát và safe failure            |
| Copy cùng một tag cho từng environment                                    | Drift và phải bảo trì trùng lặp                | Dùng shared logic, route configuration tập trung            |
| Generic và event-specific tag cho một occurrence                          | Duplicate collection                           | Một canonical tracking path và expected count               |
| Broad `webApps`/route trigger không có contract                           | Hidden coupling và accidental match            | Ưu tiên exact business Custom Event; giới hạn legacy filter |
| Regex không anchor hoặc không phân biệt case để che value không nhất quán | Unexpected match và contract drift             | Dùng canonical value và pattern explicit, bounded           |
| Click trigger cho calculation hoàn tất                                    | Click không chứng minh business success        | Trigger trên authoritative completion event                 |
| Tag sequencing để dựng business logic                                     | GTM trở thành application workflow engine      | Application cung cấp complete event data                    |
| Dùng “Tag Fired” làm collection evidence                                  | Không chứng minh payload hoặc destination      | Validate network request và downstream receipt              |
| Paused hoặc copied tag tồn tại vĩnh viễn                                  | Container clutter, ownership không rõ          | Track lifecycle và retire configuration lỗi thời            |
| Không biết owner hoặc thiếu inventory                                     | Thay đổi không an toàn, incident response chậm | Assign owner và ghi dependency                              |
| Placeholder cho required field                                            | Âm thầm tạo data-quality failure               | Fail QA và sửa upstream contract                            |
| Bypass consent bằng custom trigger                                        | Privacy và governance failure                  | Dùng consent design đã approve, test state changes          |

## 11. Ví dụ chi tiết: Tạo một FD GA4 Event tag đạt production quality (11. Detailed Best-Practice Example — Create One Production-Quality FD GA4 Event Tag)

Ví dụ này mô tả toàn bộ vòng đời của một tag. Tên trong ví dụ là tên mẫu và phải được điều chỉnh theo naming convention và measurement plan thực tế.

### Bước 1 — Bắt đầu từ measurement contract (Step 1 — Start from the measurement contract)

Không bắt đầu bằng cách bấm **New Tag**. Hãy bắt đầu từ contract đã approve.

| Contract field         | Ví dụ                                                                            |
| ---------------------- | -------------------------------------------------------------------------------- |
| Business requirement   | Đo một FD calculation đã hoàn tất                                                |
| Event name             | `calculation_action`                                                             |
| Business occurrence    | Application hoàn tất calculation và result state đã có                           |
| Authoritative signal   | Data Layer `event: "calculation_action"`                                         |
| Expected request count | Chính xác một request cho mỗi calculation hoàn tất                               |
| Required parameter     | `solution_found`: boolean theo approved Data Layer contract                      |
| Required parameter     | `connection_type`: giá trị enum đã approve                                       |
| Optional parameter     | `product_category`: giá trị đã approve; bỏ khi không có                          |
| Missing required value | Fail QA và block release; không gửi placeholder                                  |
| Missing optional value | Bỏ parameter, trừ khi contract quy định behavior khác                            |
| Privacy rule           | Không có PII, credentials, secrets, raw form values hoặc unrestricted user input |
| Consent rule           | Theo approved analytics consent matrix                                           |
| Destination            | FD GA4 destination do environment-safe configuration chọn                        |
| Owner                  | FD analytics/product owner và GTM maintainer được chỉ định                       |
| Consumers              | GA4 report, exploration hoặc downstream process đã approve                       |

Measurement contract là source of truth. Nếu contract chưa đầy đủ, phải giải quyết gap trước khi config.

### Bước 2 — Kiểm tra path hiện có và reuse asset đã approve (Step 2 — Check for existing paths and reuse approved assets)

Trước khi tạo mới:

1. Tìm trong container inventory tag, trigger hoặc dispatcher consumer đã có cho `calculation_action`.
2. Kiểm tra generic FD tag, `webApps` trigger và các điều kiện legacy `app_action`.
3. Xác định canonical path đã có chưa và tag mới có gây duplicate không.
4. Reuse Google tag/configuration, variable, consent setting và template đã approve khi contract tương thích.
5. Ghi legacy path là dependency nếu nó phải tồn tại trong thời gian migration.

Không cho rằng tag có tên gần giống là an toàn để reuse. Hãy so sánh purpose, event name, parameter, consent, destination và request count.

### Bước 3 — Kiểm tra hoặc tạo approved variable (Step 3 — Verify or create the approved variables)

Dùng value do application sở hữu trong Data Layer. Không đọc DOM hoặc raw field để tự dựng lại event.

| Variable                                           | Type                          | Source                                             | Validation                                         |
| -------------------------------------------------- | ----------------------------- | -------------------------------------------------- | -------------------------------------------------- |
| `FD - DLV - solution_found`                        | Data Layer Variable           | `solution_found` trong event `calculation_action`  | Phải là boolean và có trong event hợp lệ           |
| `FD - DLV - connection_type`                       | Data Layer Variable           | `connection_type` trong event `calculation_action` | Phải khớp approved enum                            |
| `FD - DLV - product_category`                      | Data Layer Variable           | `product_category` trong event                     | Optional; bỏ khi không có                          |
| `LUT - Shared - GA4 Measurement ID by Environment` | Lookup/configuration variable | Approved environment mapping                       | Environment không biết thì không trả production ID |

Với mỗi variable, kiểm tra:

- spelling và casing của key;
- event scope và value type;
- value có sẵn tại thời điểm trigger được evaluate;
- missing-value behavior;
- không có PII hoặc unrestricted user input vào payload;
- variable không phải alias vô tình của legacy field.

Nếu required value không có, hãy sửa application/Data Layer contract thay vì thêm DOM scrape hoặc default placeholder.

### Bước 4 — Cấu hình Google tag và destination an toàn theo environment (Step 4 — Configure the environment-safe Google tag and destination)

Reuse hoặc config FD Google tag/shared Google measurement configuration đã review.

```text
Known FD QA/staging environment
        → approved non-production Measurement ID

Known FD production environment
        → approved production Measurement ID

Unknown or unsupported environment
        → no valid Measurement ID / tag blocked
```

Dùng shared configuration variable hoặc lookup table đã review. Không dán trực tiếp production Measurement ID vào event tag nếu đã có centralized routing. Xác nhận GA4 property/stream của từng environment và ghi vào inventory.

Event tag không được âm thầm fallback từ environment bị thiếu/không biết về production.

### Bước 5 — Chọn GA4 Event tag type (Step 5 — Choose the GA4 Event tag type)

Tạo hoặc reuse built-in GA4 Event tag:

```text
Tag type: GA4 Event
Name:     GA4 Event - FD - calculation_action
Google tag / configuration: approved FD shared Google tag
Event name: calculation_action
```

Không triển khai event này bằng Custom HTML hoặc custom `gtag()` call khi GA4 Event tag đã đáp ứng behavior cần thiết.

### Bước 6 — Chỉ map parameter đã approve (Step 6 — Map only approved parameters)

| GA4 parameter      | GTM value                         | Behavior                                                     |
| ------------------ | --------------------------------- | ------------------------------------------------------------ |
| `solution_found`   | `{{FD - DLV - solution_found}}`   | Required boolean; thiếu value thì fail QA                    |
| `connection_type`  | `{{FD - DLV - connection_type}}`  | Required approved enum; thiếu/không hợp lệ thì block release |
| `product_category` | `{{FD - DLV - product_category}}` | Optional; bỏ khi không có                                    |

Không thêm raw input text, URL fragment có user data, form value, API response hoặc internal secret. Parameter chỉ được đưa vào tag khi nó có trong approved measurement contract.

Nếu GTM có thể gửi empty hoặc undefined trái contract, hãy sửa variable/tag configuration hoặc sửa contract trước khi publish.

### Bước 7 — Dùng authoritative Custom Event trigger (Step 7 — Use the authoritative Custom Event trigger)

Tạo hoặc reuse:

```text
Name:       CE - FD - calculation_action
Trigger:    Custom Event
Event name: calculation_action
Condition:  All Custom Events, trừ khi contract yêu cầu explicit FD scope filter
```

Application chỉ nên push event sau khi calculation hoàn tất và là business occurrence thực sự:

```javascript
dataLayer.push({
  event: "calculation_action",
  solution_found: true,
  connection_type: "approved_value",
  product_category: "approved_value",
});
```

Không thêm click, DOM hoặc broad page-path condition chỉ vì legacy implementation từng có. Chỉ thêm application scope condition khi container có nhiều producer cùng tạo event và measurement contract yêu cầu rõ.

### Bước 8 — Định nghĩa consent behavior (Step 8 — Define consent behavior)

Kiểm tra GA4 Event tag khi consent:

- granted trước event;
- denied trước event;
- unavailable hoặc chưa initialize;
- thay đổi sau page load;
- update trước một calculation lặp lại.

Trong ví dụ này, giả định policy FD yêu cầu analytics consent trước khi tag gửi:

```text
Analytics consent granted
        → tag có thể gửi nếu event và parameter hợp lệ

Analytics consent denied/unavailable
        → tag theo behavior block hoặc privacy-preserving đã approve
          và không bị ép gửi bằng custom trigger
```

Nếu tổ chức dùng Consent Mode với cookieless behavior đã approve, ghi rõ behavior trong consent matrix và validate request. Chỉ thấy “not fired” hoặc “fired” không phải là đủ bằng chứng.

### Bước 9 — Hoàn tất tên, description, owner và dependency (Step 9 — Complete naming, description, owner, and dependencies)

```text
Tag:     GA4 Event - FD - calculation_action
Trigger: CE - FD - calculation_action
```

Description mẫu:

```text
Purpose: Measure one completed FD calculation.
Contract: FD measurement plan / calculation_action.
Event: calculation_action.
Parameters: solution_found (required), connection_type (required),
product_category (optional; omit when unavailable).
Destination: FD Google tag selected by environment-safe configuration.
Consent: Approved analytics consent behavior; see consent matrix.
Expected count: One request per completed calculation.
Owner: FD Analytics / named maintainer.
Legacy dependency: webApps dispatcher path is not the preferred trigger;
verify consumers before migration or retirement.
Retirement: When the contract is replaced and all consumers migrate.
```

Trước release, ghi owner, approver, implementation ticket, destination mapping và legacy consumer (nếu có) trong inventory.

### Bước 10 — Chạy Preview test, gồm cả negative và duplicate case (Step 10 — Run Preview tests, including negative and duplicate cases)

Dùng GTM Preview/Tag Assistant để xem event timeline, Data Layer value, trigger result, tag fired và tag not fired. Tối thiểu phải test:

| Test case                          | Expected result                                                                 |
| ---------------------------------- | ------------------------------------------------------------------------------- |
| Valid completed calculation        | Có event `calculation_action`; tag eligible và gửi một lần khi consent cho phép |
| `solution_found = false`           | Tag vẫn theo contract; gửi boolean đã approve                                   |
| Thiếu required `connection_type`   | Không tạo production request không hợp lệ; QA fail và release bị block          |
| Thiếu optional `product_category`  | Event gửi không có parameter này nếu consent và required value hợp lệ           |
| Event application khác             | `CE - FD - calculation_action` không match                                      |
| Click nhưng chưa calculation xong  | Tag không fire                                                                  |
| Event `webApps` tương tự/legacy    | Preferred tag không fire, trừ khi migration design quy định khác                |
| Push giống nhau lặp lại            | Request count theo contract; phải điều tra duplicate                            |
| Hai calculation                    | Hai occurrence hợp lệ tạo hai request, mỗi calculation một request              |
| Consent granted                    | Behavior khớp consent matrix                                                    |
| Consent denied/unavailable         | Tag block hoặc theo privacy-preserving behavior đã approve                      |
| Consent thay đổi trước calculation | Kết quả theo documented update behavior                                         |
| SPA navigation/remount             | Không có request thêm nếu không có calculation mới                              |
| QA/staging hostname                | Chọn non-production destination                                                 |
| Production hostname                | Chỉ chọn production với environment production đã biết                          |
| Unknown hostname                   | Không fallback production; tag block hoặc không có destination hợp lệ           |

Preview chứng minh GTM đã evaluate event như thế nào; không tự nó chứng minh destination đã nhận request đúng.

### Bước 11 — Validate network request và payload (Step 11 — Validate the network request and payload)

Với mỗi positive case được phép gửi, kiểm tra browser Network panel hoặc Tag Assistant Hit Details và xác nhận:

- request đi tới Google endpoint đúng;
- Measurement ID/property đúng với environment destination;
- event name chính xác là `calculation_action`;
- required parameter có mặt và đúng type;
- optional parameter tuân theo missing-value behavior;
- không có PII, secret, raw form value hoặc parameter ngoài dự kiến;
- consent behavior thể hiện đúng policy;
- request count chính xác một cho mỗi calculation hoàn tất;
- DebugView hoặc downstream check nhận event khi áp dụng được.

Bằng chứng tối thiểu không chỉ là:

```text
Tag Fired ✓
```

Mà phải là:

```text
Correct event
Correct payload
Correct consent behavior
Correct Measurement ID/destination
Correct request count
```

### Bước 12 — Publish kèm version và evidence (Step 12 — Publish with version and evidence)

Trước khi publish:

1. Review tag, trigger, variable, exception, consent setting, Google tag/configuration, destination và reference.
2. Xác nhận legacy `webApps` path không tạo duplicate request.
3. Lưu Preview screenshot hoặc evidence export cho positive, negative, duplicate, consent và destination case.
4. Ghi workspace/version, change ticket, reviewer, owner, release notes và rollback plan.
5. Publish focused version có tên rõ ràng, ví dụ `FD calculation_action GA4 Event - initial production release`.

Nếu required parameter, destination mapping, consent setting hoặc request-count result chưa được giải quyết, không publish.

### Bước 13 — Production smoke test và monitor (Step 13 — Perform a production smoke test and monitor)

Sau khi publish:

- Chạy một controlled production calculation an toàn cho analytics QA.
- Xác nhận request tới production Measurement ID và có payload đã approve.
- Xác nhận chính xác một request được tạo.
- Xác nhận không có production traffic tới test/staging destination.
- Kiểm tra GA4 DebugView hoặc downstream validation view đã approve khi phù hợp.
- Monitor event volume, parameter completeness, error signal và duplicate-rate indicator.
- Ghi thời gian smoke test, environment, evidence và kết quả.

Không tuyên bố thành công chỉ dựa trên publish confirmation. Container đã publish vẫn có thể sai trigger, destination, consent state hoặc payload.

### Bước 14 — Ghi inventory và lifecycle state (Step 14 — Record inventory and lifecycle state)

Thêm tag và dependency vào inventory:

```text
Tag:              GA4 Event - FD - calculation_action
Type:             GA4 Event
Purpose:          One event per completed FD calculation
Trigger:          CE - FD - calculation_action
Parameters:       solution_found, connection_type, product_category (optional)
Consent:          Approved analytics consent behavior
Destination:      Environment-safe FD Google tag / GA4 destination
Expected count:   1 per completed calculation
Environment:      QA, staging, and production through reviewed routing
Owner:            FD Analytics / named maintainer
Status:            Active after production smoke test
Consumers:        Approved GA4 reports and downstream uses
Legacy path:      webApps dispatcher inventoried and tracked for migration
Replacement:      None, or the approved future replacement
Evidence:          Version, ticket, Preview, network, and smoke-test records
```

Chỉ đặt lifecycle status là `Active` sau khi production smoke test pass. Nếu tag thay thế legacy dispatcher consumer, chỉ đánh dấu legacy tag/trigger là `Deprecated` sau khi verify traffic và consumer.
