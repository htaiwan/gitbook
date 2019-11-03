# Two Sum

#### 題目

Given an array of integers, return **indices** of the two numbers such that they add up to a specific target.

You may assume that each input would have ***exactly\*** one solution, and you may not use the *same* element twice.

**Example:**

```
Given nums = [2, 7, 11, 15], target = 9,

Because nums[0] + nums[1] = 2 + 7 = 9,
return [0, 1].
```

------

#### 分析

- 應用Dictonary來處理這個問題
  - <Key: Value> = <當前數字, 當前數字的index>

------

#### 程式碼

```swift
class Solution {
    func twoSum(_ nums: [Int], _ target: Int) -> [Int] {
        var record = Dictionary<Int, Int>()

        for (index, num) in nums.enumerated() {
            let complement = target - num
            if record[complement] != nil {
                // 回index集合
                return [record[complement]!, index]
            }

            // 紀錄index跟當前數字
            record[num] = index
        }

        return []
    }
}
```

------

#### 複雜度

時間O(n), 一個for loop跑完整個陣列，空間O(n), dict的大小。