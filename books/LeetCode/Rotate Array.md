# Rotate Array

#### 題目

Given an array, rotate the array to the right by *k* steps, where *k* is non-negative.

**Example 1:**

```
Input: [1,2,3,4,5,6,7] and k = 3
Output: [5,6,7,1,2,3,4]
Explanation:
rotate 1 steps to the right: [7,1,2,3,4,5,6]
rotate 2 steps to the right: [6,7,1,2,3,4,5]
rotate 3 steps to the right: [5,6,7,1,2,3,4]
```

**Example 2:**

```
Input: [-1,-100,3,99] and k = 2
Output: [3,99,-1,-100]
Explanation: 
rotate 1 steps to the right: [99,-1,-100,3]
rotate 2 steps to the right: [3,99,-1,-100]
```

**Ｎote:**

- Try to come up as many solutions as you can, there are at least 3 different ways to solve this problem.
- Could you do it in-place with O(1) extra space?

------

#### 思路

- 如果題目沒限制O(1)的空間，這題其實就算簡單，就先建立一個一樣大的array空間，然後把題目給的array一一搬過去到對應的位置，最後再把這個array倒回原本的題目中，但這樣花的額外空間就是O(n)
- 要限制到O(1), 就是利用一個額外指針先記錄被移動的東西
  - 例如 A搬到B, 要先用個指針把B記住
  - 畫個圖，就可以了解每個回合搬動都會到出發點當作結束
  - 會有幾個回合，就看還剩多下沒有被搬動
- 看到另外一個解答，是利用翻轉，但這解法實在太神奇，實在很難可以觀察出這樣的模式，我是覺得看看就好。

------

#### 程式

```swift
// O(n)空間
class Solution {
    func rotate(_ nums: inout [Int], _ k: Int) {
        // 建立一個一樣大的array空間
        var newNums = nums
        for index in 0 ..< newNums.count {
            // 題目給的array一一搬過去到對應位置
            newNums[(index + k) % newNums.count] = nums[index]   
        }
        // 再把這個array倒回原本的題目中
        nums = newNums
    }
}
```



```swift
// O(1)空間
class Solution {
    func rotate(_ nums: inout [Int], _ k: Int) {
        var count = 0
        var start = 0
        // 確認所有數字都搬過了
        while count < nums.count {
            var current = start
            var prev = nums[start]
          
            repeat {
                let next = (current + k) % nums.count
                // 先把目前在接下要搬的東西存起來
                let temp = nums[next]
                // 開始搬移
                nums[next] = prev
                prev = temp
                current = next

                // 表示已搬完一個數字
                count += 1
              
              // 只要start還不是current, 表示這個回合還沒搬完
            } while (start != current)
          
            // 開始進行下個回合
            start += 1
        }
    }
}
```



```swift
// 神奇翻轉法
class Solution {
      func rotate(_ nums: inout [Int], _ k: Int) {
        let count = nums.count
        let reverse = k % count
        nums.reverse()
        nums[0..<reverse].reverse()
        nums[reverse..<count].reverse()
    }    
}
```



------

#### 複雜度

- 方法一
  - 時間: O(n), 一個loop搞定。
  - 空間: O(n), 多複製一個array。
- 方法二
  - 時間: O(n), 雖然看起來有while，裡面又有個repeat, 但其實只跑一回合就可以搞定
  - 空間: O(1), 一些額外指針進行紀錄。
- 方法三
  - 時間: O(n), 所有元素都要進翻轉過一遍
  - 空間: O(1), 不需要額外空間。