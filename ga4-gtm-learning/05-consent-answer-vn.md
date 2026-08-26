# Quản lý Consent và Governance trong Google Tag Manager

## Consent trong GTM là gì? (What consent means in GTM)

Consent là sự cho phép của người dùng đối với một loại storage hoặc một mục đích sử dụng dữ liệu cụ thể. Trong Google Tag Manager (GTM), các consent signal cho Google tag và những tag khác biết chúng được phép hoạt động như thế nào.

Quản lý consent gồm ba trách nhiệm tách biệt:

1. **Obtain:** Nhận lựa chọn của người dùng thông qua Consent Management Platform (CMP), banner hoặc một giải pháp consent đã được phê duyệt.
2. **Communicate:** Truyền lựa chọn đó tới GTM và các sản phẩm Google dưới dạng consent state.
3. **Enforce:** Áp dụng lựa chọn thông qua tag behavior có sẵn, additional consent checks, tag configuration và các governance control.

Consent không giống Trigger. Trigger xác định event nào đủ điều kiện để một tag chạy; consent xác định tag có được phép chạy hay không và, với tag có hỗ trợ consent, tag được phép sử dụng data hoặc storage ở mức nào.

## Tại sao consent quan trọng? (Why consent matters)

Quản lý consent giúp tổ chức:

- Tôn trọng lựa chọn về quyền riêng tư của người dùng và các yêu cầu pháp lý áp dụng.
- Kiểm soát việc sử dụng cookie, identifier và dữ liệu liên quan đến advertising.
- Ngăn việc thu thập dữ liệu chưa được phê duyệt, đồng thời vẫn duy trì privacy-safe measurement khi cơ chế đó được hỗ trợ.

Consent Mode là một cơ chế truyền signal và điều chỉnh behavior. Consent Mode **không** tự tạo consent banner, không quyết định legal basis của tổ chức và cũng không tự động khiến mọi third-party tag trở nên compliant.

## Consent Mode

Google Consent Mode cho phép website truyền consent state của người dùng tới Google. Sau đó, Google tag điều chỉnh behavior liên quan đến cookie, identifier và measurement theo state đó.

### Basic và Advanced Consent Mode (Basic and advanced implementations)

| Implementation            | Trước khi người dùng lựa chọn                                                                | Khi người dùng từ chối                                                                                              | Ảnh hưởng đến measurement                                                   |
| ------------------------- | -------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------- |
| **Basic Consent Mode**    | Google tag bị chặn và không load cho tới khi người dùng tương tác với banner.                | Những tag đang bị chặn không gửi data tới Google, kể cả consent status.                                             | Modeling dựa trên một mô hình tổng quát.                                    |
| **Advanced Consent Mode** | Google tag load với default state đã cấu hình, thường là `denied` khi policy yêu cầu opt-in. | Google tag có consent awareness có thể gửi các tín hiệu cookieless giới hạn và không sử dụng storage đã bị từ chối. | Khi đủ điều kiện, có thể hỗ trợ modeling chi tiết hơn theo từng advertiser. |

Hãy lựa chọn implementation cùng privacy owner và ghi lại quyết định đó. Không được suy luận implementation chỉ từ việc banner có đang hiển thị hay không; cần kiểm tra behavior thực tế của tag và network.

### Lưu ý quan trọng: `denied` không phải lúc nào cũng có nghĩa là không có network request

`denied` nhìn chung có nghĩa là Google tag liên quan không được sử dụng storage hoặc identifier đã bị từ chối. Tuy nhiên, trong Advanced Consent Mode, tag vẫn có thể gửi các cookieless ping giới hạn, chẳng hạn consent-state signal hoặc measurement signal. Điều này phụ thuộc vào tag, consent type, configuration và behavior của sản phẩm Google.

Vì vậy, QA phải kiểm tra đồng thời:

- Cookie, local storage hoặc identifier có được tạo ra hay được đọc hay không.
- Request có được gửi hay không, request chứa gì và có bị giới hạn đúng theo behavior đã được phê duyệt hay không.

Basic Consent Mode có kỳ vọng khác: Google tag đã bị chặn thì không nên gửi data trước khi người dùng tương tác.

## Các trạng thái consent (Consent states)

Mỗi consent type cần có một operational state rõ ràng:

- **`granted`:** Người dùng hoặc policy đã được phê duyệt cho phép storage hoặc mục đích sử dụng liên quan.
- **`denied`:** Người dùng hoặc policy không cho phép.
- **Not set / unknown:** Chưa thiết lập được state có thể sử dụng. Hãy coi đây là lỗi implementation hoặc trạng thái chưa được khởi tạo, không phải là permission.

Trong Additional Consent Check của GTM, tag chỉ pass khi tất cả consent type bắt buộc đều có state là `granted`. Nếu state là `denied` hoặc vẫn chưa xác định thì additional check đó phải không pass.

### Các consent type chính (Key consent types)

| Consent type              | Kiểm soát                                                                                                  | Cách hiểu trong governance                                                                                                      |
| ------------------------- | ---------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------- |
| `analytics_storage`       | Storage dùng cho analytics measurement, chẳng hạn analytics cookie.                                        | Cần được xem xét như điều kiện cho analytics storage khi policy yêu cầu opt-in.                                                 |
| `ad_storage`              | Storage dùng cho advertising, gồm Google Ads cookie và các identifier được Google tag được hỗ trợ sử dụng. | Cần cho advertising storage; không được xem đây là thay thế cho các control về advertising data-use ở bên dưới.                 |
| `ad_user_data`            | Việc gửi user data tới Google cho các mục đích liên quan đến advertising.                                  | Phải review riêng với cookie storage; đây là data-use signal, không đơn giản chỉ là một cookie flag.                            |
| `ad_personalization`      | Việc sử dụng data cho personalized advertising.                                                            | Phải review riêng với collection và storage. Người dùng có thể cho phép measurement nhưng không cho phép quảng cáo cá nhân hóa. |
| `functionality_storage`   | Storage hỗ trợ chức năng của site hoặc app, chẳng hạn ngôn ngữ hoặc session preference.                    | Thường mang tính thiết yếu hoặc chức năng, nhưng vẫn phải phân loại theo policy của tổ chức.                                    |
| `personalization_storage` | Storage dùng cho personalization, chẳng hạn đề xuất nội dung hoặc video.                                   | Chỉ cho phép cho mục đích personalization đã được phê duyệt.                                                                    |
| `security_storage`        | Storage dùng cho security, authentication, fraud prevention hoặc bảo vệ người dùng.                        | Thường là một security control; cần xác nhận cách xử lý với privacy owner và security owner.                                    |

Bốn type đầu thường gắn với các control về Google advertising và analytics. Ba type cuối là các privacy storage type bổ sung được GTM hỗ trợ; chúng cần được map với các category tương ứng trong CMP của tổ chức.

### Behavior khi `granted` và `denied` (Granted and denied behavior)

Hãy dùng bảng sau như một governance model, sau đó kiểm tra behavior chính xác của từng tag và vendor.

| State             | Kỳ vọng đối với Google tag                                                                                                                              | Kỳ vọng đối với third-party tag                                                                                       |
| ----------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------- |
| `granted`         | Tag có thể sử dụng storage hoặc data behavior đã được phê duyệt, tùy configuration và destination.                                                      | Tag chỉ được chạy nếu purpose của tag và consent cần thiết đã được phê duyệt.                                         |
| `denied`          | Tag không được sử dụng storage hoặc identifier đã bị từ chối. Tag có thể bị chặn hoặc có thể gửi limited cookieless signal trong Advanced Consent Mode. | Block tag hoặc áp dụng behavior consent-aware được vendor documented. Không được mặc định cho rằng vendor tự enforce. |
| Not set / unknown | Phải thiết lập default trước khi measurement. Tuyệt đối không dựa vào một default tình cờ.                                                              | Coi là chưa được phê duyệt cho tới khi state được giải quyết.                                                         |

Cũng cần phân biệt **revoke consent** với **xóa dữ liệu đã được lưu trước đó**. Consent Mode tự nó không định nghĩa quy trình xóa cookie hoặc data retention của tổ chức. Hãy xác minh CMP, browser, vendor và server-side system xử lý việc revoke như thế nào.

## Thứ tự tải trang và Consent Initialization (Page-load order and Consent Initialization)

Consent phải được thiết lập trước khi các page trigger thông thường và measurement tag được evaluate.

Thứ tự logic được khuyến nghị:

```text
Page starts
   ↓
Consent Initialization - All Pages
   ↓
Set consent defaults and load/read the CMP state
   ↓
Apply any stored user choice through the consent update
   ↓
Initialization and normal triggers
   ↓
Google tag, GA4, Ads, and other tags evaluate consent
   ↓
User changes preferences → update consent on the same page
```

### Consent Initialization và Initialization

- Chỉ dùng **Consent Initialization - All Pages** cho tag hoặc template có nhiệm vụ set hoặc update consent, chẳng hạn CMP integration hoặc default-consent template.
- Dùng trigger **Initialization** thông thường cho các tag khác cần chạy sớm nhưng không quản lý consent.
- Consent Initialization được thiết kế để chạy trước các trigger khác, bao gồm cả Initialization.

Nếu CMP hoạt động bất đồng bộ, implementation phải xử lý race giữa CMP và measurement tag. Hãy dùng CMP integration đã được phê duyệt hoặc một waiting mechanism phù hợp. Không giải quyết race bằng cách tùy ý thêm exception trigger vào Google tag.

## Default consent và consent update (Default consent and consent updates)

### Default consent

Thiết lập một default rõ ràng cho mọi consent type mà implementation sử dụng. Default phải phản ánh policy đã được phê duyệt và có thể khác nhau theo từng region.

Với một policy strict opt-in, default ban đầu thường dùng cho measurement và advertising là:

```text
analytics_storage  = denied
ad_storage          = denied
ad_user_data        = denied
ad_personalization  = denied
```

Không sao chép máy móc ví dụ này cho functionality hoặc security storage. Default là một quyết định về policy và không được làm hỏng các chức năng thiết yếu của site.

Default phải được set trước các command hoặc tag gửi measurement data. Với custom GTM consent template, hãy dùng consent API của Tag Manager, chẳng hạn `setDefaultConsentState`, thay vì dựa vào một queued `gtag('consent', ...)` call bên trong Custom HTML.

### Consent update

Khi người dùng accept, reject hoặc thay đổi một category:

1. Chuyển lựa chọn của CMP thành các Google consent type đã được phê duyệt.
2. Gửi update ngay trên page nơi lựa chọn đó xảy ra.
3. Lưu lựa chọn trong CMP hoặc consent store đã được phê duyệt.
4. Re-evaluate các tag và event trong tương lai bằng state mới.

Không chờ tới sau navigation hoặc page unload. Một update được gửi ngay trước khi chuyển page có thể chưa kịp hoàn tất, khiến page tiếp theo bắt đầu với state chưa đầy đủ. Consent Mode không tự lưu lựa chọn của người dùng; CMP hoặc consent solution phải thực hiện việc đó.

## CMP integration

CMP thường chịu trách nhiệm về user interface, mô tả category, lưu preference và consent record. GTM chịu trách nhiệm nhận state và áp dụng state đó cho các tag.

Hãy ghi lại integration contract:

| Contract item    | Quyết định cần ghi nhận                                                                      |
| ---------------- | -------------------------------------------------------------------------------------------- |
| Source of truth  | CMP hoặc consent service nào là nơi sở hữu lựa chọn hiện tại?                                |
| Category mapping | CMP category nào được map với từng Google consent type?                                      |
| Initial state    | Default nào được áp dụng trước khi CMP sẵn sàng, và áp dụng ở những region nào?              |
| Update event     | Callback, template hoặc API call nào gửi lựa chọn đã thay đổi?                               |
| Timing           | Làm thế nào để CMP bất đồng bộ không race với normal tag?                                    |
| Revocation       | Sau khi người dùng withdraw, cookie, identifier và downstream permission được xử lý thế nào? |
| Evidence         | Policy version, consent record và test evidence được lưu ở đâu?                              |

Ưu tiên supported CMP integration hoặc community template đã được review. Nếu tổ chức duy trì custom integration, integration đó phải được đưa vào change control và phải test các consent API call, race condition, regional behavior và failure path.

## Cấu hình consent trong GTM (GTM consent settings)

GTM có hai cơ chế liên quan:

### Built-in consent checks

Google tag có consent awareness chứa logic có sẵn để thay đổi behavior theo consent state. Các Google tag thường có Consent Mode support gồm:

- Google tag
- Google Analytics / GA4
- Google Ads
- Floodlight
- Conversion Linker

Với các tag này, hãy dùng built-in checks làm control chính. Review các consent type được khai báo trong consent settings của tag.

### Additional consent checks

Additional consent checks là các firing gate của GTM. Chúng hữu ích cho custom tag, partner tag hoặc third-party tag không có consent behavior có sẵn phù hợp.

Các setting gồm:

- **Not set** — Chưa cấu hình additional check; đây là default và phải được review.
- **No additional consent required** — Một quyết định rõ ràng đã được review rằng không cần consent bổ sung ngoài built-in behavior.
- **Require additional consent for tag to fire** — Tag chỉ fire khi mọi consent type được chỉ định đều là `granted`.

Quy tắc governance:

- Không thêm additional consent checks vào Google tag chỉ để lặp lại built-in checks của tag đó.
- Không dùng exception trigger để block Google tag khi Consent Mode đã kiểm soát behavior của tag.
- Dùng additional checks cho third-party hoặc custom tag đã được review và không được phép fire khi thiếu một consent type cụ thể.
- Ghi lại lý do tag được đánh dấu “No additional consent required”.
- Enable và review GTM Consent Overview thường xuyên.

Additional consent checks kiểm soát việc tag có fire hay không. Chúng không thay thế CMP, consent default, Consent Mode update hoặc legal policy.

## Các thành phần kết hợp với nhau như thế nào? (How the components fit together)

```text
CMP / consent policy
        │  default + update states
        ▼
Consent Initialization / consent APIs
        │
        ▼
GTM consent state ───────────────┐
        │                         │
        │                         ├─ Built-in consent checks
        │                         └─ Additional consent checks
        ▼
Data Layer event + variables → Trigger matches → Tag is evaluated
                                                    │
                                                    ▼
                           Google tag / GA4 destination / partner endpoint
```

Tóm tắt mối quan hệ:

- **Data Layer** mang các business event và value có cấu trúc. Bản thân Data Layer không phải là bằng chứng của consent.
- **Trigger** quyết định event có khớp với điều kiện firing của tag hay không.
- **Consent** được evaluate khi tag được xem xét để execution và có thể kiểm soát việc fire hoặc behavior.
- **Tag** biến configuration và data thành một measurement action hoặc marketing action.
- **Google tag** là kết nối Google measurement trung tâm; nó có thể gửi data tới các destination được liên kết như GA4 và Google Ads.
- **GA4 destination** nhận event từ Google tag hoặc GA4 event tag theo measurement behavior và consent behavior đã được cấu hình.

### Trigger đã match và data thực sự đã gửi (Trigger matched versus data actually sent)

Đây là những quan sát khác nhau:

| Quan sát                             | Điều quan sát đó chứng minh                              | Điều quan sát đó không chứng minh                                                       |
| ------------------------------------ | -------------------------------------------------------- | --------------------------------------------------------------------------------------- |
| Trigger matched                      | Event và các điều kiện của trigger là đúng.              | Tag đã execute hoặc đã gửi request.                                                     |
| Tag fired trong Preview              | GTM cho phép tag execute trong session của container đó. | Full measurement payload đã được gửi hoặc vendor đã chấp nhận payload.                  |
| Request hiển thị trong Network panel | Một request đã được gửi tới một endpoint.                | Mọi Data Layer value đều được đưa vào request hoặc request đó được phép về mặt pháp lý. |
| Cookie hoặc storage value tồn tại    | Đã xảy ra một thao tác storage.                          | Value đến từ đúng tag dự kiến hoặc có đúng mục đích dự kiến.                            |

Khi điều tra một discrepancy, hãy kiểm tra cùng nhau event timeline, consent state, tag status, tag parameters, browser storage và network request.

## Quy trình quản lý (Management workflow)

Dùng workflow sau cho tag mới và các material consent change:

1. **Request** — Ghi lại business purpose, vendor, destination, event và expected outcome.
2. **Classify** — Xác định các tác động liên quan đến storage, data use, personal data, advertising, personalization và security.
3. **Map** — Map purpose với một hoặc nhiều consent type và ghi lại legal/privacy decision.
4. **Inventory** — Ghi lại tag, trigger, variables, built-in checks, additional checks, owner và environment.
5. **Implement** — Cấu hình defaults, CMP updates, built-in behavior và additional checks.
6. **Test** — Validate Preview, browser storage, network requests, user flows, regions và failure paths.
7. **Review** — Lấy approval từ Analytics/GTM, Engineering, Privacy và Security khi phù hợp.
8. **Publish** — Publish một version có change description và rollback reference.
9. **Monitor** — Kiểm tra diagnostics, tag behavior, consent rates, data quality và các vendor bất ngờ.
10. **Review hoặc retire** — Định kỳ revalidate purpose, consent mapping, vendor terms và behavior thực tế; remove các tag không còn sử dụng.

## Ownership và inventory

Phải phân công owner rõ ràng cho Privacy/Legal, Analytics/GTM, CMP/Engineering, Business/Marketing, QA/Data Quality và Security. Tối thiểu cần ghi nhận:

```text
Tag name and ID; vendor and endpoint; business purpose; data collected and classification;
destination; cookies/storage/identifiers; built-in and additional consent checks;
trigger and exception configuration; CMP category mapping; regions and environments;
owner and backup owner; last review date; change ticket, approval, and QA evidence.
```

## QA, Preview và network testing

### Ma trận test tối thiểu (Minimum test matrix)

| Scenario                               | Cần kiểm tra                                                                                                           |
| -------------------------------------- | ---------------------------------------------------------------------------------------------------------------------- |
| New visitor, chưa có lựa chọn trước đó | Đúng default state xuất hiện trước các normal tag.                                                                     |
| Accept all                             | Các consent type update sang granted state đã được phê duyệt; approved tag và storage hoạt động.                       |
| Reject analytics                       | Analytics storage không được sử dụng; kiểm tra network behavior tương ứng với Basic/Advanced đã chọn.                  |
| Chỉ reject advertising                 | Advertising storage và data-use signal vẫn là `denied`, trong khi analytics behavior được phép vẫn tiếp tục hoạt động. |
| Granular choice                        | Mỗi CMP category được map đúng với Google consent type tương ứng.                                                      |
| Change hoặc revoke choice              | Update được gửi trên page hiện tại; behavior trong tương lai thay đổi; cleanup behavior được ghi nhận.                 |
| Direct landing page                    | Không phụ thuộc vào page trước đó hoặc Data Layer event của page trước.                                                |
| CMP chậm hoặc bị block                 | Default và fail-safe behavior vẫn đúng.                                                                                |
| SPA route change                       | Consent state được duy trì và event không bị duplicate.                                                                |
| Multiple tabs / returning visitor      | Stored choice được xử lý nhất quán và không âm thầm ghi đè một lựa chọn mới hơn.                                       |
| Region-specific visitor                | Banner, default và tag behavior đúng với region.                                                                       |
| Staging / production                   | Đúng container, CMP configuration, IDs và destinations.                                                                |

### Cần kiểm tra gì? (What to inspect)

1. **GTM Preview / Tag Assistant:** Consent state ở từng event, thứ tự Consent Initialization, trigger matches, tag fired và blocked, built-in checks và additional checks.
2. **Browser storage:** Cookie, local storage, session storage và việc tạo identifier trước và sau mỗi lựa chọn.
3. **Network panel:** Request timing, endpoint, consent parameters, identifiers, event name và request là limited hay full.
4. **GA4 DebugView hoặc công cụ tương đương:** Event và parameters dự kiến có tới nơi sau khi consent state được phê duyệt hay không.
5. **CMP record:** Choice, category, policy version và timestamp có được ghi lại theo policy hay không.

Không được đánh dấu test là pass chỉ vì banner đã xuất hiện hoặc trigger đã match. Phải lưu evidence cho consent state, storage, request, destination và expected outcome.

## Failure modes và edge cases

Hãy theo dõi các tình huống sau:

- **CMP timing:** CMP load quá muộn, thiếu default hoặc update bị hủy trong lúc navigation. Hãy sửa thứ tự hoặc dùng một waiting strategy đã được phê duyệt.
- **Stale hoặc duplicate consent:** Stored consent vẫn tồn tại sau khi policy thay đổi, hoặc nhiều CMP, container, snippet hay plugin gửi các state xung đột nhau.
- **Hard-coded hoặc ungoverned tags:** Site code, CMS, app, vendor plugin hoặc partner tag bypass thiết kế consent của GTM.
- **Google tag bị block quá mức:** Exception trigger hoặc additional check ngăn một tag có built-in consent awareness hoạt động như dự kiến.
- **SPA, cross-domain hoặc iframe behavior:** Route change tạo duplicate update/event, hoặc consent không được truyền nhất quán.
- **Khoảng trống ở revocation và server-side:** State mới đã được gửi nhưng cookie, server-side record hoặc downstream enforcement không được xử lý theo policy.
- **Sensitive data leakage:** Calculation inputs, account identifiers, email addresses hoặc personal data khác bị push hoặc gửi trước khi có state được phê duyệt.
- **Environment drift:** Staging và production có CMP mapping, defaults, container versions hoặc destination IDs khác nhau.

## Change control

Phải yêu cầu một documented change khi:

- Thêm, xóa hoặc đổi purpose của tag hoặc destination.
- Thay đổi consent default hoặc regional rule.
- Thay đổi CMP category hoặc mapping với Google consent type.
- Chuyển Consent Mode từ Basic sang Advanced hoặc ngược lại.
- Thay đổi built-in hoặc additional consent settings của Google tag.
- Thay đổi event parameters có thể chứa personal data hoặc sensitive data.
- Thêm server-side forwarding hoặc downstream vendor mới.

Mỗi change phải bao gồm purpose, impact assessment, inventory updates, privacy review khi được yêu cầu, test evidence, approver, publish version và rollback plan.

## Ví dụ theo kiểu FD: `calculation_action` (FD-style example)

Giả sử FD có một calculator gửi GA4 event khi người dùng hoàn thành phép tính. Event đó không được chứa personal data không cần thiết.

### Data Layer event

```javascript
dataLayer.push({
  event: "calculation_action",
  calculation_action: "completed",
  calculation_type: "eligibility",
  calculation_outcome: "qualified",
});
```

Setup được khuyến nghị:

```text
Tag:     GA4 - Event - calculation_action
Trigger: CE - calculation_action - All
Event:   calculation_action
Params:  calculation_action, calculation_type, calculation_outcome
Consent: Built-in analytics_storage behavior
```

### Flow dự kiến (Expected flow)

1. CMP và `CMP - Consent - Default` chạy thông qua Consent Initialization.
2. Default đã được phê duyệt được áp dụng, chẳng hạn `analytics_storage = denied` trước khi có lựa chọn opt-in.
3. Người dùng hoàn tất FD calculation và Data Layer event được push.
4. Trigger `calculation_action` match.
5. GTM evaluate GA4 tag và built-in consent behavior của tag.
6. Nếu analytics consent là `granted`, event có thể được gửi theo GA4 configuration.
7. Nếu analytics consent là `denied`, behavior phụ thuộc vào Basic hoặc Advanced Consent Mode đã chọn: tag có thể bị block hoặc có thể gửi limited cookieless signal mà không dùng analytics storage.

Không được tự động replay một event đã xảy ra trước consent sau khi người dùng grant consent. Cần quyết định business event đó còn hợp lệ hay không, policy có cho phép replay hay không và phải ngăn duplicate conversion hoặc calculation record như thế nào.

### Tiêu chí QA cho ví dụ

- Event không được gửi tới một destination chưa được phê duyệt.
- `calculation_action` không phải là consent type; đây là business event name hoặc parameter.
- Không có email, account ID, raw financial input hoặc personal data không cần thiết nào khác được đưa vào.
- Khi analytics là `denied`, phải quan sát được storage behavior và network behavior đúng với Consent Mode đã chọn.
- Khi analytics là `granted`, GA4 nhận event và các parameter đã được document đúng một lần.
- Consent change không tạo duplicate calculation event.
