# Chapter 28: Merge Sort

#### 前言

Merge sort主要利用的是divide-and-conquer來進行排序，跟前面學到comparison-based(Bubble, Selection, Inserction)是完全不同的概念。

------

#### 大綱

- Implementation
  - Split
  - Merge
  - Finishing up
- Performance

------

#### Split

- 不斷遞迴分割整個資料集，直到整個資料集達到base case, 以這個例子而言，當切割每個資料集的元素個數為1，表示已切割完成。

```swift
public func mergeSort<Element>(_ array: [Element]) -> [Element] where Element: Comparable {
    // 達到base case不在遞迴下去  
    guard array.count > 2 else {
        return array
    }
    // 先進行split動作
    let middle = array.count / 2
    let left = mergeSort(Array(array[0..<middle]))
    let right = mergeSort(Array(array[middle...]))
}

```

------

#### Merge

- 將left和right從小範圍的資料集不斷合併成大的

```swift
private func merge<Element>(_ left: [Element], _ right: [Element]) -> [Element] where Element: Comparable {
    var leftIndex = 0
    var rightIndex = 0

    var result: [Element] = []

    // 這個loop結束後，一定是左邊或右邊的所有元素已經都加入result
    while leftIndex < left.count && rightIndex < right.count {
        let leftElement = left[leftIndex]
        let rightElement = right[rightIndex]

        if leftElement < rightElement {
            result.append(leftElement)
            leftIndex += 1
        } else if leftElement > rightElement {
            result.append(rightElement)
            rightIndex += 1
        } else {
            result.append(leftElement)
            leftIndex += 1
            result.append(rightElement)
            rightIndex += 1
        }
    }

    // 如果是左邊有剩
    if leftIndex < left.count {
        result.append(contentsOf: left[leftIndex...])
    }

    // 如果是右邊有剩
    if rightIndex < right.count {
        result.append(contentsOf: right[rightIndex...])
    }

    return result
}
```

------

#### Finishing up

- The strategy of merge sort is to divide and conquer, so that you solve many small problems instead of one big problem.
- It has two core responsibilities: a method to divide the initial array recursively, as well as a method to merge two arrays.
- The merging function should take two sorted arrays and produce a single sorted array.

```swift
public func mergeSort<Element>(_ array: [Element])
    -> [Element] where Element: Comparable {
  guard array.count > 1 else {
    return array
  }
  let middle = array.count / 2
  let left = mergeSort(Array(array[..< middle]))
  let right = mergeSort(Array(array[middle...]))
      
  return merge(left, right)
}
```

------

#### Performance

- 切割: 是利用遞迴方式切割
  - In general, if you have an array of size n, the number of levels is log2(n).
- 合併: 每次合併都是將n個元素(可能被切成很n堆)進行左右合併
  - A single recursion level will merge n elements. This means the cost of a single recursion is O(n)
- This brings the total cost to O(log n) × O(n) = O(n log n).

雖然Merge sort的速度比之前講的0(n^2)還要快，但代價就是犧牲空間複雜度

There are log2(n) levels of recursion and at each level n elements are used. That makes the total O(n log n) in space complexity
