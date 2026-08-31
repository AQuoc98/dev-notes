# 03 — Quản lý Trigger trong GTM

## Mô hình tư duy về trigger

Trigger là một rule lắng nghe event và quyết định một tag có được phép chạy hay không. Trigger không tự gửi dữ liệu.

Mỗi tag cần ít nhất một firing trigger. Tuy nhiên, exception, cài đặt consent, sequencing, lỗi trình duyệt hoặc một cài đặt khác của tag vẫn có thể ngăn tag gửi dữ liệu.

### Logic trigger nhìn nhanh

Với một tag có firing trigger `F1` và `F2`, exception `B1` và `B2`, cùng yêu cầu consent:

```text
Tag có thể fire khi:

(F1 match OR F2 match)
AND NOT (B1 match OR B2 match)
AND consent/settings cho phép
AND mọi yêu cầu sequencing đã được đáp ứng
```

Hãy nhớ:

- Nhiều firing trigger trên một tag dùng logic **OR**.
- Nhiều điều kiện bên trong một trigger dùng logic **AND**.
- Nhiều exception là các điều kiện chặn; chỉ cần một exception match là đủ để block tag.

## Bắt đầu từ thời điểm nghiệp vụ

Trước khi tạo trigger, hãy ghi lại:

1. **Định nghĩa nghiệp vụ:** thực tế điều gì đã xảy ra?
2. **Nguồn có tính quyết định:** application event, native browser event, URL, visibility, timer hay nguồn khác?
3. **Tên event chính xác:** bao gồm chữ hoa/chữ thường và các giá trị cho phép.
4. **Điều kiện bắt buộc:** route, application, action, element hay form.
5. **Giá trị của event:** những variable nào phải có sẵn trên cùng event?
6. **Tần suất:** một lần cho mỗi occurrence, một lần mỗi page hay lặp lại?
7. **Consent và timing:** điều gì phải đúng trước khi tag có thể chạy?
8. **Bản đồ consumer:** tag nào sử dụng trigger này, và chúng có cùng ngữ nghĩa không?
9. **Owner và quy tắc retirement:** ai phê duyệt thay đổi và khi nào trigger không còn cần thiết?

### Ví dụ: chọn trigger cho signup đã xác nhận

Giả sử business muốn đo việc tạo tài khoản thành công.

Hành trình người dùng:

```text
Người dùng click Submit
→ application validate form
→ server tạo account
→ application nhận response thành công
→ application push `sign_up`
→ GTM fire tag signup của GA4
```

Click Submit không phải trigger đúng cho confirmed business outcome hoặc GA4 key event. Người dùng có thể click button rồi validation thất bại, gặp lỗi server hoặc bỏ dở quy trình. Response thành công từ server mới là thời điểm nghiệp vụ có tính quyết định.

Hãy dùng Custom Event do application sở hữu:

```javascript
window.dataLayer.push({
  event: "sign_up",
  method: "email",
  form_id: "register",
});
```

Sau đó cấu hình:

```text
Trigger type: Custom Event
Event name:   sign_up
Conditions:   All Custom Events cho đúng event name
Expected:     một lần fire cho mỗi account được tạo thành công
```

Không dùng click trigger cho confirmed outcome này. Click trigger vẫn hữu ích để đo ý định signup, nhưng nên gửi một event riêng như `sign_up_start` và không dùng chung tag `sign_up` thành công.

### Ma trận quyết định trigger

| Yêu cầu nghiệp vụ                                  | Trigger ưu tiên                    | Giải thích ngắn                                                                                                                |
| -------------------------------------------------- | ---------------------------------- | ------------------------------------------------------------------------------------------------------------------------------ |
| Chạy consent logic trước các tag khác              | **Consent Initialization**         | Dùng khi consent settings phải được thiết lập hoặc cập nhật trước khi các tag tracking khác chạy.                             |
| Chạy setup logic sớm nhất có thể                   | **Initialization**                 | Dùng cho cấu hình cần chạy trước trigger page thông thường; không dùng làm trigger mặc định cho analytics event.               |
| Theo dõi khi page được xem                         | **Page View**                      | Dùng khi việc page load là điều cần đo; page view không có nghĩa business action đã hoàn tất.                                  |
| Tracking sau khi HTML sẵn sàng                     | **DOM Ready**                      | Dùng khi tracking phụ thuộc DOM element chưa có ở thời điểm Page View ban đầu.                                                |
| Tracking sau khi toàn bộ page load                 | **Window Loaded**                  | Chỉ dùng khi tracking phụ thuộc resource như image hoặc script đã load xong.                                                   |
| Theo dõi action/kết quả đã được application xác nhận | **Custom Event**                 | Dùng khi application biết một điều có ý nghĩa đã xảy ra, như calculation hoàn tất hoặc signup thành công.                     |
| Theo dõi click button hoặc UI control              | **Click: All Elements**            | Dùng khi chính click là điều cần đo; click thể hiện ý định, không đảm bảo action thành công.                                   |
| Theo dõi click link                                | **Click: Just Links**              | Dùng cho click vào link (`<a>`), như điều hướng sang page hoặc website khác.                                                   |
| Theo dõi native HTML form submission               | **Form Submission**                | Dùng cho native form; với React, AJAX hoặc custom form, Custom Event từ application có thể đáng tin cậy hơn.                   |
| Theo dõi điều hướng trong Single Page Application | **History Change**                 | Dùng khi SPA thay đổi browser history mà không reload toàn bộ page; event từ application/router vẫn được ưu tiên nếu đáng tin cậy hơn. |
| Theo dõi khi element trở nên visible               | **Element Visibility**             | Dùng khi cần biết người dùng thực sự nhìn thấy element, section, banner hoặc component.                                         |
| Theo dõi tương tác với nội dung page               | **Scroll Depth** hoặc **YouTube Video** | Dùng cho scroll hoặc video; không xem các tương tác này là business outcome đã xác nhận.                                   |
| Chạy/kiểm tra sau một khoảng thời gian              | **Timer**                          | Dùng cho yêu cầu kỹ thuật theo thời gian; phải định nghĩa interval, giới hạn và điều kiện dừng.                                |
| Chỉ fire sau khi nhiều trigger đã xảy ra           | **Trigger Group**                  | Dùng khi nhiều trigger bắt buộc phải xảy ra trước khi tag fire; xác nhận chúng đã xảy ra nhưng không đảm bảo thứ tự.           |

## Timing và khả năng có sẵn của event

Các loại page-load trigger của GTM có vai trò timing khác nhau:

```text
Người dùng mở page
     │
     ▼
1. Consent Initialization
     │
     ▼
2. Initialization
     │
     ▼
3. Page View
     │
     ▼
   Đang parse HTML...
     │
     ▼
4. DOM Ready
     │
     ▼
 Đang load image / script / resource...
     │
     ▼
5. Window Loaded
```

Mỗi trigger có một mục đích khác nhau:

1. Consent Initialization — chạy đầu tiên cho consent configuration cần có trước khi tracking bắt đầu.
2. Initialization — chạy rất sớm cho setup cần hoàn tất trước các page trigger thông thường.
3. Page View — chạy khi page bắt đầu load, phù hợp khi chỉ cần thông tin page cơ bản.
4. DOM Ready — chạy sau khi HTML được parse; dùng khi tracking phụ thuộc DOM element.
5. Window Loaded — chạy sau khi page và resource đã load xong; chỉ dùng khi tracking thực sự phụ thuộc resource hoàn chỉnh.

> Dùng trigger sớm nhất tại thời điểm mọi dữ liệu bắt buộc đã có. Không chờ tới stage muộn hơn nếu requirement tracking không cần điều đó.

Ví dụ:

```text
Cần cấu hình consent?
→ Consent Initialization

Cần setup sớm?
→ Initialization

Cần Page URL / Page Path?
→ Page View

Cần đọc DOM element?
→ DOM Ready

Cần resource đã load hoàn toàn?
→ Window Loaded
```

Dùng đúng stage giúp tránh hai vấn đề phổ biến:

- Quá sớm: dữ liệu bắt buộc có thể chưa tồn tại.
- Quá muộn: tracking bị trì hoãn không cần thiết và có thể bị bỏ lỡ nếu người dùng rời page.

## Xây dựng trigger đúng cách

Luồng khuyến nghị:

```text
Define
  ↓
Search
  ↓
Create
  ↓
Filter
  ↓
Attach
  ↓
Preview
  ↓
Validate
  ↓
Publish & Monitor
```

### Bước 1 — Chuẩn bị measurement contract

Trước khi tạo trigger, định nghĩa chính xác business event cần đo và thời điểm event trở nên hợp lệ.

Tối thiểu cần định nghĩa:

- ý nghĩa nghiệp vụ;
- nguồn và timing có tính quyết định;
- Data Layer event;
- giá trị bắt buộc và tùy chọn;
- cách xử lý khi thiếu dữ liệu;
- số event kỳ vọng;
- environment;
- yêu cầu consent;
- các tag consumer.

Ví dụ:

```text
Business event:
Account creation được server xác nhận

Authoritative moment:
Application nhận xác nhận account đã được tạo thành công

Data Layer event:
sign_up

Required values:
method, form_id

Missing behavior:
Thiếu giá trị bắt buộc → không gửi / fail QA

Expected count:
Một event sign_up cho mỗi account tạo thành công

Trigger:
REG - CE - sign_up - Confirmed

Consumer tag:
REG - GA4 Event - sign_up

Environment:
Production và các environment QA/staging được phê duyệt

Consent:
Analytics consent theo thiết kế đã được phê duyệt
```

### Bước 2 — Tìm kiếm trước khi tạo

Trước khi tạo trigger mới, hãy tìm trong GTM container và tracking inventory hiện có.

Kiểm tra:

- trigger có cùng event và scope;
- tag đã gửi cùng GA4 event;
- variable cung cấp giá trị bắt buộc;
- exception hiện có;
- yêu cầu consent;
- Trigger Group;
- tag sequencing;
- trigger khác có thể tạo ra cùng business event.

Ví dụ, trước khi tạo:

```text
REG - CE - sign_up - Confirmed
```

hãy kiểm tra các trigger tương tự:

```text
REG - CE - sign_up
REG - CE - signup
REG - CE - account_created
```

Đồng thời kiểm tra consumer của chúng:

```text
Existing Trigger
      ↓
Existing GA4 Tag
      ↓
Đã gửi sign_up chưa?
```

Cách này giúp tránh duplicate tracking.

Chỉ reuse trigger khi các consumer có cùng:

```text
Event
Scope
Timing
Filters
Consent behavior
Missing-data behavior
Duplicate behavior
```

Trước khi sửa shared trigger, phải review toàn bộ consumer vì thay đổi có thể ảnh hưởng nhiều tag hoặc project.

### Bước 3 — Tạo và cấu hình trigger

Trong GTM:

1. Mở **Triggers** và chọn **New**.
2. Nhập tên trigger đã được phê duyệt.
3. Chọn **Trigger Configuration**.
4. Chọn loại trigger phù hợp.
5. Cấu hình các setting riêng của event.
6. Chọn **All ...** chỉ khi mọi occurrence đều nằm trong scope.
7. Nếu không, chọn **Some ...** và thêm filter bắt buộc.
8. Lưu trigger.

### Bước 4 — Định nghĩa filter cẩn thận

Trigger filter quyết định trigger có match hay không.

Mỗi filter gồm ba phần:

```text
Variable + Operator + Expected Value
```

Ví dụ:

```text
Page Path   equals          /pricing
Click ID    equals          pricing-submit
Page Path   matches RegEx   ^/products(?:/|$)
```

| Yêu cầu                              | Operator khuyến nghị          |
| ------------------------------------ | ----------------------------- |
| Match một giá trị chính xác          | `equals`                      |
| Value có thể chứa text ở bất kỳ đâu  | `contains`                    |
| Value phải bắt đầu bằng text cụ thể  | `starts with`                 |
| Value phải kết thúc bằng text cụ thể | `ends with`                   |
| Match nhiều value theo pattern       | `matches RegEx`               |
| Pattern không phân biệt hoa thường   | `matches RegEx (ignore case)` |
| Match DOM element cụ thể             | `matches CSS selector`        |
| Loại trừ một giá trị chính xác        | `does not equal`              |
| Loại trừ value chứa text cụ thể       | `does not contain`            |
| Loại trừ một pattern                  | `does not match RegEx`        |
| So sánh giá trị số                   | `less than`, `greater than`, v.v. |

#### Xử lý giá trị không hợp lệ hoặc bị thiếu

Với các filter quan trọng, cũng cần xem xét khi variable chứa:

```text
undefined
null
giá trị rỗng
sai chữ hoa/chữ thường
giá trị không hợp lệ
sai kiểu dữ liệu
```

Nếu business value bắt buộc bị thiếu hoặc không hợp lệ, hành vi an toàn thường là **fail closed**:

```text
Giá trị bắt buộc bị thiếu hoặc không hợp lệ
        ↓
Trigger không match
        ↓
Tag không fire
        ↓
QA phát hiện vấn đề tracking
```

### Bước 5 — Gắn trigger và review consumer

Khi gắn trigger vào tag, hãy review toàn bộ cấu hình tag.

Kiểm tra:

- firing trigger hiện có;
- tag khác gửi cùng GA4 event;
- exception;
- consent settings;
- tag sequencing;
- Google tag và Measurement ID;
- environment routing;
- firing options;
- nguy cơ duplicate event.

Ví dụ:

```text
Firing Trigger A
OR
Firing Trigger B
→ Tag đủ điều kiện fire
```

Vì vậy, không cấu hình:

```text
All Pages
+
Pricing Page
```

với kỳ vọng:

```text
All Pages
AND
Pricing Page
```

`All Pages` đã khiến tag đủ điều kiện trên mọi page.

Nếu nhiều điều kiện phải cùng đúng, đặt chúng trong một trigger phù hợp hoặc dùng thiết kế đã được phê duyệt khác như Trigger Group khi ngữ nghĩa của nó phù hợp.

Đồng thời kiểm tra trigger hoặc tag khác có thể tạo cùng business event từ cùng một hành động người dùng hay không.

### Bước 6 — Test trong GTM Preview

Không dừng kiểm thử chỉ vì GTM hiển thị:

```text
Tag Fired ✓
```

Hãy validate toàn bộ luồng GTM:

```text
Data Layer
     ↓
Variables
     ↓
Trigger
     ↓
Exceptions / Consent
     ↓
Tag
```

Với một hành động test có kiểm soát:

1. Kết nối GTM Preview / Tag Assistant tới QA hoặc staging environment.
2. Thực hiện một business action có kiểm soát.
3. Chọn event liên quan trong timeline.
4. Xác nhận Data Layer event và các giá trị.
5. Kiểm tra mọi variable được trigger và tag sử dụng.
6. Xác nhận điều kiện trigger.
7. Review **Tags Fired** và **Tags Not Fired**.
8. Kiểm tra exception và consent behavior.
9. Lặp lại action khi cần để xác minh tần suất event.
10. Test các case âm và edge case.

Ví dụ:

```text
Data Layer:
event = sign_up
method = google

        ↓

Variable:
REG - DLV - method
→ google

        ↓

Trigger:
REG - CE - sign_up - Confirmed
→ matched

        ↓

Tag:
REG - GA4 Event - sign_up
→ fired
```

Tối thiểu cần test:

```text
Kết quả thành công
Kết quả thất bại
Thiếu giá trị bắt buộc
Giá trị không hợp lệ
Duplicate action/callback
Reload/navigation
Consent denied
QA/staging environment
Production environment
```

Cũng phải xác minh tần suất kỳ vọng:

```text
Một account tạo thành công
        ↓
Một event sign_up
```

không phải:

```text
Một account tạo thành công
        ↓
sign_up
sign_up
```

### Bước 7 — Validate dữ liệu downstream

Trigger match và tag fire không đảm bảo GA4 đã nhận đúng event.

Hãy validate request cuối cùng.

Với GA4, xác nhận:

- network request đã được gửi;
- request dùng đúng Measurement ID;
- event name đúng spelling và case đã được phê duyệt;
- tên và kiểu parameter đúng;
- parameter bắt buộc có mặt;
- parameter tùy chọn tuân theo contract;
- số request khớp số event kỳ vọng;
- không chứa PII, credential, token, secret hoặc user input không giới hạn;
- consent behavior đúng;
- request được route tới đúng environment.

Ví dụ:

```text
Production
→ Production Measurement ID

Staging
→ Staging Measurement ID
```

Luồng validation khuyến nghị:

```text
GTM Preview
     ↓
Tag fired
     ↓
Network request / Hit Details
     ↓
Payload event đúng
     ↓
Measurement ID đúng
     ↓
GA4 DebugView
```

Hãy dùng network request hoặc Hit Details làm bằng chứng chính ở tầng transport.

GA4 DebugView có thể cung cấp xác nhận bổ sung khi có thể debug, nhưng không nên là bằng chứng duy nhất cho việc implementation đúng.

### Bước 8 — Review, publish và monitor

Sau khi test hoàn tất, review và publish trigger theo release process thông thường của GTM.

Trước khi publish:

1. Xác nhận measurement contract.
2. Review consumer và shared dependency của trigger.
3. Xác nhận bằng chứng QA.
4. Xác nhận environment routing.
5. Xác nhận consent và privacy behavior.
6. Publish thay đổi container theo version.
7. Thêm release note có ý nghĩa.
8. Cập nhật trigger inventory.

Ví dụ thông tin release:

```text
Container version:
v128

Change:
Add REG - CE - sign_up - Confirmed

Expected behavior:
Một event sign_up cho mỗi account được server xác nhận tạo thành công

Tested environments:
QA và production smoke test

Status:
Active
```

Sau khi publish, monitor các hành vi bất thường như:

```text
Event tăng đột biến không mong muốn
Event giảm bất thường
Duplicate event
Thiếu parameter
Sai destination
Rò rỉ giữa các environment
```

Với trigger reusable hoặc shared, duy trì inventory entry gồm:

```text
Trigger name
Trigger type
Business meaning
Event/source
Filters
Consumers
Owner
Environment
Consent behavior
Expected frequency
Status
Last review date
```

## Đặt tên và mô tả trigger

Sử dụng:

```text
[SCOPE] - [TYPE] - [BUSINESS EVENT OR PURPOSE] - [QUALIFIER]
```

| Type | Ý nghĩa                          | Ví dụ                                              |
| ---- | -------------------------------- | -------------------------------------------------- |
| `CI`   | Consent Initialization           | `SHARED - CI - Consent Defaults - All Pages`       |
| `INIT` | Initialization                   | `SHARED - INIT - Google tag - All Pages`           |
| `PV`   | Page View                        | `WEB - PV - Product Detail - /products/*`          |
| `DOM`  | DOM Ready                        | `WEB - DOM - Pricing Widget - /pricing`            |
| `WL`   | Window Loaded                    | `WEB - WL - Full Resource Load - Campaign Landing` |
| `CE`   | Custom Event                     | `REG - CE - sign_up - Confirmed`                   |
| `CLK`  | Click: All Elements              | `CALC - CLK - Calculator - Submit`                 |
| `LINK` | Click: Just Links                | `DOCS - LINK - Documentation - External`           |
| `FORM` | Form Submission                  | `CONTACT - FORM - Lead Form`                       |
| `HC`   | History Change                   | `WEB - HC - SPA Route - Virtual Pageview`          |
| `VIS`  | Element Visibility               | `WEB - VIS - Pricing CTA - Once Per Page`          |
| `TMR`  | Timer                            | `SUPPORT - TMR - Chat Widget - Loaded`             |
| `GRP`  | Trigger Group                    | `CHECKOUT - GRP - Payment + Confirmation`          |
| `EXC`  | Exception/blocking trigger       | `SHARED - EXC - Internal Traffic - QA`             |

Không dùng `Trigger 1`, `New Trigger`, `Test` hoặc `Temp` cho item đang live.

## Filter, URL, regex và selector

### Chọn thành phần URL nhỏ nhất

| Yêu cầu                          | Ưu tiên                              |
| -------------------------------- | ----------------------------------- |
| Chỉ route                        | `Page Path`                         |
| Đầy đủ protocol, host, path, query | `Page URL`                        |
| Query parameter                  | URL variable riêng                  |
| Link destination                 | `Click URL`                         |
| Fragment/history value           | History hoặc URL variable phù hợp   |

Không dùng full URL equality khi query string, trailing slash, protocol hoặc host alias có thể thay đổi, trừ khi các khác biệt đó là một phần của contract.

### Anchor regex khi boundary quan trọng

Pattern không anchor có thể match các giá trị ngoài ý muốn. Ưu tiên:

```text
^/products(?:/|$)
^https://(www\.)?example\.com/checkout(?:/|$)
^(?:Calculation|Download|Upload)$
```

Ghi lại cả giá trị được match và near-miss. Ví dụ `/products(?:/|$)` phải match `/products` và `/products/item`, nhưng không match `/productivity`.

Chỉ dùng “ignore case” khi application contract cho phép không phân biệt hoa thường. Nếu không, khác biệt về case phải fail QA để sửa source contract.

### CSS selector

Ưu tiên ID ổn định hoặc attribute do team sở hữu rõ ràng. Tránh class do framework sinh tự động, class layout và selector dựa trên visible text. Test cả element cần match và các element lân cận không được match.

## Inventory, reuse và change control

### Mẫu inventory

Duy trì một row cho mỗi trigger. Không thay đổi shared trigger trước khi xác định toàn bộ consumer.

| Field                | Cần ghi nhận                                                                    |
| -------------------- | ------------------------------------------------------------------------------ |
| Trigger ID và name   | Tên GTM và container trigger ID nếu có                                         |
| Type/event           | Loại trigger và exact event name hoặc page-load event                          |
| Conditions           | Mọi variable, operator, value, regex flag, selector và route scope             |
| Consuming tags       | Tất cả tag tham chiếu trigger và event mỗi tag gửi                             |
| Exceptions           | Blocking trigger gắn với từng tag consumer                                    |
| Group/sequencing     | Trigger Group chứa nó và dependency tag sequencing                            |
| Timing risk          | Giá trị sớm/muộn, mất navigation, container load muộn, SPA hoặc browser risk   |
| Consent              | Built-in và additional consent requirements                                    |
| Frequency            | Một lần/event, một lần/page, lặp lại hoặc application deduplication             |
| Owner/reviewer       | Team chịu trách nhiệm và approver                                              |
| Environment/version  | QA/staging/production, workspace và published container version                |
| Status               | Proposed, Active, Verified, Deprecated hoặc Retired                            |
| Retirement condition | Replacement, date, migration hoặc business condition                          |

### Trước khi thay đổi shared trigger

1. Ghi lại published version hiện tại hoặc export một bản có thể khôi phục.
2. Review **References to this Trigger** và mọi tag exception/sequencing.
3. Ghi rõ behavior cũ, behavior mới, consumer bị ảnh hưởng và số lượng kỳ vọng.
4. Test case thay đổi và mọi case hiện có của consumer.
5. Review Preview, network evidence, consent behavior và GA4 DebugView.
6. Publish với version name, owner, evidence và rollback point rõ ràng.
7. Cập nhật inventory và thông báo cho owner của mọi consumer bị ảnh hưởng.

### Retirement

Chỉ retire trigger khi:

- tag consumer đã được xóa, thay thế hoặc remap rõ ràng;
- không còn Trigger Group hoặc sequencing dependency đang hoạt động;
- replacement đã pass test positive, negative, duplicate, consent và downstream;
- đã giữ một version/export có thể khôi phục;
- inventory ghi rõ replacement và lý do retirement.

## Test plan và record

### Phạm vi test bắt buộc

Test ở các tầng application/Data Layer, GTM, browser Network và GA4 DebugView. Tag xuất hiện trong **Tags Fired** tự nó chưa phải bằng chứng đủ.

| ID  | Test case                                 | Behavior trigger kỳ vọng                              | Behavior downstream kỳ vọng                         |
| --- | ----------------------------------------- | ----------------------------------------------------- | --------------------------------------------------- |
| T01 | Event hợp lệ và đúng kỳ vọng              | Trigger match một lần                                 | Tag đúng và một request đúng                        |
| T02 | Sai event name hoặc case                  | Trigger không match                                   | Không có key-event/business-outcome request          |
| T03 | URL, selector hoặc action tương tự         | Trigger không match                                   | Không có event không liên quan                       |
| T04 | Thiếu hoặc sai giá trị bắt buộc           | Tag bị block hoặc theo rule đã ghi nhận               | Không có business outcome gây hiểu nhầm              |
| T05 | Form không hợp lệ hoặc server response lỗi | Không có success trigger                              | Không có successful-outcome request                  |
| T06 | Double click, retry, submit lặp lại       | Không có duplicate ngoài ý muốn                       | Số request khớp tracking plan                       |
| T07 | SPA route, revisit, back/forward, reload  | Route behavior khớp contract                          | Không duplicate virtual pageview                    |
| T08 | Consent denied, granted và updated        | Consent rule allow/block đúng thiết kế               | Request có consent behavior đúng                     |
| T09 | Exception condition                       | Tag tương ứng bị block                                | Tag không liên quan không bị ảnh hưởng               |
| T10 | Trigger Group theo các thứ tự khác nhau   | Group chỉ fire khi mọi member bắt buộc đã fire       | Không fire khi group chưa hoàn tất                   |
| T11 | Downstream request                        | Có thể trace trigger/tag path                         | Event, parameter, destination, type, count đúng      |
| T12 | Browser và navigation được hỗ trợ         | Link/form vẫn hoạt động                               | Tag không ngăn navigation hoặc submission            |

### Mẫu test record

Hoàn thành bảng này cho từng trigger trong scope. Không đánh dấu Pass nếu chưa có evidence.

```text
Environment:            [QA/staging URL]
GTM container:          [container]
Workspace/version:      [workspace / published version]
GA4 property/stream:    [test property and web stream]
Browser/device:         [browser and version]
Consent state:          [state before and during test]
Tester/date:            [name / YYYY-MM-DD]
Evidence location:      [Preview, Network, DebugView links hoặc sanitized capture]
```

| Test ID | Trigger               | Setup/action                                           | Expected result                                                  | Actual result                                         | Evidence     | Status  |
| ------- | --------------------- | ------------------------------------------------------ | ---------------------------------------------------------------- | ----------------------------------------------------- | ------------ | ------- |
| T01     | `FD-T01`              | Approved input action on approved FD route             | Input tag fires once; request matches plan                       | **Chưa chạy — cần Preview evidence**                 | `[add link]` | Pending |
| T02     | `FD-T02`              | `Product_Selected_Action` on approved FD route         | Product Selected tag fires once                                  | **Chưa chạy — cần Preview evidence**                 | `[add link]` | Pending |
| T03     | `FD-T03`              | Generic FD `webApps` event                             | Generic tag chỉ fire nếu purpose khác biệt                        | **Chưa chạy — cần so sánh consumer/event**           | `[add link]` | Pending |
| T04     | FD triggers           | Wrong action, missing action, unrelated route          | Tag liên quan không fire                                          | **Chưa chạy — cần Preview evidence**                 | `[add link]` | Pending |
| T05     | FD triggers           | Repeat action, retry, hoặc double submit               | Count theo tracking plan; không duplicate ngoài ý muốn             | **Chưa chạy — cần Preview và Network**               | `[add link]` | Pending |
| T06     | SPA/FD route          | Initial load, route change, back/forward, revisit      | Route behavior đúng, không duplicate pageview                    | **Chưa chạy — cần application và Preview evidence**  | `[add link]` | Pending |
| T07     | Consuming tags        | Consent denied, granted, rồi updated                    | Tag theo consent behavior đã phê duyệt                            | **Chưa chạy — cần consent test**                     | `[add link]` | Pending |
| T08     | Consuming tags        | Từng exception condition đã cấu hình                   | Tag cần block bị block; tag không liên quan không đổi             | **Chưa chạy — cần tag settings**                     | `[add link]` | Pending |
| T09     | Trigger Group, nếu có | Fire từng member rồi các member còn lại theo hai thứ tự | Group chưa đủ không fire; group đủ thì fire                     | **Chưa chạy — cần group membership**                 | `[add link]` | Pending |
| T10     | Consuming tags        | Kiểm tra Network và DebugView                          | Event, parameter, destination, type, consent, count đúng          | **Chưa chạy — cần Network/DebugView**                | `[add link]` | Pending |

Do source material không bao gồm bằng chứng GTM Preview hoặc network, các row FD ở trên là completion record bắt buộc, không phải tuyên bố rằng test đã pass.

## Tài liệu tham khảo

- [Tag Manager Help — About triggers](https://support.google.com/tagmanager/answer/7679316?hl=en): behavior của trigger, trigger filter và yêu cầu tag phải có trigger.
- [Tag Manager Help — Custom event trigger](https://support.google.com/tagmanager/answer/7679219?hl=en): dùng custom event được push vào data layer để trigger tag.
- [Tag Manager Help — Best practices for trigger configuration](https://support.google.com/tagmanager/answer/7679102?hl=en): test, giới hạn filter và các lưu ý về Consent Initialization.
- [Tag Manager Help — Preview and debug containers](https://support.google.com/tagmanager/answer/6107056?hl=en): dùng Tag Assistant để kiểm tra firing status, thứ tự và dữ liệu được xử lý trước khi publish.
