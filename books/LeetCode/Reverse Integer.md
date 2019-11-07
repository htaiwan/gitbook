# Reverse Integer

#### 題目

Given a 32-bit signed integer, reverse digits of an integer.

**Example 1:**

```
Input: 123
Output: 321
```

**Example 2:**

```
Input: -123
Output: -321
```

**Example 3:**

```
Input: 120
Output: 21
```

**Note:**
Assume we are dealing with an environment which could only store integers within the 32-bit signed integer range: [−231, 231 − 1]. For the purpose of this problem, assume that your function returns 0 when the reversed integer overflows

------

### 思路

- 利用餘數來解
  - 第一次取出來的餘數，剛好會是翻轉後的第一個數字

- 32bit要如何處理

------

### 程式碼

```swift
class Solution {
    func reverse(_ x: Int) -> Int {
      var y = x
      var result = 0
      // 注意while條件
      while (y > 0) {
        // 第一次取出來的餘數,會因為while loop乘上最多次的10,變成第一個數字
        result = 10 * result + y % 10 
        y /= 10
      }
      // 32bit處理
      if result > Int.max || result < Int.min {
        return 0
      }
      
      return result
    }
}
```

------

#### 複雜度

時間O(n), n是X的長度，總共要進行n次的轉換，時間O(1)