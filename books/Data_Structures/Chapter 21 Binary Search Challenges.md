# Chapter 21: Binary Search Challenges

> Challenge 1:
>
> In the previous chapter, you implemented binary search as an extension of the RandomAccessCollection protocol. Since binary search only works on sorted collections, exposing the function as part of RandomAccessCollection will have a chance of misuse.
> Your challenge is to implement binary search as a free function

思路: 把binary search從RandomAccessCollection的extension抽離出來，那就是RandomAccessCollection當作parameter傳到function中

```swift
func binarySearch<Elements: RandomAccessCollection>(for element: Elements.Element, in collection: Elements, in range: Range<Elements.Index>? = nil) -> Elements.Index? where Elements.Element: Comparable {

    let range = range ?? collection.startIndex..<collection.endIndex
    guard range.lowerBound < range.upperBound else {
        return nil
    }

    let size = collection.distance(from: range.lowerBound, to: range.upperBound)
    let middle = collection.index(range.lowerBound, offsetBy: size / 2)
    if collection[middle] == element {
        return middle
    } else if collection[middle] > element {
        return binarySearch(for: element, in: collection, in: range.lowerBound..<middle)
    } else {
        return binarySearch(for: element, in: collection, in: middle..<range.upperBound)
    }

}
```



------

> Challenge 2:
>
> Write a function that searches a sorted array and that finds the range of indices for a particular elemen

- 思路: binary search可以快速定位出元素位置，因為現在元素會有重複，且元素是有排序，所以一但定位成功，先往前或往後看一個元素，判斷當前元素是否是邊界，若不是，就繼續遞迴。

```swift
// 因爲題目確保是sorted, 所以可以利用binary search來找這個range
func findIndices(of value: Int, in array:[Int]) -> CountableRange<Int>? {

    guard let startIndex = findStartIndex(of: value, in: array, range: 0..<array.count) else {
        return nil
    }

    guard let endIndex = findEndIndex(of: value, in: array, range: 0..<array.count) else {
        return nil
    }

    return startIndex..<endIndex
}
```

```swift
func findStartIndex(of value: Int, in array: [Int], range: CountableRange<Int>) -> Int? {

    let middleIndex = range.lowerBound + (range.upperBound - range.lowerBound) / 2

    // 處理遞迴終結case
    if middleIndex == 0 || middleIndex == array.count {
        if array[middleIndex] == value {
            return middleIndex
        } else {
            return 0
        }
    }

    if array[middleIndex] == value {
        if array[middleIndex - 1] != value {
            // 表示當前middleIndex就是最小邊界
            return middleIndex
        } else {
            // 遞迴
            return findStartIndex(of: value, in: array, range: range.lowerBound..<middleIndex)
        }
    } else if value < array[middleIndex] {
        // 遞迴
        return findStartIndex(of: value, in: array, range: range.lowerBound..<middleIndex)
    } else {
        // 遞迴
        return findStartIndex(of: value, in: array, range: middleIndex..<range.upperBound)
    }

}
```

```swift
func findEndIndex(of value: Int, in array: [Int], range:  CountableRange<Int>) -> Int? {

    let middleIndex = range.lowerBound + (range.upperBound - range.lowerBound) / 2

      if middleIndex == 0 || middleIndex == array.count - 1 {
        if array[middleIndex] == value {
          return middleIndex + 1
        } else {
          return nil
        }
      }

      if array[middleIndex] == value {
        if array[middleIndex + 1] != value {
            // 表示當前middleIndex就是最大邊界
          return middleIndex + 1
        } else {
            // 遞迴
          return findEndIndex(of: value, in: array, range: middleIndex..<range.upperBound)
        }
      } else if value < array[middleIndex] {
        // 遞迴
        return findEndIndex(of: value, in: array, range: range.lowerBound..<middleIndex)
      } else {
        // 遞迴
        return findEndIndex(of: value, in: array, range: middleIndex..<range.upperBound)
      }
}
```

