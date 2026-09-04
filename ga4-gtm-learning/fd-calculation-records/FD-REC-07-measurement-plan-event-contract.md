# FD-REC-07 — Measurement Plan & Event Contract

> Record này dùng đúng cấu trúc record chuẩn tại [Section 07 — Measurement Plan](../07-measurement-plan-answer-vn.md). Đây là bản áp dụng cho project FD, không phải template mới. Các giá trị `FAKE`/`SIMULATED` chỉ phục vụ thiết kế và review; chưa có setup live trên GA4/GTM.

## 0. Record metadata

| Field | Giá trị |
|---|---|
| Record ID | `FD-REC-07` |
| Plan ID | `FD-MP-001` |
| Version | `1.0-approved` |
| Phase | Phase 1 — Measurement Plan và Event Contract |
| Product / Journey | FD web application / `J-FD-CALC-001` |
| Source of truth | Event Contract và Parameter Dictionary trong record này |
| Baseline dependency | [`FD-REC-00` / Phase 0 trong master journey](../11-fd-calculation-journey.md) |
| Status | **Approved — Phase 1 complete; Phase 2 handoff ready** |
| Business owner | `[business owner — placeholder]` |
| Application owner | `fd-developer@strongtie.com` — simulated alias |
| Analytics owner | `fd-analytics-owner@strongtie.com` — simulated alias |
| GTM owner | `fd-gtm-implementer@strongtie.com` — simulated alias |
| Privacy/consent reviewer | `[privacy owner — placeholder]` |
| Created / last updated | `2026-09-04` |

## 0.1 Handoff summary

Record này được bàn giao cho business, application, analytics, privacy và GTM team để review trước khi triển khai. `FD Analytics/GTM Lead` là primary owner của semantic contract và handoff; các owner chức năng phê duyệt phần quyết định thuộc trách nhiệm của mình.

| Hạng mục | Kết luận hiện tại |
|---|---|
| Recommendation | Schema 1.0 đã được approve; mở Phase 2 handoff, sau đó triển khai theo exit gate |
| Live implementation | Chưa thực hiện; Phase 0 dùng simulated baseline |
| Main risk | Gửi sai business moment, gửi full snapshot hoặc triển khai khi consent/destination chưa được approve |
| Go condition | Schema 1.0 đã được approve; Phase 2 chỉ bắt đầu implementation sau khi handoff được review |
| No-go condition | Còn ambiguity về valid occurrence, parameter allowlist, response correlation, consent hoặc routing |

## 0.2 Mục đích quản lý của bộ record

`FD-REC-07` là bộ hồ sơ quyết định cho flow `calculation_action`. Mục đích không phải là ghi chép thủ tục, mà là giữ một nguồn sự thật xuyên suốt từ câu hỏi nghiệp vụ đến Application, Data Layer, GTM, GA4, QA và báo cáo.

Bộ record này phải giúp team trả lời được bốn câu hỏi trước khi triển khai:

1. Event này đo business fact nào và khi nào được tính là một occurrence hợp lệ?
2. Field nào được phép đi qua Data Layer/GTM tới GA4 và field nào phải bị loại bỏ?
3. Consent, privacy, environment và destination nào đang áp dụng?
4. Khi contract thay đổi, consumer nào bị ảnh hưởng và cần migrate ra sao?

### 0.3 Mục đích từng record trong `FD-REC-07`

| Record | Mục đích chính | Quyết định/rủi ro mà record kiểm soát | Được sử dụng bởi |
|---|---|---|---|
| `Project Context / Baseline` | Neo project vào đúng product, Journey, hostname, GA4 stream, GTM container, owner và consent baseline | Ngăn nhầm QA với production, nhầm destination hoặc triển khai ngoài scope | Tất cả phase; đặc biệt Phase 2–6 |
| `Journey / Event Coverage Matrix` | Chuyển business question thành phạm vi event tối thiểu cần đo | Ngăn track mọi click/UI state và ngăn thêm event không phục vụ quyết định nào | Business, Analytics, Application |
| `Event Contract` | Định nghĩa ý nghĩa canonical của `calculation_action`, authoritative moment, valid occurrence, failure và deduplication | Ngăn đo trước khi API response hợp lệ, nhầm no-output với failure hoặc gửi duplicate | Application, Data Layer, GTM, QA, Reports |
| `Parameter Dictionary` | Quy định tên, type, allowed values, source, required/optional, privacy và GA4 registration cho từng field | Ngăn gửi cả `inputs` object, gửi PII/raw response, sai type hoặc đăng ký quá nhiều custom definitions | Application, GTM, GA4, Reporting |
| `Consent / Data Classification` | Xác định field/event nào được phép gửi theo `analytics_storage` và xử lý khi bị denied/unknown | Ngăn analytics bypass consent hoặc đưa restricted data vào GA4 | CMP, GTM, Privacy, QA |
| `Key-Event / Custom-Definition Decision Record` | Tách quyết định “có thu thập event” khỏi quyết định “đánh dấu key event/đăng ký custom definition” | Ngăn biến mọi calculation thành conversion và ngăn register field không có nhu cầu phân tích | Analytics, Business, GA4, Reports |
| `Traceability Matrix` | Nối requirement → Application state → Data Layer → GTM → consent → destination | Ngăn handoff đứt đoạn và giúp xác định owner khi một layer không khớp | Application, GTM, QA, Release |
| `Schema Lifecycle Register` | Theo dõi version, change, affected consumer, migration và approval | Ngăn đổi ngầm ý nghĩa `solution_found`, allowed value hoặc occurrence rule làm hỏng downstream | Tất cả owner; đặc biệt Release và Monitoring |

### 0.4 Operating model và trách nhiệm

| Vai trò | Trách nhiệm trong `FD-REC-07` |
|---|---|
| **FD Analytics/GTM Lead — primary owner** | Chịu trách nhiệm cuối cùng về tính nhất quán của Measurement Plan, Event Contract, parameter allowlist, consent boundary, destination và handoff giữa các phase. Primary owner duy trì version, mở decision khi có conflict và không cho phép chuyển phase khi thiếu acceptance gate quan trọng. |
| **Implementation support — requester/FD team member** | Bạn cung cấp context nghiệp vụ, xác nhận behavior của Application/API/UI, kiểm tra các assumption kỹ thuật, chuẩn bị evidence và thực hiện các action được giao. Bạn không cần tự duy trì một contract song song; mọi thay đổi được cập nhật vào record này và section research tương ứng. |
| Business owner | Xác nhận business question, ý nghĩa outcome và quyết định key event. |
| Application owner | Xác nhận snapshot, response correlation, type/unit, allowed values và idempotency. |
| Analytics owner | Review allowlist, cardinality, custom definitions và reporting usefulness. |
| Privacy/consent reviewer | Review classification, consent requirement và hành vi khi bị từ chối. |
| GTM implementer | Chỉ triển khai theo contract đã approve; không tự suy luận lại business logic trong GTM. |

### 0.5 Cách bộ record vận hành theo flow

```text
Project Context / Baseline
→ Journey / Event Coverage Matrix
→ Event Contract
→ Parameter Dictionary
→ Consent / Data Classification
→ Key-Event / Custom-Definition Decision
→ Traceability Matrix
→ Schema Lifecycle Register
→ Handoff sang Application, GTM, GA4, QA và Reporting
```

Quy tắc quản lý:

- Không bắt đầu Phase 2 hoặc Phase 3 nếu Event Contract và Parameter Dictionary chưa được approve.
- Không để GTM tự tạo business meaning mới; nếu behavior thực tế khác contract, dừng handoff và mở change trong Schema Lifecycle Register.
- Mọi thay đổi làm ảnh hưởng event meaning, occurrence, parameter, consent, destination hoặc reportability phải cập nhật record liên quan và version trước khi triển khai.
- `FD-REC-07` là nguồn chuẩn về semantic contract; Section 01 dùng để triển khai Application/Data Layer, Sections 02–04 dùng để triển khai GTM, Section 05 dùng cho consent, Sections 08–10 dùng cho QA/report/release.

## 1. Project Context / Baseline

| Field | Nội dung |
|---|---|
| Product / Journey | FD calculation / `J-FD-CALC-001` |
| Business purpose | Đo các calculation attempt đã có terminal outcome và phân biệt attempt có output với attempt không có output hoặc error |
| Platform / source | Client-side FD web application; Application là nguồn business truth |
| QA hostname / stream | `app-staging.strongtie.com` / `FD Web — QA — SIMULATED` / `G-FAKEFDQA01` |
| Production hostname / stream | `app.strongtie.com` / `FD Web — Production — SIMULATED` / `G-FAKEFDPROD1` |
| GTM | Container `GTM-FAKEFD01`, workspace `WS-FD-CALC-001` |
| Environment routing | Hostname allowlist và hostname-to-Measurement-ID routing theo `FD-REC-00`; unknown hostname bị chặn |
| Timezone / currency | `Europe/London` / `GBP` theo baseline giả lập; không phải parameter của event |
| Consent baseline | `analytics_storage=denied` trước consent; chỉ cho phép analytics khi state là `granted` |
| Full snapshot boundary | Application gửi snapshot tới Calculation API và có thể giữ snapshot cho application QA; không map toàn bộ `inputs` object thành một GA4 parameter |
| Effective / next review | Sau khi business, application, analytics và privacy owner approve; review lại khi schema hoặc reporting requirement thay đổi |

## 2. Journey / Event Coverage Matrix

| Journey ID | Journey | Câu hỏi nghiệp vụ | Chuỗi event dự kiến | Kết quả chính | Owner | Status |
|---|---|---|---|---|---|---|
| `J-FD-CALC-001` | FD calculation | Trong mỗi calculation attempt được ghi nhận, hệ thống có tạo ra kết quả (solution) hay không? | Một calculation attempt có terminal outcome → `calculation_action` | `solution_found="Yes"` hoặc `solution_found="No"` | Business + Analytics | Draft — review required |

Nói đơn giản: sau khi người dùng nhập dữ liệu hợp lệ và Application đã kết thúc một attempt, FD cần ghi nhận attempt đó **có tìm thấy solution hay không**. Response là mảng có `length > 0` thì gửi `solution_found="Yes"`; response là `[]` thì gửi `solution_found="No"`. Kết thúc attempt do API error, timeout, cancellation hoặc stale response cũng gửi `solution_found="No"` theo contract. Từ event này, team có thể đếm số attempt được ghi nhận và phân tích `solution_found="Yes"/"No"` theo các nhóm input đã được duyệt.

Input validation không tạo event. API failure, timeout, cancellation và stale response được Application phân loại là error và vẫn gửi một `calculation_action` với `solution_found="No"`. Vì vậy, nếu không bổ sung một field trạng thái lỗi riêng, GA4 sẽ không phân biệt được `"No"` do “response rỗng” với `"No"` do “error”. Đây là reporting limitation đã được ghi nhận trong contract: báo cáo không được diễn giải `"No"` như no-solution rate thuần.

## 3. Event Contract

| Field | Quyết định hiện tại |
|---|---|
| Requirement / Journey ID | `FD-MP-001` / `J-FD-CALC-001` |
| Business question / decision | Đo calculation attempt đã có terminal outcome và phân tích `solution_found="Yes"/"No"` |
| Event name / type | `calculation_action` / Custom event |
| Definition | Một calculation attempt mà Application đã kết thúc bằng response hợp lệ hoặc error thuộc contract, sau khi đã có complete input snapshot |
| Authoritative moment / source | Application, khi attempt có terminal outcome; không phải click, render, route change hoặc input change riêng lẻ |
| Valid occurrence | Complete snapshot đã được gửi tới Calculation API và Application nhận response đúng snapshot hoặc phân loại một error được phép ghi nhận; response có `length > 0` → `solution_found="Yes"`; response `[]` hoặc error → `solution_found="No"` |
| Invalid input | UI hiển thị input validation → không push `calculation_action` |
| API/server failure | HTTP 5xx hoặc network error → Application phân loại error và push một `calculation_action` với `solution_found="No"` |
| Timeout / cancellation | Request timeout hoặc bị cancel → Application phân loại error và push một `calculation_action` với `solution_found="No"` |
| Stale response | Nếu Application kết thúc attempt hiện tại vì stale/cancellation theo contract → push một `calculation_action` với `solution_found="No"` đúng một lần. Response callback của snapshot cũ đến sau khi attempt đã terminal hoặc sau khi đã có snapshot mới → bỏ qua, không push event bổ sung |
| Retry / duplicate callback | Automatic retry cùng attempt sau error không tạo event mới. User chủ động submit lại cùng snapshot hoặc tạo snapshot mới là occurrence mới nếu Application mở attempt mới. Duplicate callback, remount và replay không tạo event mới |
| Data Layer signal | `event: "calculation_action"`, `event_schema_version: "1.0"`, `app_name: "fd"`, `solution_found: "Yes"/"No"`, `inputs: { complete snapshot }` |
| Required Data Layer state | `event_schema_version`, `app_name`, `solution_found` và complete `inputs` snapshot theo Application contract |
| GA4 mapping boundary | GTM chỉ map scalar fields nằm trong GA4 allowlist đã approve; không gửi `inputs` object, API response body hoặc internal correlation token |
| GTM mapping | Một Custom Event Trigger authoritative và một GA4 Event Tag ở Phase 3; names/IDs phải được ghi vào các record Sections 02–04 khi phase đó mở |
| Environment / destination | QA dùng `G-FAKEFDQA01`; production dùng `G-FAKEFDPROD1`; hostname không được allowlist thì không có destination |
| Consent / privacy | Theo `analytics_storage` policy trong `FD-REC-00` và Section 05; `denied` hoặc unknown → suppress analytics event |
| Key-event status | **Yes** — theo business decision trong review |
| Custom-definition status | `solution_found` cần custom dimension event-scoped để phân tích Yes/No outcome rate; phải được giữ trong GA4 parameter allowlist |
| Owner / reviewer / version | Application owner, analytics owner, GTM owner và privacy reviewer; schema `1.0`, record version `1.0-approved` |

## 4. Parameter Dictionary

### 4.1 Nguyên tắc mapping

- Complete snapshot phải tồn tại trong Application request payload và complete Data Layer message theo contract.
- GA4 chỉ nhận scalar parameter đã được approve cho câu hỏi phân tích cụ thể.
- Trong record này, numeric contract chỉ bao gồm `fx` và `fy`; các numeric input khác được để ngoài scope và không map sang GA4.
- Field thiếu trong complete snapshot là lỗi contract; không thay bằng empty string, `unknown` hoặc giá trị của event trước.

### 4.1.1 GA4 parameter allowlist đã nhận trong review

Allowlist được support đề xuất gồm:

```text
app_name
event_schema_version
solution_found
country
language
building_code
design_method
connection_type
fx
fy
```

`solution_found` đã được bổ sung vào allowlist theo review mới. Application phải gửi đúng chuỗi `"Yes"` hoặc `"No"`; GTM không chuyển đổi boolean và không tự suy luận lại giá trị. Quyết định custom dimension đã được approve; việc tạo các definition thực tế thuộc Phase 4.

### 4.2 Top-level fields

| Event | Parameter | Ý nghĩa | Type / scope | Bắt buộc? | Giá trị được phép | Khi thiếu hoặc sai | Nguồn | Privacy / consent | Cardinality / volume | GA4 registration |
|---|---|---|---|---|---|---|---|---|---|---|
| `calculation_action` | `event_schema_version` | Version của event contract | String / event | Yes | `1.0` ở version này | Application không emit hoặc handoff fail | Data Layer top-level | Analytics-safe; `analytics_storage` | Low | Collect for QA; chưa đăng ký custom definition |
| `calculation_action` | `app_name` | Xác định product phát event | String / event | Yes | `fd` | Application không emit hoặc handoff fail | Data Layer top-level | Analytics-safe; `analytics_storage` | Low | Collect; chưa đăng ký custom definition |
| `calculation_action` | `solution_found` | Response có tạo output hay không; response rỗng và error đều là `"No"` theo contract | String / event | Yes | `"Yes"`, `"No"` | Application không emit; không suy luận hoặc chuyển đổi ở GTM | Data Layer top-level | Analytics-safe; `analytics_storage` | Low | Custom dimension event-scoped — Approved; create in Phase 4 |

### 4.3 Approved categorical snapshot fields

Các field dưới đây là các scalar field được đề xuất map từ `inputs` sang GA4. Tên ở cột `Parameter` là tên GA4 phẳng; path nguồn vẫn nằm ở cột `Nguồn`.

| Event | Parameter | Ý nghĩa | Type / scope | Bắt buộc? | Giá trị được phép | Khi thiếu hoặc sai | Nguồn | Privacy / consent | Cardinality / volume | GA4 registration |
|---|---|---|---|---|---|---|---|---|---|---|
| `calculation_action` | `country` | Quốc gia áp dụng calculation | String / event | Yes trong snapshot | Controlled country code, ví dụ `gb` | Không emit complete event | Data Layer `inputs.country` | Analytics; `analytics_storage` | Low | Approved for collection; custom dimension approved — create in Phase 4 |
| `calculation_action` | `language` | Ngôn ngữ calculation | String / event | Yes trong snapshot | Controlled language code, ví dụ `en` | Không emit complete event | Data Layer `inputs.language` | Analytics; `analytics_storage` | Low | Approved for collection; custom dimension approved — create in Phase 4 |
| `calculation_action` | `building_code` | Building/code standard được chọn | String / event | Yes trong snapshot | Controlled code list | Không emit complete event | Data Layer `inputs.building_code` | Analytics; `analytics_storage` | Low/Medium | Approved for collection; custom dimension approved — create in Phase 4 |
| `calculation_action` | `design_method` | Phương pháp design | String / event | Yes trong snapshot | Controlled method list, ví dụ `lsd` | Không emit complete event | Data Layer `inputs.design_method` | Analytics; `analytics_storage` | Low | Approved for collection; custom dimension approved — create in Phase 4 |
| `calculation_action` | `connection_type` | Loại connection | String / event | Yes trong snapshot | Controlled connection list | Không emit complete event | Data Layer `inputs.connection_type` | Analytics; `analytics_storage` | Low/Medium | Approved for collection; custom dimension approved — create in Phase 4 |

### 4.4 Numeric snapshot fields

Trong scope của contract này chỉ quản lý hai numeric field `fx` và `fy`. Đây là các field optional: nếu được gửi thì phải đúng type, unit và range; nếu không có thì không tự tạo giá trị thay thế. Các numeric input khác của payload mẫu không thuộc contract này và không được map sang GA4.

| Event | Parameter | Ý nghĩa | Type / scope | Bắt buộc? | Giá trị được phép | Khi thiếu hoặc sai | Nguồn | Privacy / consent | Cardinality / volume | GA4 registration |
|---|---|---|---|---|---|---|---|---|---|---|
| `calculation_action` | `fx` | Giá trị input `fx` | Number/decimal / event | No | `0–500`, unit `kN` | Nếu có giá trị ngoài range hoặc sai type → input validation, không emit event | Data Layer `inputs.fx` | Analytics; `analytics_storage` | Medium/High | Approved for collection; not registered in initial release |
| `calculation_action` | `fy` | Giá trị input `fy` | Number/decimal / event | No | `0–500`, unit `kN` | Nếu có giá trị ngoài range hoặc sai type → input validation, không emit event | Data Layer `inputs.fy` | Analytics; `analytics_storage` | Medium/High | Approved for collection; not registered in initial release |

`inputs` là composite object dành cho Application/Data Layer và QA; không tạo một GA4 parameter tên `inputs`. Các key khác trong snapshot mẫu nằm ngoài scope của record này. API response body và request/response correlation token cũng không thuộc GA4 payload.

## 5. Consent / Data Classification

| Event / parameter | Classification | Consent requirement | Hành vi khi bị từ chối | Destination | Owner / status |
|---|---|---|---|---|---|
| `calculation_action` và các scalar parameter đã approve | Analytics | `analytics_storage=granted` | Suppress event; không replay event sau consent nếu chưa có decision riêng | QA hoặc production stream theo hostname | Analytics + privacy / Approved in review |
| `event_schema_version`, `app_name`, `solution_found` | Analytics / controlled | `analytics_storage=granted` | Không gửi GA4 | QA hoặc production stream theo hostname | Analytics / Approved in review |
| Categorical input fields | Analytics / controlled | `analytics_storage=granted` | Không gửi field/event theo consent policy | QA hoặc production stream theo hostname | Analytics + privacy / Approved in review |
| Complete `inputs` snapshot trong application request/Data Layer | Internal application QA data; không phải GA4 destination | Không map sang GA4 | Application xử lý theo application privacy policy; không expose thêm qua analytics | Calculation API/application QA only | Application + privacy / Approved in review |
| API response body, secret, token hoặc PII nếu xuất hiện | Restricted / prohibited for GA4 | Không được đưa vào GA4 | Remove/suppress và lập defect nếu bị expose | Không có GA4 destination | Application + privacy / Must not collect |

Consent baseline kế thừa `FD-REC-00`: default `denied`, CMP là source of truth, unknown/error là fail-safe `denied`. Chi tiết triển khai thuộc Section 05 và Phase 3.

## 6. Key-Event / Custom-Definition Decision Record

| Decision ID | Event/parameter | Quyết định dự kiến | Lý do | Trạng thái |
|---|---|---|---|---|
| `FD-DEC-001` | `calculation_action` — key event | **Yes** theo business decision trong review | Completed calculation/error attempt là outcome quan trọng cần được theo dõi trong GA4 | Approved in review |
| `FD-DEC-002` | `solution_found` — custom dimension | **Required** event-scoped; values are `"Yes"`/`"No"` | Cần giữ field này để phân tích output/No outcome rate và phục vụ key event | Approved in review; create in Phase 4 |
| `FD-DEC-003` | Approved categorical inputs | **Approved for collection**: `country`, `language`, `building_code`, `design_method`, `connection_type` | Đây là nhóm field được chọn để phân tích; không tự động gửi các input category còn lại | Confirmed in review |
| `FD-DEC-004` | Numeric inputs | **Approved for collection**: `fx`, `fy` là number/decimal, unit `kN`, range `0–500`, optional | Chỉ quản lý hai numeric field có requirement rõ trong scope hiện tại | Confirmed in review; không đăng ký trong initial release |
| `FD-DEC-005` | `event_schema_version`, `app_name` | Collect for contract/diagnostic; không đăng ký custom definition ở initial release | Hữu ích cho validation nhưng chưa phải dimension nghiệp vụ chính | Confirmed in review |

## 7. Traceability Matrix

| Requirement / event | Application state | Data Layer | GTM | Consent | Destination | Owner / status |
|---|---|---|---|---|---|---|
| Valid output | Response match đúng snapshot và response có `length > 0` | `event=calculation_action`, `solution_found="Yes"`, complete `inputs` | Custom Event Trigger → GA4 Event Tag; mapping theo allowlist | `analytics_storage=granted` | QA/prod theo hostname | Application + GTM / Pending implementation |
| Valid no-output | Response match đúng snapshot nhưng response là `[]` | `event=calculation_action`, `solution_found="No"`, complete `inputs` | Cùng Trigger/Tag, không suy luận lại outcome | `analytics_storage=granted` | QA/prod theo hostname | Application + GTM / Pending implementation |
| Input validation | UI validation state | Không push `calculation_action` | Trigger không match | Không có analytics event | Không có destination | Application / Contract defined |
| API failure/timeout/cancel | Application phân loại error của calculation attempt | `event=calculation_action`, `solution_found="No"`, complete snapshot nếu có | Cùng Trigger/Tag; GTM không suy luận error | `analytics_storage=granted` | QA/prod theo hostname | Application + GTM / Error semantics updated |
| Stale response | Attempt hiện tại bị terminalize bởi stale/cancellation theo contract; late response của attempt cũ sau đó bị bỏ qua | Nếu attempt được terminalize: `event=calculation_action`, `solution_found="No"`, complete snapshot của attempt đó; late callback không tạo event | Cùng Trigger/Tag; GTM không suy luận stale state | `analytics_storage=granted` | QA/prod theo hostname | Application + GTM / Error semantics updated |
| Duplicate callback | Cùng attempt được callback lặp | Chỉ một push | Một Trigger match và một GA4 request | Theo consent | QA/prod theo hostname | Application / Pending idempotency test |

Tên Variable, Trigger và Tag cụ thể sẽ được chốt trong các record Sections 02–04 ở Phase 3; Phase 1 chỉ định nghĩa contract và handoff requirement.

## 8. Schema Lifecycle Register

| Change ID | Event / parameter | Version hiện tại | Version đề xuất | Loại thay đổi | Consumer bị ảnh hưởng | Hành động migration / handoff | Approval / ngày hiệu lực | Status |
|---|---|---|---|---|---|---|---|---|
| `FD-CHG-001` | `calculation_action` và parameter contract | Chưa có | `1.0` | Initial approval | Application, Data Layer, GTM, GA4, QA, reporting | Schema đã được approve; Phase 2 dùng contract để build adapter; Phase 3 map GTM; Phase 4 tạo custom definitions | Business + Application + Analytics + Privacy / `2026-09-04` | Approved in review |

Rule: thay đổi ý nghĩa của `solution_found`, valid occurrence, required field hoặc allowed value phải tạo lifecycle change và review lại consumer; không âm thầm reuse schema `1.0`.

## 9. Handoff requirements

### Phase 2 — Application/Data Layer

- Giữ complete snapshot bất biến từ lúc tạo request đến lúc đối chiếu response hoặc phân loại error.
- Correlate response với đúng snapshot. Nếu stale/cancellation là terminal error của attempt thì emit `solution_found="No"` một lần; late response của attempt đã terminal hoặc snapshot cũ không được emit thêm.
- Chỉ emit sau terminal outcome: response hợp lệ hoặc error thuộc contract.
- Không emit khi có input validation; API failure, timeout, cancellation và stale response emit `solution_found="No"` theo contract.
- Emit đúng `event_schema_version=1.0`, top-level fields và complete `inputs` snapshot.
- Viết application contract tests cho valid output, valid no-output, error event và các duplicate/negative scenarios.

### Phase 3 — GTM

- Dùng một Custom Event Trigger authoritative cho `calculation_action`.
- Tạo Data Layer Variables chỉ cho scalar allowlist đã được approve.
- Không gửi toàn bộ `inputs` object hoặc API response.
- Áp dụng consent và hostname routing từ `FD-REC-00`.
- Ghi Variable/Trigger/Tag vào records Sections 02–04 theo đúng template research.

### Phase 4 — GA4

- Chỉ tạo custom definitions sau khi `FD-DEC-002` và các field decision được approve.
- Không đăng ký toàn bộ parameter tự động.
- Dùng event/parameter đã xử lý để xây chart; không định nghĩa lại business meaning trong report record.

### Phase 5 — QA

- Dùng contract này làm nguồn test: valid output, valid no-output, validation, API failure, stale response, retry/duplicate callback, unknown hostname và consent denied/granted.
- Evidence phải kiểm tra Application log/Data Layer, GTM Preview, Network và GA4 DebugView.

## 10. Phase 1 review và acceptance

### 10.1 Đã giải quyết

- [x] Dùng đúng record structure từ Section 07.
- [x] Xác định business question và valid business moment.
- [x] Phân biệt valid no-output với validation/API failure/stale response.
- [x] Xác định Application là authoritative source.
- [x] Xác định boundary giữa full snapshot và GA4 scalar allowlist.
- [x] Gắn destination, consent và routing baseline từ `FD-REC-00`.
- [x] Business owner xác nhận business question.
- [x] Business owner quyết định `calculation_action` là key event.
- [x] Application owner xác nhận `fx` và `fy` là number/decimal, unit `kN`, range `0–500`, optional; các numeric key khác nằm ngoài scope.
- [x] Application owner xác nhận duplicate callback, user submit lại, stale response và remount/replay không tạo duplicate event.
- [x] Business/Analytics contract decision: `solution_found="No"` dùng chung cho response `[]` và API error; báo cáo phải ghi rõ đây là No outcome gộp, không phải no-solution rate thuần.
- [x] Analytics/privacy owner approve GA4 parameter allowlist đã cập nhật, gồm `solution_found` với allowed values `"Yes"` và `"No"`.
- [x] Privacy/consent reviewer approve consent policy, data classification và hành vi suppress khi `analytics_storage` là `denied` hoặc `unknown`.
- [x] Application owner approve retry rule: automatic retry cùng attempt không tạo event mới; user chủ động submit lại hoặc tạo snapshot mới là occurrence mới.
- [x] Analytics owner approve custom-definition decision: tạo event-scoped custom dimension cho `solution_found`, `country`, `language`, `building_code`, `design_method`, `connection_type`; không đăng ký `fx`, `fy`, `app_name`, `event_schema_version` trong initial release.
- [x] Business/Application/Analytics/Privacy approve schema `1.0` và cho phép handoff sang Phase 2.

### 10.2 Kết luận Phase 1

Phase 1 đã hoàn tất. Không còn checklist hoặc decision mở trong Phase 1; các thay đổi tiếp theo phải tuân theo Schema Lifecycle Register.

### 10.3 Ghi chú handoff

Schema `1.0` là nguồn bắt buộc cho Phase 2 Application/Data Layer. `FD-REC-01` sẽ được tạo theo structure của Section 01 và phải được đối chiếu với implementation thực tế trước khi Phase 2 hoàn tất.

Business question, key-event decision, `fx`/`fy` domain, duplicate callback, error semantics, allowlist, consent policy, retry rule và schema `1.0` đã được giải quyết trong review này.

```text
Current status: Approved — Phase 1 complete; Phase 2 handoff ready
Phase 1 actual setup: Not applicable — planning/documentation phase; no live GA4/GTM changes
Open decisions: none for Phase 1; reporting limitation của `solution_found="No"` đã được ghi nhận
Next action: Review Phase 2 handoff; create FD-REC-01 from Section 01 before Application implementation
```
