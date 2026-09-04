# 01 — Thiết kế Data Layer

## 1. Mục tiêu và phạm vi

Tài liệu này định nghĩa cách frontend application phát hành business event đáng tin cậy và an toàn về quyền riêng tư cho Google Tag Manager (GTM) và Google Analytics 4 (GA4).

Data Layer là ranh giới contract giữa application code và GTM. Application sở hữu business truth; GTM đọc các giá trị đã được phê duyệt và chuyển chúng tới GA4. Thiết kế này phục vụ quy trình GTM/GA4 ổn định, có thể lặp lại, không nhằm phục vụ quảng cáo hay tối ưu campaign.

### Trong phạm vi

- Đặt tên event, quy tắc xác định một lần xảy ra hợp lệ (occurrence), payload schema, kiểu dữ liệu, allowed values và versioning.
- Một message `dataLayer.push()` đầy đủ, tự chứa đủ thông tin cho mỗi lần xảy ra hợp lệ đã được phê duyệt.
- Typed frontend analytics adapter và các safeguard cho API bất đồng bộ, SPA và component lifecycle.
- Quyền riêng tư, ranh giới consent, chống duplicate, validation và bàn giao cho GTM.
- `calculation_action` của FD làm pattern triển khai chuẩn.

### Ngoài phạm vi

- Chi tiết GTM Variables, Triggers, Tags hoặc GA4 report; xem Sections 02–04 và 09.
- Chi tiết triển khai consent management; xem Section 05.
- Template governance, release monitoring hoặc vận hành incident; xem Sections 06, 08 và 10.
- Google Ads, media buying, campaign optimization hoặc advertising attribution.

## 2. Tổng quan: ranh giới hệ thống và vòng đời event

### 2.1 Vai trò của từng thành phần

Các định nghĩa dưới đây bám theo vai trò được mô tả trong tài liệu Google Tag Manager và GA4:

| Thành phần                | Cách hiểu và trách nhiệm                                                                                                                                                                                   |
| ------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Application (website/app) | Product code render UI, nhận input, gọi API, quyết định business outcome có xảy ra hay không và publish event đã được phê duyệt.                                                                           |
| Data Layer                | Một JavaScript object, thông thường là `window.dataLayer`, được GTM và `gtag.js` dùng để truyền event data và variable có cấu trúc cho các tag. Data Layer chỉ mang dữ liệu, không tự gửi dữ liệu tới GA4. |
| Google Tag Manager (GTM)  | Hệ thống quản lý tag: đọc giá trị Data Layer qua Variables, lắng nghe bằng Triggers, áp dụng consent/routing và chạy Tags để gửi dữ liệu tới destination.                                                  |
| Google Analytics 4 (GA4)  | Analytics property nhận event và parameter từ Google tag hoặc GA4 Event tag, sau đó xử lý dữ liệu cho Realtime, DebugView, Reports và Explorations.                                                        |

Tóm tắt trong một câu: Application phát business fact, Data Layer mang message, GTM routing và gửi message, còn GA4 tiếp nhận và phân tích event.

### 2.2 Vòng đời event

```text
Application xác nhận business fact
        ↓
Application push một Data Layer message đầy đủ
        ↓
GTM xử lý message theo thứ tự trong queue
        ↓
GTM Variables đọc các field đã được phê duyệt
        ↓
GTM Trigger nhận diện event
        ↓
Consent và quy tắc destination được đánh giá
        ↓
GA4 Event tag gửi payload đã được phê duyệt
```

Boundary rule: Application sở hữu business truth, Data Layer mang message, GTM routing và gửi message, còn GA4 tiếp nhận và phân tích. Chỉ push event sau khi Application chứng minh được kết quả nghiệp vụ, và chỉ một lần cho mỗi lần xảy ra hợp lệ.

## 3. Các quy tắc thiết kế cốt lõi

### 3.1 Đặt tên theo business fact, không theo UI action

Dùng tên business event ổn định như `sign_up`, `purchase` hoặc `calculation_action`. Không đặt tên theo màu button, CSS selector, component hoặc vị trí trên màn hình. UI có thể thay đổi nhưng ý nghĩa nghiệp vụ phải giữ nguyên.

### 3.2 Định nghĩa khi nào một lần tính được xem là hợp lệ

Trước khi triển khai, cần ghi rõ thời điểm một business event được tính là đã xảy ra. Ví dụ:

- server xác nhận account creation;
- purchase được chấp nhận với transaction hợp lệ;
- một bộ input FD được chấp nhận và API đã trả về response tương ứng.

Thay đổi input, click, bắt đầu request hoặc component render chưa đủ để ghi nhận event. Với FD, response có `length > 0` là một lần tính có solution (`solution_found = "Yes"`); response `[]` là một lần tính không có solution (`solution_found = "No"`). API failure, timeout, cancellation hoặc stale response cũng phát event theo error contract với `solution_found = "No"`. Input validation không phát event.

### 3.3 Mỗi message phải tự chứa đủ thông tin

Đặt event name và toàn bộ giá trị bắt buộc trong cùng một push. Không phụ thuộc vào giá trị còn sót từ message trước. Điều này ngăn GTM Variables đọc nhầm dữ liệu cũ khi application phát event liên tiếp.

### 3.4 Dùng contract (bộ quy ước chung) tường minh và có version

`Contract` là bản quy ước mà Application, Data Layer, GTM và GA4 cùng phải tuân theo. Với mỗi field, ghi rõ: tên, kiểu dữ liệu, bắt buộc hay tùy chọn, nguồn dữ liệu, giá trị/unit được phép, phân loại quyền riêng tư và cách xử lý khi thiếu. Dùng các giá trị ổn định cho máy đọc; không bắt GTM đổi display label của UI thành giá trị chuẩn. `event_schema_version` là mã phiên bản của bộ quy ước này. Khi thay đổi làm cho code hoặc cấu hình cũ không còn tương thích, phải tăng version và cập nhật Application, GTM, QA và reporting cùng nhau.

### 3.5 Chỉ thu thập dữ liệu hữu ích tối thiểu

Mỗi field phải trả lời một measurement question đã được phê duyệt. Không đưa toàn bộ form hoặc application state vào Data Layer chỉ vì chúng đang có sẵn. Không bao giờ gửi email, tên, credential, access token, password, comment không giới hạn, raw user text hoặc API output nhạy cảm.

### 3.6 Business logic nằm trong application

Application hoặc API response quyết định `solution_found`, transaction validity, registration success và các outcome khác. Analytics adapter chỉ validate và publish snapshot đã được phê duyệt. GTM chỉ transport và routing, không tính toán hoặc suy luận business result.

### 3.7 Consent là một ranh giới riêng

Application có thể tạo Data Layer message trước khi consent được cấp, nhưng message đó không chứng minh collection được phép. Consent default, update và tag behavior được định nghĩa ở Section 05 và kiểm tra ở Section 08.

## 4. Thiết kế contract và schema

### 4.1 Contract record

Tạo một record trước khi code:

```text
Event name:
Business definition:
Một lần xảy ra hợp lệ (valid occurrence):
Emission timing:
Expected frequency:
Required fields:
Optional fields:
Source của từng field:
Allowed values và units:
Privacy classification:
Data Layer schema version:
Owner và approver:
GTM/GA4 consumers:
```

Record này là nguồn cho Measurement Plan (Section 07), thiết kế GTM asset (Sections 02–04), QA expectation (Section 08) và field readiness của report (Section 09).

### 4.2 Event envelope (khung thông tin chung) của FD

`Event envelope` là phần khung nằm ngoài cùng của message. Nó cho biết message thuộc event nào, dùng version nào, do application nào phát ra, kết quả ra sao và snapshot input nằm ở đâu. Contract calculation của FD dùng khung ổn định sau:

| Field                  | Type    | Bắt buộc | Source và quy tắc                                                                                                     |
| ---------------------- | ------- | -------- | --------------------------------------------------------------------------------------------------------------------- |
| `event`                | string  | Có       | Application constant; giá trị chính xác `calculation_action`.                                                         |
| `event_schema_version` | string  | Có       | Application constant; version hiện tại `1.0`.                                                                         |
| `app_name`             | string  | Có       | Application constant; giá trị chính xác `fd`.                                                                         |
| `solution_found`       | string  | Có       | Response có `length > 0` → `"Yes"`; response `[]` hoặc API error → `"No"`. Chỉ Application quyết định giá trị này. |
| `inputs`               | object  | Có       | Snapshot đầy đủ đã được phê duyệt, gắn với API request tương ứng.                                                     |

Contract FD hiện tại dùng chuỗi literal `"Yes"`/`"No"` cho `solution_found`. Application phải phát đúng hai giá trị này; không âm thầm convert type trong GTM.

### 4.3 Schema `inputs` của FD

Giữ các field dưới đây trong snapshot do Application quản lý khi chúng được Measurement Plan phê duyệt. Dùng các mã ổn định để máy đọc và kiểu number/Boolean tự nhiên; ghi rõ unit cho mọi giá trị số.

| Field                                            | Type    | Quy tắc                                                   |
| ------------------------------------------------ | ------- | --------------------------------------------------------- |
| `country`                                        | string  | Country code có kiểm soát.                                |
| `language`                                       | string  | Language code có kiểm soát.                               |
| `building_code`                                  | string  | Design-code identifier có kiểm soát.                      |
| `design_method`                                  | string  | Enum design method có kiểm soát.                          |
| `unit_system`                                    | string  | Enum unit system có kiểm soát.                            |
| `connection_type`                                | string  | Enum connection type có kiểm soát; review cardinality.    |
| `fastener_installation`                          | string  | Enum installation có kiểm soát.                           |
| `fx`, `fy`                                       | number  | Input số, có tài liệu về unit/ý nghĩa.                    |
| `load_duration`                                  | string  | Enum load duration có kiểm soát.                          |
| `main_member_thickness`, `side_member_thickness` | number  | Thickness dạng số, có unit rõ ràng.                       |
| `side_member_grade`, `main_member_grade`         | string  | Enum material grade có kiểm soát.                         |
| `side_member_density`, `main_member_density`     | number  | Density dạng số, có unit rõ ràng.                         |
| `contact_length`                                 | number  | Length dạng số, có unit rõ ràng.                          |
| `predrilled`                                     | boolean | Boolean; không dùng `Yes`/`No` trừ khi contract thay đổi. |
| `fastener_angle`                                 | number  | Angle dạng số, có unit rõ ràng.                           |
| `service_class`                                  | string  | Enum service class có kiểm soát.                          |

Application nên giữ nguyên toàn bộ snapshot input đã gửi tới API để khi QA cần có thể đối chiếu response với đúng bộ input đó. Trong GTM/GA4, chỉ map các giá trị đơn lẻ (scalar field — một field chứa một giá trị), chẳng hạn `connection_type`, `unit_system` hoặc `solution_found`, khi chúng nằm trong danh sách reporting đã được phê duyệt; không gửi cả object `inputs` như một parameter. Nếu application dùng một mã tạm để ghép request với response, mã đó chỉ nên nằm trong application log. Không đưa mã này vào GA4 nếu chưa được duyệt riêng vì đây không phải field báo cáo cần thiết.

### 4.4 Quy tắc đặt tên và data shape

- Dùng `snake_case` viết thường cho event và payload key.
- Chỉ dùng một canonical event name; không tạo các alias như `fd_calc`, `calculate_click` và `calculation_done` cho cùng một business fact.
- Chỉ dùng nested object khi contract cần namespace rõ ràng như `inputs`. GTM đọc nested path bằng Data Layer Variable Version 2 (Section 02).
- Đặt `event` và mọi field bắt buộc trong cùng một push.
- Chỉ omit optional value khi contract cho phép; không thay dữ liệu thiếu bằng empty string, `unknown` hoặc value của event trước.
- Giữ nguyên natural type. Chuẩn hóa label UI hoặc string phải nằm trong application code.

## 5. Pattern triển khai frontend

### 5.1 Dùng một typed analytics adapter (adapter có kiểm tra kiểu dữ liệu)

Không đặt raw `dataLayer.push()` rải rác trong UI component. Một application-owned adapter nhỏ giúp event name và payload type có thể search, review, mock và test. Adapter không được chứa product decision logic.

### 5.2 Snapshot và API bất đồng bộ

`Snapshot` là “ảnh chụp” toàn bộ giá trị input tại một thời điểm. Khi event phụ thuộc vào API, hãy xử lý theo thứ tự:

1. Chuẩn hóa các input hiện tại về đúng kiểu và giá trị đã quy định.
2. Tạo một snapshot đầy đủ và không thay đổi snapshot đó sau khi gửi request.
3. Gửi chính snapshot đó tới API.
4. Khi response quay về, kiểm tra response thuộc về snapshot nào bằng sequence, request token hoặc cơ chế nội bộ tương đương.
5. Phân loại response: có output, không có output, input không hợp lệ, request bị hủy, timeout hoặc server lỗi.
6. Chỉ push một event đầy đủ cho loại response đã được contract cho phép.

Nếu người dùng đã nhập giá trị mới, response cũ phải được xem là stale và không được dùng để tạo event cho giá trị mới. Request thất bại cũng không được ghi nhận nhầm thành trường hợp “không có output”.

### 5.3 Safeguard cho SPA và component lifecycle

- Không emit business success từ mount, render, route restoration hoặc generic click handler.
- Định nghĩa debounce hoặc commit behavior cho input thay đổi liên tục; không phát một business event cho mỗi keystroke trừ khi đó là contract rõ ràng.
- Bảo vệ trước retry, duplicate callback, React Strict Mode, remount và websocket replay bằng idempotency ở source.
- Chọn một canonical source cho SPA page view: Enhanced Measurement, GTM History Change hoặc application/router event. Không dùng các source chồng lấn.
- Giữ analytics transport non-blocking. Tracking failure phải quan sát được trong development/QA nhưng không làm hỏng product action.

### 5.4 Application contract tests

Tự động hóa tối thiểu các kiểm tra sau:

- confirmed success phát đúng một event với name, version, type và allowed value chính xác;
- valid no-output phát event đã thống nhất với `solution_found = "No"`;
- API failure, timeout, cancellation và stale response phát error event với `solution_found = "No"`; input validation và abandoned UI không phát event;
- stale response, retry, duplicate callback, remount và Strict Mode không tạo duplicate;
- message chứa full approved snapshot và không có prohibited field;
- optional field được omit thay vì tự tạo giá trị.

## 6. Validation và bàn giao cho GTM

Trước khi bàn giao event cho GTM, frontend owner cung cấp:

```text
Event name và schema version:
Occurrence và timing rule:
Số lần dự kiến cho mỗi lần xảy ra hợp lệ:
Data Layer key/path và type của từng field:
Allowed values và units:
Required và optional fields:
Semantics của valid no-output và failure:
Privacy review:
Consent dependency:
QA build/hostname:
Application test evidence:
```

GTM implementer sẽ map path đã phê duyệt vào Variables, tạo một Custom Event Trigger authoritative, áp dụng consent và environment routing, rồi map allowlist vào GA4 Event tag. Xem Sections 02–05 để biết chi tiết triển khai.

## 7. Lưu ý vận hành và guardrails

### 7.1 Stale data và persistence

GTM có thể giữ lại giá trị Data Layer qua nhiều message. Một message bỏ qua field có thể bị đọc thành giá trị từ event trước. Same-push completeness và missing-data behavior rõ ràng là biện pháp kiểm soát; không sửa stale value bằng Custom JavaScript tạm thời trong GTM.

### 7.2 Phòng duplicate

Một lần xảy ra hợp lệ phải tạo một application message. Kiểm tra duplicate container installation, GTM Tag chồng lấn, retry, remount, Strict Mode, SPA route restoration và nhiều analytics library. Các calculation hợp lệ riêng biệt vẫn phải là các event riêng.

### 7.3 Scope và quyền riêng tư

Chỉ giữ full snapshot khi cần cho application QA hoặc internal record đã được phê duyệt. Gửi tới GA4 chỉ scalar allowlist cần cho một câu hỏi có tài liệu. Không để PII, secret, raw free text hoặc API response body xuất hiện trong analytics payload trên browser.

### 7.4 Ranh giới debug

Data Layer presence chỉ chứng minh application đã push message; không chứng minh GTM đã fire, consent cho phép collection, request tới đúng stream hoặc processed GA4 data đã sẵn sàng. Xác minh từng ranh giới theo evidence sequence của Section 08.

## 8. Bản đồ tham chiếu chéo

- [Section 02 — Variable Management](02-variable-management-answer-vn.md): canonical Data Layer Variables, nested Version 2 path, naming và missing-data behavior.
- [Section 03 — Trigger Management](03-trigger-management-answer-vn.md): Custom Event Trigger filter, exception và firing-count control.
- [Section 04 — Tag Management](04-tag-management-answer-vn.md): Google tag, GA4 Event tag, parameter allowlist, consent và destination routing.
- [Section 05 — Consent Management](05-consent-answer-vn.md): consent default, update và denied-state behavior.
- [Section 06 — Template Governance](06-template-governance-answer-vn.md): reviewed template và governance khi cần custom template.
- [Section 07 — Measurement Plan](07-measurement-plan-answer-vn.md): business question, field approval, ownership và contract versioning.
- [Section 08 — Debug/QA](08-debug-qa-answer-vn.md): test setup, expected behavior, evidence, defect và retest.
- [Section 09 — Reports and Charts](09-reports-charts-answer-vn.md): field readiness, event-level QA, user-level interpretation và processing window.
- [Section 10 — Release Monitoring](10-release-monitoring-answer-vn.md): release gate, smoke test, observation, incident và rollback.

## 9. Ví dụ và Journey hoàn chỉnh

Các ví dụ được đặt ở cuối để những phần trước có thể dùng như contract và hướng dẫn triển khai tái sử dụng.

### 9.1 Đặt tên business event

Tránh gắn event với một chi tiết UI:

```javascript
// Không nên
window.dataLayer.push({ event: "green_button_click" });

// Nên dùng: business fact ổn định
window.dataLayer.push({ event: "sign_up" });
```

### 9.2 Ví dụ typed adapter

```typescript
type AnalyticsEvent =
  | {
      event: "sign_up";
      event_schema_version: "1.0";
      method: "email" | "google" | "apple";
      form_id: string;
    }
  | {
      event: "calculation_action";
      event_schema_version: "1.0";
      app_name: "fd";
      solution_found: "Yes" | "No";
      inputs: {
        connection_type: string;
        unit_system: "metric" | "imperial";
      };
    };

export function track(event: AnalyticsEvent): void {
  window.dataLayer = window.dataLayer || [];
  window.dataLayer.push(event);
}
```

Application service chỉ gọi adapter sau khi biết authoritative result:

```typescript
const result = await accountService.createAccount(input);

if (result.ok) {
  track({
    event: "sign_up",
    event_schema_version: "1.0",
    method: result.method,
    form_id: "registration",
  });
}
```

### 9.3 Journey FD `calculation_action`

```text
Người dùng thay đổi một input FD
→ application tạo complete input snapshot
→ application gửi snapshot đó tới calculation API
→ API trả response cho đúng snapshot
→ application xác định response có tạo output hay không
→ application đặt solution_found là "Yes" hoặc "No"
→ application push một complete calculation_action message
→ GTM map field được phê duyệt và gửi event tới GA4
```

Payload chuẩn cho response hợp lệ có tạo output:

```javascript
window.dataLayer.push({
  event: "calculation_action",
  event_schema_version: "1.0",
  app_name: "fd",
  solution_found: "Yes",
  inputs: {
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
  },
});
```

Với response `[]`, giữ nguyên snapshot đầy đủ và đặt `solution_found: "No"`. Với API error, timeout, cancellation hoặc stale response, phát error event theo contract và cũng đặt `solution_found: "No"`. Không phát event khi UI có input validation.

### 9.4 Shape ecommerce tùy chọn

Các nguyên tắc Data Layer vẫn giữ nguyên với ecommerce, nhưng contract có cả event-level và item-level scope. Chỉ dùng pattern này khi project đã phê duyệt requirement ecommerce:

```javascript
window.dataLayer.push({
  event: "purchase",
  ecommerce: {
    transaction_id: "T_12345",
    value: 30.03,
    currency: "USD",
    items: [
      {
        item_id: "SKU_12345",
        price: 10.01,
        quantity: 3,
      },
    ],
  },
});
```

Giữ `items` ở dạng array, bảo toàn kiểu số, dùng `transaction_id` có thẩm quyền và định nghĩa retry/replay deduplication. Xem hướng dẫn ecommerce trong Measurement Plan và Debug/QA trước khi triển khai.

## Tài liệu tham khảo

- [Google for Developers — The data layer](https://developers.google.com/tag-platform/tag-manager/datalayer)
- [Tag Manager Help — Components of Google Tag Manager](https://support.google.com/tagmanager/answer/6103657?hl=en)
- [Google Analytics — Set up events](https://developers.google.com/analytics/devguides/collection/ga4/events)
- [Google Analytics — Measure ecommerce](https://developers.google.com/analytics/devguides/collection/ga4/ecommerce)
