# 06 — Tài liệu Quản trị & Quản lý Template trong GTM

## Lý thuyết (Theory)

### GTM template là gì?

Template định nghĩa **giao diện cấu hình** và **phần triển khai chạy trong sandbox** cho một **tag type** hoặc **variable type** trong GTM. Template quyết định người dùng có thể cấu hình những field nào, các field được validation ra sao, phần implementation có thể thực hiện gì và cần những permission nào.

Khu vực Templates chứa các tag template và variable template tùy chỉnh hoặc được import trong container. Đây không phải là danh sách của tất cả tag hoặc variable đã được cấu hình.

### Template khác Tag và Variable như thế nào?

Template định nghĩa **cách một loại hoạt động**. Tag hoặc variable là một **instance đã được cấu hình** và được tạo từ template đó.

```text
Template: "Vendor Analytics Tag"
        ↓ định nghĩa
Các field cấu hình, validation, permission và sandboxed implementation
        ↓ tạo ra
Các tag instance
        ↓
Vendor Analytics — Purchase
Vendor Analytics — Sign Up
```

| Đối tượng    | Vai trò                                                                    | Ví dụ                              |
| ------------ | -------------------------------------------------------------------------- | ---------------------------------- |
| **Template** | Định nghĩa một tag type hoặc variable type có thể tái sử dụng              | Template gửi event đến vendor      |
| **Tag**      | Instance đã cấu hình, thực hiện một action khi đủ điều kiện chạy           | Gửi `purchase` đến vendor          |
| **Variable** | Instance đã cấu hình, trả về hoặc biến đổi một giá trị để nơi khác sử dụng | Chuẩn hóa giá trị product category |

Thay đổi một tag instance thông thường chỉ ảnh hưởng đến instance đó. Thay đổi template nền có thể ảnh hưởng đến mọi tag hoặc variable instance phụ thuộc vào template, vì vậy mọi thay đổi template đều cần phân tích dependency và regression test.

### Vì sao cần template?

Template cung cấp một cách có kiểm soát để đóng gói chức năng cho người dùng GTM. Template có thể:

- cung cấp giao diện cấu hình rõ ràng thay vì yêu cầu người dùng tự sửa implementation code;
- validation input trước khi tag hoặc variable được sử dụng;
- chạy implementation logic trong GTM sandboxed environment;
- khai báo các API và permission mà template cần;
- chuẩn hóa vendor integration và các pattern nội bộ;
- hỗ trợ review, testing, ownership, versioning và retirement.

Template giúp tăng tính nhất quán và giảm các rủi ro implementation có thể tránh được. Tuy nhiên, template không loại bỏ nhu cầu review về security, privacy, consent hoặc vận hành.

## Các loại Template (Template Types)

GTM custom template chủ yếu được dùng để định nghĩa hai loại sau:

| Loại                  | Mục đích                                                                           | Ví dụ                                               |
| --------------------- | ---------------------------------------------------------------------------------- | --------------------------------------------------- |
| **Tag Template**      | Định nghĩa một action mà tag instance có thể thực hiện                             | Gửi một event đã được phê duyệt đến vendor endpoint |
| **Variable Template** | Định nghĩa cách một variable instance tính toán, chuẩn hóa hoặc trả về một giá trị | Trả về campaign value đã được chuẩn hóa             |

Mental model hữu ích:

```text
Tag Template      → thực hiện một action
Variable Template → trả về một giá trị
```

Sự khác biệt này quan trọng trong quá trình review. Tag template phải mô tả execution, destination, consent và success/failure behavior. Variable template phải mô tả source của input, transformation rule, validation và giá trị trả về khi input bị thiếu hoặc không hợp lệ.

## Nguồn của Template (Template Sources)

| Nguồn                          | Ý nghĩa                                            | Yêu cầu quản trị                                                                                  |
| ------------------------------ | -------------------------------------------------- | ------------------------------------------------------------------------------------------------- |
| **Built-in**                   | Template hoặc GTM option được Google cung cấp      | Ưu tiên khi đáp ứng requirement; vẫn áp dụng các bước cấu hình và testing thông thường            |
| **Community Template Gallery** | Template do bên thứ ba phát hành thông qua Gallery | Review publisher, source, license, maintenance, code, permission, endpoint, consent và update     |
| **Custom**                     | Template do tổ chức tự phát triển và duy trì       | Cần owner, source history, security/privacy review, test, version control và lifecycle management |

### Thứ tự lựa chọn ưu tiên

Sử dụng phương án được hỗ trợ và an toàn nhất nhưng vẫn đáp ứng requirement đã được phê duyệt:

1. Google-provided built-in template đáp ứng requirement.
2. Community Template Gallery template đã được review cẩn thận.
3. Custom template có maintainer cụ thể, đã được code/security review và có test.
4. Custom HTML hoặc Custom JavaScript chỉ được dùng như một exception đã được ghi nhận, khi các lựa chọn được hỗ trợ và an toàn hơn không đáp ứng được requirement.

Lựa chọn cuối cùng là exception path, không phải shortcut để bỏ qua template governance.

## Cấu trúc của một Template (Anatomy of a Template)

Một template dùng trong production phải có thể được giải thích thông qua các thành phần dưới đây. Nếu một thành phần không áp dụng, hãy ghi rõ `None` hoặc `Not applicable` thay vì để hành vi của nó không rõ ràng.

| Thành phần                   | Câu hỏi cần trả lời                                                                                                  |
| ---------------------------- | -------------------------------------------------------------------------------------------------------------------- |
| **Purpose**                  | Vì sao template này tồn tại và nó đáp ứng business hoặc measurement requirement nào đã được phê duyệt?               |
| **Type**                     | Đây là tag template hay variable template?                                                                           |
| **Fields**                   | Người dùng cung cấp những cấu hình nào? Label, help text và default có rõ ràng không?                                |
| **Validation**               | Giá trị nào được chấp nhận, từ chối, bắt buộc hoặc chuẩn hóa?                                                        |
| **Sandboxed implementation** | Template code thực hiện những gì, theo từng bước?                                                                    |
| **APIs**                     | Template sử dụng những GTM sandboxed API nào và vì sao?                                                              |
| **Permissions**              | Implementation có thể truy cập resource hoặc capability nào?                                                         |
| **Endpoints**                | Tag có thể gửi data đến đâu? Destination có rõ ràng và được phê duyệt không?                                         |
| **Data handling**            | Template có thể đọc, biến đổi, lưu trữ hoặc gửi data nào? Data cá nhân bị cấm có được loại trừ không?                |
| **Consent**                  | Cần consent state nào và điều gì xảy ra khi consent bị denied, unknown hoặc thay đổi?                                |
| **Success/failure**          | Khi success, timeout, input không hợp lệ hoặc network failure thì tag xử lý thế nào? Variable trả về gì khi failure? |
| **Consumers**                | Những tag, variable, team, report hoặc downstream system nào phụ thuộc vào template?                                 |
| **Owner**                    | Ai maintain, approve change, trả lời câu hỏi và xác nhận retirement?                                                 |
| **Version**                  | Version imported, released hoặc source commit nào đang được phê duyệt?                                               |
| **Lifecycle**                | Template đang ở trạng thái Proposed, Under Review, Approved, Active, Deprecated hay Retired?                         |

```text
Template
├── Configuration UI và fields
├── Validation
├── Sandboxed implementation
├── APIs và permissions
├── Data, endpoints và consent behavior
├── Tests
└── Metadata về ownership, version và lifecycle
        ↓
Các tag hoặc variable instance phụ thuộc
```

## Sandboxing và Permissions

Custom template chạy trong GTM sandboxed JavaScript environment thay vì unrestricted page JavaScript. Sandbox giới hạn những gì template code có thể trực tiếp thực hiện. Quyền truy cập đến các capability nhạy cảm được cung cấp thông qua sandboxed API đã được phê duyệt và template permission.

Về mặt khái niệm:

```text
Template code
      ↓
Sandboxed API
      ↓
Permission check
      ↓
Resource hoặc action được cho phép
```

### Least privilege

> Template chỉ nên được cấp những permission cần thiết để thực hiện đúng purpose đã được mô tả.

Ví dụ, một template chỉ cần gửi request đã được phê duyệt đến `analytics.vendor.example` thì không nên yêu cầu thêm các capability không liên quan như storage, cookie, page hoặc network. Mỗi permission phải có business justification và technical justification rõ ràng.

### Nguyên tắc security quan trọng

```text
Sandboxed ≠ tự động an toàn
```

Sandbox làm giảm phạm vi truy cập của implementation, nhưng template vẫn có thể không an toàn hoặc không phù hợp nếu có permission quá rộng, gửi data đến endpoint không rõ ràng, xử lý data sai, có source độc hại hoặc không được maintain, hoặc tự thay đổi behavior mà không qua review. Vì vậy, Community Template phải được review như third-party code.

## Hướng dẫn quyết định về Template (Template Decision Guide)

Trước khi import, approve hoặc tạo template, hãy sử dụng production gate sau:

1. **Đã có requirement được phê duyệt chưa?**  
   Nếu chưa, không import hoặc build template.
2. **Built-in template có đáp ứng requirement không?**  
   Nếu có, sử dụng built-in option.
3. **Đã có Community Template được review chưa?**  
   Nếu có, evaluate template đó trước khi tự build custom code.
4. **Source có đáng tin cậy và còn được maintain không?**  
   Nếu không, reject hoặc tìm một alternative an toàn hơn.
5. **Tất cả permission được yêu cầu có cần thiết không?**  
   Nếu không, giảm permission hoặc reject template.
6. **Endpoint và data handling đã được hiểu và phê duyệt chưa?**  
   Nếu chưa, không approve.
7. **Consent behavior đã được định nghĩa chưa?**  
   Nếu chưa, phải định nghĩa trước khi dùng trong production.
8. **Đã biết các tag và variable phụ thuộc chưa?**  
   Nếu chưa, inventory consumers trước khi release hoặc update.
9. **Có thể test template và các consumer đại diện không?**  
   Nếu không, không publish.
10. **Đã có owner, version record và kế hoạch update/rollback chưa?**  
    Nếu chưa, phải phân công ownership và định nghĩa quy trình vận hành trước.

## Tiêu chuẩn thiết kế (Design Standards)

Mọi template được approve phải tuân theo các tiêu chuẩn sau:

- Bắt đầu bằng business hoặc measurement requirement đã được ghi nhận.
- Ưu tiên implementation được hỗ trợ và có mức quyền hạn thấp nhất nhưng vẫn đáp ứng requirement.
- Sử dụng field label, help text, safe default và behavior required/optional rõ ràng.
- Validation input ngay tại boundary của template; không chỉ dựa vào downstream system để reject giá trị sai.
- Giới hạn permission ở phạm vi hẹp và giải thích từng permission.
- Làm rõ endpoint, phân biệt environment và đảm bảo destination đã được phê duyệt.
- Mô tả data nào được đọc, biến đổi, lưu trữ và gửi đi.
- Định nghĩa consent behavior trước khi implementation được active trong production.
- Định nghĩa behavior rõ ràng cho missing input, invalid input, denied consent, duplicate, timeout và network failure.
- Không đặt secret trong template code, field, example hoặc configuration.
- Không approve việc thu thập hoặc truyền prohibited personal data.
- Lưu trữ source, version, change history, test, owner và dependent consumer ở nơi có thể tra cứu.
- Mọi update phải có thể review và rollback.

## Ví dụ thực tế — Đánh giá một Community Tag Template

### Tình huống

Team cần gửi event `purchase_completed` đến **Vendor X Analytics** khi một purchase đã hoàn tất. GTM không có built-in tag hỗ trợ request format bắt buộc của Vendor X.

### Quy trình đánh giá

```text
Requirement:
Gửi một purchase_completed event đã được phê duyệt đến Vendor X
        ↓
Built-in template có sẵn không?
Không
        ↓
Community Template Gallery có template phù hợp không?
Có — Vendor X Analytics Tag
        ↓
Review publisher, repository, license, maintenance và documentation
        ↓
Review field, code, permission, endpoint, data handling và consent
        ↓
Import vào non-production workspace
        ↓
Test normal, invalid, duplicate, denied-consent và network-failure case
        ↓
Approve đúng version và ghi nhận consumer, owner cùng rollback plan
```

### Review record cụ thể

| Khu vực review   | Evidence hoặc quyết định                                                                                                                  |
| ---------------- | ----------------------------------------------------------------------------------------------------------------------------------------- |
| Purpose          | Gửi một `purchase_completed` event sau khi application xác nhận purchase hoàn tất                                                         |
| Type             | Tag Template                                                                                                                              |
| Data input       | `transaction_id`, `currency`, `value` và item summary đã được phê duyệt từ data layer                                                     |
| Trigger contract | Authoritative `purchase_completed` event; không dùng generic button click                                                                 |
| Endpoint         | `https://collect.vendor-x.example/events`; production và staging destination được tách biệt rõ ràng                                       |
| Permission       | Chỉ dùng sandboxed capability cần thiết để đọc approved value và gửi request                                                              |
| Consent          | Chỉ gửi khi đáp ứng analytics/marketing consent requirement đã được phê duyệt; denied hoặc unresolved consent sẽ ngăn request             |
| Success/failure  | Xử lý theo success path đã được mô tả; không retry theo cách tạo duplicate purchase nếu retry behavior chưa được thiết kế và test rõ ràng |
| Consumer         | Tag `Vendor X — Purchase Completed` và downstream Vendor X conversion workflow                                                            |
| Decision         | Chỉ approve version đã review; reject nếu permission rộng hơn mức cần thiết hoặc maintenance không rõ ràng                                |

## Inventory & Ownership

Hãy duy trì một inventory tập trung cho mọi non-built-in template và các dependent instance của chúng.

| Template | Type           | Purpose              | Origin/source      | Version         | Permissions | Endpoints                 | Consent               | Consumers          | Owner           | Last review  | Status | Replacement             |
| -------- | -------------- | -------------------- | ------------------ | --------------- | ----------- | ------------------------- | --------------------- | ------------------ | --------------- | ------------ | ------ | ----------------------- |
| `[name]` | Tag / Variable | `[approved purpose]` | Gallery / `[repo]` | `[SHA/version]` | `[summary]` | `[approved destinations]` | `[required behavior]` | `[tags/variables]` | `[team/person]` | `YYYY-MM-DD` | Active | `[replacement or None]` |

Ownership không chỉ là người đã import template. Owner chịu trách nhiệm về maintenance, review cadence, consumer impact analysis, quyết định update, incident response và retirement. Template không có owner cụ thể không nên được active trong production.

## Quy trình Test (Test Workflow)

1. Chỉ import hoặc edit template trong dedicated non-production workspace.
2. Review field, validation, implementation, API, permission, endpoint, data handling, consent và test trước khi tạo consumer.
3. Test trực tiếp template khi được hỗ trợ, bao gồm normal, missing, invalid, denied-consent và failure case.
4. Test các tag hoặc variable phụ thuộc đại diện trong GTM Preview.
5. Kiểm tra network behavior: destination, payload, request count và environment.
6. Test negative case và duplicate case để bảo đảm một business occurrence không tạo ra duplicate request ngoài dự kiến.
7. Lưu evidence, defect, approved version, owner và rollback/export information.
8. Chỉ publish thông qua version control thông thường sau khi được review và approve.

## Ảnh hưởng của Template Update (Template Update Impact)

Template update là một code change và dependency change, không chỉ là thay đổi metadata. Một template có thể được nhiều tag hoặc variable instance sử dụng:

```text
Community Template v1
        ↓
Tag A   Tag B   Tag C

Template được update lên v2
        ↓
Behavior của A, B và C đều có thể thay đổi
```

Update có thể ảnh hưởng đến mọi dependent instance thông qua thay đổi về implementation code, field, default, validation, permission, endpoint, data handling, consent behavior hoặc success/failure behavior. Trước khi accept update:

1. Xác định tất cả dependent tag và variable.
2. Review change detail, source diff và exact version.
3. Review permission và endpoint được thêm, xóa hoặc thay đổi.
4. Review field, default, validation và consent behavior.
5. Retest các consumer đại diện, bao gồm consumer quan trọng và có volume cao.
6. Ghi nhận approved version và rollback path.
7. Chỉ publish update sau khi đạt cùng tiêu chuẩn approval như một code change mới.

Hãy coi Gallery update là một code change mới, ngay cả khi update được cung cấp dưới dạng automatic hoặc routine update.

## Các Anti-pattern Thường gặp (Common Anti-patterns)

| Anti-pattern                                  | Vấn đề                                                              | Cách tiếp cận nên dùng                                                       |
| --------------------------------------------- | ------------------------------------------------------------------- | ---------------------------------------------------------------------------- |
| Import Gallery template mà không review       | Code và permission của bên thứ ba được tin tưởng một cách ngầm định | Review publisher, source, permission, endpoint, data, consent và maintenance |
| Dùng custom code khi đã có supported template | Tạo thêm implementation và maintenance risk không cần thiết         | Tuân theo preferred selection order                                          |
| Permission quá rộng                           | Template có thể truy cập nhiều hơn purpose yêu cầu                  | Áp dụng least privilege và justify từng permission                           |
| Network endpoint không rõ                     | Không thể quản trị hoặc validation destination của data             | Ghi nhận và approve exact endpoint cùng environment                          |
| Automatic update không review                 | Dependent instance có thể thay đổi behavior bất ngờ                 | Coi mỗi update là một code change mới                                        |
| Không có consumer inventory                   | Update hoặc retirement có thể làm hỏng tag/variable phụ thuộc       | Inventory consumer trước khi approve, update hoặc remove                     |
| Không có owner                                | Quyết định về security, maintenance và retirement bị bỏ ngỏ         | Chỉ định named accountable owner                                             |
| Chỉnh sửa trực tiếp trong production          | Khó test, review và rollback hơn                                    | Dùng non-production workspace và version control thông thường                |
| Đặt secret trong template code hoặc field     | Credential có thể bị lộ và khó rotate an toàn                       | Không đặt secret; dùng server-side hoặc platform mechanism đã được approve   |
| Đọc DOM để bù cho data contract còn thiếu     | UI change có thể âm thầm làm hỏng measurement                       | Ưu tiên authoritative data-layer contract                                    |
| Xóa template trước khi migrate consumer       | Tag hoặc variable phụ thuộc có thể fail hoặc trở nên unmanaged      | Migrate consumer trước và lưu rollback record                                |
