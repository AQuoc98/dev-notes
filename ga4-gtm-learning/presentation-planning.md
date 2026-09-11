# Kịch bản thuyết trình GTM và GA4 — 120 phút

> **Mục tiêu của tài liệu:** chuẩn bị nội dung, thời lượng, lời dẫn, ví dụ và điểm tương tác cho một buổi trình bày 2 giờ về cách thiết kế và vận hành đo lường bằng GTM/GA4.
>
> **Phạm vi:** frontend/application, Data Layer, GTM, GA4, consent, QA, reporting, release và monitoring. Không đi sâu vào Google Ads, tối ưu media, Firebase, server-side GTM hoặc Measurement Protocol.
>
> **Ranh giới quan trọng:** các giá trị và bằng chứng trong bộ tài liệu FD hiện tại là mô phỏng thiết kế. Chưa được coi là bằng chứng runtime hoặc bằng chứng production. Khi trình bày, cần nói rõ điểm này để không tạo hiểu nhầm rằng đã có thao tác trên Application, GTM, GA4 hay production.

## 1. Thông điệp chính của buổi trình bày

Sau buổi này, người nghe cần trả lời được bốn câu hỏi:

1. Doanh nghiệp cần GA4 để hỗ trợ quyết định gì, và tại sao “có dữ liệu” chưa đồng nghĩa với “đo lường tốt”?
2. GTM và GA4 nằm ở đâu trong chuỗi Application → Data Layer → GTM → Network → GA4 → Report?
3. Một event đáng tin cậy phải được thiết kế, kiểm thử và quản lý như thế nào?
4. Khi có lỗi hoặc thay đổi tracking, team phải truy vết, release, monitor và rollback ra sao?

Thông điệp xuyên suốt nên lặp lại:

> **Application sở hữu business truth; Data Layer mang contract; GTM làm routing và vận chuyển; GA4 tiếp nhận, xử lý và hỗ trợ phân tích. Mỗi layer phải có trách nhiệm và bằng chứng riêng.**

Không nên biến buổi trình bày thành danh sách thao tác trong giao diện GTM/GA4. Điểm có giá trị hơn là cho người nghe thấy cách suy nghĩ và các cổng kiểm soát giúp dữ liệu không bị sai, trùng, thiếu, gửi nhầm nơi hoặc vi phạm consent/privacy.

## 2. Chuẩn bị trước buổi trình bày

### 2.1 Tài liệu dùng để chuẩn bị

Các file research từ `00` đến `10` được đọc và đối chiếu trước buổi nói để hoàn thiện lời thoại. Không cần mở chúng trong lúc trình bày; thông tin cần thiết đã được rút gọn trên slide và trong lời thoại của `presentation-vn-script.md`.

Chỉ chuẩn bị để mở các file record khi đến Slide 18 về FD journey:

1. `11-fd-calculation-journey.md`.
2. `fd-calculation-records/FD-REC-01` đến `FD-REC-11` theo handoff cần minh họa.
3. Issue records thực tế được dùng trước buổi nói để điền vào Slide 2–3; không cần mở trong lúc trình bày.

### 2.2 Nguyên tắc mở tài liệu tham khảo

- Các slide kiến thức không mở research file trong lúc trình bày.
- Nội dung trên slide phải ít chữ; phần giải thích nằm trong lời thoại của script.
- Chỉ mở `11-fd-calculation-journey.md` và các `FD-REC-*` khi đến phần FD journey.
- Khi có khác biệt giữa answer file và `FD-REC-*` active, dùng `FD-REC-*` active cho ví dụ FD và ghi lại khác biệt cần reconcile.
- Không gọi dữ liệu mô phỏng là runtime evidence.

### 2.3 Những điều cần xác nhận trước

Các mục dưới đây hiện đang để mở, nên không tự khẳng định trong buổi nói:

- Project hiện tại thực sự đang dùng container, property, stream và environment nào?
- Có runtime evidence nào đã được phép thu thập hay toàn bộ hiện chỉ là simulation?
- Các vấn đề quản lý đang xảy ra là vấn đề ownership, quy trình, kỹ thuật hay phối hợp liên team?
- Người nghe là frontend developer, analyst, product, privacy hay manager? Mức thuật ngữ cần điều chỉnh theo nhóm chính.

Nếu chưa có câu trả lời, giữ nguyên dưới dạng **“cần bổ sung sau”** thay vì lấp bằng giả định.

**Việc cần xử lý trước khi làm slide:** hợp nhất contract giữa các ví dụ Sections 01–04 và master FD. Một số ví dụ trong Sections 01–03 vẫn mô tả schema cũ, `solution_found = "No"`, `inputs` dạng object, mapping `fx/fy` hoặc filter schema `1.0`, trong khi `11-fd-calculation-journey.md` và các `FD-REC-*` active xác định schema `3.0`, `No_solution`, `event_id` opaque và scalar allowlist. Trong buổi nói, chỉ dùng contract hiện hành từ các `FD-REC-*` active; nếu chưa cập nhật các section cũ, ghi rõ đây là khác biệt lịch sử cần reconcile, không trình bày hai phiên bản như cùng đúng.

## 3. Agenda 120 phút

| Thời gian | Phần | Mục tiêu | Tài liệu nguồn |
|---:|---|---|---|
| 0–15 phút | Vấn đề hiện tại của project | Nêu gap, impact, owner và câu hỏi cần giải quyết | Issue records |
| 15–20 phút | Mở đầu và mục tiêu | Đặt phạm vi, outcome và runtime boundary | README |
| 20–28 phút | Change Request Governance | Scope, risk, owner, approval và closure | 00 |
| 28–38 phút | Giá trị GA4 và định nghĩa cốt lõi | Nối dữ liệu với quyết định và thống nhất thuật ngữ | 07, 09 |
| 38–48 phút | Bản đồ hệ thống và vòng đời event | Giải thích trách nhiệm của từng layer | 01 |
| 48–60 phút | Measurement Plan và Event Contract | Bắt đầu từ business question, event meaning và schema | 07 |
| 60–70 phút | Data Layer design | Thiết kế message đúng, đủ, tối thiểu và chống duplicate | 01 |
| 70–80 phút | GTM Variables, Triggers, Tags | Định nghĩa và quản lý lifecycle asset | 02–04 |
| 80–88 phút | Consent và Template Governance | Consent là gate riêng; custom template là ngoại lệ có kiểm soát | 05–06 |
| 88–93 phút | Recap / nghỉ ngắn | Kiểm tra hiểu biết và chuyển sang ví dụ phụ | — |
| 93–98 phút | FD journey — ví dụ phụ | Nối các khái niệm thành một flow ngắn | 11, FD-REC |
| 98–108 phút | Debug và QA theo từng layer | Chứng minh event đi đúng từ source tới GA4 | 08 |
| 108–116 phút | Reports, release và monitoring | Từ dữ liệu đã xử lý đến quyết định vận hành | 09–10 |
| 116–120 phút | Kết luận và hành động tiếp theo | Chốt nguyên tắc và checklist làm PPT | 11, 00 |

Nếu không nghỉ, dùng phút 78–83 cho recap và hai câu tương tác trước khi chuyển sang case FD.

## 4. Kịch bản chi tiết và lời dẫn

> **Nguồn điều khiển khi trình bày và tạo PPT:** dùng `presentation-vn-script.md`. File này chỉ giữ định hướng, nguồn tham khảo và các quyết định về thời lượng; nội dung hiển thị/lời thoại theo từng slide nằm trong script.

### 4.1 Mở đầu — 0 đến 5 phút

**Mục tiêu:** tạo ngữ cảnh và tránh kỳ vọng sai về phạm vi.

**Slide gợi ý:** tiêu đề, mục tiêu buổi nói, một sơ đồ chuỗi đo lường, phạm vi/ngoài phạm vi.

**Lời dẫn mẫu:**

> Hôm nay chúng ta không chỉ nói cách tạo một Tag trong GTM. Chúng ta sẽ đi qua toàn bộ câu hỏi: doanh nghiệp cần biết điều gì, event được sinh ra ở đâu, ai sở hữu ý nghĩa của event, làm sao để dữ liệu an toàn, làm sao chứng minh request đúng, và sau khi publish thì phát hiện lỗi bằng cách nào.
>
> Bộ tài liệu này lấy journey `calculation_action` của FD làm ví dụ. Tuy nhiên, các giá trị hiện tại là simulation ở mức thiết kế; chưa phải runtime evidence. Vì vậy, mục tiêu của buổi này là hiểu quy trình và decision gates, không tuyên bố project đã chạy production thành công.

**Tương tác mở đầu:** hỏi người nghe: “Nếu một event xuất hiện trong GA4, điều đó đã đủ để kết luận tracking đúng chưa?” Ghi nhận các câu trả lời và quay lại ở phần QA. Câu trả lời mong muốn là **chưa đủ**: cần kiểm tra ý nghĩa, payload, consent, destination, duplicate, scope và dữ liệu đã xử lý.

### 4.2 Vì sao GA4 cần thiết cho doanh nghiệp — 5 đến 15 phút

**Thông điệp:** GA4 không phải chỉ là nơi xem số liệu; GA4 là một phần trong hệ thống biến hành vi thành bằng chứng để ra quyết định.

**Ba lớp giá trị cần giải thích:**

1. **Đo lường:** biết business event nào đã xảy ra, với population và grain nào.
2. **Chẩn đoán:** phát hiện drop-off, missing event, duplicate, sai parameter, sai destination hoặc sai consent.
3. **Ra quyết định:** product, engineering, marketing và management dùng cùng một định nghĩa để hành động.

**Ví dụ nên dùng:**

- Nếu chỉ biết “có 1.000 lượt mở trang”, doanh nghiệp chưa biết có bao nhiêu lần tính hợp lệ, bao nhiêu lần có solution, bao nhiêu lần lỗi và kết quả có khác nhau theo method/country hay không.
- Nếu event `calculation_action` gửi cả response hoặc toàn bộ `inputs`, dữ liệu có thể “nhiều” hơn nhưng lại khó bảo vệ, khó phân tích và dễ làm tăng cardinality.
- Nếu rate được tính bằng event count ở tử số nhưng user count ở mẫu số, biểu đồ vẫn có thể trông hợp lý nhưng trả lời sai câu hỏi.

**Định nghĩa cần chốt:**

- **Event:** một occurrence của một business fact.
- **Parameter:** thuộc tính mô tả event.
- **Population:** tập đối tượng được đưa vào phân tích.
- **Grain:** đơn vị của mỗi count, ví dụ user, session, event hoặc item.
- **Scope:** cấp độ mà dimension/metric thuộc về.
- **Key event:** event được đánh dấu để theo dõi như outcome quan trọng; không phải event nào cũng mặc định là key event.

**Lời dẫn mẫu:**

> GA4 chỉ có giá trị khi chúng ta biết mình đang đếm cái gì, vì sao đếm, và quyết định nào sẽ thay đổi sau khi nhìn thấy con số đó. Một dashboard đẹp không sửa được event contract sai. Vì vậy, phần quan trọng nhất thường xảy ra trước khi mở giao diện GA4.

**Câu hỏi kiểm tra:** “Một event `calculation_action` có nên được gọi là conversion/key event ngay không?” Câu trả lời theo baseline FD: chưa mặc định; cần quyết định business, success condition, mixed outcome và effective date trước.

### 4.3 Bản đồ hệ thống và trách nhiệm — 15 đến 25 phút

**Sơ đồ trên slide:**

```text
Application
  → Data Layer message
  → GTM Variables
  → GTM Trigger
  → Consent / routing
  → GA4 Event tag
  → Network request
  → GA4 DebugView / Realtime / processed data
  → Report / Exploration / Decision
```

**Giải thích trách nhiệm:**

| Layer | Câu hỏi sở hữu | Không nên làm |
|---|---|---|
| Application | Business fact có thực sự xảy ra chưa? | Để GTM suy đoán từ click/DOM |
| Data Layer | Message có schema, version và field được duyệt chưa? | Push toàn bộ state hoặc raw input |
| Variable | Cần đọc giá trị nào và source là gì? | Che lỗi bằng fallback tuỳ tiện |
| Trigger | Khi nào Tag đủ điều kiện chạy? | Dùng rule quá rộng để thay event chuẩn |
| Tag | Gửi gì tới destination nào? | Tính lại business result |
| Consent | Có được phép collection không? | Coi consent là một filter phụ tùy chọn |
| GA4 | Xử lý, hiển thị và phân tích thế nào? | Dùng report để chứng minh upstream đúng |

**Lời dẫn mẫu:**

> Khi có lỗi, đừng bắt đầu bằng câu “Tag có fire không?”. Hãy hỏi “business fact có xảy ra không?”, “Data Layer có message đúng không?”, rồi mới đi xuống GTM, Network và GA4. Lớp đầu tiên thất bại thường là nơi cần sửa; các lớp sau chỉ phản ánh hoặc vận chuyển lỗi đó.

### 4.4 Measurement Plan và Event Contract — 25 đến 37 phút

**Thông điệp:** phải bắt đầu từ câu hỏi và quyết định, không bắt đầu từ tên Tag.

**Trình tự trình bày:**

1. Xác định audience và business decision.
2. Viết business moment và valid occurrence.
3. Chọn event name ổn định, không phụ thuộc UI.
4. Định nghĩa parameter dictionary: tên, type, required/optional, source, allowed value, privacy, missing behavior.
5. Xác định Data Layer handoff, GTM consumers, consent boundary, destination và schema lifecycle.
6. Quyết định key event, custom definition và cách report.
7. Ghi owner, approver, version và traceability.

**Ví dụ FD nên trình bày:**

- Event: `calculation_action`.
- Valid occurrence: một lần tính hợp lệ sau khi Application đã phân loại response/API outcome; click, input change, request start hoặc component render chưa đủ.
- `solution_found = "Yes"` khi có solution; `"No_solution"` cho trạng thái không có solution theo contract hiện tại, trong đó technical outcome vẫn là một blocker cần quyết định thêm.
- Payload chỉ gồm các scalar đã được allowlist, ví dụ `app_name`, `event_id`, `event_schema_version`, `solution_found`, `country`, `language`, `building_code`, `design_method`, `connection_type`.
- Không gửi `fx`, `fy`, toàn bộ `inputs`, API response, token, secret hoặc PII.

**Câu nói cần nhấn mạnh:**

> Measurement Plan là nơi thống nhất ý nghĩa trước khi có implementation. `FD-REC-07` là semantic source of truth; các record downstream không được tự định nghĩa lại event.

### 4.5 Data Layer design — 37 đến 49 phút

**Các nguyên tắc cần đưa lên slide:**

- Đặt tên theo business fact, không theo button, CSS selector hoặc component.
- Mỗi push tự chứa event name và các field cần thiết; không phụ thuộc stale value từ message trước.
- Contract phải rõ và có `event_schema_version`.
- Business logic nằm trong Application; analytics adapter chỉ validate và publish snapshot.
- Payload tối thiểu, có allowlist, có privacy classification.
- Một lần xảy ra hợp lệ chỉ phát một event; xử lý duplicate, remount, replay và stale response.
- Consent là boundary riêng; Application push message không có nghĩa là collection đã được phép.

**Snippet minh họa ở mức ý tưởng, không cần live code:**

```js
dataLayer.push({
  event: 'calculation_action',
  app_name: 'fd',
  event_id: '<opaque-per-occurrence-id>',
  event_schema_version: '3.0',
  solution_found: 'Yes',
  country: 'VN',
  language: 'vi',
  building_code: '...',
  design_method: '...',
  connection_type: '...'
});
```

**Điểm cần giải thích:** `event_id` là UUID ngẫu nhiên cho mỗi occurrence để đối soát giữa các layer; không dùng làm User-ID hoặc custom dimension. Quyết định collection/retention của opaque ID vẫn thuộc `FD-OPEN-004`.

**Anti-pattern cần nêu:** lấy `click text`, DOM hoặc toàn bộ object API làm dữ liệu GA4. Cách này có thể chạy nhanh nhưng làm business semantics, privacy và maintainability phụ thuộc vào UI hoặc dữ liệu không ổn định.

### 4.6 Variables, Triggers và Tags — 49 đến 60 phút

Dùng công thức ba câu hỏi để người nghe nhớ:

```text
Variable = Cần dùng giá trị nào?
Trigger  = Khi nào Tag chạy?
Tag      = Cần gửi hoặc thực thi điều gì?
```

**Variables:** ưu tiên Data Layer Variable cho business value; reuse khi contract hoàn toàn tương thích; ghi rõ missing-data behavior; dùng native type đơn giản nhất; Custom JavaScript là lựa chọn cuối cùng.

**Triggers:** chọn business moment trước, sau đó chọn Custom Event khi Application đã xác nhận outcome. Các condition trong một Trigger là AND; nhiều firing Trigger thường là OR; exception có thể chặn Tag; consent vẫn là gate riêng. Với FD, không thay `calculation_action` bằng click hoặc DOM rule rộng.

**Tags:** dùng native Google tag/GA4 Event tag khi đáp ứng được yêu cầu; map parameter theo allowlist và type; kiểm tra destination/environment; tránh overlap, duplicate fire và sequencing không cần thiết.

**Ví dụ flow FD:**

```text
DLV(event = calculation_action)
  → Custom Event Trigger
  → filter app_name = fd và schema = 3.0
  → consent/routing cho phép
  → GA4 Event tag
  → request với scalar parameters đã duyệt
```

**Lời dẫn mẫu:**

> GTM không phải nơi sửa business truth. Nếu `solution_found` sai, trước hết hãy kiểm tra Application và Data Layer. Việc thêm một Variable hoặc Custom JavaScript để “đoán lại” chỉ làm lỗi khó truy vết hơn.

### 4.7 Consent và Template Governance — 60 đến 68 phút

**Consent:**

- Xác định source of truth cho consent.
- Có default, update, persistence và revoke rõ ràng.
- Dùng Consent Initialization để trạng thái ban đầu sẵn sàng trước các Tag khác.
- Unknown, denied, delayed hoặc error phải fail-closed theo policy đã duyệt.
- Test granted, denied, unresolved, delayed và revoked; không chỉ test happy path.

**Template:**

- Kiểm tra native capability trước khi dùng custom template.
- Nếu cần custom template, phải có contract, sandbox/permission, owner, review, test, deployment record và lifecycle.
- Template không được trở thành cách né governance hoặc che business logic.

**Câu chuyển:**

> Đến đây chúng ta đã có một thiết kế hợp lý. Nhưng thiết kế chưa phải bằng chứng. Phần tiếp theo sẽ trả lời cách chứng minh nó chạy đúng.

### 4.7A Vấn đề quản lý của project hiện tại — 68 đến 73 phút

Phần này cố ý để mở để người trình bày bổ sung dữ liệu thật. Không nên biến các blocker mô phỏng của FD thành khẳng định về vấn đề vận hành của project.

**Cách trình bày mỗi vấn đề trong tối đa một phút:**

```text
Hiện tượng → tác động/rủi ro → layer hoặc nguyên nhân đầu tiên
→ quyết định cần có → giải pháp 01–10 → owner → bằng chứng đóng
```

**Lời dẫn mẫu:**

> Phần này sẽ nối framework với tình hình thực tế của project. Hiện tại chúng ta chưa điền các vấn đề cụ thể. Khi bổ sung, mỗi vấn đề cần có bằng chứng, owner, mức rủi ro và điều kiện đóng; nếu không, danh sách lỗi sẽ không giúp team ra quyết định.

Nếu có nhiều vấn đề, chỉ chọn 2–3 vấn đề có tác động lớn nhất để nói trong buổi 2 giờ; các vấn đề còn lại đưa vào phụ lục hoặc follow-up.

### 4.8 FD journey end-to-end — 83 đến 98 phút

**Khuyến nghị:** Có, nên trình bày journey FD, nhưng chỉ như **case study xuyên suốt 15 phút**, không đọc toàn bộ record. Đây là phần giúp người nghe nối 10 section thành một flow thực tế.

**Cấu trúc 1 slide overview + 4 slide chi tiết:**

#### Slide 1 — Bối cảnh và outcome

- User thực hiện một calculation.
- Application gọi API và xác định outcome.
- Event terminal là `calculation_action`.
- Contract hiện tại là schema `3.0`, payload tối thiểu.

#### Slide 2 — Quyết định semantics

- `FD-REC-07` là source of truth.
- Valid occurrence không phải click hoặc request start.
- Cần phân biệt/đánh giá `Yes`, `No_solution`, technical error, timeout và cancellation.
- Nêu bốn open decisions `FD-OPEN-001` đến `004` như ví dụ về điều quản lý chưa thể tự suy đoán.

#### Slide 3 — Implementation path

```text
Application snapshot
  → typed analytics adapter
  → dataLayer.push(calculation_action)
  → DLV / Custom Event Trigger / native GA4 Event tag
  → consent + hostname routing
  → approved GA4 destination
```

Chỉ ra các guardrail: one event per valid occurrence, opaque `event_id`, scalar allowlist, fail-closed consent và hostname.

#### Slide 4 — QA và báo cáo

- Application/data layer: message đúng schema, không duplicate.
- GTM Preview: Trigger và Tag đúng/không đúng theo scenario.
- Network: request đúng destination, event name, parameter và count.
- DebugView/Realtime: event xuất hiện đúng property/stream.
- Processed report: chỉ dùng sau processing window và field readiness.
- Không gọi `Yes / (Yes + No_solution)` là pure solution-success rate nếu `No_solution` vẫn gộp technical outcome.

#### Slide 5 — Trạng thái hiện tại

- Documentation phases đã hoàn tất ở mức simulation-design.
- Runtime readiness đang **Blocked** vì chưa có live implementation/runtime evidence.
- Việc tiếp theo: giải quyết open decisions, xác minh baseline thật, chạy QA/runtime verification được cấp quyền, sau đó mới release/monitor.

**Lời dẫn mẫu:**

> Journey này có giá trị vì nó cho thấy một thiết kế tốt không chỉ là một event name. Nó bao gồm semantics, schema, privacy, consent, routing, QA, report definition và release decision. Đồng thời, nó cũng cho thấy tài liệu hoàn chỉnh vẫn chưa đồng nghĩa với runtime đã được chứng minh.

### 4.9 Debug và Analytics QA — 98 đến 108 phút

**Trình tự kiểm tra cần ghi nhớ:**

```text
Test Run Setup
→ Data Safety Check
→ Required Test Matrix
→ Scenario Execution Summary
→ Application / Data Layer
→ GTM Preview / Tag Assistant
→ Network request
→ GA4 DebugView / Realtime
→ processed data khi cần
```

**Nguyên tắc chẩn đoán:** tìm layer lỗi đầu tiên. Ví dụ:

- Không có Data Layer message → xem Application/adapter.
- Message sai field/schema → xem contract hoặc Application.
- Message đúng nhưng Trigger không match → xem event name/filter/Variable.
- Tag fire nhưng request sai → xem Tag mapping, consent hoặc routing.
- Request đúng nhưng DebugView sai property → xem destination/Measurement ID/environment.
- DebugView đúng nhưng report chưa có → xem processing window, field readiness, scope hoặc report definition.

**Test matrix tối thiểu:** happy path, missing/invalid field, duplicate, wrong hostname, denied/unknown consent, wrong schema, wrong destination và regression với event liên quan.

**Data safety:** dùng synthetic data; không lưu PII, secret, raw form value, credential hoặc unapproved token trong evidence.

**Điểm cần nói rõ:** Preview pass không chứng minh production release đúng. Runtime verification chỉ được ghi khi thực sự có runtime run được cấp quyền; không dùng giá trị mô phỏng để điền thay.

### 4.10 Reports, release và monitoring — 108 đến 118 phút

#### Reports và Explorations

Trước khi dựng chart, ghi rõ:

```text
Audience → business question → decision
→ population → grain → scope
→ dimension/metric/formula
→ report hoặc exploration
→ limitation → interpretation → action
```

Nhấn mạnh: chart chỉ là lớp trình bày. Cần kiểm tra field readiness, data freshness, thresholding, sampling, identity, cardinality và scope. Path Exploration dùng để khám phá, không tự chứng minh causation.

#### Release và monitoring

Một material change cần Release Record, risk level, owner, scope, environment, version, approval, QA evidence, smoke test, monitoring owner, observation window và rollback/mitigation path.

Luồng release:

```text
Phân loại change
→ QA pass
→ version + approval
→ Go / Hold decision
→ publish đúng environment
→ production smoke test
→ observation / processed-data check
→ Close, Pending, Exception hoặc Incident
```

Rollback phải khôi phục một cặp Application/GTM tương thích schema. Nó chỉ khôi phục hành vi cấu hình cho các event phát sinh sau đó; không xóa/sửa dữ liệu GA4 đã xử lý và không sửa được dữ liệu lịch sử bằng cách quay version GTM.

### 4.11 Kết luận — 118 đến 120 phút

**Năm câu chốt:**

1. Bắt đầu từ business question và event contract, không bắt đầu từ Tag.
2. Application sở hữu business truth; GTM không được suy đoán thay Application.
3. Consent, privacy và destination là các gate độc lập cần bằng chứng.
4. QA phải đi xuyên qua từng layer; “Tag đã fire” chỉ là một mảnh bằng chứng.
5. Release là một vòng đời có approval, monitoring, rollback và affected-period treatment.

**Lời kết mẫu:**

> Nếu chỉ nhớ một điều, hãy nhớ rằng analytics là một hệ thống phân phối dữ liệu có contract, chứ không phải một vài đoạn tracking rời rạc. Khi contract rõ, mỗi layer có owner, mỗi thay đổi có bằng chứng và mỗi release có monitoring, team mới có thể tin vào số liệu và dùng số liệu để ra quyết định.

## 5. Cách trình bày các vấn đề quản lý của project hiện tại

Phần này người dùng sẽ bổ sung sau. Nên giữ đúng cấu trúc dưới đây để nối vấn đề với giải pháp trong Sections 01–10:

| Vấn đề hiện tại | Tác động | Bằng chứng | Section/giải pháp liên quan | Owner | Trạng thái |
|---|---|---|---|---|---|
| `[Bổ sung]` | `[Bổ sung]` | `[Link/evidence]` | `[01–10]` | `[Tên/vai trò]` | `Open` |

Khi đã có dữ liệu, trình bày mỗi vấn đề theo công thức:

```text
Hiện tượng → rủi ro → nguyên nhân/layer đầu tiên → quyết định cần có
→ giải pháp trong tài liệu → bằng chứng pass → owner và next action
```

Không nên chỉ liệt kê lỗi kỹ thuật. Với audience quản lý, cần bổ sung ownership, risk, approval, dependency, deadline, tác động tới dữ liệu lịch sử và điều kiện đóng vấn đề.

## 6. Quyết định về việc trình bày FD journey

**Nên trình bày, nhưng có giới hạn.**

### Nên đưa vào vì

- Đây là ví dụ duy nhất nối được event semantics, Data Layer, GTM, consent, QA, report và release.
- Nó làm rõ sự khác nhau giữa tài liệu thiết kế, runtime evidence và production evidence.
- Nó cho thấy các open decisions cần product, application, analytics và privacy cùng tham gia.
- Nó giúp người nghe nhìn thấy cách áp dụng Sections 01–10 thay vì học từng khái niệm rời rạc.

### Không nên trình bày toàn bộ vì

- `11-fd-calculation-journey.md` và các `FD-REC-*` chứa nhiều chi tiết record, không phù hợp để đọc trong 2 giờ.
- Các giá trị hiện tại là simulated; nếu trình bày như kết quả thực tế sẽ gây hiểu nhầm.
- Người nghe có thể bị cuốn vào chi tiết riêng của FD và bỏ qua pattern có thể tái sử dụng.

### Cách giới hạn

- Dành khoảng 15 phút cho journey.
- Dùng journey để minh họa một nguyên tắc ở mỗi layer.
- Chỉ chiếu các decision và blocker quan trọng; để link tới record cho phần đọc thêm.
- Gắn nhãn rõ `SIMULATION / DOCUMENTATION-ONLY` trên slide liên quan.
- Khi có runtime thật, tổ chức một buổi follow-up riêng về evidence và kết quả.

## 7. Bộ slide tối thiểu

1. Title và mục tiêu.
2. “GA4 giúp doanh nghiệp quyết định gì?”
3. Sai lầm: có event không có nghĩa là đo đúng.
4. Chuỗi Application → Data Layer → GTM → GA4 → Report.
5. Trách nhiệm của từng layer.
6. Measurement Plan và Event Contract.
7. Data Layer contract của FD.
8. Variable / Trigger / Tag: ba câu hỏi.
9. Consent và Template Governance.
10. FD journey end-to-end.
11. QA theo layer và test matrix.
12. Report: population, grain, scope, formula.
13. Release, monitoring, rollback.
14. Open decisions và next actions.
15. Recap / Q&A.

Mỗi slide nên có một thông điệp chính, một ví dụ hoặc sơ đồ và một câu chuyển. Không đưa nguyên văn toàn bộ record lên slide.

## 8. Câu hỏi dự kiến và câu trả lời ngắn

**“Tại sao không bắt click trong GTM cho nhanh?”**

Click chỉ chứng minh ý định UI, không chứng minh business outcome. Nếu câu hỏi là kết quả calculation, Application phải phát custom event sau khi có kết quả được xác nhận.

**“Có thể để GTM tự tính `solution_found` không?”**

Không nên. Business logic thuộc Application/API; GTM chỉ đọc field đã được phê duyệt và routing tới destination.

**“Event có trong DebugView là đã đúng chưa?”**

Chưa. Cần đối chiếu source message, Trigger/Tag, Network, destination, consent, parameter, duplicate và report readiness.

**“Rollback có sửa được số liệu GA4 đã sai không?”**

Không. Rollback giúp ngăn hành vi sai phát sinh tiếp; dữ liệu lịch sử cần được đánh dấu affected period và xử lý theo quyết định phân tích.

**“Có phải event nào cũng nên là key event?”**

Không. Key event cần business rationale, success condition, scope và effective date rõ ràng.

**“Tại sao không gửi toàn bộ `inputs` để sau này phân tích?”**

Data minimization: chỉ gửi field phục vụ measurement question đã duyệt. Gửi toàn bộ input tăng rủi ro privacy, cardinality và làm schema khó bảo trì.

## 9. Checklist diễn giả

### Trước buổi nói

- [ ] Chốt audience chính và mức thuật ngữ.
- [ ] Xác nhận phần project issues vẫn là placeholder hay đã có nội dung.
- [ ] Đánh dấu rõ mọi ví dụ FD là simulation.
- [ ] Chuẩn bị sơ đồ end-to-end và một ví dụ payload tối giản.
- [ ] Chuẩn bị hai câu hỏi tương tác ở phút 8 và phút 73.
- [ ] Mở sẵn các record cần dẫn link, không đọc nguyên văn.

### Trong buổi nói

- [ ] Không để phần định nghĩa chiếm quá nửa thời lượng.
- [ ] Luôn nối lý thuyết với một quyết định hoặc một bằng chứng.
- [ ] Phân biệt event-level với user-level.
- [ ] Phân biệt DebugView/Realtime với processed report.
- [ ] Nhắc lại runtime boundary của FD.
- [ ] Ghi lại các câu hỏi chưa trả lời để đưa vào follow-up.

### Sau buổi nói

- [ ] Cập nhật phần project issues bằng vấn đề có owner và evidence.
- [ ] Chuyển câu hỏi còn mở thành action hoặc decision record.
- [ ] Xác định nội dung cần một buổi hands-on riêng.
- [ ] Không đánh dấu runtime/release là complete nếu chưa có evidence được cấp quyền.

## 10. Next actions đề xuất

1. Bổ sung bảng “vấn đề quản lý của project hiện tại”.
2. Xác nhận audience, mục tiêu và format: lecture, workshop hay review.
3. Chốt bốn open decisions của FD (`FD-OPEN-001` đến `004`) trước khi chạy runtime/demo thật; case study simulation có thể trình bày ngay và dùng chính các blocker này làm nội dung học tập.
4. Tạo slide theo bộ tối thiểu ở Section 7.
5. Nếu có demo thật, tách demo khỏi simulation và chuẩn bị dữ liệu synthetic, environment an toàn, consent matrix và rollback plan.
6. Sau buổi trình bày, dùng câu hỏi của người nghe để ưu tiên cải thiện các record 00–10.

## 11. Checklist cuối cùng trước khi làm file PPT

- [ ] Đã bổ sung danh sách vấn đề hiện tại của project ở đầu `presentation-vn-script.md`.
- [ ] Mỗi vấn đề có hiện tượng, tác động, bằng chứng, owner, trạng thái và câu hỏi cần quyết định.
- [ ] Agenda trong script cộng đúng 120 phút và bắt đầu bằng project issues.
- [ ] Mỗi phần có mục tiêu nói, tài liệu tham khảo, lời dẫn, checklist và câu chuyển.
- [ ] Các câu trả lời còn thiếu đã được đánh dấu `[CẦN BỔ SUNG]`, không tự suy đoán.
- [ ] Các tài liệu tham khảo được mở theo đúng section; không mở nhầm example schema lịch sử.
- [ ] Contract trình bày dùng `FD-REC-*` active, schema `3.0` và scalar allowlist hiện hành.
- [ ] Mọi ví dụ FD được gắn nhãn `SIMULATION / DOCUMENTATION-ONLY` nếu chưa có runtime evidence.
- [ ] Đã quyết định phần nào cần demo, phần nào chỉ giải thích trên slide.
- [ ] Đã chuẩn bị câu trả lời cho các câu hỏi về consent, duplicate, report scope, rollback và key event.
- [ ] Đã kiểm tra số lượng slide phù hợp với 120 phút, không biến slide thành bản sao của research files.
- [ ] Chỉ bắt đầu tạo PPT sau khi checklist trong `presentation-vn-script.md` đạt trạng thái đủ để trình bày.
