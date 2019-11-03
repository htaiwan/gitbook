# Move Zeroes

#### 題目

Given an array `nums`, write a function to move all `0`'s to the end of it while maintaining the relative order of the non-zero elements.

**Example:**

```
Input: [0,1,0,3,12]
Output: [1,3,12,0,0]
```

**Note**:

1. You must do this **in-place** without making a copy of the array.
2. Minimize the total number of operations.

------

#### 分析

- 利用指針來記錄目前非零元素

------

#### 程式碼

```swift
class Solution {
    func moveZeroes(_ nums: inout [Int]) {
        var idx = 0
        
        for (i, num) in nums.enumerated() {
            if num != 0 {
                // 依序掃過陣列一次，將非0的數字往前移動
                nums[idx] = num
                idx += 1
            }
        }
        // 最後將剩下的陣列空間都補為0
        while idx < nums.count {
            nums[idx] = 0
            idx += 1
        }
    }
}
```

------

#### 複雜度

時間O(n), 一次loop搞定，空間O(1)只用一個變數進行紀錄