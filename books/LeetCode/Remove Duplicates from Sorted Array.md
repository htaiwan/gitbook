# Remove Duplicates from Sorted Array

#### 題目

Given a sorted array *nums*, remove the duplicates [**in-place**](https://en.wikipedia.org/wiki/In-place_algorithm) such that each element appear only *once* and return the new length.

Do not allocate extra space for another array, you must do this by **modifying the input array [in-place](https://en.wikipedia.org/wiki/In-place_algorithm)** with O(1) extra memory.

```
Given nums = [1,1,2],

Your function should return length = 2, with the first two elements of nums being 1 and 2 respectively.

It doesn't matter what you leave beyond the returned length.
```

```
Given nums = [0,0,1,1,1,2,2,3,3,4],

Your function should return length = 5, with the first five elements of nums being modified to 0, 1, 2, 3, and 4 respectively.

It doesn't matter what values are set beyond the returned length.
```

------

#### 思路

- 有序陣列，利用兩個快慢指標來處理。
- 慢指針用來指向當前不重複的index。
- 快指針用來循環掃過整個loop。

------

#### 程式

```Swift
func removeDuplicates(_ nums: inout [Int]) -> Int {
  if (nums.count == 0) { return 0 }
  
  var slow = 0
  
  // fast指針用來循環掃過整個loop。
  for fast in 0..<nums.count {
      // fast指到下一個不重複的數字
    if nums[fast] != nums[slow] {
      // 移動slow指針
      slow += 1
      // 將目前fast指向的數字給slow指針所在的位置
      nums[slow] = nums[fast]
    }
  }
  
  // 因為index是從0開始，所以總長度是slow+1
  return slow + 1
}
```

------

#### 複雜度

- 時間: O(n) n: 陣列長度
  - fast指針至少要循環掃過array一遍。
- 空間: O(1)
  - 額外空間給予slow指針。