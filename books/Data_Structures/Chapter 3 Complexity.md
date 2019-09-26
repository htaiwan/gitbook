# Chapter 3: Complexity

#### 前言

複雜度是用來衡量一個演算法的**scalability**。

當算法在大數量規模的數據下是否還可以保持優秀的效能

------

#### 大綱

- [Time complexity](#1)
  - Constant time
  - Linear Time
  - Quadratic time
  - Logarithmic time
  - Quasilinear time
  - Other time complexities
- [Comparing time complexity](#2)
- [Space complexity](#3)

------

<h2 id="1">Time complexity</h2>

常見到的時間複雜度

- Constant time: O(1)
- Linear time: O(n)
- Quadratic time: O(n^2)
- Logarithmic time complexity: O(log n)
- Quasilinear time complexity: O(n log n)

其他比較少見的時間複雜度

These time complexities include polynomial time, exponential time, factorial time and more

------

<h2 id="2">Comparing time complexity</h2>

對於一樣的需求，不同的算法有不同的複雜度。

```swift
// 方法一: O(n)
func sumFromOne(upto n: Int) -> Int {
  var result = 0
  for i in 1...n {
    result += i
  }
  return result
}
  
sumFromOne(upto: 10000)

// 方法二: O(n), 但會比方法一快一點, 因為這是compiler code
func sumFromOne(upto n: Int) -> Int {
  return (1...n).reduce(0, +)
}
sumFromOne(upto: 10000)

// 方法三: O(1), 利用Fredrick Gauss
func sumFromOne(upto n: Int) -> Int {
  return (n + 1) * n / 2
}
sumFromOne(upto: 10000) 
```



------

<h2 id="3">Space complexity</h2>

空間複雜度，常常有機會遇到的情況是拿空間換取時間。但有些情況下我們也會拿時間去換取空間。