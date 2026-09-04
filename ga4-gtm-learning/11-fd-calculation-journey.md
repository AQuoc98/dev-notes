# FD `calculation_action` — GA4/GTM Setup Journey

> Tài liệu này được xây dựng từng phase. Các identifier có hậu tố `FAKE`, `SIMULATED` hoặc được ghi là `[placeholder]` chỉ là dữ liệu giả lập để học tập và thiết kế. Không dùng chúng cho production.

## 0. Trạng thái journey

| Phase | Nội dung | Trạng thái |
|---|---|---|
| 0 | Kiểm kê hệ thống, environment, consent, quyền truy cập và test baseline | **Completed — simulated setup approved** |
| 1 | Measurement Plan và Event Contract | **Completed — schema 1.0 approved** |
| 2 | Application Data Layer và analytics adapter | **Handoff ready — implementation pending** |
| 3 | GTM Variables, Trigger, Tags và routing | Pending |
| 4 | GA4 custom definitions, reports và charts | Pending |
| 5 | Debug/QA và evidence | Pending |
| 6 | Release, monitoring và rollback | Pending |

**Current execution scope: Phase 2 handoff review.** Phase 0 đã được user review/approve với simulated values; Phase 1 đã approve schema `1.0`; chưa có thay đổi live trên GA4/GTM và chưa sửa Application. Phase 2 đang ở bước chuẩn bị implementation.

### Quy tắc trạng thái

- `Completed — simulated` chỉ có nghĩa là bộ thiết kế và thông số giả lập đã hoàn tất; chưa chứng minh tài khoản thật đã được tạo.
- `Pending` giữ nguyên cho đến khi phase đó có evidence thực tế.
- `Blocked` dùng khi thiếu quyền, thiếu environment, thiếu consent decision hoặc có lỗi material chưa được xử lý.

## 0.1 Quy trình làm việc cho từng phase

Mỗi phase trong journey này được thực hiện theo cùng một vòng đời:

1. Tạo hoặc mở **document/record lưu trữ** cần thiết cho phase.
2. Ghi requirement, owner, environment, expected result và status trước khi setup.
3. Thực hiện các bước setup thực tế trên Application, GTM hoặc GA4 theo phạm vi của phase.
4. Review configuration, dependency, privacy, destination và duplicate risk.
5. Thu thập evidence và cập nhật acceptance criteria.
6. Cập nhật status, open items, owner và next action trong chính journey này.

Không đánh dấu phase là `Completed` chỉ vì configuration đã được tạo. Phase chỉ hoàn tất khi có review và evidence phù hợp.

## 0.2 Phân biệt hướng dẫn setup và tài liệu quản lý thực tế

| Loại | Ý nghĩa | Dùng khi nào | Có phải evidence không? |
|---|---|---|---|
| `HOW-TO` | Hướng dẫn cách tạo/cấu hình trên Application, GTM hoặc GA4 | Khi thực hiện thao tác setup | Không; chỉ là instruction |
| `RECORD` | Tài liệu quản lý của project có giá trị thật, owner, status, version và review | Trước, trong và sau setup | Có thể chứa pointer tới evidence, nhưng không thay thế evidence |
| `EVIDENCE` | Bằng chứng runtime hoặc platform, ví dụ screenshot, Preview, Network, DebugView hoặc version history | Sau khi thực hiện setup/test | Có |
| `MASTER JOURNEY` | File điều phối phase, status, decision, blocker, link record và next action | Xuyên suốt project | Chứa pointer; không thay thế evidence gốc |

Quy tắc áp dụng:

- Các file research `01`–`10` là **playbook và nguồn template/record chuẩn**. Khi mở phase, phải dùng cấu trúc tương ứng từ section đó; nếu thực tế FD cho thấy cần cải tiến, cập nhật đồng bộ section research và journey.
- File journey này là **MASTER JOURNEY**.
- Các `FD-REC-xx` là **project management records** sẽ được tạo và cập nhật theo từng phase.
- Hướng dẫn `HOW-TO` có thể nằm trong journey và research; giá trị thực tế phải được ghi vào `RECORD`.
- Evidence phải được lưu ở nơi kiểm soát quyền truy cập và được link từ `RECORD` và journey.

## 0.3 Document/record register của FD journey

Journey này là master index. `FD-REC-00` đã hoàn tất ở Phase 0; `FD-REC-07` đã được tạo thành draft khi mở Phase 1. Các record còn lại vẫn là roadmap và sẽ được tạo khi phase tương ứng mở. Quy ước lưu trữ:

```text
Master journey:
  ga4-gtm-learning/11-fd-calculation-journey.md

Project records:
  ga4-gtm-learning/fd-calculation-records/FD-REC-xx-<record-name>.md

Evidence:
  Restricted evidence location được ghi trong record tương ứng
```

`FD-REC-00` tiếp tục được lưu trực tiếp trong các mục Phase 0 của master journey vì đây là system inventory compact. `FD-REC-07` và các record của phase sau được lưu thành file riêng trong `fd-calculation-records/`.

| Record ID | Document project thật | File/địa điểm lưu | Nguồn chuẩn | Mục đích | Thời điểm tạo | Status |
|---|---|---|---|---|---|---|
| `FD-REC-00` | Phase 0 System Inventory Record | Journey mục 2.1–6; GA4 destination cụ thể tại mục 2.2 | Journey Phase 0 | Environment, GA4/GTM foundation, consent, access và duplicate baseline | Trước Phase 0 setup | Completed — simulated setup approved |
| `FD-REC-01` | Application/Data Layer Specification | Sẽ tạo khi bắt đầu Phase 2 | Section 01 | Snapshot, payload, response correlation, event contract và application tests | Trước application change | Handoff ready |
| `FD-REC-02` | GTM Variable Inventory | Sẽ xác định khi mở Phase 3 | Section 02 | Variable name, source path, type, missing behavior và consumers | Trước tạo Variable | Roadmap |
| `FD-REC-03` | GTM Trigger Inventory | Sẽ xác định khi mở Phase 3 | Section 03 | Authoritative event, filters, expected frequency và exceptions | Trước tạo Trigger | Roadmap |
| `FD-REC-04` | GTM Tag Inventory | Sẽ xác định khi mở Phase 3 | Section 04 | Google tag, GA4 Event tag, mapping, consent, destination và expected count | Trước tạo Tag | Roadmap |
| `FD-REC-05` | Consent Decision Record | Sẽ xác định khi mở Phase 3 | Section 05 | CMP source of truth, default/update/revoke và `analytics_storage` | Trước consent setup | Roadmap |
| `FD-REC-06` | Template Governance Decision | Sẽ xác định khi mở Phase 3 | Section 06 | Xác nhận native tag đủ dùng hoặc ghi custom-template exception | Trước template decision | Roadmap |
| `FD-REC-07` | Measurement Plan & Event Contract | `fd-calculation-records/FD-REC-07-measurement-plan-event-contract.md` | Section 07 | Business question, event meaning, parameters, privacy và destination | Trước implementation | Approved — schema 1.0 |
| `FD-REC-08` | QA Run & Evidence Record | Sẽ xác định khi mở Phase 5 | Section 08 | Test matrix, execution summary và evidence từng layer | Trước Preview/QA | Roadmap |
| `FD-REC-09` | GA4 Report/Exploration Record | Sẽ xác định khi mở Phase 4 | Section 09 | Field readiness, report requirement, chart và interpretation | Sau collection validation | Roadmap |
| `FD-REC-10` | Release & Monitoring Record | Sẽ xác định khi mở Phase 6 | Section 10 | Version, approval, smoke test, threshold, rollback và observation | Trước publish | Roadmap |

`FD-REC-00` là một record logic của toàn bộ Phase 0. Các bảng từ mục 2.1 đến mục 6 là các phần lưu trữ của record này; mục 2.2 là nơi lưu cụ thể GA4 property, web stream và Measurement ID. Record đã được user review/approve với simulated values.

## 0.5 Handoff brief — Leadership và implementation team

### Mục đích bàn giao

Đây là bộ tài liệu quản lý cho một event GA4/GTM thực tế của FD, được chuẩn bị để sếp và các team liên quan đánh giá trước khi quyết định triển khai. Master journey là nơi điều phối status và decision; các `FD-REC-xx` là hồ sơ chi tiết; các mục `HOW-TO` là hướng dẫn thực hiện sau khi decision được approve.

### Đề xuất của owner

Đề xuất **approve semantic design của `calculation_action` và tiếp tục sang Phase 2**, với điều kiện các open decision trong `FD-REC-07` được chốt trước khi Application hoặc GTM được triển khai. Kiến trúc đề xuất là một nguồn event authoritative từ Application, một GTM flow, tách QA/production destination và hostname routing có allowlist.

### Quyết định cần sếp/team phê duyệt

| Decision | Đề xuất của owner | Decision owner | Trạng thái |
|---|---|---|---|
| Business meaning | Đo calculation attempt sau khi Application có terminal outcome: response có `length > 0` → `solution_found="Yes"`; response `[]` hoặc error thuộc contract → `solution_found="No"` | Business + Application | Approved in review |
| Negative cases | Input validation không tạo event; API failure, timeout, cancellation, stale response và duplicate callback được xử lý theo contract, trong đó error gửi `solution_found="No"` và duplicate chỉ gửi một lần | Business + Application | Approved in review |
| GA4 payload | Allowlist gồm `app_name`, `event_schema_version`, `solution_found`, `country`, `language`, `building_code`, `design_method`, `connection_type`, `fx`, `fy`; `solution_found` dùng `"Yes"`/`"No"` | Analytics + Privacy | Approved in review |
| `calculation_action` là key event? | **Yes** theo business decision | Business | Approved in review |
| `solution_found` custom definition | Tạo event-scoped custom dimension cho `solution_found` và 5 categorical fields; không đăng ký `fx`, `fy`, `app_name`, `event_schema_version` trong initial release | Analytics | Approved in review |
| Environment architecture | Hai GA4 destinations QA/production, một GTM container và hostname routing/allowlist | Analytics + GTM | Simulated baseline approved; live setup pending |
| Consent | `analytics_storage=denied` mặc định; chỉ thu thập khi được phép; `denied`/`unknown` thì suppress event | Privacy + Analytics | Approved in review |
| Implementation authorization | Cho phép chuẩn bị handoff Phase 2 theo schema `1.0`; actual Application change vẫn chờ review handoff | Leadership | Approved for handoff; implementation pending |

### Phạm vi và giới hạn của gói bàn giao

- Phase 0 đã được review với simulated values; chưa xác minh account/ID/quyền thật và chưa thay đổi live GA4/GTM.
- Phase 1 đã approve `FD-REC-07` và schema `1.0`; Phase 2 handoff đang chờ review trước khi sửa Application.
- Phase 2–6 chưa thực hiện: chưa sửa Application, chưa tạo GTM/GA4 asset, chưa tạo chart, chưa chạy QA runtime và chưa publish production.
- Không phê duyệt việc gửi raw API response, request token, secret, PII hoặc toàn bộ `inputs` object tới GA4.

### Tài liệu để review

1. Master journey này: scope, status, Phase 0 baseline, tutorial setup và decision log.
2. [`FD-REC-07 — Measurement Plan & Event Contract`](fd-calculation-records/FD-REC-07-measurement-plan-event-contract.md): semantic contract, parameter allowlist, consent/privacy, traceability và handoff requirements.
3. Sections 01–10: research playbook được dùng làm chuẩn cho các record và implementation ở phase tương ứng.

### Handoff gate

```text
Leadership/team review
→ Approve business meaning và occurrence rule
→ Approve GA4 parameter allowlist + consent/privacy
→ Approve key-event/custom-definition decisions
→ Approve schema 1.0
→ Mở Phase 2 Application/Data Layer
→ Mở Phase 3 GTM implementation
```

Nếu một decision bị từ chối hoặc cần thay đổi, cập nhật `FD-REC-07` và schema lifecycle trước khi chuyển sang implementation. Không tạo asset dựa trên assumption chưa được ghi nhận.


## 0.4 Quy tắc review và update

Mỗi lần review sẽ cập nhật tối thiểu:

```text
Phase / Record ID:
Current status:
What was configured:
What was verified:
Evidence:
Open item / blocker:
Owner:
Reviewer:
Next action:
Last updated:
```

Các giá trị giả lập chỉ dùng để chuẩn bị cấu hình. Khi chuyển sang setup thật, thay bằng identifier thật và giữ lại simulated value trong lịch sử thay đổi nếu cần truy vết.

## 1. Phạm vi và mục tiêu

### 1.1 Mục tiêu nghiệp vụ

Đo một calculation attempt của FD sau khi application đã:

1. Nhận thay đổi input.
2. Tạo complete input snapshot.
3. Đặt snapshot vào request payload và gửi payload đó tới Calculation API.
4. Nhận response là kết quả mà Calculation API trả về cho snapshot tương ứng.
5. Với response hợp lệ: nếu response có `length > 0`, đặt `solution_found` là `"Yes"`; nếu response là `[]`, đặt `solution_found` là `"No"`.
6. Nếu UI xuất hiện input validation, không gửi `calculation_action` event.
7. Nếu API failure, timeout, cancellation hoặc stale response được Application phân loại là error, push một `calculation_action` với `solution_found` là `"No"`.
8. Với calculation attempt đã có terminal outcome, push một complete `calculation_action` message.
9. Để GTM map các field đã duyệt và gửi event tới GA4.

### 1.2 Boundary

```text
Application giữ business truth
Data Layer truyền message
GTM đọc, kiểm tra, route và gửi
GA4 nhận, xử lý và cung cấp báo cáo
```

GTM không tự tính `solution_found`, không đọc DOM để suy luận kết quả và không gửi toàn bộ object `inputs` như một GA4 parameter.

## 2. Phase 0 — System inventory và quyền truy cập

### 2.1 [RECORD] Hostname và environment đã được cung cấp

| Mục | Giá trị |
|---|---|
| QA/staging URL | `https://app-staging.strongtie.com/fd` |
| QA/staging hostname | `app-staging.strongtie.com` |
| Production URL | `https://app.strongtie.com/fd` |
| Production hostname | `app.strongtie.com` |
| Product | FD web application |
| Khu vực giả lập | UK / `gb` |
| Timezone giả lập | `Europe/London` |
| Currency mặc định giả lập | `GBP` — chỉ là property setting, không gửi cùng event này |

Đây là thông tin scope do requester cung cấp, không phải thông tin giả lập. Nó được lưu trong `FD-REC-00` để xác định environment thuộc phạm vi triển khai.

### 2.2 [RECORD] GA4 account, properties, web streams và Measurement IDs giả lập

Trong setup mới, dùng hai GA4 properties tách biệt để QA không làm nhiễu production. Đây là lựa chọn an toàn cho POC; nếu tổ chức có một property dùng chung, phải thay bằng routing/filter policy được phê duyệt.

| Mục | QA | Production |
|---|---|---|
| Analytics account | `Strongtie Analytics — SIMULATED` | `Strongtie Analytics — SIMULATED` |
| Property name | `FD Web — QA — SIMULATED` | `FD Web — Production — SIMULATED` |
| Property ID | `123456789` | `123456790` |
| Web stream name | `FD Web QA — app-staging — SIMULATED` | `FD Web Production — app — SIMULATED` |
| Stream ID | `9876543210` | `9876543211` |
| Stream URL | `https://app-staging.strongtie.com` | `https://app.strongtie.com` |
| Measurement ID | `G-FAKEFDQA01` | `G-FAKEFDPROD1` |
| Timezone | `Europe/London` | `Europe/London` |
| Currency | `GBP` | `GBP` |
| Enhanced Measurement | Enabled, phải kiểm tra path không tạo `calculation_action` | Enabled, phải kiểm tra path không tạo `calculation_action` |
| Status | Simulated | Simulated |

Bảng này là baseline giả lập của `FD-REC-00`. Khi tạo GA4 thật, thay bằng property/stream/Measurement ID thật và bổ sung evidence xác minh.

Không ghi Measurement ID thật vào tài liệu chia sẻ rộng. Khi có giá trị thật, chỉ lưu ở nơi được kiểm soát quyền truy cập.

### 2.3 [HOW-TO] Cách tạo GA4 property và web stream

Thực hiện riêng cho QA và production:

1. Mở `https://analytics.google.com`.
2. Vào **Admin** → tạo hoặc chọn Analytics account.
3. Chọn **Create property**, đặt tên theo bảng trên, chọn timezone và currency đã được business owner duyệt.
4. Trong property, mở **Data streams** → **Add stream** → **Web**.
5. Nhập URL gốc của environment, ví dụ `https://app-staging.strongtie.com`, và stream name.
6. Mở stream vừa tạo và copy **Measurement ID** bắt đầu bằng `G-`.
7. Kiểm tra Enhanced Measurement; giữ một source authoritative cho page view và các automatic event. Không tạo thêm một manual path trùng với source đó.
8. Ghi property ID, stream ID, Measurement ID thật, owner và ngày tạo vào **Phase 0 System Inventory Record (`FD-REC-00`)**, trong bảng GA4 account/property/stream ở mục 2.2 của journey này. `Owner` và ngày tạo ở đây là của GA4 destination/environment.

GA4 có thể cần quyền Editor trở lên ở property để tạo web stream và custom definitions. Việc tạo custom definitions sẽ thực hiện ở Phase 4, sau khi event đã được collection và QA.

### 2.4 [RECORD] GTM account, container, workspace và environment giả lập

| Mục | Giá trị giả lập |
|---|---|
| GTM account | `Strongtie Web Tag Management — SIMULATED` |
| Web container | `FD Web — app.strongtie.com — SIMULATED` |
| Container ID | `GTM-FAKEFD01` |
| Container type | Web |
| Main workspace | `Default — SIMULATED` |
| Working workspace | `WS-FD-CALC-001 — calculation_action` |
| QA custom environment | `ENV-FD-STAGING` |
| QA destination URL | `https://app-staging.strongtie.com/fd` |
| Live environment | `Live — Production` |
| Production destination URL | `https://app.strongtie.com/fd` |
| Status | Simulated |

Bảng này cũng là một phần của `FD-REC-00`, dùng để lưu GTM foundation baseline. Khi setup thật, cập nhật container ID, workspace, environment, snippet status và published version kèm evidence.

### 2.5 [DECISION RECORD] Quyết định dùng một container

QA và production là hai subdomain của cùng một web product, có cùng logic tag nhưng dùng GA4 destination khác nhau. Vì vậy, journey giả lập dùng **một web container**, custom GTM environment cho staging và hostname lookup để chọn Measurement ID.

Nếu sau này QA và production có lifecycle hoặc tag policy hoàn toàn khác nhau, cần mở lại quyết định và tách container bằng một Schema Lifecycle/Release Record mới.

### 2.6 [HOW-TO] Cách tạo GTM account và container

1. Mở `https://tagmanager.google.com`.
2. Chọn **Create account**.
3. Đặt account name là tên tổ chức, country là UK theo setup giả lập.
4. Tạo container `FD Web — app.strongtie.com`, chọn target platform **Web**.
5. Lấy container snippet và cài đúng một lần trên mỗi page của cả hai hostname.
6. Không cài thêm hard-coded `gtag.js`, plugin GA4 của CMS hoặc một GTM container thứ hai cùng page nếu chưa có decision riêng.

### 2.7 [HOW-TO] Cách tạo workspace

1. Mở container → **Workspaces**.
2. Chọn **New workspace**.
3. Đặt tên `WS-FD-CALC-001 — calculation_action`.
4. Ghi description: `New FD calculation_action contract, QA routing, consent and GA4 mapping.`
5. Chỉ đưa các thay đổi liên quan journey này vào workspace; không trộn cleanup, ecommerce hoặc hotfix khác.

Workspace này là nơi tạo Variables, Trigger, Tags và consent configuration của các phase sau.

### 2.8 [HOW-TO] Cách tạo GTM environment

1. Vào **Admin** → **Environments** → **New**.
2. Đặt tên `ENV-FD-STAGING`.
3. Điền destination URL `https://app-staging.strongtie.com/fd`.
4. Bật **Enable debugging by default** cho environment giả lập nếu team cần QA liên tục.
5. Tạo environment và publish version đầu tiên cho environment này.
6. Cài environment-specific container snippet vào staging. Production dùng Live snippet.
7. Kiểm tra staging không bao giờ nhận production Measurement ID và production không nhận QA Measurement ID.

Custom environment chỉ ảnh hưởng server/browser đã dùng đúng environment snippet hoặc preview link; nó không tự thay đổi Live production container.

## 3. Google tag và duplicate collection inventory

### 3.1 [RECORD] Google tag giả lập

| Mục | Giá trị |
|---|---|
| Tag name | `FD - Google tag - Primary` |
| Tag type | Google tag |
| Tag ID source | `{{FD - LUT - Hostname to Measurement ID}}` |
| Trigger | `Initialization - All Pages`, giới hạn hostname đã allowlist |
| QA ID | `G-FAKEFDQA01` |
| Production ID | `G-FAKEFDPROD1` |
| Unknown hostname | Không fire / không có destination |
| Consent | Built-in Google tag consent behavior; `analytics_storage` theo policy |

### 3.2 [HOW-TO] Cách tạo Google tag trong GTM

> **Review note:** Đây là hướng dẫn sẽ thực hiện ở **Phase 3 — GTM Variables, Trigger, Tags và routing**. Trong Phase 0, chúng ta chỉ ghi nhận thiết kế giả lập; không tạo hoặc publish tag trên GTM thật.

1. Mở workspace `WS-FD-CALC-001` → **Tags** → **New**.
2. Đặt tên `FD - Google tag - Primary`.
3. Chọn tag type **Google tag**.
4. Ở **Tag ID**, chọn Variable `{{FD - LUT - Hostname to Measurement ID}}` sẽ tạo ở Phase 3.
5. Gắn trigger Initialization có hostname allowlist theo hướng dẫn ở mục 3.2.1.
6. Mở **Advanced Settings → Consent Settings** và review built-in consent checks.
7. Save; chưa publish production ở Phase 0.

### 3.2.1 [HOW-TO] Hostname allowlist và routing control

Ở đây có hai lớp kiểm soát khác nhau:

| Lớp | Nhiệm vụ | Cấu hình dự kiến |
|---|---|---|
| Firing allowlist | Quyết định tag có được phép chạy trên hostname hay không | Chỉ cho phép `app-staging.strongtie.com` và `app.strongtie.com` |
| Destination routing | Chọn Measurement ID đúng theo hostname | Staging → `G-FAKEFDQA01`; production → `G-FAKEFDPROD1` |

Không nên dùng Measurement ID production làm giá trị mặc định. Hostname không nằm trong allowlist phải bị chặn và không được gửi dữ liệu tới bất kỳ destination nào. Lookup Table chỉ chọn destination; nó không thay thế cho firing allowlist.

#### Cách A — Khuyến nghị: tạo Initialization trigger riêng

1. Trong GTM, vào **Variables** → **Configure** và bật built-in variable **Page Hostname**.
2. Vào **Triggers** → **New**.
3. Đặt tên `FD - Init - Known hostnames`.
4. Chọn trigger type **Initialization**.
5. Chọn **Some Initialization Events**.
6. Tạo một điều kiện:

   ```text
   Page Hostname matches RegEx ^(app-staging|app)\.strongtie\.com$
   ```

   Regex này chỉ match đúng hai hostname đã được phê duyệt. Nó không match `www.strongtie.com`, localhost, một subdomain khác hoặc hostname có thêm chuỗi ở đầu/cuối.

7. Gắn trigger này vào `FD - Google tag - Primary`.
8. Dùng Preview để kiểm tra:
   - `app-staging.strongtie.com` → tag được phép chạy và dùng `G-FAKEFDQA01`.
   - `app.strongtie.com` → tag được phép chạy và dùng `G-FAKEFDPROD1`.
   - hostname khác → tag không fire và không có GA4 destination.

#### Cách B — Dùng filter trên trigger có sẵn

Nếu team muốn giữ trigger **Initialization - All Pages**, chọn **Some Initialization Events** trên chính trigger đó và thêm cùng điều kiện:

```text
Page Hostname matches RegEx ^(app-staging|app)\.strongtie\.com$
```

Không tạo hai dòng điều kiện `Page Hostname equals ...` trên cùng một trigger, vì các dòng filter trong cùng trigger được đánh giá theo AND và khi đó một page không thể đồng thời có cả hai hostname. Nếu không dùng regex, hãy tạo hai trigger Initialization riêng và gắn cả hai vào tag.

#### Tạo Variable routing theo hostname

1. Vào **Variables** → **New** → chọn **Lookup Table**.
2. Đặt tên `FD - LUT - Hostname to Measurement ID`.
3. Ở **Input Variable**, chọn `{{Page Hostname}}`.
4. Nhập các mapping:

   | Input | Output |
   |---|---|
   | `app-staging.strongtie.com` | `G-FAKEFDQA01` |
   | `app.strongtie.com` | `G-FAKEFDPROD1` |

5. Để **Default Value** trống hoặc dùng giá trị không hợp lệ được policy phê duyệt; không dùng production ID làm default.
6. Save Variable và chọn nó tại trường **Tag ID** của Google tag.

Trong setup thật, thay hai giá trị `G-FAKE...` bằng Measurement ID thật được ghi trong record Phase 0. Trước khi publish, phải kiểm tra cả **Tag Assistant** và **Network request** để xác nhận hostname và Measurement ID đi cùng nhau. Các GA4 Event tag cho `calculation_action` cũng phải chịu cùng environment/routing control; chỉ bảo vệ Google tag là chưa đủ nếu event tag vẫn có thể fire trên hostname không được phép.

#### “Routing control tương đương đã review” nghĩa là gì?

Đó là một cơ chế khác nhưng đã được owner/analytics/QA phê duyệt và vẫn bảo đảm đủ hai kết quả: chọn đúng destination theo environment và chặn hostname không xác định. Ví dụ có thể là hai container được tách riêng cho QA và production, hoặc một routing variable/trigger đã có sẵn với cùng nguyên tắc allowlist. Việc chỉ dùng URL path `/fd`, chỉ dùng custom GTM environment, hoặc để production ID làm default không được xem là routing control tương đương.

### 3.3 [RECORD] Duplicate collection baseline giả lập

Vì đây là setup mới, baseline mong muốn là:

| Collection path | Trạng thái giả lập | Cách kiểm tra thực tế |
|---|---|---|
| Một GTM container snippet | Expected: 1 container `GTM-FAKEFD01` | Page source, Tag Assistant |
| Một Google tag trong GTM | Expected: 1 canonical Google tag | GTM Tags, Preview |
| Một GA4 Event tag cho `calculation_action` | Expected: 1 | GTM Tags, workspace search |
| Hard-coded `gtag.js`/GA4 script | Expected: 0 | Page source, Network |
| CMS/plugin GA4 integration | Expected: 0 | CMS/app configuration |
| Application `gtag('event', ...)` cho cùng event | Expected: 0 | Search source code |
| Application manual GA4/Measurement Protocol sender | Expected: 0, ngoài scope | Source code, server config |
| Enhanced Measurement tạo `calculation_action` | Expected: 0 | GA4 Events, DebugView |
| Legacy click/DOM tag cho cùng business fact | Expected: 0 | GTM Tags/Triggers |
| Một calculation occurrence | Expected: 1 Data Layer push → 1 Trigger match → 1 GA4 request | GTM Preview + Network |

### 3.4 [HOW-TO + EVIDENCE] Quy trình kiểm tra duplicate

1. Search codebase cho `GTM-`, `G-`, `gtag(`, `dataLayer.push`, `calculation_action` và các plugin analytics.
2. Mở staging bằng Tag Assistant, xem Summary, Events, Tags Fired và Consent.
3. Trong DevTools Network, lọc `gtm`, `collect`, `google-analytics` và ghi số request.
4. Chạy một calculation hợp lệ duy nhất.
5. Đối chiếu count ở Application log, Data Layer, GTM Preview và Network.
6. Nếu có hơn một source gửi cùng business fact, giữ một source authoritative và lập defect trước khi tiếp tục.

## 4. CMP, consent policy và `analytics_storage`

### 4.1 [RECORD] CMP giả lập

| Mục | Giá trị giả lập |
|---|---|
| CMP | `Strongtie Consent Manager — SIMULATED` |
| CMP version | `1.0.0-simulated` |
| Source of truth | CMP consent store, không phải GTM Variable tự tạo |
| Purpose | Analytics measurement cho FD |
| Google consent type | `analytics_storage` |
| Default trước lựa chọn | `denied` |
| User grants analytics | `granted` |
| User rejects analytics | `denied` |
| CMP chậm/lỗi/unknown | Fail-safe: `denied` |
| Consent Mode giả lập | Basic consent mode |
| `ad_storage`, `ad_user_data`, `ad_personalization` | Not in scope; không configure như analytics consent |
| Owner | `[privacy owner — placeholder]` |
| Policy approval | `[privacy approval — placeholder]` |

Đây là policy giả lập để thiết kế flow, không phải kết luận pháp lý. Privacy owner phải duyệt Basic/Advanced, vùng áp dụng, default và hành vi khi revoke trước production.

### 4.2 [HOW-TO] Cách cấu hình consent trong GTM

1. Xác nhận CMP thật và integration/template đã được privacy owner phê duyệt.
2. Trong GTM, vào **Admin → Container Settings** và bật **Enable consent overview**.
3. Dùng **Consent Initialization - All Pages** cho tag/template đặt default consent và nhận update từ CMP.
4. Đảm bảo default `analytics_storage = denied` xảy ra trước Google tag và measurement tags.
5. Khi người dùng accept analytics, CMP gửi update `analytics_storage = granted`.
6. Khi người dùng reject hoặc revoke, CMP gửi update `analytics_storage = denied`.
7. Không dùng exception Trigger để bypass consent.
8. Không replay calculation đã xảy ra trước consent nếu Measurement Plan chưa phê duyệt cơ chế đó.

Nếu không có CMP thật, Phase 3 không được coi là production-ready; chỉ được dùng simulated consent state trong QA.

## 5. Quyền truy cập giả lập

Các email dưới đây chỉ là alias giả lập, không phải email thật.

| Role | Account/permission giả lập | GTM container | GA4 property | Trách nhiệm |
|---|---|---:|---:|---|
| Developer | `fd-developer@strongtie.com` | Read | Analyst hoặc Viewer | Application Data Layer, source debug và contract tests |
| GTM implementer | `fd-gtm-implementer@strongtie.com` | Edit | Viewer | Variables, Trigger, Tags và Preview; không tự publish |
| Analytics owner | `fd-analytics-owner@strongtie.com` | Approve | Editor | Measurement Plan, custom definitions, reports và review |
| QA reviewer | `fd-qa@strongtie.com` | Read | Analyst | Chạy test, DebugView/Realtime và evidence |
| Publisher | `fd-publisher@strongtie.com` | Publish | Viewer hoặc Editor theo property change | Publish version sau approval và smoke test |
| Backup GTM admin | `strongtie-tag-admin@strongtie.com` | Account Admin | Không bắt buộc | Break-glass và đảm bảo không mất quyền quản trị |
| Backup GA4 admin | `strongtie-analytics-admin@strongtie.com` | Không bắt buộc | Administrator | User management và property recovery |

### 5.1 [HOW-TO] Cách cấp quyền GTM

1. Vào GTM → **Admin → Account User Management** hoặc **Container User Management**.
2. Chọn **Add users** và nhập Google account thật.
3. Cấp Account `User` cho người cần xem account; chỉ cấp Account `Admin` cho admin được duyệt.
4. Ở Container permissions, chọn đúng `Read`, `Edit`, `Approve` hoặc `Publish`.
5. Kiểm tra có ít nhất hai active administrators.
6. Kiểm tra quyền kế thừa và ghi access review date.

### 5.2 [HOW-TO] Cách cấp quyền GA4

1. Vào GA4 → **Admin → Property access management**.
2. Chọn **Add users**.
3. Nhập email thật, chọn role thấp nhất đủ dùng.
4. Chỉ cấp Editor/Administrator cho người cần custom definitions, property settings hoặc user management.
5. Với QA, tránh cấp quyền export/share dữ liệu ngoài phạm vi cần thiết.
6. Ghi role, data restriction, owner và ngày review vào access inventory.

## 6. Test URL, synthetic data và browser matrix

### 6.1 [RECORD] Test URL giả lập

| Mục | Giá trị |
|---|---|
| Primary QA URL | `https://app-staging.strongtie.com/fd` |
| Tag Assistant session | Chạy trên primary QA URL, không dùng production |
| Test run ID | `QA-FD-CALC-RUN-001` |
| Build | `fd-web-simulated-build-001` |
| Test identity | `fd-qa-synthetic-001` — chỉ là internal label, không gửi GA4 |
| Consent reset | Fresh browser profile hoặc xóa consent state theo test record |

### 6.2 [RECORD] Synthetic calculation data

Dùng safe values, không dùng dữ liệu người dùng thật, token, email hoặc raw user text.

| Scenario | API result giả lập | Event expectation |
|---|---|---|
| `TC-FD-01` valid output | Response có `length > 0` cho snapshot | 1 event, `solution_found: "Yes"` |
| `TC-FD-02` valid no-output | Response là `[]` | 1 event, `solution_found: "No"` |
| `TC-FD-03` invalid input | UI hiển thị input validation | Không gửi `calculation_action` event |
| `TC-FD-04` server failure | HTTP 5xx hoặc network error | 1 error event, `solution_found: "No"` |
| `TC-FD-05` stale response | Attempt bị terminalize bởi stale/cancellation | 1 error event cho attempt đó, `solution_found: "No"`; late callback của snapshot cũ sau khi attempt đã terminal thì bị bỏ qua |
| `TC-FD-06` retry/duplicate callback | Cùng occurrence được callback nhiều lần | Chỉ 1 event |

Snapshot mẫu an toàn:

```javascript
{
  country: "gb",
  language: "en",
  building_code: "en_1995_1_1_2004_a2_2014",
  design_method: "lsd",
  unit_system: "metric",
  connection_type: "clt_floor_floor_half_lap_joint",
  fastener_installation: "typical",
  fx: 1,
  fy: 0,
  load_duration: "medium_term",
  main_member_thickness: 180,
  side_member_thickness: 180,
  side_member_grade: "c24",
  side_member_density: 350,
  main_member_grade: "c24",
  main_member_density: 350,
  contact_length: 3000,
  predrilled: false,
  fastener_angle: 90,
  service_class: "service_class_1",
}
```

Các giá trị trên chỉ là synthetic test fixture. Internal request/response correlation token, nếu có, phải ở application log và không gửi tới GA4 nếu chưa được duyệt.

### 6.3 [RECORD] Browser được hỗ trợ cho QA giả lập

| Nhóm | Browser matrix |
|---|---|
| Desktop primary | Chrome stable mới nhất — Tag Assistant, GTM Preview, Network |
| Desktop regression | Edge stable mới nhất, Firefox stable mới nhất |
| Apple desktop | Safari stable mới nhất |
| Mobile | Chrome Android và Safari iOS stable mới nhất |
| Evidence baseline | Chrome desktop với fresh profile |

Mỗi test record phải ghi browser version, device, consent state, GTM environment, GA4 property/stream và timestamp.

## 7. Phase 0 checklist và acceptance criteria

- [x] Có hostname QA/staging và production.
- [x] Có bộ GA4 property/stream/Measurement ID giả lập cho hai environment.
- [x] Có GTM account/container/workspace/environment giả lập.
- [x] Có Google tag và hostname routing design.
- [x] Có duplicate collection inventory và cách kiểm tra.
- [x] Có CMP/consent policy giả lập và mapping `analytics_storage`.
- [x] Có role matrix cho developer, GTM implementer, analytics, QA và publisher.
- [x] Có QA URL, synthetic data, test IDs và browser matrix.
- [x] Bypass xác minh account/property/container, Measurement ID và quyền truy cập thật — dùng simulated values.
- [x] Bypass thực hiện setup thực tế Phase 0 — chỉ ghi nhận simulated configuration, không thay đổi platform thật.
- [x] User review và approve Phase 0.

CMP thật, GTM environment snippet thật và duplicate collection audit thật được xem là nằm trong phạm vi bypass của Phase 0 simulation; không đánh dấu chúng là verified.

### [RECORD] Phase 0 acceptance decision

```text
Status: Completed — simulated setup approved
Actual account access: Bypassed — simulated values filled by Codex
Actual setup: Bypassed — simulated configuration documented; no live GA4/GTM changes made
Actual duplicate audit: Simulated baseline only; live platform audit not performed
Actual CMP verification: Simulated baseline only; live CMP verification not performed
Phase 1 entry: Approved — planning started
Owner: [owner — placeholder]
Reviewer: Requester
Review date: 2026-09-04
```

## 8. Phase 1 — Measurement Plan và Event Contract

### 8.1 [RECORD] Phase 1 record

Record triển khai của Phase 1 là [`FD-REC-07`](fd-calculation-records/FD-REC-07-measurement-plan-event-contract.md), dùng đúng cấu trúc từ Section 07:

```text
Project Context / Baseline
→ Journey / Event Coverage Matrix
→ Event Contract
→ Parameter Dictionary
→ Consent / Data Classification
→ Key-Event / Custom-Definition Decision Record
→ Traceability Matrix
→ Schema Lifecycle Register
```

### 8.2 Phase 1 checklist và status

#### Đã giải quyết

- [x] Tạo `FD-REC-07` theo template/record structure của Section 07.
- [x] Ghi business question cho FD calculation.
- [x] Định nghĩa `calculation_action` và authoritative business moment.
- [x] Xác định valid output (`length > 0` → `"Yes"`), valid no-output (`[]` → `"No"`), error (`"No"`) và input validation không emit event.
- [x] Phân biệt complete snapshot của Application/Data Layer với scalar allowlist gửi GA4.
- [x] Gắn consent, privacy, destination và routing baseline từ `FD-REC-00`.
- [x] Tạo traceability và schema lifecycle draft để handoff sang các phase sau.
- [x] Business owner xác nhận business question.
- [x] Business owner xác nhận `calculation_action` là key event.
- [x] Application owner xác nhận `fx` và `fy`: number/decimal, `kN`, `0–500`, optional; numeric key khác ngoài scope.
- [x] Application owner xác nhận duplicate callback, user submit lại, stale response và remount/replay không tạo duplicate event.
- [x] Business/Analytics contract decision: `solution_found="No"` gộp response `[]` và API error; báo cáo phải gọi là No outcome rate gộp.
- [x] Analytics/privacy owner approve GA4 parameter allowlist đã cập nhật, gồm `solution_found` với allowed values `"Yes"` và `"No"`.
- [x] Privacy/consent reviewer approve consent policy, data classification và hành vi suppress khi `analytics_storage` là `denied` hoặc `unknown`.
- [x] Application owner approve retry rule: automatic retry cùng attempt không tạo event mới; user chủ động submit lại hoặc tạo snapshot mới là occurrence mới.
- [x] Analytics owner approve custom-definition decision: tạo event-scoped custom dimension cho `solution_found`, `country`, `language`, `building_code`, `design_method`, `connection_type`; không đăng ký `fx`, `fy`, `app_name`, `event_schema_version` trong initial release.
- [x] Business/Application/Analytics/Privacy approve schema `1.0` và cho phép handoff sang Phase 2.

#### Kết luận Phase 1

Phase 1 đã hoàn tất. Không còn checklist hoặc decision mở trong Phase 1; các thay đổi tiếp theo phải tuân theo Schema Lifecycle Register.

```text
Phase 1 status: Completed — schema 1.0 approved; Phase 2 handoff ready
Actual setup: Not applicable — planning/documentation phase; no live GA4/GTM changes
Open decisions: none for Phase 1; reporting limitation của `solution_found="No"` đã được ghi nhận
Next action: Review Phase 2 handoff before Application implementation
```

### 8.3 Phase 2 handoff — Application/Data Layer

Phase 2 có nhiệm vụ triển khai business logic tracking trong Application và tạo complete calculation_action message. Phase này chưa cấu hình GTM/GA4 và chưa gửi dữ liệu production.

#### Đầu vào bắt buộc

- Schema 1.0 đã được approve trong FD-REC-07.
- Section 01 — Data Layer Design làm implementation guide.
- Flow thực tế của Application: input change → snapshot → API request → matching response → terminal outcome.
- Quy tắc consent và boundary: Application có thể tạo message nội bộ, nhưng GTM/GA4 chỉ được collect khi consent cho phép.

#### Phạm vi triển khai

| Workstream | Yêu cầu Phase 2 | Kết quả bàn giao |
|---|---|---|
| Snapshot | Tạo snapshot đầy đủ, bất biến cho từng request; không dùng state đã thay đổi để đối chiếu response | Snapshot có thể truy vết với đúng request/response trong Application log |
| Response correlation | Mỗi attempt chỉ được terminalize một lần. Nếu Application kết thúc attempt vì stale/cancellation theo contract thì emit `"No"` một lần; callback của response cũ đến sau khi attempt đã terminal thì bị bỏ qua | Không dùng response cũ để cập nhật state hoặc tạo event bổ sung |
| Outcome mapping | Response có length > 0 → `solution_found="Yes"`; response `[]` → `"No"`; API error/timeout/cancellation/stale error làm attempt terminal → `"No"` một lần | Một terminal outcome có giá trị chuẩn |
| Validation | UI input validation → không push calculation_action | Không có event cho invalid input |
| Occurrence/idempotency | Automatic retry cùng attempt không tạo event mới; user chủ động submit lại hoặc tạo snapshot mới là occurrence mới; duplicate callback/remount/replay không tạo event mới | Tối đa một push cho mỗi occurrence |
| Data Layer message | Push đúng event name, schema version, app_name, solution_found và complete inputs; không đưa token, secret, PII hoặc API response body | Message sẵn sàng cho GTM đọc ở Phase 3 |

#### Data Layer contract tối thiểu

~~~javascript
window.dataLayer.push({
  event: "calculation_action",
  event_schema_version: "1.0",
  app_name: "fd",
  solution_found: "Yes", // hoặc "No"
  inputs: {
    // complete snapshot của Application
  },
});
~~~

GTM sẽ map các scalar parameter đã approve ở Phase 3. Application không được chuyển trách nhiệm tính solution_found cho GTM.

#### Deliverables cần tạo ở Phase 2

1. FD-REC-01 — Application/Data Layer Specification, dùng structure của Section 01.
2. Application adapter hoặc tracking helper phát đúng một complete message cho mỗi occurrence.
3. Unit/contract tests cho output, empty response, error, input validation, stale response và duplicate scenarios.
4. Implementation note ghi rõ file/module đã thay đổi, test command, kết quả test và mọi deviation so với schema 1.0.

#### Exit gate trước khi chuyển sang Phase 3

- FD-REC-01 được review và cập nhật theo code thực tế.
- Automated tests pass cho toàn bộ outcome và negative cases.
- DevTools/Application log chứng minh snapshot được correlate đúng response.
- Data Layer message có đúng "Yes"/"No" và không chứa prohibited fields.
- Không có thay đổi ngoài scope; mọi deviation phải mở change trong Schema Lifecycle Register.

Phase 2 chưa bao gồm tạo Data Layer Variable, Trigger, Google tag, GA4 Event tag, custom definition, chart, production publish hoặc monitoring. Các nội dung đó thuộc Phase 3–6.

## 9. Tài liệu chính thức dùng cho Phase 0

- [GA4 — Set up Analytics for a website and/or app](https://support.google.com/analytics/answer/14183469)
- [Tag Manager — Create an account and container](https://support.google.com/tagmanager/answer/14842164)
- [Tag Manager — Workspaces](https://support.google.com/tagmanager/answer/7059647)
- [Tag Manager — Environments](https://support.google.com/tagmanager/answer/6311518)
- [Tag Manager — Add the Google tag in GTM](https://support.google.com/tagmanager/answer/14842872)
- [Tag Manager — Consent mode support](https://support.google.com/analytics/answer/10718549)
- [GA4 — About consent mode](https://support.google.com/analytics/answer/10000067)
- [GA4 — Verify consent mode](https://support.google.com/analytics/answer/14218557)
- [GA4 — Access and data-restriction management](https://support.google.com/analytics/answer/9305587)
- [GTM — Managing users and permissions](https://support.google.com/tagmanager/answer/6107011)
