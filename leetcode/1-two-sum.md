# LeetCode 1 — Two Sum

> 💡 Hãy thử **tự suy nghĩ** qua phần "Tư duy giải bài" trước khi nhảy xuống code. Bạn sẽ học được nhiều hơn rất nhiều!

---

## 1. Phân tích đề bài

### Đề bài (dịch & diễn giải)

Cho một mảng số nguyên `nums` và một số nguyên `target`. Tìm **2 chỉ số** `i` và `j` sao cho `nums[i] + nums[j] === target`.

- Mỗi input có **đúng 1 lời giải**.
- **Không được dùng cùng 1 phần tử 2 lần** (tức `i !== j`).
- Có thể trả về kết quả theo **thứ tự bất kỳ**.

### Ví dụ trực quan

```
nums = [2, 7, 11, 15],  target = 9   → [0, 1]    (vì 2 + 7 = 9)
nums = [3, 2, 4],       target = 6   → [1, 2]    (vì 2 + 4 = 6)
nums = [3, 3],          target = 6   → [0, 1]    (hai phần tử giống nhau — vẫn hợp lệ vì khác index)
```

### Input / Output / Constraints

| Mục | Chi tiết |
| --- | --- |
| **Input** | `nums: number[]`, `target: number` |
| **Output** | `number[]` — mảng 2 chỉ số |
| **Constraints** | $2 \le \text{nums.length} \le 10^4$; $-10^9 \le \text{nums}[i], \text{target} \le 10^9$; có **đúng 1 lời giải** |

### Edge cases cần lưu ý

- **Phần tử trùng giá trị** như `[3, 3]` với `target = 6` → phải trả về 2 index khác nhau, không phải cùng 1 index.
- **Số âm**: `nums = [-3, 4, 3, 90]`, `target = 0` → `[0, 2]`.
- **Target âm**: `nums = [-1, -2, -3, -4]`, `target = -3` → `[0, 1]`.
- **Mảng nhỏ nhất** (length = 2): chỉ có 1 cặp duy nhất.

---

## 2. Tư duy giải bài

### 🤔 Câu hỏi gợi ý để bạn tự suy nghĩ

1. Cách "ngây thơ" nhất là gì? — Thử **mọi cặp** xem cặp nào tổng bằng `target`. Độ phức tạp bao nhiêu?
2. Khi đứng tại `nums[i]`, ta cần tìm số nào? — Cần tìm $\text{complement} = \text{target} - \text{nums}[i]$. Vậy bài toán biến thành **"số `complement` có tồn tại trong mảng không?"**
3. **Tìm kiếm trong mảng** có cấu trúc dữ liệu nào nhanh hơn $O(n)$? — `Set` / `Map` (hash table) với lookup trung bình $O(1)$.
4. Nếu mảng **đã sắp xếp**, có cách nào dùng **Two Pointers**?

### Pattern nhận diện

- **HashMap Lookup** (kinh điển): bài "tìm cặp tổng = target" → 99% trường hợp dùng hash map.
- **Two Pointers** (nếu mảng đã sort hoặc cho phép sort): chạy 2 con trỏ từ 2 đầu, dịch chuyển dựa trên tổng so với target.
- Đây là **bài học đầu tiên** giúp bạn nhận ra: **"tradeoff thời gian ↔ bộ nhớ"** — dùng thêm bộ nhớ ($O(n)$ hash map) để giảm thời gian từ $O(n^2)$ xuống $O(n)$.

### Hành trình tư duy: brute force → tối ưu

1. **Brute force:** 2 vòng lặp lồng nhau, thử mọi cặp `(i, j)` → $O(n^2)$.
2. **Sort + Two Pointers:** Sort mảng, dùng 2 pointers từ 2 đầu → $O(n \log n)$. Nhưng **sort làm mất index gốc** → phải lưu lại index trước khi sort.
3. **HashMap one-pass:** Duyệt 1 lần, mỗi phần tử kiểm tra xem `target - nums[i]` đã thấy trước đó chưa → $O(n)$.

---

## 3. Ba solutions khả thi

### ✅ Solution 1: Brute Force (2 vòng lặp lồng nhau)

#### Ý tưởng cốt lõi

Thử **mọi cặp** `(i, j)` với `i < j`, kiểm tra xem `nums[i] + nums[j] === target` không.

#### Các bước chi tiết

1. Vòng lặp ngoài `i` từ `0` đến `n - 2`.
2. Vòng lặp trong `j` từ `i + 1` đến `n - 1`.
3. Nếu `nums[i] + nums[j] === target` → trả về `[i, j]`.
4. Nếu không tìm được → trả về `[]` (theo đề thì không xảy ra).

#### Trace ví dụ: `nums = [2, 7, 11, 15]`, `target = 9`

| `i` | `j` | `nums[i] + nums[j]` | Match? |
| --- | --- | --- | --- |
| 0 (2) | 1 (7) | 9 | ✅ → return `[0, 1]` |

#### Code

```js
/**
 * @param {number[]} nums
 * @param {number} target
 * @return {number[]}
 */
function twoSum(nums, target) {
  const n = nums.length;

  // Thử mọi cặp (i, j) với i < j để không trùng phần tử
  for (let i = 0; i < n - 1; i++) {
    for (let j = i + 1; j < n; j++) {
      if (nums[i] + nums[j] === target) {
        return [i, j];
      }
    }
  }

  return []; // Theo đề luôn có lời giải, nhánh này chỉ để an toàn
}
```

#### Complexity

- **Time:** $O(n^2)$ — vòng lặp lồng nhau.
- **Space:** $O(1)$ — không dùng bộ nhớ phụ.

#### Ưu / Nhược

- ✅ Đơn giản, dễ hiểu, không cần data structure phụ.
- ❌ Quá chậm với $n$ lớn ($n = 10^4$ → tới $10^8$ phép so sánh, nguy cơ TLE).

#### Khi nào dùng

- Khi mảng **rất nhỏ** ($n < 100$) hoặc khi bộ nhớ **cực kỳ hạn chế**.

---

### ✅ Solution 2: Two-Pass HashMap

#### Ý tưởng cốt lõi

Duyệt **2 lần**:
- Lần 1: lưu tất cả `(value → index)` vào HashMap.
- Lần 2: với mỗi `nums[i]`, tìm `complement = target - nums[i]` trong map. Nếu tồn tại và **index khác `i`** → trả về cặp.

#### Các bước chi tiết

1. Khởi tạo `map = new Map()`.
2. Lặp `i` từ `0` đến `n - 1`: `map.set(nums[i], i)`.
3. Lặp `i` từ `0` đến `n - 1`:
   - `complement = target - nums[i]`.
   - Nếu `map.has(complement)` **và** `map.get(complement) !== i` → return `[i, map.get(complement)]`.

#### Trace ví dụ: `nums = [3, 2, 4]`, `target = 6`

**Lần 1 (build map):**
```
map = { 3 → 0, 2 → 1, 4 → 2 }
```
**Lần 2:**
| `i` | `nums[i]` | `complement` | `map.has(comp)` | `map.get(comp) !== i`? | Kết quả |
| --- | --- | --- | --- | --- | --- |
| 0 | 3 | 3 | ✓ | 0 === 0 → ✗ | bỏ qua |
| 1 | 2 | 4 | ✓ | 2 !== 1 → ✓ | return `[1, 2]` |

#### Code

```js
function twoSum(nums, target) {
  const map = new Map();

  // Pass 1: lưu (giá trị → index) vào map
  for (let i = 0; i < nums.length; i++) {
    map.set(nums[i], i);
  }

  // Pass 2: tìm complement
  for (let i = 0; i < nums.length; i++) {
    const complement = target - nums[i];
    // Kiểm tra complement tồn tại VÀ không phải chính phần tử đang xét
    if (map.has(complement) && map.get(complement) !== i) {
      return [i, map.get(complement)];
    }
  }

  return [];
}
```

#### Complexity

- **Time:** $O(n)$ — 2 lần duyệt mảng, mỗi lookup/insert vào `Map` là $O(1)$ trung bình.
- **Space:** $O(n)$ — map chứa tối đa $n$ phần tử.

#### Ưu / Nhược

- ✅ Nhanh hơn brute force rất nhiều ($O(n)$ vs $O(n^2)$).
- ✅ Code rõ ràng, dễ giải thích logic "build rồi tìm".
- ❌ Duyệt 2 lần — có thể tối ưu thành 1 lần (Solution 3).
- ❌ Phải check `map.get(complement) !== i` vì cùng index không được dùng 2 lần.

#### Khi nào dùng

- Khi muốn code **dễ hiểu** với người mới, tách logic build/query rõ ràng.

---

### ✅ Solution 3: One-Pass HashMap ⭐ (Tối ưu)

#### Ý tưởng cốt lõi

**Insight quan trọng:** Khi đứng tại `nums[i]`, ta chỉ cần biết: *"có số nào trước đó bằng `target - nums[i]` không?"* Vì vậy có thể **vừa duyệt vừa build map**, không cần 2 lần.

#### Các bước chi tiết

1. Khởi tạo `map = new Map()`.
2. Lặp `i` từ `0` đến `n - 1`:
   - `complement = target - nums[i]`.
   - **Trước tiên**, check `map.has(complement)`:
     - Nếu có → return `[map.get(complement), i]`.
   - **Sau đó**, mới `map.set(nums[i], i)` (để không tự match chính mình).

> 🔑 **Mấu chốt:** Kiểm tra **trước**, lưu **sau** — điều này đảm bảo phần tử hiện tại không bao giờ tự match với chính nó.

#### Trace ví dụ: `nums = [2, 7, 11, 15]`, `target = 9`

| `i` | `nums[i]` | `complement` | `map.has(comp)`? | Hành động | `map` sau bước |
| --- | --- | --- | --- | --- | --- |
| 0 | 2 | 7 | ✗ | set(2 → 0) | `{2:0}` |
| 1 | 7 | 2 | ✓ → return `[0, 1]` | — | — |

#### Trace ví dụ 2: `nums = [3, 3]`, `target = 6`

| `i` | `nums[i]` | `complement` | `map.has(comp)`? | Hành động | `map` |
| --- | --- | --- | --- | --- | --- |
| 0 | 3 | 3 | ✗ | set(3 → 0) | `{3:0}` |
| 1 | 3 | 3 | ✓ → return `[0, 1]` | — | — |

→ Trường hợp 2 phần tử trùng được xử lý **tự nhiên** mà không cần check riêng. 🎯

#### Code

```js
function twoSum(nums, target) {
  const map = new Map(); // key: giá trị đã thấy, value: index của nó

  for (let i = 0; i < nums.length; i++) {
    const complement = target - nums[i];

    // CHECK TRƯỚC: nếu complement đã được thấy ở vị trí trước → ta có cặp!
    if (map.has(complement)) {
      return [map.get(complement), i];
    }

    // LƯU SAU: để các vòng sau có thể thấy nums[i]
    // (đảm bảo nums[i] không tự match với chính nó)
    map.set(nums[i], i);
  }

  return [];
}
```

#### Complexity

- **Time:** $O(n)$ — chỉ duyệt 1 lần.
- **Space:** $O(n)$ — map chứa tối đa $n$ phần tử (trường hợp xấu nhất là cặp ở cuối).

#### Ưu / Nhược

- ✅ Tối ưu nhất: 1 lần duyệt, không cần check `!== i`.
- ✅ Logic "vừa hỏi vừa lưu" rất tự nhiên cho bài toán "tìm cặp".
- ❌ Cần thêm $O(n)$ bộ nhớ — nhưng đây là tradeoff hợp lý.

#### Khi nào dùng

- **Default choice** cho bài Two Sum và phần lớn biến thể của nó. Đây là solution chuẩn để học pattern HashMap.

---

### 🎁 Bonus: Solution 4 — Sort + Two Pointers

#### Ý tưởng cốt lõi

Sort mảng (nhưng **giữ lại index gốc**), sau đó dùng 2 con trỏ từ 2 đầu — nếu tổng nhỏ hơn target thì `left++`, lớn hơn thì `right--`.

#### Code

```js
function twoSum(nums, target) {
  // Tạo mảng [value, originalIndex] rồi sort theo value
  const indexed = nums.map((v, i) => [v, i]).sort((a, b) => a[0] - b[0]);

  let left = 0;
  let right = indexed.length - 1;

  while (left < right) {
    const sum = indexed[left][0] + indexed[right][0];
    if (sum === target) {
      return [indexed[left][1], indexed[right][1]];
    } else if (sum < target) {
      left++; // cần tổng lớn hơn → tăng left
    } else {
      right--; // cần tổng nhỏ hơn → giảm right
    }
  }

  return [];
}
```

- **Time:** $O(n \log n)$ do sort.
- **Space:** $O(n)$ cho mảng `indexed`.
- **Khi nào dùng:** Khi đề biến thể là **"Two Sum II — Input array is sorted"** (LC 167) — không cần sort lại.

---

## 4. Bảng so sánh các solutions

| Approach | Time | Space | Độ khó triển khai | Ưu điểm | Nhược điểm |
| -------- | ---- | ----- | ----------------- | ------- | ---------- |
| **1. Brute Force** | $O(n^2)$ | $O(1)$ | Rất dễ | Không tốn bộ nhớ, dễ hiểu | Quá chậm với $n$ lớn |
| **2. Two-Pass HashMap** | $O(n)$ | $O(n)$ | Dễ | Logic build/query rõ ràng | Duyệt 2 lần, cần check `!== i` |
| **3. One-Pass HashMap** ⭐ | $O(n)$ | $O(n)$ | Dễ-trung bình | Tối ưu nhất, code gọn | Cần $O(n)$ bộ nhớ phụ |
| **4. Sort + Two Pointers** | $O(n \log n)$ | $O(n)$ | Trung bình | Không cần hash, hữu ích khi mảng đã sort | Mất index gốc, sort phụ thuộc engine |

---

## 5. Phân tích Big O chi tiết

### Time Complexity

#### Solution 1 — Brute Force

- Vòng ngoài chạy $n - 1$ lần.
- Vòng trong với mỗi `i` chạy $n - i - 1$ lần.
- Tổng số phép so sánh:

$$\sum_{i=0}^{n-2} (n - i - 1) = \frac{n(n-1)}{2} = O(n^2)$$

#### Solution 2 — Two-Pass HashMap

- **Pass 1:** $n$ phép `map.set()`, mỗi phép $O(1)$ trung bình → $O(n)$.
- **Pass 2:** $n$ phép `map.has()` + `map.get()`, mỗi phép $O(1)$ trung bình → $O(n)$.
- **Tổng:** $O(n) + O(n) = O(n)$.

#### Solution 3 — One-Pass HashMap

- 1 vòng lặp duy nhất chạy tối đa $n$ lần.
- Mỗi vòng: 1 trừ ($O(1)$) + 1 `has` ($O(1)$) + tối đa 1 `set` ($O(1)$).
- **Tổng:** $O(n)$.

> 📌 **Lưu ý quan trọng:** HashMap lookup là $O(1)$ **trung bình** (amortized). Trong trường hợp xấu nhất khi xảy ra **collision hàng loạt**, nó có thể lên $O(n)$ → tổng $O(n^2)$. Nhưng với hash function tốt (JS `Map` sử dụng implementation tối ưu của V8), trường hợp xấu này gần như không xảy ra trong thực tế.

#### Solution 4 — Sort + Two Pointers

- `.map()` tạo mảng index: $O(n)$.
- `.sort()`: $O(n \log n)$ — đây là bottleneck.
- 2 pointers duyệt: $O(n)$.
- **Tổng:** $O(n \log n)$.

### Space Complexity

- **Solution 1:** Không tạo dữ liệu phụ → $O(1)$.
- **Solution 2 & 3:** Map chứa tối đa $n$ entry → $O(n)$.
- **Solution 4:** Mảng `indexed` chứa $n$ cặp → $O(n)$.

### 🔍 Vì sao Solution 3 thắng Solution 2?

Cả hai cùng $O(n)$, nhưng Solution 3:
- Chỉ duyệt **1 lần** (constant factor nhỏ hơn).
- Map **nhỏ hơn** ở thời điểm bất kỳ (chỉ chứa các phần tử **đã xét trước `i`**, không phải toàn bộ mảng).
- **Không cần check `!== i`** vì ta chưa lưu `nums[i]` trước khi check.

---

## 6. Góc nhìn giảng dạy

### Khái niệm cần nắm

- **HashMap (Map trong JS):** cấu trúc lưu cặp `(key, value)` với truy xuất $O(1)$ trung bình. **Vũ khí số 1** cho các bài "tìm phần tử có thỏa mãn điều kiện X không?".
- **Complement thinking:** thay vì hỏi *"có cặp nào tổng = target không?"*, hãy đổi sang *"với mỗi phần tử `x`, có `target - x` trong mảng không?"* — Đây là kỹ thuật **biến đổi vấn đề** rất quan trọng trong DSA.
- **Tradeoff thời gian ↔ bộ nhớ:** Solution 3 dùng thêm $O(n)$ bộ nhớ để giảm thời gian từ $O(n^2)$ xuống $O(n)$. Đây là pattern xuất hiện **liên tục** trong DSA.

### Lỗi tư duy phổ biến

1. **Trả về sai format:** Đề yêu cầu trả về *indices*, không phải *values*. Người mới hay quên `i, j` và trả về `nums[i], nums[j]`.
2. **Dùng cùng phần tử 2 lần:** Trong Solution 2, nếu không check `map.get(complement) !== i`, với `nums = [3, 2, 4]`, `target = 6` ta sẽ sai bị trả về `[0, 0]` (vì `complement = 3` cũng nằm ở index 0).
3. **Quên trường hợp số trùng:** Với `[3, 3]`, target `6` — Solution 3 xử lý đúng vì check **trước** khi lưu. Nhưng dễ viết sai thành "lưu trước, check sau" → bug.
4. **Dùng `Object` thay vì `Map`:** Trong JS, `{}` có vấn đề với key kiểu number (tự convert sang string) và có thể có prototype pollution. **Luôn dùng `Map` cho bài DSA.**
5. **Nested loop với index sai:** Quên `j = i + 1` (dùng `j = 0`) sẽ duyệt cả cặp `(0, 0)` → sai.

### Cách nhận biết bài tương tự trong tương lai

Bài có những "signal" này thường giải bằng **HashMap one-pass**:

- "Find pair / two elements such that..." (sum, difference, product, XOR)
- "Has the array seen X before?"
- "Count occurrences of Y"
- Đề có *1 mảng* và cần *tìm mối quan hệ giữa 2 phần tử*.

→ Hỏi ngay: **"Có thể biến vấn đề thành 'kiểm tra X đã tồn tại chưa?' không?"** Nếu có → HashMap.

---

## 7. Kết luận

### Solution tối ưu nhất: **Solution 3 — One-Pass HashMap** ⭐

**Vì sao?**

- Time tối ưu: $O(n)$ — không thể tốt hơn vì cần đọc ít nhất 1 lần.
- Space $O(n)$ là tradeoff chấp nhận được.
- Code **ngắn, sạch**, không cần check edge case `!== i`.
- Là **template chuẩn** để giải hàng loạt bài "find pair" khác.

**Solution 2** vẫn tốt khi bạn muốn tách logic *build/query* để code dễ đọc hơn cho người mới.

**Solution 4 (Sort + 2 pointers)** chỉ tối ưu khi mảng **đã sort sẵn** (LC 167).

### Bài LeetCode tương tự nên luyện

| # | Tên | Pattern liên quan |
| --- | --- | --- |
| **167** | Two Sum II — Input Array Is Sorted | Two pointers (mảng đã sort) |
| **15** | 3Sum | Sort + 2 pointers, fix 1 phần tử |
| **18** | 4Sum | Generalization của 3Sum |
| **454** | 4Sum II | HashMap chia đôi (meet in the middle) |
| **560** | Subarray Sum Equals K | Prefix sum + HashMap |
| **653** | Two Sum IV — Input is a BST | HashMap + tree traversal |
| **170** | Two Sum III — Data Structure Design | HashMap design |
| **219** | Contains Duplicate II | HashMap với điều kiện khoảng cách |

Sau khi luyện xong nhóm trên, pattern **"HashMap lookup + complement thinking"** sẽ trở thành phản xạ. 🚀
