# First Unique Character in a String

### 題目

Given a string, find the first non-repeating character in it and return it's index. If it doesn't exist, return -1.

**Examples:**

```
s = "leetcode"
return 0.

s = "loveleetcode",
return 2.
```



**Note:** You may assume the string contain only lowercase letters.

------

### 思路

- 既然要回傳index, 就要想辦法紀錄index, 利用Dic<字元, 出現次數>
- 如何利用字元回找array的index
- 注意Dictionary的宣告寫法

------

#### 程式碼

```swift
class Solution {
    func firstUniqChar(_ s: String) -> Int {
      var chars = Array(s)
      // 注意Dictionary的宣告寫法
      var dic = Dictionary<Character, Int>()
      for index in 0..<chars.count {
        let key = chars[index]
        // 記錄每個char出現的次數
        if dic[key] != nil {
          dic[key]! += 1
        } else {
          dic[key] = 1
        }
      }
      
      var hasMinIndex = false
      var minIndex = Int.max
      
      for key in dic.keys {
        // 只找出現次數為1
        if dic[key]! == 1 {
          hasMinIndex = true
          // 如何利用字元回找array的index
          let index = chars.firstIndex(of: key)
          minIndex = min(minIndex, index!)
        }
      }
      
      if hasMinIndex {
        return minIndex
      } else {
        return -1
      }
    }
}
```

------

#### 複雜度

時間O(n), n為s的長度，至少要掃過一遍產生dic。空間O(n), 字典大小會隨著字串的長度而變化。