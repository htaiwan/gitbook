# Intersection of Two Arrays II

#### 題目

Given two arrays, write a function to compute their intersection.

**Example 1:**

```
Input: nums1 = [1,2,2,1], nums2 = [2,2]
Output: [2,2]
```

**Example 2:**

```
Input: nums1 = [4,9,5], nums2 = [9,4,9,8,4]
Output: [4,9]
```

**Note:**

- Each element in the result should appear as many times as it shows in both arrays.
- The result can be in any order.

**Follow up:**

- What if the given array is already sorted? How would you optimize your algorithm?
- What if *nums1*'s size is small compared to *nums2*'s size? Which algorithm is better?
- What if elements of *nums2* are stored on disk, and the memory is limited such that you cannot load all elements into the memory at once?

------

#### 思路

在面對這個問題，如果沒有Note的資訊時，記得要問自己這些問題，第一個問題array是否有重複的元素，再來output順序是否有一定要求的排序。

這題目有兩種解法，可以對付接下來的follow up的問題

- <方法一>: 利用Dic來紀錄對應<number, count>, 然後比對兩個array所產生的對應的dict, 再返回成題目所要的array
- <方法二>: 利用排序方式，先排序兩個array在進行一一比較

Follow up 

- What if the given array is already sorted? How would you optimize your algorithm?
  - 利用方法二，我們可以在線性時間完成這個問題, 因為不需要額外再進行排序了。
- What if *nums1*'s size is small compared to *nums2*'s size? Which algorithm is better?
  - 利用方法一，因為可以減少產生的Dic的大小。
- What if elements of *nums2* are stored on disk, and the memory is limited such that you cannot load all elements into the memory at once?
  - 如果num1是可以放進mem裡，那麼可以利用方法一，先產生num1的dict放到mem中，再動態一一比較nums2的數字就好。
  - 如果都放不進去，那麼就利用方法二先排序，在動態一一比較兩個array數字

------

#### 程式

```swift
class Solution {
    func intersect(_ nums1: [Int], _ nums2: [Int]) -> [Int] {
        let hashDict1 = makeHashDict(nums1)
        let hashDict2 = makeHashDict(nums2)
        var res = [Int]()
        for (num, time1) in hashDict1 {
            if let time2 = hashDict2[num] {
                // 在nums1出現的數字也在nums2出現，找出最小出現次數
                let time = min(time1, time2)
                for _ in 0..<time {
                    res.append(num)
                }
            }
        }
        
        return res
    }
    
    func makeHashDict(_ nums:[Int]) -> [Int: Int] {
        // 用hashDict來記錄nums1出現<元素,次數>
        var hashDict = [Int: Int]()
        for num in nums {
            if let _ = hashDict[num] {
                hashDict[num]! += 1
            } else {
                hashDict[num] = 1
            }
        }
        
        return hashDict
    }
}
```

```swift
func intersect(_ nums1: [Int], _ nums2: [Int]) -> [Int] {
    var nums1 = nums1.sorted()
    var nums2 = nums2.sorted()
    var res = [Int]()
    var i = 0, j = 0
    
    while i < nums1.count && j < nums2.count {
        if nums1[i] == nums2[j] {
            res.append(nums1[i])
            i += 1 ; j += 1
        } else if nums1[i] < nums2[j] {
            i += 1
        } else {
            j += 1
        }
    }
    
    return res
}
```

------

#### 複雜度

- 方法一: Dict
  - 時間: O(n)
  - 空間: O(n)
- 方法二: Sort
  - 時間: O(nlogn)
  - 空間: O(1)

