#  Contains Duplicate

#### 題目

Given an array of integers, find if the array contains any duplicates.

Your function should return true if any value appears at least twice in the array, and it should return false if every element is distinct.

**Example 1:**

```
Input: [1,2,3,1]
Output: true
```

**Example 2:**

```
Input: [1,2,3,4]
Output: false
```

------

#### 思路

一開始的直覺就是力用資料結構Set來處理重複的問題，另外一種解法就是透過排序以後再來處理，就不需要額外的資料結構了。

------

#### 程式

```swift
class Solution {
    func containsDuplicate(_ nums: [Int]) -> Bool {
        var set: Set<Int> = []
        for index in 0..<nums.count {
            if set.contains(nums[index]) {
                return true
            } else {
                set.insert(nums[index])
            }
        }
        
        return false
    }
}
```

```swift
class Solution {
    func containsDuplicate(_ nums: [Int]) -> Bool {
        if nums.count == 0 || nums.count == 1 { return false }
        var sorted = nums.sorted()
        for index in 0..<sorted.count-1 {
            if sorted[index] == sorted[index+1] {
                return true
            }
        }
        return false
    }
}
```



------

#### 複雜度

- 利用Set
  - 時間: 每次Set的insert或contain的判斷是O(1), 但需要重複n次, 所以總共是O(n)
  - 空間: O(n), 看array的長度
- 利用sort
  - 時間: 排序需要O(nlogn), 然後掃過一一比較是O(n), 所以需要O(nlogn)
  - 空間: O(1), 看是哪種排序，如果是heap sort就需要O(1)的額外輔助空間