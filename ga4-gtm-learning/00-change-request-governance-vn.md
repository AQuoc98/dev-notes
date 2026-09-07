# 00 — Governance Change Request cho FD GA4/GTM

## 1. Mục tiêu và phạm vi

Tài liệu này là lớp quản lý project cho FD calculation journey. Nội dung bao gồm tiếp nhận request, ownership, risk, đánh giá ảnh hưởng, traceability, evidence, bàn giao release, monitoring và đóng request cho project FD.

Tài liệu này áp dụng các tài liệu research/reference trong Sections 01–10 vào một project FD. Các section đó chứa kiến thức, template dùng lại, record structure và ví dụ; chúng không phải project record.

Tài liệu này không định nghĩa cách triển khai Variable, Trigger, Tag, consent, template, report hoặc runtime test. Sections 01–10 cung cấp standard và cấu trúc record có thể tái sử dụng; các quyết định cụ thể của project phải nằm trong các record `FD-REC-*` tương ứng.

> **Phạm vi áp dụng:** Governance này được áp dụng cho FD calculation journey. Sections 01–10 vẫn là tài liệu research/reference; các project record cụ thể của FD nằm trong fd-calculation-records/ và được điều phối bởi [11 — FD calculation journey](11-fd-calculation-journey.md). Không thêm FD Change Request ID trực tiếp vào các file research.

## 2. Change Request Record bắt buộc

Tạo một Change Request Record trước khi approve một thiết kế thuộc phạm vi governance hoặc bắt đầu implementation. Change ở chế độ simulation/documentation-only cũng cần CR nếu làm thay đổi current design baseline. Mỗi record bị ảnh hưởng phải link tất cả CR liên quan và xác định CR nào đang quản lý current state.

| Field | Nội dung bắt buộc |
|---|---|
| Change Request ID | Mã duy nhất, ổn định, ví dụ CR-001. |
| Project / journey | FD web application / calculation_action / journey ID. |
| Requester | Cá nhân hoặc team yêu cầu thay đổi. |
| Business owner | Người chịu trách nhiệm về kết quả nghiệp vụ. |
| Technical owner | Người điều phối trách nhiệm triển khai. |
| QA owner | Người chịu trách nhiệm validation. |
| Approver/publisher | Người duyệt và chịu trách nhiệm publish. |
| Change mode | Runtime implementation, simulation/design, documentation-only hoặc incident response. |
| Request type | New, Modify, Deprecate, Retire, Hotfix, Consent, Routing, Report-only, Documentation-only, Composite/Umbrella hoặc Incident. |
| Risk level | Low, Medium hoặc High, kèm lý do. |
| Business purpose | Quyết định hoặc kết quả mà change cần hỗ trợ. |
| Acceptance criteria | Điều kiện quan sát được để approve và close. |
| GA4 scope | Property, web stream, Measurement ID, key event hoặc custom definition bị ảnh hưởng. |
| GTM scope | Account, container, workspace, environment và asset bị ảnh hưởng. |
| Application scope | Product, journey, URL/hostname, application version và build. |
| Change summary | Điều gì thay đổi và điều gì không thay đổi. |
| Change reading instruction | Với từng CR được link từ từng record, ghi rõ `Required — affected`, `Context only — not changed`, `Historical lineage — superseded` hoặc `Not applicable`, kèm lý do. |
| Version transition / before-after state | Historical baseline, current design state, proposed state và contract/schema version trước/sau change. |
| CR lineage | `Supersedes`, `Superseded by`, current governing CR và các CR chỉ được giữ làm historical link. |
| Runtime baseline verification | Application build, GTM version, GA4 destination/schema thực tế đã quan sát và timestamp của evidence, hoặc tuyên bố rõ rằng chưa xác minh được live baseline. |
| Effective release / migration boundary | Named release, ngày áp dụng và cách xử lý dữ liệu trước/sau change. |
| Record update order | Thứ tự các project record cần cập nhật sau approval và trước khi close. |
| Historical comparability / rollback decision | Có được so sánh/gộp giá trị cũ và mới hay không, và rollback phải khôi phục đồng bộ trạng thái nào. |
| Impact assessment | Event, parameter, consumer, consent, report, data quality và historical comparability bị ảnh hưởng. |
| Linked records | Các project record `FD-REC-*` bị ảnh hưởng, Section 01–10 dùng làm governing reference và ticket/reference bên ngoài. |
| Evidence location | Nơi lưu evidence đã sanitize, quyền truy cập và retention period. |
| Deployment-path decision | Direct deployment, compatibility migration, report/document-only hoặc N/A; lựa chọn phải dựa trên runtime-baseline evidence. |
| Release plan | Target environment, named version, release window, smoke test và rollback/containment path tương ứng với deployment path đã chọn. |
| Monitoring plan | Signal, baseline, threshold, observation window, owner và escalation path. |
| Open decisions/blockers | Decision ID, owner, exit condition, target/review date và lifecycle approval bị ảnh hưởng. |
| Document status và dates | Draft/review/design approval/documentation completion/supersession cùng ngày tạo, target và documentation-completion. |
| Runtime status và dates | Not planned/not started/blocked/approved/in QA/published/monitoring/closed cùng ngày publish và runtime-closure. |

## 3. Document lifecycle và runtime lifecycle

Theo dõi hai status độc lập. Một design document hoàn tất không chứng minh change đã được triển khai; một CR bị supersede cũng không chứng minh target schema của CR đó từng chạy ở runtime.

### 3.1 Document lifecycle

```text
Draft → Review → Approved — design → Documentation complete → Superseded
```

| Document status | Ý nghĩa |
|---|---|
| Draft | Đang làm rõ request, scope hoặc before/after state. |
| Review | Đang review impact, ownership, privacy, acceptance criteria và record-update order. |
| Approved — design | Proposed design có thể trở thành current design baseline; status này không cấp quyền triển khai runtime. |
| Documentation complete | CR và các simulation/design record bị ảnh hưởng đã nhất quán và link đầy đủ. |
| Superseded | Một CR được approve sau đó đang quản lý current design. Giữ CR này làm historical lineage bất biến và link tới successor. |

### 3.2 Runtime lifecycle

```text
Documentation-only: Not planned
Runtime intended: Not started → Blocked hoặc Approved for implementation
                  → In QA → Published → Monitoring → Closed
```

| Runtime status | Ý nghĩa |
|---|---|
| Not planned | Documentation-only hoặc simulation work không có runtime scope được authorize. |
| Not started | Có ý định thực hiện runtime work nhưng chưa bắt đầu. |
| Blocked | Không thể tiếp tục vì thiếu decision, access, baseline, evidence, privacy condition hoặc còn material defect. |
| Approved for implementation | Các owner bắt buộc đã approve runtime scope và deployment path được chọn. |
| In QA | Đã có Application/GTM version xác định trong test environment được duyệt và đang validation. |
| Published | Named version đã được publish vào đúng environment. |
| Monitoring | Đang quan sát sau publish và chờ processed-data check. |
| Pending | Chỉ còn dependency đã ghi nhận, có owner, due date và expected check. |
| Exception | Residual risk không blocking đã được chấp nhận rõ ràng, có owner, mitigation, reviewer và ngày hết hạn/review. |
| Closed | Runtime acceptance, evidence, monitoring, affected-period assessment và follow-up đã hoàn tất. |

Quy tắc:

- Không dùng `Approved — design` làm quyền triển khai hoặc publish.
- Simulation CR có thể là `Documentation complete` trong khi runtime là `Not planned`, `Not started` hoặc `Blocked`.
- `Superseded` áp dụng cho document/contract lineage; phải ghi riêng superseded version có từng được deploy hay không.
- Không để runtime change đã publish ở trạng thái `Approved for implementation` hoặc `In QA`.
- Không đặt runtime status là `Closed` khi còn blocking defect, thiếu processed-data check hoặc follow-up chưa có owner.

## 4. Đánh giá ảnh hưởng bắt buộc

Trước design approval hoặc implementation, trả lời từng mục. Nếu một layer không bị ảnh hưởng, ghi N/A kèm lý do.

- [ ] Application hoặc Data Layer có thay đổi.
- [ ] Event name, business meaning, occurrence rule hoặc schema version có thay đổi.
- [ ] Required/optional parameter, data type, allowed value hoặc missing-data behavior có thay đổi.
- [ ] GTM Variable, Trigger, Tag, exception, sequencing hoặc workspace có thay đổi.
- [ ] Consent default, update, revoke, CMP, policy hoặc privacy classification có thay đổi.
- [ ] Template source, version, permission, endpoint hoặc consumer có thay đổi.
- [ ] GA4 property, web stream, Measurement ID, key event, custom definition, identity hoặc data filter có thay đổi.
- [ ] Report, Exploration, chart, metric, dimension, scope, population hoặc interpretation có thay đổi.
- [ ] Có nguy cơ duplicate, overlap, routing sai, sai request count hoặc legacy collection path.
- [ ] Phạm vi regression cho journey, browser, route hoặc environment lân cận.
- [ ] Historical comparability và affected-period impact.
- [ ] Giới hạn của rollback hoặc containment.
- [ ] Current design baseline so với verified live baseline, bao gồm evidence cho biết prior schema đã hoặc chưa được deploy.
- [ ] Direct-deployment hay compatibility-migration path và rollback order tương ứng.
- [ ] Superseded CR, current governing CR và downstream record đang giữ historical lineage.

Request phải xác định layer đầu tiên dự kiến thay đổi và các layer downstream cần validation. Change chỉ ảnh hưởng documentation hoặc report layout phải được phân loại rõ là report-only hoặc documentation-only.

Chỉ dùng Composite/Umbrella CR khi các change cùng chia sẻ một business objective, schema transition, approval set và release boundary, đồng thời bắt buộc phải deploy atomically. Nếu một component có thể được approve, release, rollback hoặc close độc lập, hãy tạo CR riêng và link dưới dạng dependency.

## 5. Quy tắc traceability

Duy trì một chuỗi reference:

Change Request
  → predecessor/successor CR lineage và current governing contract
  → requirement hoặc Measurement Plan đã approve
  → các FD project record được chọn dựa trên Sections 01–06
  → FD-REC-09 report/configuration impact
  → verified live baseline và deployment path được chọn
  → FD-REC-08 QA evidence và defect/retest record
  → FD-REC-11 end-to-end runtime verification khi có real run
  → FD-REC-10 Release và Monitoring Records
  → closure decision và affected-period assessment

Dùng governing Change Request ID trong filename, ticket link, release note, evidence folder và monitoring record. Khi một project record đã được thay đổi bởi nhiều CR, giữ tất cả ID liên quan và chỉ rõ CR đang quản lý current state; không ghi đè historical lineage bằng ID mới nhất.

Các file Sections 01–10 là reference được tham chiếu. Change Request ID nằm trong FD project record và evidence tương ứng, không nằm trong các file research.

### 5.1 Mô hình contract state và runtime baseline

Không dùng từ “current” nếu chưa chỉ rõ đang nói tới state nào:

| State | Ý nghĩa | Evidence |
|---|---|---|
| Historical design baseline | Schema/decision cũ được giữ lại để diễn giải | Prior CR và record version history |
| Current design baseline | Design mới nhất đã được approve cho documentation và downstream planning | Governing CR và các record `FD-REC-*` hiện tại |
| Proposed target | Change còn ở Draft/Review và chưa trở thành design baseline | Phần before/after của open CR |
| Verified live baseline | Target environment đang thực sự chạy gì tại thời điểm kiểm tra | Application build, GTM version, GA4 destination/schema, timestamp và runtime evidence |
| Published target | Named version được quan sát sau release | Release record, smoke test và monitoring evidence |

Deployment path phải dựa trên verified live baseline:

- Nếu chưa có live event contract, dùng controlled direct deployment cho approved target.
- Nếu đã xác minh có contract cũ đang live, định nghĩa các Application/GTM state tương thích, migration window và coordinated rollback.
- Nếu không biết live baseline, runtime status là `Blocked`; không được giả định simulation mới nhất trong tài liệu đã được deploy.

### 5.2 Versioning và điều hướng giữa các record

Dùng ba lớp traceability:

- CR chain sở hữu lý do version thay đổi, CR nào là current, CR nào bị supersede và từng version có từng được deploy hay không.
- Current Change Request sở hữu purpose, before/after design, impact, approval, baseline evidence, deployment path, release, monitoring và closure.
- Mỗi FD record bị ảnh hưởng sở hữu current design state của layer đó. Record link governing CR và các historical CR liên quan, hiển thị record/schema version và giữ version history ngắn hoặc before/after note.

Giữ stable record ID, ví dụ `FD-REC-02`, và thay đổi record version khi cần. Không tạo file record mới cho mọi thay đổi nhỏ. Project mới hoặc major baseline được quản lý riêng có thể dùng một record packet copy.

Trước design approval, giữ nguyên current design state và ghi proposal trong CR/draft record. Sau design approval, các record bị ảnh hưởng có thể hiển thị **current design state** mới trong khi runtime chưa thay đổi. Chỉ sau publication và verification, release record mới được xác định target là **published runtime state**. Giữ state cũ làm historical context.

Không yêu cầu người mới tự suy luận Change Request có liên quan hay không chỉ từ CR ID. Mỗi project record có link đến Change Request phải ghi rõ **Change reading status**:

| Change reading status | Ý nghĩa đối với người đọc mới |
|---|---|
| **Required — affected** | Phải mở CR trước khi dựa vào current/proposed state, triển khai record, review QA, approve release hoặc diễn giải affected period. Contract, behavior, schema hoặc acceptance expectation của record đã thay đổi. |
| **Context only — not changed** | Record được link để người đọc hiểu impact ở cấp project, nhưng decision/state của chính record không thay đổi. Chỉ cần mở CR khi review toàn bộ change hoặc dependency của record. |
| **Historical lineage — superseded** | CR này giải thích state cũ nhưng không quản lý implementation hiện tại. Phải đi theo link `Superseded by` trước khi hành động. |
| **Not applicable** | CR không ảnh hưởng đến record này. Người đọc không cần mở CR khi sử dụng record bình thường; record phải ghi rõ lý do N/A. |

Record cũng phải có một câu **Why this matters** ngắn. Nếu record link nhiều CR, dùng một row cho mỗi CR gồm CR ID, reading status và reason; không dùng một status tổng hợp cho tất cả link. Direct link là cơ chế điều hướng; status và reason theo từng CR là cơ chế ra quyết định.

Mỗi section sở hữu quyết định thuộc layer của mình:

| Section | Sở hữu |
|---|---|
| 01 | Application/Data Layer contract và schema handoff. |
| 02 | Variable source, value behavior, consumer và lifecycle. |
| 03 | Trigger source, filter, overlap và firing behavior. |
| 04 | Tag mapping, destination, consent setting và request behavior. |
| 05 | Consent contract, state transition và privacy behavior. |
| 06 | Template source, permission, endpoint, version và rollback/export. |
| 07 | Business meaning, event contract, parameter approval và schema lifecycle. |
| 08 | Runtime test result, evidence, defect và retest. |
| 09 | Report requirement, field readiness, asset configuration và interpretation. |
| 10 | Release decision, monitoring, incident, rollback và closure. |

## 6. Quy tắc evidence và an toàn dữ liệu

- [ ] Chỉ lưu evidence tối thiểu nhưng đủ chứng minh decision.
- [ ] Ghi environment, application/build, GTM version, GA4 property/stream, browser/device, tester, reviewer và timestamp.
- [ ] Tách configuration evidence khỏi runtime delivery evidence.
- [ ] Đánh dấu ví dụ mô phỏng là simulated; không trình bày như runtime result.
- [ ] Dùng dữ liệu synthetic/test và test identity đã được duyệt.
- [ ] Redact PII, credential, secret, raw form value, token và unrestricted user input.
- [ ] Ghi limitation và follow-up của processing window.
- [ ] Hạn chế quyền truy cập evidence theo policy của project.

## 7. Risk và approval baseline

| Risk | Loại change thường gặp | Kiểm soát tối thiểu |
|---|---|---|
| Low | Documentation hoặc report layout không ảnh hưởng collection. | Owner review và record phù hợp. |
| Medium | Thay đổi Variable, Trigger, Tag, routing hoặc parameter không breaking. | Impact assessment, QA Section 08, named version, approval, smoke test và monitoring ngắn. |
| High | Event/schema meaning, consent/privacy, destination, key event, property setting, permanent filter hoặc data-quality change material. | Cross-functional approval, full QA, production smoke test an toàn, rollback/containment owner, monitoring và processed-data validation. |

Project có thể điều chỉnh baseline này, nhưng mọi rule nghiêm ngặt hơn phải được ghi trong Change Request Record.

Với simulation/design work, ghi lại các control mà risk level yêu cầu nhưng giữ runtime status là `Not planned`, `Not started` hoặc `Blocked`. Chỉ thực hiện các control đó sau khi có runtime authorization; không đánh dấu hoàn tất chỉ dựa trên paper review.

## 8. Definition of done

### 8.1 Hoàn tất documentation/design

Chỉ đánh dấu CR là `Documentation complete` khi:

- [ ] Purpose, before/after state, risk, owner và acceptance criteria đã đầy đủ và được review.
- [ ] Impact assessment đã hoàn tất, bao gồm layer không bị ảnh hưởng được ghi N/A kèm lý do.
- [ ] Current governing CR, superseded CR và current design schema đã rõ ràng.
- [ ] Mỗi record bị ảnh hưởng link CR bằng reading status và reason riêng cho từng CR.
- [ ] Record update order, report impact, QA plan, rule chọn release path, monitoring field và rollback alternative đã được ghi.
- [ ] Simulated value và runtime evidence còn thiếu đã được label rõ.
- [ ] Blocking decision có owner, exit criteria và target/review date.

Documentation completion không cấp quyền implementation và không được báo cáo là runtime `Closed`.

### 8.2 Đóng runtime

Chỉ đánh dấu runtime CR là `Closed` khi:

- [ ] Verified live baseline và deployment path được chọn đã được ghi kèm evidence.
- [ ] Các positive, negative, duplicate, consent, privacy, routing, migration/direct-deployment và regression test bắt buộc đều pass.
- [ ] Đã verify đúng Application build, GA4 property/stream, GTM version, environment và destination dự kiến.
- [ ] Production smoke test hoàn tất khi change yêu cầu.
- [ ] Monitoring có owner, baseline hoặc expected range, threshold, observation window và escalation path.
- [ ] Processed-data validation bắt buộc đã hoàn tất, hoặc có Pending item hợp lệ với owner, due date và expected check.
- [ ] Defect, exception, residual risk, rollback/containment result và affected period đã được ghi.
- [ ] Release và Monitoring Record đã đóng hoặc được link tới follow-up đã duyệt.
- [ ] Publish date và runtime-closure date đã được ghi.

Documentation-only CR dùng runtime status `Not planned`. Simulation CR có thể được triển khai sau này dùng `Not started` hoặc `Blocked`; runtime project trong tương lai phải xác minh lại baseline và approval, không được kế thừa release decision từ simulation.

## 9. Template Change Request dùng lại

Change Request ID:

Change mode (runtime / simulation-design / documentation-only / incident):

Requester:

Business owner:

Technical owner:

QA owner:

Approver/publisher:

Request type:

Composite/Umbrella scope và lý do phải atomic, hoặc N/A:

Risk level và lý do:

Business purpose:

Acceptance criteria:

GA4 property/stream/Measurement ID:

GTM account/container/workspace/environment:

Application/journey/URL/build:

Change summary:

Change reading instruction cho từng linked record và từng CR (Required / Context only / Historical lineage / N/A) và lý do:

Historical design baseline / version:

Current design baseline / governing CR:

Proposed state / target version:

Supersedes / Superseded by:

Verified live baseline (Application build / GTM version / GA4 destination-schema / timestamp / evidence), hoặc `No verified live baseline`:

Effective release / migration boundary:

Deployment path (direct / compatibility migration / report-document only / N/A) và lý do dựa trên evidence:

Record update order:

Historical comparability / rollback decision:

Impact assessment:

Các project record `FD-REC-*` được link và Section 01–10 dùng làm governing reference:

Evidence location/access/retention:

Target release/version/window:

Smoke-test method:

Rollback hoặc containment path:

Monitoring signal/baseline/threshold/window/owner/escalation:

Open decisions/blockers (ID / owner / exit condition / target hoặc review date / lifecycle bị ảnh hưởng):

Document status:

Runtime status:

Ngày created/target/documentation-complete/published/runtime-closed:

Open follow-up, owner và due date:

## 10. Checklist bàn giao giữa các section

Trước khi bàn giao công việc từ section này sang section khác:

- [ ] Governing Change Request ID và các historical CR ID liên quan có mặt trong linked record.
- [ ] Mỗi linked CR có reading status và reason riêng; không có aggregate status che khuất CR nào là current.
- [ ] Current requirement, design version, verified live version, owner, document status và runtime status rõ ràng.
- [ ] Link Supersedes/Superseded-by và historical interpretation đã đầy đủ.
- [ ] Deployment path dựa trên verified runtime-baseline evidence; unknown baseline phải block runtime handoff.
- [ ] Expected behavior và acceptance criteria không bị định nghĩa lại ở downstream.
- [ ] Evidence link đã sanitize và người tiếp theo có quyền truy cập.
- [ ] Layer không bị ảnh hưởng được ghi N/A kèm lý do.
- [ ] Pending, Blocked và Exception có owner, exit criteria và date.
- [ ] Input bắt buộc của section tiếp theo đã đầy đủ.

## 11. Hướng dẫn sử dụng tài liệu trong FD journey

Sử dụng tài liệu này như control sheet cho một FD change thuộc phạm vi governance, bao gồm simulation/design change làm thay đổi current design baseline. Mở hoặc cập nhật Change Request khi có thay đổi về requirement, event contract, application mapping, giá trị Data Layer, GTM Variable/Trigger/Tag, kỳ vọng QA, report/configuration, release hoặc monitoring behavior.

Các tài liệu research ở Section 01–10 cung cấp kiến thức, template có thể tái sử dụng, cấu trúc record và ví dụ. Đây là tài liệu tham chiếu, không phải project record. Quyết định, owner, status, evidence và approval cụ thể của project phải nằm trong các FD record và được điều phối bằng tài liệu này.

### Quy trình khuyến nghị

1. **Mở một Change Request cho một thay đổi logic.** Gán ID duy nhất, ví dụ `FD-CR-001`; ghi mode, requester, reason, scope, target release, owner, document status và runtime status. Chỉ dùng Composite/Umbrella CR khi các change bên trong là atomic trong cùng một schema/release boundary.
2. **Mô tả hành vi trước/sau thay đổi.** Ghi rõ current behavior, proposed behavior, FD journey bị ảnh hưởng và acceptance criteria theo cách có thể kiểm chứng.
3. **Phân loại impact và risk.** Chọn change type, đánh dấu risk High/Medium/Low và liệt kê mọi layer bị ảnh hưởng. Nếu layer không bị ảnh hưởng, ghi `N/A` kèm lý do.
4. **Dùng Section 01–10 làm thư viện tham chiếu.** Chỉ chọn những knowledge, template, record structure hoặc example cần thiết để đánh giá và thực hiện thay đổi. Không copy CR ID vào các tài liệu research.
5. **Điều phối change qua các FD record.** Cập nhật các record `FD-REC-*` liên quan, giữ historical CR link, đánh dấu current governing CR, phân công reviewer và đính kèm evidence đã sanitize khi có. Trình tự thường dùng là contract → application/Data Layer → consent → GTM → report/configuration → QA → release và monitoring.
6. **Xác minh live baseline và chọn deployment path.** Không suy luận một simulation hoặc approved design đã được deploy. Chọn direct deployment khi chưa có live contract; chỉ dùng compatibility migration khi có evidence cho thấy contract cũ đang live.
7. **Review, approve và execute.** Design approval chỉ cập nhật design baseline. Runtime implementation cần authorization riêng và mọi blocker phải được giải quyết.
8. **Hoàn tất đúng lifecycle.** Đánh dấu documentation complete khi design DoD pass. Chỉ đánh dấu runtime closed sau khi QA, release, monitoring, affected-period và processed-data requirement đều pass.

### Các quy tắc tránh lỗi phổ biến

- Lưu giá trị và quyết định cụ thể của project trong `fd-calculation-records/`; giữ Section 01–10 ở dạng có thể tái sử dụng.
- Không âm thầm thay đổi event value, required field, occurrence rule hoặc allowed-value list đã được approve. Hãy mở CR mới hoặc một amendment có version rõ ràng.
- Không đánh dấu runtime QA là `Pass` khi test mới chỉ được mô phỏng hoặc review trên giấy.
- Không gọi một design là “current” nếu chưa phân biệt current design với verified live state.
- Không xóa superseded CR khỏi record history và không triển khai dựa trên CR đó nếu chưa đi theo successor link.
- Không dùng một aggregate reading status khi record link nhiều CR.
- Dùng CR làm điểm điều hướng: reviewer phải có thể đi từ request đến record bị ảnh hưởng, evidence, release và kết quả monitoring.

## 12. Ví dụ thực tế — Đổi `solution_found` từ `No` thành `No_solution`

Đây là ví dụ simulation lịch sử được thể hiện trong [`FD-CR-001`](fd-calculation-records/FD-CR-001-solution-found-value-change.md), không phải implementation contract hiện tại và không phải yêu cầu thay đổi các tài liệu research. Product team quyết định giá trị kết hợp `No` quá mơ hồ và cần đổi thành `No_solution`. Error vẫn nằm trong nhóm no-output/error kết hợp; nếu tách thành value `Error` riêng thì cần một Change Request khác. Trong FD journey hiện tại, `FD-CR-001` đã được [`FD-CR-002`](fd-calculation-records/FD-CR-002-runtime-readiness-hardening.md) và schema `3.0` supersede.

### Tóm tắt Change Request

| Hạng mục | Quyết định minh họa |
|---|---|
| Change Request ID | `FD-CR-001` (chỉ là ví dụ) |
| Project / journey | FD `calculation_action` |
| Change mode | Simulation/design; runtime chưa được thực hiện |
| Trước thay đổi | `solution_found = No` |
| Sau thay đổi | `solution_found = No_solution` |
| Change type | Modify / Schema và semantic value |
| Risk | High — vocabulary của allowed value và logic report downstream thay đổi |
| Reason | Làm rõ outcome no-solution và giảm mơ hồ cho analyst, reviewer |
| Acceptance criteria tối thiểu | `Yes` giữ nguyên; case terminal no-output/error hợp lệ emit `No_solution` theo combined semantics đã approve; value cũ `No` không còn được emit sau release; report, QA expectation và monitoring dùng value mới; invalid, duplicate, consent và routing behavior vẫn đúng |
| Document/runtime status | Documentation complete rồi được supersede / `Not started — never executed` |

### Đánh giá impact và kế hoạch thực hiện

| Layer | Việc cần làm trong FD project | Tham chiếu |
|---|---|---|
| Contract và lifecycle | Cập nhật parameter dictionary, allowed values, semantics, version và approval history. Ghi lại value cũ và release áp dụng. | Section 07 / `FD-REC-07` |
| Application và Data Layer | Cập nhật mapping emit `No_solution`; kiểm tra invalid data không được emit và empty/error outcome tuân theo combined semantics đã approve. | Section 01 / `FD-REC-01` |
| GTM Variable và Tag | Cập nhật validation của allowed value và mapping parameter sang GA4. | Sections 02 và 04 / `FD-REC-02`, `FD-REC-04` |
| Trigger | Cập nhật schema filter từ `1.0` lên `2.0`, dù authoritative firing moment không thay đổi. | Section 03 / `FD-REC-03` |
| Consent | Ghi `N/A` kèm lý do nếu consent behavior và tag blocking không thay đổi. | Section 05 / `FD-REC-05` |
| Template | Ghi `N/A` kèm lý do nếu không thay đổi custom template hoặc template permission. | Section 06 / `FD-REC-06` |
| QA và evidence | Cập nhật expected value và negative case; thực hiện runtime test cho happy path, no-solution, invalid, duplicate, consent, routing và processed-data check. | Section 08 / `FD-REC-08` |
| Report và configuration | Cập nhật dimension, filter, calculated field, formula, dashboard và documentation phân biệt `Yes` với `No_solution`. | Section 09 / `FD-REC-09` |
| Release và monitoring | Xác minh live baseline trước. Dùng direct deployment nếu không có live v1 contract, hoặc compatibility giữa v1/v2 theo nguyên tắc mutually exclusive khi có evidence cho thấy v1 đang live; publish named version, smoke test, monitor và ghi rollback/affected period. | Section 10 / `FD-REC-10` |

### Quyết định đóng request

Với simulation này, chỉ đánh dấu CR là `Documentation complete` sau khi các record bị ảnh hưởng và historical interpretation đã nhất quán; giữ runtime là `Not started` vì không có release evidence. Implementation thực tế chỉ có thể được đánh dấu runtime `Closed` sau khi deployment path đã chọn, QA, intended destination, smoke test, monitoring window, affected-period assessment và processed-data check đều pass. Khi một CR mới thay thế design, đặt document status là `Superseded`, link successor và giữ lại thông tin older schema đã từng được deploy hay chưa.
