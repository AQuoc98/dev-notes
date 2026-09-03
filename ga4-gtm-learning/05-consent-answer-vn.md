# 05 — Quản lý Consent cho GA4 trong Google Tag Manager

## 1. Mục tiêu, phạm vi và đầu ra (Objective, scope, outputs)

### Mục tiêu

Chuẩn hóa cách nhận lựa chọn consent của người dùng, truyền lựa chọn đó vào Google Tag Manager (GTM), rồi kiểm tra để bảo đảm tag GA4 chỉ thu thập những gì policy đã phê duyệt.

### Phạm vi

- Thiết lập consent default và consent update trong web GTM container.
- Consent Initialization trigger, Consent Mode, built-in consent checks và additional consent checks.
- Mapping với CMP (Consent Management Platform), lưu lựa chọn, revoke và kiểm soát environment.
- QA cho storage, network request, destination và việc event tới GA4.
- Ví dụ thực tế với event FD calculation_action.

Quyết định pháp lý, phạm vi theo từng khu vực, lựa chọn CMP và cách triển khai advertising thuộc về privacy/business owner. Các consent type cho advertising chỉ được đưa vào khi tag inventory thực sự cần; đây không phải trọng tâm của tài liệu.

### Đầu ra cần có

Mỗi tag có kiểm soát consent cần có:

1. Purpose, destination và consent type đã được phê duyệt.
2. Default và đường đi của update được ghi lại.
3. Cấu hình GTM và owner rõ ràng.
4. QA record gồm consent state, tag behavior, storage, request và destination.

## 2. Tổng quan: Consent là điều kiện kiểm soát việc đo lường

### 2.1 Định nghĩa dễ hiểu

| Thuật ngữ | Ý nghĩa thực tế |
| --- | --- |
| **Consent state** | Giá trị hiện tại của một consent type: granted, denied hoặc chưa khởi tạo. |
| **CMP** | Banner hoặc preference center dùng để hỏi và lưu lựa chọn của người dùng. |
| **Consent Mode** | Cơ chế của Google để truyền consent state tới Google tag, từ đó điều chỉnh cách tag dùng storage và gửi measurement. |
| **Consent Initialization - All Pages** | Trigger chạy sớm nhất trong GTM, chỉ dành cho tag/template có nhiệm vụ set hoặc update consent. |
| **Built-in consent check** | Cơ chế consent có sẵn trong Google tag. |
| **Additional consent check** | Firing gate của GTM dành cho custom tag hoặc third-party tag đã được review. |
| **Downstream** | Hệ thống ở sau GTM nhận hoặc xử lý kết quả; trong tài liệu này, GA4 DebugView là bước kiểm tra đầu tiên và GA4 Reports đã xử lý là bước kiểm tra sau. |

Data Layer, Variable, Trigger, Tag và GA4 đã được giải thích ở Sections 01–04. Consent không thay thế các thành phần đó; consent chỉ cung cấp bối cảnh cho phép khi tag được evaluate.

### 2.2 Vòng đời (Lifecycle)

~~~text
CMP / policy đã phê duyệt
        ↓
Consent Initialization - All Pages
        ↓
Set default rõ ràng và áp dụng lựa chọn đã lưu
        ↓
Người dùng chọn hoặc đổi preference → gửi update ngay trên page hiện tại
        ↓
Application push business event vào Data Layer
        ↓
Trigger match → tag evaluate built-in/additional consent checks
        ↓
GA4 request, behavior giới hạn theo consent hoặc tag bị block
~~~

Application vẫn có thể push event vào Data Layer khi analytics consent là denied. Event đó không chứng minh người dùng đã cho phép. Bằng chứng cần xem là consent behavior của tag và request thực tế.

### 2.3 Chọn Basic hay Advanced Consent Mode

Chọn mode cùng privacy owner và ghi vào consent contract. Banner đang hiển thị không cho biết chắc implementation là Basic hay Advanced; cần kiểm tra tag và Network behavior thực tế.

| Mode | Trước khi người dùng chọn | Khi analytics consent là denied | Điều cần kiểm tra |
| --- | --- | --- | --- |
| **Basic** | Google tag bị block cho đến khi người dùng tương tác với banner. | Tag bị block không nên gửi data. | Trước khi có lựa chọn, không có GA4 request từ tag bị block. |
| **Advanced** | Google tag load với default đã cấu hình, thường là denied ở nơi policy yêu cầu opt-in. | Tag có consent awareness có thể gửi limited cookieless signal nhưng không được dùng analytics storage đã bị denied. | Kiểm tra cả storage và nội dung request. |

Behavior cụ thể còn tùy tag, consent type, configuration và sản phẩm Google. Không được cam kết “không có network request nào” nếu chưa test đúng implementation đã chọn.

## 3. Consent contract: quyết định trước khi cấu hình GTM

Hãy ghi lại các quyết định sau trước khi tạo hoặc sửa consent tag:

| Hạng mục | Cần quyết định |
| --- | --- |
| Purpose và destination | Thu thập dữ liệu gì, vì sao và gửi đi đâu. |
| Consent type | Consent type nào kiểm soát purpose; với dự án chỉ đo GA4, bắt đầu bằng analytics_storage nếu policy yêu cầu. |
| Region | Default và banner áp dụng ở khu vực nào. |
| Default state | State trước khi có lựa chọn đã lưu có thể dùng được. |
| Nguồn và thời điểm update | CMP callback hoặc template nào gửi update và gửi lúc nào. |
| Persistence | CMP lưu lựa chọn ở đâu để dùng cho page/visit tiếp theo. |
| Revoke | Điều gì thay đổi ngay và dữ liệu đã lưu trước đó được xử lý thế nào. |
| Unknown/failure | Fail-safe behavior khi CMP chậm, bị block hoặc trả về giá trị sai. |
| Mode và version | Basic hay Advanced Consent Mode; policy/implementation version nào. |
| Owner và evidence | Người chịu trách nhiệm, ticket, approval và nơi lưu QA. |

### 3.1 Mô hình state

- **granted:** Purpose đã được cho phép dùng storage hoặc data behavior tương ứng.
- **denied:** Không được dùng storage hoặc identifier đã bị từ chối; tag bị block hoặc chạy theo behavior đã được Consent Mode quy định.
- **Not set / unknown:** Chưa có state đáng tin cậy. Đây là lỗi khởi tạo, không phải permission.

Với Additional Consent Check, mọi consent type bắt buộc phải là granted tại thời điểm tag được evaluate. Một update xảy ra sau đó không tự động hợp thức hóa lần chạy trước.

### 3.2 Consent type trong tag inventory

| Consent type | Kiểm soát | Cách dùng trong tài liệu GA4 này |
| --- | --- | --- |
| analytics_storage | Analytics storage, ví dụ analytics cookie. | Consent chính cho GA4 khi policy yêu cầu. |
| ad_storage | Storage liên quan advertising. | Chỉ thêm khi có advertising tag đã được phê duyệt. |
| ad_user_data | Gửi user data cho Google vì mục đích advertising. | Quyết định riêng về data use; không tự suy ra từ analytics consent. |
| ad_personalization | Dùng data cho personalized advertising. | Quyết định riêng cho advertising. |
| functionality_storage | Storage cần cho chức năng site, ví dụ language setting. | Phân loại cùng privacy owner; không sao chép default của analytics. |
| personalization_storage | Storage cho personalization, ví dụ recommendation. | Chỉ thêm khi có purpose đã được phê duyệt. |
| security_storage | Storage cho authentication, fraud prevention hoặc security. | Thường là thiết yếu, nhưng vẫn cần xác nhận policy. |

## 4. Chi tiết triển khai trong GTM

### 4.1 Xác định một source of truth

Chỉ dùng một CMP hoặc consent service đã được phê duyệt làm nơi quản lý lựa chọn hiện tại. Trước khi sửa GTM, cần ghi lại:

- CMP category và mapping sang Google consent type.
- Container và environment nhận state.
- Callback, template hoặc integration gửi update.
- Owner và policy version.

Ưu tiên integration/template chính thức hoặc đã được review. Nếu phải tự tạo consent template trong GTM, dùng Tag Manager Consent APIs **setDefaultConsentState** và **updateConsentState**. Không thay các API này bằng lệnh gtag consent bên trong Custom HTML; Google lưu ý rằng lệnh gtag được xếp hàng có thể được xử lý sau message kế tiếp.

### 4.2 Đặt đúng thứ tự với Consent Initialization

1. Dùng **Consent Initialization - All Pages** cho CMP/default-consent tag hoặc template.
2. Set default cho mọi consent type mà container sử dụng, trước khi measurement tag có thể gửi data.
3. Áp dụng lựa chọn đã lưu hoặc gửi lựa chọn mới qua update path đã phê duyệt.
4. Dùng **Initialization** cho tag cần chạy sớm nhưng không quản lý consent.
5. Chỉ để application event và measurement tag chạy khi consent state đã sẵn sàng.

Consent Initialization luôn chạy trước Initialization và các trigger khác. Đây không phải trigger “chạy sớm cho mọi tag”. Nếu CMP bất đồng bộ, dùng waiting mechanism được integration tài liệu hóa; không tùy ý thêm exception trigger để che race condition.

### 4.3 Default, update, persistence và revoke

**Default**

- Set giá trị rõ ràng cho mọi consent type được dùng.
- Chỉ dùng regional default khi policy đã yêu cầu.
- Với strict analytics opt-in, analytics_storage = denied là điểm bắt đầu thường gặp; phải xác nhận theo policy thực tế.
- Không để giá trị CMP bị thiếu hoặc sai biến thành granted một cách ngầm định.

**Update**

- Chuyển lựa chọn của CMP thành các Google consent type đã được phê duyệt.
- Gửi update ngay trên page người dùng vừa xác nhận hoặc đổi preference.
- Lưu lựa chọn trong CMP hoặc consent store đã được phê duyệt cho các page sau.
- Các tag chạy về sau phải evaluate theo state mới.

**Revoke**

- Gửi update mới khi người dùng đổi từ granted sang denied.
- Ghi lại CMP có xóa client storage hoặc yêu cầu downstream deletion hay không; Consent Mode không tự định nghĩa quy trình này.
- Không replay business event xảy ra trước consent nếu measurement plan chưa cho phép và chưa có cơ chế chống duplicate.

### 4.4 Cấu hình consent settings của tag

Google tag hỗ trợ Consent Mode có built-in consent behavior. Hãy review consent type hiển thị trong từng Google tag và dùng built-in behavior làm control chính.

Với custom hoặc third-party tag, chọn một Additional Consent Checks setting đã được review:

| GTM setting | Cách dùng |
| --- | --- |
| **Not set** | Trạng thái tạm thời khi tag chưa được review; không được coi là đã được phê duyệt. |
| **No additional consent required** | Ghi rõ vì sao built-in behavior hoặc thiết kế của tag đã đủ. |
| **Require additional consent for tag to fire** | Thêm mọi consent type phải là granted trước khi tag chạy. |

Không thêm Additional Consent Check trùng lặp cho Google tag và không dùng exception Trigger để ghi đè Consent Mode. Consent check trả lời “tag có được phép chạy không?”; Trigger vẫn trả lời “event có khớp không?”.

### 4.5 Environment và change control

Giữ CMP configuration, consent default, container ID, destination và policy version nhất quán giữa staging và production. Mọi consent change cần:

1. Cập nhật contract và inventory.
2. Privacy/business review khi phù hợp.
3. Test trong GTM Preview và Browser Network/storage.
4. Publish container version có rollback reference.
5. Theo dõi sau release để phát hiện request bất ngờ, tag bị block sai hoặc environment drift.

## 5. QA và evidence

### 5.1 Thứ tự test

1. Dùng browser profile sạch hoặc xóa CMP storage theo hướng dẫn đã ghi nhận.
2. Ghi expected default và behavior tương ứng với Consent Mode đã chọn.
3. Mở GTM Preview, kiểm tra Consent Initialization trước application event.
4. Thực hiện lựa chọn consent đã phê duyệt, sau đó chạy business event.
5. Đối chiếu GTM status, browser storage, Network request và GA4 DebugView.
6. Lặp lại với deny, change/revoke, CMP chậm, direct landing, SPA navigation và từng environment.

Section 08 quy định evidence template và cách quyết định pass/fail đầy đủ. Ở đây chỉ tập trung vào evidence riêng của consent.

### 5.2 Ma trận tối thiểu: làm gì và kiểm tra gì

Chạy mỗi dòng như một test riêng. Bắt đầu từ điều kiện browser được nêu, thực hiện action, rồi lưu evidence theo cột cuối.

| Tình huống test | Thao tác | Điều kiện pass |
| --- | --- | --- |
| Lần đầu vào site, chưa có lựa chọn | Xóa CMP storage theo hướng dẫn, load page và kiểm tra Consent Initialization trước khi bấm banner. | Default được phê duyệt xuất hiện trước normal tag; không tag nào âm thầm coi state chưa biết là granted. |
| Grant analytics rồi chạy business event | Chọn analytics option đã được phê duyệt, xác nhận consent update rồi chạy một business event. | Update xảy ra trên cùng page; GA4 tag dùng built-in behavior; storage được phép và một request dự kiến được ghi nhận. |
| Deny analytics rồi chạy business event | Từ chối analytics, xác nhận state denied rồi chạy cùng event đó. | Không dùng analytics storage đã bị denied. Basic block tag hoặc Advanced chỉ có limited behavior đã tài liệu hóa. |
| Đổi hoặc revoke consent | Bắt đầu ở granted, chạy một event, revoke analytics trong preference center rồi chạy event thứ hai. | Revoke update gửi trên page hiện tại; event thứ hai theo denied behavior; không tạo duplicate business event. |
| CMP chậm hoặc bị block | Trong environment test, throttle hoặc block CMP rồi load page và chạy event. | Default và fail-safe behavior vẫn đúng; CMP bị thiếu không âm thầm biến thành granted. |
| Direct landing và refresh | Mở trực tiếp một deep link rồi refresh mà không đi qua page khác. | Consent state được khởi tạo từ source đã duyệt; test không phụ thuộc Data Layer message của page trước. |
| SPA route change | Đổi route không full reload rồi chạy event một lần. | Consent đã lưu vẫn có sẵn; không tạo duplicate consent update hoặc business event. |
| Staging và production | Chạy flow đã duyệt ở từng environment và đối chiếu ID, destination. | CMP configuration, container, measurement ID, destination và policy version khớp environment record. |

### 5.3 Evidence và tiêu chí pass

Phải lưu:

- Consent state và thứ tự trong GTM Preview, gồm event Consent Initialization.
- Tag status, built-in check, Additional Consent Check và Trigger đã match.
- Cookie/storage và việc tạo identifier trước/sau mỗi lựa chọn.
- Network endpoint, timing, event name, field liên quan consent, identifier và payload allowlist.
- GA4 DebugView hoặc downstream check đã được phê duyệt. Downstream nghĩa là hệ thống ở sau GTM; nó xác nhận request đã được nhận hoặc xử lý, không chỉ xác nhận GTM cho phép tag chạy.
- CMP record, policy version, environment, timestamp và tester.

Consent test chỉ pass khi state, storage behavior, request behavior, destination và downstream result khớp với tài liệu đã phê duyệt. “Downstream result” nghĩa là hệ thống kế tiếp xác nhận việc nhận hoặc xử lý đúng; không có nghĩa mọi report đã cập nhật ngay lập tức. Banner xuất hiện hoặc Trigger match riêng lẻ không đủ làm evidence.

## 6. Lưu ý vận hành và lỗi thường gặp

### Quy tắc để implementation dễ kiểm soát

- Consent state là context, không phải business event. Không push event giả consent_granted chỉ để làm GA4 tag fire.
- Chỉ có một source of truth. Nhiều CMP, snippet, plugin hoặc container có thể ghi đè state của nhau.
- State bị thiếu, sai hoặc quá cũ phải được coi là chưa được phê duyệt cho tới khi xử lý; không tự chuyển thành granted.
- Update phải idempotent để callback lặp lại không tạo duplicate update hoặc business event.
- Không đưa raw calculation input, email, account ID, credential hoặc user input không giới hạn vào Data Layer/GA4 nếu chưa được phê duyệt.

### Lỗi thường gặp và cách xử lý

| Lỗi hoặc dấu hiệu | Thường có nghĩa là | Cách xử lý thực tế |
| --- | --- | --- |
| Tag fire trước khi thấy default | Thiếu Consent Initialization, trigger chạy quá muộn hoặc gắn nhầm tag. | Sửa Consent Initialization rồi test lại bằng browser state sạch. |
| Đã deny nhưng vẫn tạo analytics storage | Google tag hoặc custom tag đang bypass consent control. | Kiểm tra built-in/additional check, script hard-code và plugin; loại bỏ đường bypass. |
| Update hoặc event xuất hiện hai lần | Nhiều callback, container hoặc SPA handler cùng fire. | Dùng một source of truth và thêm idempotency/duplicate protection. |
| GTM báo fired nhưng không thấy request | Có thể do browser restriction, ad blocker hoặc network failure. | Tách evidence cấu hình khỏi evidence delivery; test bằng browser sạch đã được duyệt và ghi nhận limitation. |
| Staging và production gửi tới nơi khác nhau | Environment configuration bị drift. | So sánh CMP mapping, container version, ID, destination và policy version trước khi publish. |
| Revoke nhưng cookie cũ vẫn còn | Revoke và xóa client storage là hai việc khác nhau; Consent Mode không tự làm việc này. | Ghi rõ CMP/browser cleanup behavior và test cùng privacy owner. |
| Server-side hoặc iframe không theo state mới | Consent chưa được truyền sang downstream component. | Bổ sung propagation contract và test end-to-end riêng. |

Sau mỗi thay đổi quan trọng của tag, hãy review GTM Consent Overview để bảo đảm mọi tag đều có consent setting có chủ đích.

## 7. Liên kết với các section khác

- **Section 01 — Data Layer Design:** business event và payload contract; Data Layer message không phải bằng chứng consent.
- **Section 02 — Variable Management:** chỉ map scalar field đã duyệt; giá trị thiếu không được biến thành permission.
- **Section 03 — Trigger Management:** Trigger match tách biệt với consent decision; event Trigger phải hẹp và authoritative.
- **Section 04 — Tag Management:** cấu hình Google tag/GA4, parameter allowlist và kiểm tra request.
- **Section 07 — Measurement Plan:** định nghĩa expected consent cho từng material event trước khi triển khai.
- **Section 08 — Debug and QA:** dùng evidence template và quyết định pass/fail đầy đủ.
- **Section 09 — Reports and Charts:** chỉ kiểm tra dữ liệu GA4 đã xử lý sau processing window được ghi nhận.
- **Section 10 — Release Monitoring:** theo dõi consent regression và destination bất ngờ ở release production đầu tiên.

## 8. Journey minh họa: FD calculation_action

Ví dụ này dùng event contract của FD và không gửi raw input hoặc personal data.

### Setup record

~~~text
CMP:                 approved web CMP
Default:             analytics_storage = denied until the approved choice
Update:              CMP callback → updateConsentState
GA4 tag:             GA4 - Event - calculation_action
Trigger:             CE - calculation_action - All
Destination:         approved GA4 web stream only
Additional check:    none; rely on the Google tag built-in analytics consent behavior
~~~

### Application message

~~~javascript
dataLayer.push({
  event: "calculation_action",
  calculation_action: "completed",
  calculation_type: "eligibility",
  calculation_outcome: "qualified"
});
~~~

### Flow dự kiến

1. Consent Initialization set default đã được phê duyệt.
2. Người dùng grant hoặc deny analytics consent; CMP gửi update trên cùng page.
3. Application hoàn tất calculation và push một message calculation_action.
4. Trigger match và GA4 tag được evaluate.
5. Nếu granted, event được gửi một lần với scalar parameter đã duyệt.
6. Nếu denied, tag theo Basic/Advanced behavior đã chọn và không dùng analytics storage bị denied.
7. Nếu consent đổi sau calculation, không replay event trừ khi measurement plan cho phép rõ ràng.

### Evidence chấp nhận

- Default và update đúng, xuất hiện trước event.
- Quan sát được đúng một application message, một Trigger match và một destination được phê duyệt.
- Không có email, account ID, raw financial input hoặc internal request token.
- Storage và Network behavior khớp Consent Mode đã chọn.
- GA4 DebugView nhận event đúng theo behavior đã phê duyệt.

## Tài liệu tham khảo (References)

- [Google for Developers — Set up consent mode on websites](https://developers.google.com/tag-platform/security/guides/consent): default, update, timing cùng page và Tag Manager Consent APIs.
- [Tag Manager Help — Tag Manager consent mode support](https://support.google.com/tagmanager/answer/10718549?hl=en): Consent Initialization, built-in check, Additional Consent Check, consent type và Consent Overview.
- [Google for Developers — Consent mode overview](https://developers.google.com/tag-platform/security/concepts/consent-mode): behavior của Basic và Advanced Consent Mode.
