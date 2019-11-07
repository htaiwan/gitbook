# Reverse String

### 題目

Write a function that reverses a string. The input string is given as an array of characters `char[]`.

Do not allocate extra space for another array, you must do this by **modifying the input array [in-place](https://en.wikipedia.org/wiki/In-place_algorithm)** with O(1) extra memory.

You may assume all the characters consist of [printable ascii characters](https://en.wikipedia.org/wiki/ASCII#Printable_characters).

 

**Example 1:**

```
Input: ["h","e","l","l","o"]
Output: ["o","l","l","e","h"]
```

**Example 2:**

```
Input: ["H","a","n","n","a","h"]
Output: ["h","a","n","n","a","H"]
```

------

### 思路

- 如何利用兩根指針進行交換

------

#### 程式碼

```swift
class Solution {
    func reverseString(_ s: inout [Character]) {
      var i = 0
      var j = s.count - 1
      while (i < j) {
        // 兩根指針的交換
        var temp = s[i]
        s[i] = s[j]
        s[j] = temp
        i += 1
        j -= 1
      }
    }
}
```

------

#### 複雜度

一個for loop搞定，交換次數n/2, 所以時間整體為O(n)，需要額外i,j temp這3個指針進行儲存，空間為常數O(1)