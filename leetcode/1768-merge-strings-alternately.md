# LeetCode 1768 — Merge Strings Alternately

> 💡 Trước khi đọc code, hãy thử **tự suy nghĩ** qua từng phần phân tích bên dưới. Bạn sẽ học được nhiều hơn rất nhiều so với việc nhảy thẳng vào lời giải.

---

## 1. Phân tích đề bài

### Đề bài (dịch & diễn giải)

Cho hai chuỗi `word1` và `word2`. Hãy **trộn (merge)** hai chuỗi này bằng cách **lần lượt lấy 1 ký tự từ `word1`, rồi 1 ký tự từ `word2`**, bắt đầu từ `word1`. Nếu một trong hai chuỗi dài hơn, **nối phần còn lại** vào cuối kết quả.

### Ví dụ trực quan

```
word1 = "abc",  word2 = "pqr"   → "apbqcr"
word1 = "ab",   word2 = "pqrs"  → "apbqrs"   (phần "rs" thừa của word2 được nối ở cuối)
word1 = "abcd", word2 = "pq"    → "apbqcd"   (phần "cd" thừa của word1 được nối ở cuối)
```

### Input / Output / Constraints

| Mục | Chi tiết |
| --- | --- |
| **Input** | `word1: string`, `word2: string` |
| **Output** | `string` — chuỗi đã merge |
| **Constraints** | $1 \le |word1|, |word2| \le 100$, chỉ chứa chữ thường |

### Edge cases cần lưu ý

- Hai chuỗi **bằng độ dài** → merge "đẹp" không dư.
- `word1` **dài hơn** `word2` → phần dư của `word1` ở cuối.
- `word2` **dài hơn** `word1` → phần dư của `word2` ở cuối.
- Chuỗi **độ dài chênh lệch lớn** (ví dụ 1 vs 100) → vẫn phải xử lý phần dư đúng.
- Theo constraint, **không có chuỗi rỗng**, nhưng code nên vẫn an toàn nếu có.

---

## 2. Tư duy giải bài

### 🤔 Câu hỏi gợi ý để bạn tự suy nghĩ

1. Khi merge, ta đang **đi qua cả hai chuỗi cùng lúc**. Điều đó gợi ý cấu trúc nào?
2. Nếu hai chuỗi **khác độ dài**, vấn đề "ai dài hơn" giải quyết thế nào?
3. Trong JS, **string concatenation** trong vòng lặp có hiệu quả không? Có nên dùng `Array` rồi `.join()` không?

### Pattern nhận diện

Đây là dạng **Two Pointers** kinh điển:

- Một pointer `i` chạy trên `word1`.
- Một pointer `j` chạy trên `word2`.
- Vòng lặp dừng khi **một trong hai** (hoặc cả hai) hết ký tự.

Đây cũng là biến thể của pattern **"merge two sequences"** (giống bước merge trong Merge Sort, hoặc merge two sorted lists).

### Hành trình tư duy: brute force → tối ưu

1. **Brute force:** Chuyển 2 chuỗi thành mảng, nối từng phần tử bằng vòng lặp `for` với index — vẫn $O(n+m)$ nhưng dùng nhiều bộ nhớ tạm.
2. **Tối ưu:** Dùng **two pointers** + mảng kết quả (rồi `.join('')`) → tránh tạo string mới mỗi lần concat (string trong JS là immutable).
3. **Elegant:** Lặp tới `max(len1, len2)` và kiểm tra biên trong mỗi vòng — code gọn hơn.

---

## 3. Ba solutions khả thi

### ✅ Solution 1: Two Pointers (kinh điển)

#### Ý tưởng cốt lõi

Dùng 2 con trỏ `i`, `j` chạy song song trên `word1` và `word2`. Mỗi vòng lấy 1 ký tự từ mỗi chuỗi, sau khi vòng lặp kết thúc thì nối phần dư.

#### Các bước chi tiết

1. Khởi tạo mảng `result = []`, `i = 0`, `j = 0`.
2. Lặp khi `i < word1.length` **VÀ** `j < word2.length`:
   - Push `word1[i++]` vào `result`.
   - Push `word2[j++]` vào `result`.
3. Nếu còn dư ở `word1` → push toàn bộ `word1.slice(i)`.
4. Nếu còn dư ở `word2` → push toàn bộ `word2.slice(j)`.
5. Trả về `result.join('')`.

#### Trace ví dụ: `word1="abcd"`, `word2="pq"`

| Vòng | `i` | `j` | Push | `result` |
| --- | --- | --- | --- | --- |
| 1 | 0→1 | 0→1 | `a`, `p` | `['a','p']` |
| 2 | 1→2 | 1→2 | `b`, `q` | `['a','p','b','q']` |
| Sau loop | 2 | 2 (hết) | slice `word1.slice(2)="cd"` | `['a','p','b','q','cd']` |
| `.join('')` | | | | **`"apbqcd"`** ✅ |

#### Code

```js
/**
 * @param {string} word1
 * @param {string} word2
 * @return {string}
 */
function mergeAlternately(word1, word2) {
  const result = [];
  let i = 0;
  let j = 0;

  // Lấy luân phiên 1 ký tự mỗi chuỗi cho tới khi 1 chuỗi cạn
  while (i < word1.length && j < word2.length) {
    result.push(word1[i++]);
    result.push(word2[j++]);
  }

  // Nối phần dư (slice trả về '' nếu i/j đã ở cuối → an toàn)
  if (i < word1.length) result.push(word1.slice(i));
  if (j < word2.length) result.push(word2.slice(j));

  // join('') gộp mảng thành chuỗi 1 lần duy nhất → tránh chi phí concat lặp lại
  return result.join('');
}
```

#### Complexity

- **Time:** $O(n + m)$ với $n = |word1|, m = |word2|$.
- **Space:** $O(n + m)$ cho mảng `result`.

#### Ưu / Nhược

- ✅ Rõ ràng, dễ debug; tách phần "merge song song" và "xử lý dư" rạch ròi.
- ❌ Hơi dài; có 2 nhánh `if` sau vòng lặp.

#### Khi nào dùng

- Khi bạn muốn code **đọc gần với mô tả đề bài** — rất hợp khi giải thích cho người khác.

---

### ✅ Solution 2: Single Loop tới `max(len1, len2)`

#### Ý tưởng cốt lõi

Thay vì 2 nhánh tách rời, lặp **một lần** từ `0` đến `max(len1, len2) - 1`. Trong mỗi vòng, kiểm tra biên trước khi push.

#### Các bước chi tiết

1. Tính `n = max(word1.length, word2.length)`.
2. Lặp `k` từ `0` đến `n - 1`:
   - Nếu `k < word1.length` → push `word1[k]`.
   - Nếu `k < word2.length` → push `word2[k]`.
3. `result.join('')`.

#### Trace ví dụ: `word1="ab"`, `word2="pqrs"` (n = 4)

| k | `k < |w1|`? | push | `k < |w2|`? | push | result |
| - | --- | --- | --- | --- | --- |
| 0 | ✓ | `a` | ✓ | `p` | `['a','p']` |
| 1 | ✓ | `b` | ✓ | `q` | `['a','p','b','q']` |
| 2 | ✗ | — | ✓ | `r` | `['a','p','b','q','r']` |
| 3 | ✗ | — | ✓ | `s` | `['a','p','b','q','r','s']` |

→ `"apbqrs"` ✅

#### Code

```js
function mergeAlternately(word1, word2) {
  const n = Math.max(word1.length, word2.length);
  const result = [];

  // Một vòng lặp duy nhất; điều kiện biên xử lý trong mỗi bước
  for (let k = 0; k < n; k++) {
    if (k < word1.length) result.push(word1[k]); // ký tự thứ k của word1 (nếu còn)
    if (k < word2.length) result.push(word2[k]); // ký tự thứ k của word2 (nếu còn)
  }

  return result.join('');
}
```

#### Complexity

- **Time:** $O(n + m)$ — vòng lặp chạy $\max(n, m)$ vòng, mỗi vòng O(1).
- **Space:** $O(n + m)$.

#### Ưu / Nhược

- ✅ Code **ngắn gọn**, không cần xử lý phần dư riêng biệt.
- ❌ Mỗi vòng có thêm 2 phép so sánh biên — vi mô chậm hơn Solution 1 một chút (không đáng kể).

#### Khi nào dùng

- Khi bạn muốn **code ngắn, ít chia nhánh**, thích hợp cho phỏng vấn nhanh.

---

### ✅ Solution 3: Recursion (đệ quy)

#### Ý tưởng cốt lõi

Định nghĩa hàm đệ quy: "merge bắt đầu từ index `i` của `word1` và `j` của `word2`".

- Base case: nếu `word1` cạn → trả về `word2.slice(j)`; nếu `word2` cạn → trả về `word1.slice(i)`.
- Recursive case: `word1[i] + word2[j] + merge(i+1, j+1)`.

#### Các bước chi tiết

1. Gọi `helper(0, 0)`.
2. Trong `helper(i, j)`:
   - Nếu `i >= word1.length` → return `word2.slice(j)`.
   - Nếu `j >= word2.length` → return `word1.slice(i)`.
   - Return `word1[i] + word2[j] + helper(i+1, j+1)`.

#### Trace ví dụ: `word1="ab"`, `word2="pq"`

```
helper(0,0) = "a" + "p" + helper(1,1)
helper(1,1) = "b" + "q" + helper(2,2)
helper(2,2) = word1 cạn → return word2.slice(2) = ""
→ "ab" + "pq"... gộp lại = "apbq"
```

#### Code

```js
function mergeAlternately(word1, word2) {
  // Hàm đệ quy lấy ký tự tại vị trí i, j rồi đệ quy phần còn lại
  function helper(i, j) {
    if (i >= word1.length) return word2.slice(j); // word1 hết → nối phần dư word2
    if (j >= word2.length) return word1.slice(i); // word2 hết → nối phần dư word1
    return word1[i] + word2[j] + helper(i + 1, j + 1);
  }

  return helper(0, 0);
}
```

#### Complexity

- **Time:** $O(n + m)$ — mỗi bước đệ quy O(1), tổng có $\min(n,m)$ bước trước khi hit base case.
- **Space:** $O(n + m)$ — **call stack** sâu tới $\min(n, m)$ + chuỗi kết quả + concat strings tạo nhiều object trung gian (xem mục 5).

#### Ưu / Nhược

- ✅ Cách diễn đạt **functional**, đẹp về mặt khái niệm.
- ❌ Risk **stack overflow** nếu chuỗi quá dài (ở đây constraint ≤ 100 nên an toàn).
- ❌ String concat `word1[i] + word2[j] + ...` tạo nhiều string trung gian → chậm hơn iterative trong thực tế.

#### Khi nào dùng

- Khi muốn **luyện tư duy đệ quy** hoặc đề bài có cấu trúc phân rã tự nhiên (như cây). **Không khuyến nghị** cho production với chuỗi lớn.

---

## 4. Bảng so sánh các solutions

| Approach | Time | Space | Độ khó triển khai | Ưu điểm | Nhược điểm |
| -------- | ---- | ----- | ----------------- | ------- | ---------- |
| **1. Two Pointers** | $O(n+m)$ | $O(n+m)$ | Dễ | Rõ ràng, gần với mô tả đề | Code dài hơn (2 nhánh xử lý dư) |
| **2. Single Loop max** | $O(n+m)$ | $O(n+m)$ | Dễ | Ngắn, gọn, không cần slice dư | Có thêm so sánh biên mỗi vòng |
| **3. Recursion** | $O(n+m)$ | $O(n+m)$ + call stack $O(\min(n,m))$ | Trung bình | Functional, đẹp khái niệm | Stack overflow nếu input lớn; concat string chậm |

---

## 5. Phân tích Big O chi tiết

### Time Complexity — vì sao là $O(n + m)$?

- **Solution 1:** Vòng `while` chạy $\min(n, m)$ vòng, mỗi vòng làm 2 thao tác push → $O(1)$. Sau đó `slice` trên phần dư mất $O(|dư|) = O(\max(n,m) - \min(n,m))$. Cuối cùng `join('')` duyệt toàn bộ mảng kết quả → $O(n + m)$.

$$T = \min(n,m) \cdot O(1) + O(|\text{dư}|) + O(n+m) = O(n + m)$$

- **Solution 2:** Vòng `for` chạy $\max(n, m)$ lần, mỗi lần 2 phép so sánh + tối đa 2 push → $O(1)$ mỗi vòng. `join` cũng $O(n+m)$ → tổng $O(n + m)$.

- **Solution 3:** Mỗi lần gọi đệ quy giảm cả `i` và `j` đi 1 → tối đa $\min(n,m)$ lần gọi đệ quy + 1 lần `slice` ở base case. Tuy nhiên, **chuỗi concat** `a + b + helper(...)` mỗi bước tạo string mới có độ dài tăng dần. Nếu tính khắt khe, concat trong loop kiểu này thực tế có thể lên tới $O((n+m)^2)$ vì mỗi concat sao chép cả chuỗi cũ. Trong JS engines hiện đại có optimization (rope strings) nên thường vẫn gần $O(n+m)$, nhưng **không đảm bảo**.

### Space Complexity

- **Mảng `result`** lưu $n + m$ ký tự → $O(n + m)$.
- **Chuỗi output cuối** cũng $O(n + m)$ — không tránh được vì đề yêu cầu trả về chuỗi.
- **Solution 3:** thêm **call stack** sâu $\min(n, m)$ → $O(\min(n, m))$ phụ.

### 🔍 Tại sao dùng `Array + join` thay vì `+=`?

Trong JS, **string immutable** → `result += char` thực tế tạo string mới mỗi lần, chi phí có thể đẩy tổng lên $O((n+m)^2)$ trong trường hợp xấu. Dùng `Array.push` + `join('')` đảm bảo $O(n+m)$ vững chắc.

---

## 6. Góc nhìn giảng dạy

### Khái niệm cần nắm

- **Two Pointers** không nhất thiết phải trên *cùng* một mảng — có thể là 2 con trỏ trên **2 mảng/chuỗi khác nhau** (như bài này hoặc bước merge của Merge Sort).
- **Immutable strings**: trong JS, Java, Python, mỗi phép `+=` trên string tạo object mới. Đây là lỗi hiệu năng phổ biến của người mới.
- **Invariant** trong vòng lặp: ở mỗi bước, mảng `result` chứa đúng `i + j` ký tự đầu tiên đã merge. Giữ invariant đúng giúp bạn debug nhanh.

### Lỗi tư duy phổ biến

1. **Quên xử lý phần dư** khi một chuỗi cạn trước → output thiếu ký tự. Đây là bug số 1.
2. **Off-by-one** ở điều kiện `while`: dùng `||` thay vì `&&` → truy cập `undefined`. Hãy nhớ: chúng ta cần **cả hai** còn ký tự để alternate; khi một bên cạn, chuyển sang xử lý dư.
3. **Dùng `+=` với string trong loop** → chấp nhận được trên LeetCode (vì $n \le 100$) nhưng là *thói quen xấu* cho input lớn.
4. **Quên `i++`, `j++`** dẫn đến vòng lặp vô hạn.

### Cách nhận biết bài tương tự trong tương lai

Bài có những "signal" này thường giải bằng **two pointers / merge pattern**:

- "Merge / interleave / combine **two** sorted sequences"
- "Process **two arrays/strings** simultaneously"
- "Alternate between..." / "round-robin..."
- Đề có *2 input cùng cấu trúc* và phải tạo *1 output kết hợp*.

---

## 7. Kết luận

### Solution tối ưu nhất: **Solution 2 (Single Loop max)**

**Vì sao?**

- Time/Space đều **tối ưu** $O(n+m)$ — không thể tốt hơn vì phải đọc hết cả 2 chuỗi.
- Code **ngắn nhất, ít nhánh nhất** → ít chỗ sai, dễ review.
- Không cần `slice` phần dư → ít chi phí ẩn.

**Solution 1** cũng rất tốt, đặc biệt khi bạn muốn code đọc *đúng như mô tả đề bài* — phù hợp cho phỏng vấn để giải thích từng bước.

**Solution 3** chỉ nên dùng khi luyện đệ quy, không phải lựa chọn sản xuất.

### Bài LeetCode tương tự nên luyện

| # | Tên | Pattern liên quan |
| --- | --- | --- |
| **88** | Merge Sorted Array | Two pointers merge (in-place) |
| **21** | Merge Two Sorted Lists | Two pointers trên linked list |
| **283** | Move Zeroes | Two pointers trên 1 mảng |
| **344** | Reverse String | Two pointers đối nghịch |
| **567** | Permutation in String | Two pointers + sliding window |
| **2562** | Find the Array Concatenation Value | Two pointers từ 2 đầu mảng |

Sau khi luyện xong nhóm trên, bạn sẽ **phản xạ ngay** với pattern "duyệt song song 2 sequence". 🚀
