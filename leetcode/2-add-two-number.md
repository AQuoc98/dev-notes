# LeetCode 2 — Add Two Numbers

> 💡 Hãy thử **tự suy nghĩ** qua phần "Tư duy giải bài" trước khi nhảy xuống code. Bạn sẽ học được nhiều hơn rất nhiều!

---

## 1. Phân tích đề bài

### Đề bài (dịch & diễn giải)

Cho 2 list liên kết `l1` và `l2`, mỗi list biểu diễn 1 số không âm theo cách **chữ số đảo ngược** (đầu là chữ số hàng đơn vị). Cộng 2 số này lại và trả về result dưới dạng list liên kết mới.

- Mỗi node chứa đúng 1 chữ số (0-9).
- Các list **không rỗng**.
- Có thể có **chữ số carry > 0** khi cộng xong cuối cùng → cần thêm node mới là ` ListNode(1)`.

### Ví dụ trực quan

```
l1 = [2, 4, 3]   (342 đảo ngược)
l2 = [5, 6, 4]   (465 đảo ngược)
         ↑
      342 + 465 = 807 → trả về [8, 0, 7]

l1 = [9, 9, 9]   (999)
l2 = [1]          (1)
         ↑
      999 + 1 = 1000 → trả về [1, 0, 0, 0]
```

### Input / Output / Constraints

| Mục | Chi tiết |
| --- | --- |
| **Input** | `l1: ListNode`, `l2: ListNode` (đầu của mỗi list) |
| **Output** | `ListNode` — đầu của list kết quả |
| **Constraints** | Số nút mỗi list từ 1 đến 100; giá trị node `[0-9]`; số không rỗng |

### Edge cases cần lưu ý

- **List có độ dài khác nhau**: List dài hơn sẽ tiếp tục chạy sau khi list ngắn hết, vẫn phải tính carry nếu còn.
- **Còn carry cuối cùng**: Sau khi 2 list đều hết node nhưng `carry > 0` → tạo thêm node `1`.
- **Số bằng 0**: Khi input có giá trị 0 (`[0]`) vẫn là hợp lệ và không làm thay đổi kết quả.

---

## 2. Tư duy giải bài

### 🤔 Câu hỏi gợi ý để bạn tự suy nghĩ

1. Vì số được lưu **đảo ngược**, vậy chúng ta cần làm gì? — Cộng từ **chữ số hàng đơn vị** (đầu list) → đó là cách list này được thiết kế, không phải cần reverse lại.
2. Khi nào xuất hiện `carry`? Khi tổng 2 chữ số ≥ 10 → carry = sum // 10.
3. Khi list có độ dài khác nhau làm sao xử lý? Dùng pointer null để kiểm tra và chỉ dùng node còn tồn tại.
4. After khi cả 2 list đều hết nút nhưng `carry > 0`, chúng ta có thể tạo thêm một nút mới với giá trị `1`.

### Pattern nhận diện

- **Linked List Traversal (Single Pass)**: Bài toán yêu cầu duyệt qua 2 lists song song, cộng từng cặp số. Đây là pattern **"Two Pointers on Linked Lists"** — chỉ cần 1 pass duy nhất.
- **Carry Logic**: Giống như bài cộng thủ công, mỗi bước kiểm tra tổng ≥ 10 → carry = 1 nếu có, hoặc 0 nếu không có.

### Hành trình tư duy: brute force → tối ưu

1. **Brute force (số học)**: Chuyển tất cả số về dạng number bình thường bằng cách parse lại từ list liên kết, rồi cộng 2 số lại và convert back thành list liên kết mới. Độ phức tạp $O(n+m)$.
2. **Linked List Traversal**: Duyệt song song 2 danh sách, chỉ cần **1 lần pass duy nhất** → đó là solution tối ưu nhất.

---

## 3. Ba solutions khả thi

### ✅ Solution 1: Linked List Traversal (Tối ưu ⭐)

#### Ý tưởng cốt lõi

Duyệt qua cả 2 list `l1`, `l2` **song song**, giống như cộng số học theo từng chữ số:

- Lấy chữ số đầu tiên của mỗi list, cộng lại + thêm carry từ bước trước.
- Tạo node mới cho kết quả và lưu lại trong `dummy`.
- Tiếp tục đến khi 1 hoặc cả 2 list hết nút → nhưng vẫn phải kiểm tra `carry > 0`.

#### Các bước chi tiết

1. Tạo dummy head `dummy = new ListNode(0)` để làm đầu chóa. Dùng `current` trỏ vào `dummy` (pointer đang gắn ở danh sách kết quả).
2. Khởi tạo `carry = 0`.
3. Duyệt khi **cả 2 list đều có node** (` l1 ≠ null && l2 !== null`) hoặc `carry > 0`:
   - Nếu `l1` còn thì lấy giá trị tại đó, nếu `l1 === null` → coi là 0. Do tương tự cho `l2`.
   - Tính tổng: `sum = val1 + val2 + carry`.
   - Lấy chữ số kết quả: `digit = sum % 10`.
   - Tạo node mới với giá trị đó và gán vào danh sách.
   - Cập nhật `carry = Math.floor(sum / 10)`.
   - Chuyển `l1`, `l2` đến node tiếp theo (nếu còn).
4. Đầu kết quả là `dummy.next` (bỏ dummy đầu).

#### Code

```js
class ListNode {
    val;
    next;
    
    constructor(val, next = null) {
        this.val = val;
        this.next = next;
     }
}

function addTwoNumbers(l1: ListNode, l2: ListNode): ListNode {
    let dummy = new ListNode(0);
    let current = dummy;
    let carry = 0;

    while (l1 || l2 || carry > 0) {
        const val1 = l1 ? l1.val : 0;
        const val2 = l2 ? l2.val : 0;

        const sum = val1 + val2 + carry;
        const digit = sum % 10;
        carry = Math.floor(sum / 10);

        current.next = new ListNode(digit);
        current = current.next;

        if (l1) l1 = l1.next;
        if (l2) l2 = l2.next;
    }

    return dummy.next;
}
```

#### Trace ví dụ: `l1 = [2,4,3]` → 342, `l2 = [5,6,4]` → 465 (342 + 465 = 807)

| Step | l1 | l2 | carry | val1 | val2 | sum | digit | result |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 0 | 2 | 5 | 0 | 2 | 5 | 7 | 7 | [7] |
| 1 | 4 | 6 | 0 | 4 | 6 | 10 | 0 | [7,0] |
| 2 | 3 | 4 | 1 | 3 | 4 | 8 | 8 | [7,0,8] |

→ Trá về `[8,0,7]`. ✅

#### Complexity

- **Time:** $O(\max(m, n))$ — duyệt qua tất cả nút của list dài hơn.
- **Space:** $O(1)$ — chỉ dùng 3 biến phụ (dummy, current, carry). Không tính node mới tạo ra result.

--- 

#### ✅ Ưu / Nhược điểm

- ✅ Đơn giản, logic giống cộng số học thủ công.
- ✅ Tối ưu nhất: 1 pass duy nhất, không cần sort hay extra space.
- ❌ Code hơi dài vì phải xử lý 3 case (l1 null, l2 null, cả hai đều có).
  
#### Khi nào dùng

Là **đáp án chuẩn** cho bài Add Two Numbers. Đây là pattern **"Linked List addition"** — duyệt song song + carry logic.

---

## 4. Phân tích Big O chi tiết

### Time Complexity: $O(\max(m, n))$

- Giả sử `l1` dài là `m` node và `l2` dài là `n` node.
- Mỗi vòng lặp của `while` tiến trình **ít nhất 1 nút** (vì chỉ dùng l1 || l2 || carry > 0).
- Vậy tối đa chạy số bước bằng độ dài của list dài hơn → $O(\max(m, n))$.

### Space Complexity: $O(1)$ (không tính result)

- Chỉ dùng:
  - `dummy`: ListNode đầu chóa.
  - `current`: trỏ vào node đang xây dựng kết quả.
  - `carry`: carry từ bước trước.
- Không có mảng, set, map phụ trợ → $O(1)$ auxiliary space.
- Nếu tính memory cho output (linked list mới tạo ra) thì là $O(\max(m,n))$ để lưu result — nhưng đó là **output storage**, not algorithmic complexity.

---

## 5. Góc nhìn giảng dạy

### Khái niệm cần nắm

- **Linked List Traversal Pattern**: Khi có 2 list song song, thường dùng 2 pointers duyệt cùng lúc (song song), không cần backtracking hay extra structure.
- **Carry Logic**: Giống như addition thủ công trong primary school — khi total ≥ 10, carry = 1. Đây là pattern xuất hiện nhiều bài liên quan đến số học trên các cấu trúc dữ liệu rời rạc.

### Lỗi tư duy phổ biến

1. **Quên case `l1 === null` hoặc `l2 === null`**: Sau khi list ngắn hơn hết node, code vẫn cần tiếp tục xử lý phần còn lại của list dài hơn.
2. **Quên carry cuối cùng**: Sau khi cả 2 list đều hết node nhưng `carry = 1`, phải tạo thêm một node mới. Đây là lỗi hay gặp nhất.
3. **Sai điều kiện vòng lặp**: Dùng `while (l1 && l2)` thay vì `while (l1 || l2 || carry > 0)`. Điều này khiến kết quả bị thiếu khi có carry cuối cùng hoặc độ dài khác nhau giữa 2 list.

### Cách nhận biết bài tương tự trong tương lai

Khi gặp các bài về **"Addition on Data Structures"** với điều kiện:
- List biểu diễn số (reverse order): Linked List Addition
- Array với prefix sum hoặc tổng phần tử con: Prefix Sum + HashMap hoặc Sliding Window
- Large integer không thể lưu vào number: BigInt hay phân tích chữ số từng phần

---

## 6. Kết luận

### Solution tối ưu nhất: **Linked List Traversal (Solution 1)** ⭐

**Vì sao?**

- Time $O(\max(m,n))$ — duyệt qua tất cả nút cần thiết, không bỏ sót.
- Space $O(1)$ (không tính output) — auxiliary space tối thiểu.
- Logic đơn giản, dễ hiểu và mở rộng được cho biến thể như subtraction, multiplication (khó hơn).

### Bài LeetCode tương tự nên luyện

| # | Tên | Pattern liên quan |
| --- | --- | --- |
| **460** | Lexicographical Numbers | Binary Tree Traversal + String comparison |
| **21** | Merge Two Sorted Lists | Merge logic on 2 sorted lists (kèm carry nếu có số học) |
| **19** | Remove Nth Node From End of List | Linked list traversal + count pass |
| **2** | Add Two Numbers (Reverse order, với Carry ⭐) | Single pass traversal |
| **23** | Merge k Sorted Lists | Min-Heap + Merge pattern |
| **146** | LRU Cache | Doubly linked list + HashMap (thời gian không gian trade-off) |

---

## 7. Bonus: Biến thể thường gặp

### Add Two Numbers II — Reverse Order (Normal instead of Reverse)

Một số biến thể sẽ thay đổi cách lưu số từ **reverse** → **normal order**, đòi hỏi cần reverse lại list sau khi cộng xong.

---

### Add Two Numbers Reverse — Input Arrays are Already Sorted

Nếu có thêm điều kiện input là sorted list, vẫn dùng pattern này nhưng phải reverse ra đầu tiên rồi mới làm như trên.
