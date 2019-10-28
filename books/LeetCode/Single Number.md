# Single Number

#### 題目

Given a **non-empty** array of integers, every element appears *twice* except for one. Find that single one.

**Note:**

Your algorithm should have a linear runtime complexity. Could you implement it without using extra memory?

**Example 1:**

```
Input: [2,2,1]
Output: 1
```

**Example 2:**

```
Input: [4,1,2,1,2]
Output: 4
```

------

#### 思路

一招半式打天下，跟contains Duplicate相同的技巧，利用一個額外的資料結構處理，雖然可以達到O(n)的時間複雜度，但空間就無法滿足要求了，查了一下，網路的神奇的解答就是用Bitwise XOR Operator來操作，一樣的數字進行XOR會變成0, 所以最後出現的數字就是我們的single number

[Swift XOR](https://docs.swift.org/swift-book/LanguageGuide/AdvancedOperators.html)

------

#### 程式

```swift
class Solution {
    func singleNumber(_ nums: [Int]) -> Int {
        var set: Set<Int> = []
        for index in 0..<nums.count {
            if set.contains(nums[index]) {
                set.remove(nums[index])
            } else {
                set.insert(nums[index])
            }
        }
        
        return set.first!
    }
}
```

```swift
class Solution {
    func singleNumber(_ nums: [Int]) -> Int {
        var a = 0
        for index in 0..<nums.count {
            a ^= nums[index]
        }
        
        return a
    }
}
```

------

#### 複雜度

- 利用Set
  - 時間: O(n), contains, remove, insert都是O(1), 但要重複n個元素
  - 空間: O(n)
- 利用Bitwise XOR Operator
  - 時間: O(n)
  - 空間: O(1)