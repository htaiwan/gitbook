# Plus One

#### 題目

Given a **non-empty** array of digits representing a non-negative integer, plus one to the integer.

The digits are stored such that the most significant digit is at the head of the list, and each element in the array contain a single digit.

You may assume the integer does not contain any leading zero, except the number 0 itself.

**Example 1:**

```
Input: [1,2,3]
Output: [1,2,4]
Explanation: The array represents the integer 123.
```

**Example 2:**

```
Input: [4,3,2,1]
Output: [4,3,2,2]
Explanation: The array represents the integer 4321.
```

------

#### 分析

- For loop的reversed的使用
- 處理進位 ex.[9], [9,9]
- 商數跟餘數的取得

------

#### 程式

```swift
func plusOne(_ digits: [Int]) -> [Int] {
    var r = digits
    var s = 0
    // For loop的reversed的使用
    for i in (0..<r.count).reversed() {
        if (i == r.count - 1) {
            s = r[i] + 1
            r[i] = s % 10
            s /= 10
            // Edge case: 處理digits = [9]
            if (r.count == 1 && s > 0) {
                r.insert(s, at: 0)
            }
        } else {
            // 商數跟餘數的取得
            r[i] = (r[i] + s) % 10
            s = (r[i] + s) / 10
            if (i == 0 && s > 0) {
                r.insert(s, at: i)
            }
        }
    }

    return r
}
```

------

#### 複雜度

- 一個for loop完成，所以是O(n)