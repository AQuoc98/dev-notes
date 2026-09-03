# 06 — Quản trị Template trong GTM

## 1. Mục tiêu, phạm vi và đầu ra (Objective, scope, outputs)

### Mục tiêu

Đưa ra quy trình gọn và an toàn để quyết định khi nào cần GTM custom template, review code và permission, test các Tag/Variable sử dụng template, rồi retire template khi không còn cần.

### Phạm vi

- Custom Tag Template và Custom Variable Template trong web GTM container.
- Template built-in, Community Template Gallery và template do team tự phát triển.
- Field, validation, sandboxed JavaScript, API, permission, endpoint, consent, test, version và owner.
- Ảnh hưởng của template tới các Tag/Variable instance đang sử dụng nó.
- FD calculation_action làm pattern tham chiếu.

Chi tiết về Data Layer, Variable, Trigger, Tag và Consent đã nằm ở Sections 01–05. Section này chỉ quản lý lớp template dùng lại, không thay thế quy tắc của từng cấu hình được tạo từ template.

### Đầu ra cần có

Mỗi template active trong production cần có:

1. Requirement đã được phê duyệt và lý do cần dùng template.
2. Template contract gồm field, validation, data, endpoint, consent, permission và behavior khi hoàn tất.
3. Owner, source/version, consumer inventory, test và rollback/export record.
4. Review record cho mỗi lần update và các Tag/Variable instance phụ thuộc.

## 2. Tổng quan: GTM template là gì?

### 2.1 Template và instance khác nhau thế nào?

Template định nghĩa cách một Tag type hoặc Variable type hoạt động. Tag hoặc Variable là instance đã được cấu hình và tạo ra từ template đó.

| Đối tượng | Ý nghĩa thực tế |
| --- | --- |
| **Template** | Định nghĩa field cấu hình, validation, sandboxed code, API và permission. |
| **Tag instance** | Thực hiện action khi Trigger và consent settings cho phép. |
| **Variable instance** | Trả về một giá trị để Trigger hoặc Tag sử dụng. |

Sửa một Tag instance thường chỉ ảnh hưởng Tag đó. Sửa template nền có thể ảnh hưởng mọi instance phụ thuộc, nên phải coi đây là code change và dependency change.

Custom template phù hợp khi requirement đã được phê duyệt nhưng không thể giải quyết bằng Tag, Variable built-in hoặc cấu hình đơn giản. Google mô tả custom template là lựa chọn an toàn hơn so với Custom HTML hoặc Custom JavaScript không giới hạn.

### 2.2 Hai loại template

| Loại | Template phải làm gì? |
| --- | --- |
| **Tag Template** | Thực hiện action đã duyệt, chỉ gửi tới endpoint đã duyệt và báo success/failure rõ ràng. |
| **Variable Template** | Đọc input đã duyệt, validate/transform và trả về một giá trị; không tự gửi request. |

### 2.3 Thứ tự chọn phương án

Dùng phương án đơn giản và được hỗ trợ nhất:

1. Built-in option do Google cung cấp.
2. Community Template Gallery đã được review.
3. Custom template của tổ chức, có source và security review.
4. Custom HTML hoặc Custom JavaScript chỉ là exception đã được ghi nhận.

Không import hoặc build template chỉ vì template đang có sẵn. Luôn bắt đầu từ business/measurement requirement đã được duyệt.

## 3. Template contract và review

### 3.1 Các nội dung bắt buộc

Ghi lại các mục sau trước khi approve:

| Hạng mục | Cần ghi nhận |
| --- | --- |
| Purpose và type | Vì sao template tồn tại và là Tag hay Variable Template. |
| Field và default | Input hiển thị trong GTM, required/optional, help text và safe default. |
| Validation | Type, allowed value, normalization và behavior khi input sai. |
| Data handling | Data được đọc, transform, lưu hoặc gửi; loại trừ data bị cấm. |
| Endpoint | Exact HTTPS destination và URL riêng theo environment. |
| API và permission | Từng sandbox API được dùng và lý do cần dùng. |
| Consent | Consent bắt buộc, behavior khi denied/unknown và khi update. |
| Completion behavior | Tag success/failure, timeout, retry, duplicate; Variable trả về gì khi thiếu hoặc sai. |
| Consumer | Tag/Variable phụ thuộc và mức độ quan trọng. |
| Owner và lifecycle | Owner, reviewer, version, status, ngày review và điều kiện retire. |

### 3.2 Sandbox và permission

Custom template chạy trong GTM sandboxed JavaScript, không chạy như page JavaScript tự do. Template chỉ truy cập capability thông qua sandbox API và permission đã khai báo.

Sandbox giúp giới hạn quyền truy cập nhưng không có nghĩa template tự động an toàn. Cần áp dụng least privilege:

- Chỉ yêu cầu API/permission thực sự cần cho purpose đã mô tả.
- Giới hạn network permission bằng exact HTTPS URL match pattern.
- Không yêu cầu page, cookie, storage hoặc Data Layer access nếu template không cần.
- Không đặt credential, secret hoặc user input không giới hạn trong code/field.
- Review Community Template như third-party code: publisher, source, maintenance, license, endpoint và lịch sử update.

### 3.3 Production gate

Chỉ approve khi tất cả câu trả lời là “có”:

1. Requirement đã được duyệt và built-in option không đáp ứng được?
2. Source đáng tin cậy, còn được maintain và có version?
3. Field, validation, code, endpoint, consent và permission đã được hiểu rõ?
4. Đã biết consumer, owner, test và rollback/export record?
5. Có thể test trong non-production workspace?

Nếu có câu trả lời “không”, template chưa được đưa vào production.

## 4. Quy trình triển khai

### 4.1 Import hoặc build

1. Tạo inventory record trước khi import hoặc viết code.
2. Dùng các khu vực Info, Fields, Code và Permissions trong Template Editor.
3. Đặt label, help text, type, required/optional rule và safe default rõ ràng cho từng field.
4. Validate input ngay tại boundary của template.
5. Giới hạn implementation vào action hoặc value đã được phê duyệt.
6. Khai báo endpoint và permission chính xác.
7. Export hoặc commit source/version đã approve để có thể khôi phục.

Tag Template phải dùng success/failure callback của GTM để báo hoàn tất. Variable Template phải trả về giá trị xác định hoặc undefined theo behavior đã ghi nhận.

### 4.2 Test trước khi publish

Dùng unit test của template khi có, sau đó test các consumer đại diện:

1. Input hợp lệ thông thường.
2. Input thiếu hoặc sai.
3. Consent denied hoặc unknown.
4. Gọi trùng, timeout và network failure.
5. Đúng endpoint, environment, payload và request count.
6. Behavior của các Tag/Variable quan trọng trong GTM Preview.

Unit test của GTM có thể chạy code với sample input và assertion. Tuy nhiên, unit test không thay thế validation check hoặc test permission/network thực tế.

### 4.3 Inventory và ownership

Mỗi non-built-in template cần có một record tập trung:

~~~text
name | type | purpose | source/repository | approved version
permissions | endpoints | consent | consumers
owner | reviewer | last review | status | replacement/retirement condition
~~~

Owner chịu trách nhiệm về maintenance, security/privacy review, consumer impact, incident response, update và retirement. Template không có owner không được active trong production.

### 4.4 Update và ảnh hưởng tới dependency

Mọi template update đều phải được coi là code change:

1. Xác định toàn bộ Tag/Variable phụ thuộc.
2. Review source diff, exact version, field, default, validation, permission, endpoint và consent behavior.
3. Retest consumer quan trọng và có volume cao.
4. Ghi approved version, owner, evidence và rollback/export path.
5. Chỉ publish sau khi đạt cùng tiêu chuẩn review như template mới.

Không chấp nhận Gallery update tự động nếu chưa kiểm tra impact.

### 4.5 Retire

Trước khi xóa template:

1. Tìm toàn bộ Tag/Variable phụ thuộc.
2. Migrate hoặc remove các consumer đó.
3. Xác nhận không còn requirement đã duyệt phụ thuộc template.
4. Giữ lại version cuối, decision, evidence và rollback record.
5. Đánh dấu Deprecated hoặc Retired theo lifecycle policy.

## 5. Lưu ý và anti-pattern thường gặp

| Anti-pattern | Rủi ro | Cách làm nên dùng |
| --- | --- | --- |
| Import template không có requirement hoặc review | Code và permission không rõ trở thành production-active. | Chạy production gate trước. |
| Dùng Custom HTML khi đã có option được hỗ trợ | Tăng maintenance và access risk không cần thiết. | Ưu tiên built-in hoặc custom template đã review. |
| Permission rộng hoặc endpoint wildcard | Template có thể đọc/gửi data vượt purpose. | Dùng least privilege và HTTPS allowlist chính xác. |
| Không có consumer inventory | Update/xóa template làm hỏng Tag/Variable phụ thuộc. | Inventory consumer trước approve, update hoặc retire. |
| Không có owner/version/rollback | Không kiểm soát được incident hoặc update. | Ghi owner chịu trách nhiệm và version có thể khôi phục. |
| Template đọc DOM để suy ra business result | UI đổi làm measurement đổi âm thầm. | Giữ business truth ở Application/Data Layer (Section 01). |
| Gửi PII, secret hoặc raw input | Tạo rủi ro privacy và security. | Tuân thủ data contract và payload allowlist đã duyệt. |

Template governance không thay thế controls ở Sections 01–05: Application giữ business truth, Variable đọc giá trị, Trigger chọn business moment, Tag gửi data đã duyệt và Consent kiểm soát permission.

## 6. Liên kết với các section khác

- **Section 01 — Data Layer Design:** event contract và payload an toàn.
- **Section 02 — Variable Management:** ưu tiên native Variable; ghi nhận consumer của Variable Template.
- **Section 03 — Trigger Management:** dùng application event authoritative; template không tự suy ra success từ UI rule rộng.
- **Section 04 — Tag Management:** Tag type, parameter allowlist, destination, request count và validation.
- **Section 05 — Consent:** Consent Initialization và consent behavior của template/Tag phụ thuộc.
- **Section 07 — Measurement Plan:** requirement đã duyệt để biện minh cho template.
- **Section 08 — Debug and QA:** evidence và pass/fail record.
- **Section 10 — Release Monitoring:** theo dõi production sau template hoặc dependency update.

## 7. Journey minh họa: FD calculation_action

### Requirement

Gửi event FD calculation_action đã được duyệt tới GA4 bằng Google tag và GA4 Event tag.

### Quyết định governance

Không cần Custom Tag Template. Built-in Google tag và GA4 Event tag đã đáp ứng destination, event configuration, consent behavior và validation trong Preview/GA4. Dùng native Data Layer Variable của Section 02 và Custom Event Trigger authoritative của Section 03.

~~~text
FD event contract đã duyệt
        ↓
Built-in Google tag + GA4 Event tag đáp ứng requirement
        ↓
Không import hoặc build custom template
        ↓
Validate một event, một request, parameter được duyệt, consent và destination
~~~

Nếu sau này có requirement không dùng được built-in option, hãy dùng flow triển khai custom template ở mục 8 bên dưới.

## 8. Journey: triển khai khi thực sự cần custom template

### Tình huống và khoảng trống

Team cần gửi summary của calculation_action đã được phê duyệt tới một endpoint non-GA4 được phê duyệt cho workflow đo lường nội bộ. Built-in Google tag và GA4 Event tag gửi được tới GA4 nhưng không thể gửi tới endpoint này hoặc dùng request envelope đã được review. Vì vậy, Custom Tag Template là cần thiết cho destination riêng này.

Template không được tự tính kết quả FD. Application vẫn chịu trách nhiệm về solution_found và Data Layer contract; template chỉ map và gửi các scalar field đã được duyệt.

### Deployment record

> Đây là biểu mẫu governance do team tự chuẩn hóa, không phải biểu mẫu Google cung cấp. Các trường được tổng hợp từ template contract và production gate ở trên, Tag/Consent contract của Sections 04–05 và chuỗi evidence QA ở Section 08.

~~~text
Requirement:       một calculation_action summary → endpoint đã duyệt
Built-in gap:      GA4 tag không gửi được tới endpoint/envelope bắt buộc
Source:            Community Template đã review hoặc source do tổ chức sở hữu
Input allowlist:   calculation_action, calculation_type, solution_found
Consent:           cần analytics/measurement consent; denied hoặc unknown thì block
Endpoint:          HTTPS staging và production URL chính xác
Permission:        chỉ đọc field đã duyệt và gửi tới endpoint trong allowlist
Owner/version:     owner, source revision, ngày review và rollback export
~~~

### Các bước triển khai

1. Ghi requirement, destination, payload allowlist, expected count, consent rule, owner và environment.
2. Xác nhận built-in Tag, Variable hoặc cấu hình đơn giản không đáp ứng được requirement.
3. Ưu tiên Community Template đã review; nếu không có thì tạo template do tổ chức sở hữu và lưu source history.
4. Trong Template Editor, định nghĩa field rõ ràng, validation, safe default, sandbox code, endpoint permission chính xác và success/failure behavior.
5. Cấu hình Tag phụ thuộc với application Trigger authoritative và Additional Consent Check đã duyệt. Không dùng Trigger click hoặc DOM quá rộng.
6. Chạy unit test cho input hợp lệ, thiếu, sai và duplicate. Bổ sung test timeout, denied-consent và network failure khi template hỗ trợ các path này.
7. Test Tag phụ thuộc trong non-production container: GTM Preview → payload/request count → endpoint receipt.
8. Review source/version, permission, endpoint, data handling, consent, consumer inventory và evidence với owner.
9. Publish version có rollback/export record rồi theo dõi release production đầu tiên.

### Evidence chấp nhận

- Requirement và built-in gap được ghi nhận.
- Template chỉ gửi scalar field đã duyệt tới đúng endpoint của environment.
- Denied hoặc unknown consent ngăn request theo contract.
- Một calculation occurrence hợp lệ tạo một template execution và một request.
- Behavior với invalid, no-output, duplicate, timeout và network failure được ghi nhận và test.
- Lưu đủ GTM Preview, Network, endpoint receipt, owner approval, version và rollback evidence.

## Tài liệu tham khảo (References)

- [Google for Developers — Custom templates quick start guide](https://developers.google.com/tag-platform/tag-manager/templates)
- [Google for Developers — Sandboxed JavaScript](https://developers.google.com/tag-platform/tag-manager/templates/sandboxed-javascript)
- [Google for Developers — Custom template permissions](https://developers.google.com/tag-platform/tag-manager/templates/permissions)
- [Google for Developers — Custom template tests](https://developers.google.com/tag-platform/tag-manager/templates/tests)
