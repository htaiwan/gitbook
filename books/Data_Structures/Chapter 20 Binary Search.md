# Chapter 20: Binary Search

#### 前言

Binary Search是一個非常有效率的搜尋算法，只需要O(log n)，這前提是

- The collection must be able to perform index manipulation in constant time. This means that the collection must be a RandomAccessCollection.
- The collection must be sorted.

------

#### 大綱

- Example
  - Step 1: Find middle index
  - Step 2: Check the element at the middle index
  - Step 3: Recursively call binary Search
- Implementation

------

#### Implementation

```swift
// binarySearch僅適用於RandomAccessCollection
// RandomAccessCollection的特性是 - 根據index取出colleciton的元素需要constant time
// 再來需要限制裡面的元素是可以被比較的
public extension RandomAccessCollection where Element: Comparable {

    func binarySearch(for value: Element, in range: Range<Index>? = nil) -> Index? {
        // 如果range是nil, 則將range進行初始化，其範圍是涵蓋整個colleciton
        let range = range ?? startIndex..<endIndex

        // 確保collection至少含有一個元素
        guard range.lowerBound < range.upperBound else { return nil }

        let size = distance(from: range.lowerBound, to: range.upperBound)

        // Step 1: Find middle index
        let middle = index(range.lowerBound, offsetBy: size / 2)

        // Step 2: Check the element at the middle index
        if self[middle] == value {
            return middle
        } else if self[middle] > value {
            // Step 3: Recursively call binary Search
            return binarySearch(for: value, in: range.lowerBound..<middle)
        } else {
            // Step 3: Recursively call binary Search
            return binarySearch(for: value, in: middle..<range.upperBound)
        }
    }
}
```

