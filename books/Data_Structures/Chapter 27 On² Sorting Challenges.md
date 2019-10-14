# Chapter 27: O(n²) Sorting Challenges

> Challenge 1
>
> Given a collection of Equatable elements, bring all instances of a given value in the array to the right side of the array.

- 思路: 利用兩根指針，一根指針尋找要被交換的元素，一根指針指向當前最右邊的index。

```swift
extension MutableCollection where Self: BidirectionalCollection, Element: Equatable {

    mutating func rightAlign(value: Element) {
        // 用兩個指針來分別紀錄左右兩邊的邊界位置
        var left = startIndex
        var right = index(before: endIndex)

        // time: O(n)
        while left < right {
            // 右邊的值等於value
            while self[right] == value {
                // right指針仼左移
                formIndex(before: &right)
            }
            while self[left] != value {
                // left指針繼續移動
                formIndex(after: &left)
            }
            guard left < right else {
                return
            }
            swapAt(left, right)
        }
    }
}
```



------

> Challenge 2
>
> Given a collection of Equatable elements, return the first element that is a duplicate in the collection

- 思路: 利用set來記錄當前不重複的元素

```swift
extension Sequence where Element: Hashable {

    var firstDuplicate: Element? {
        // 利用set來記錄
        var found: Set<Element> = []
        for value in self {
            if found.contains(value) {
                return value
            } else {
                found.insert(value)
            }
        }
        return nil
    }
}
```



------

> Challenge 3
>
> Reverse a collection of elements by hand. Do not rely on the reverse or reversed methods.

- 思路: 兩根指針分別從兩側向中間夾擠

```swift
extension MutableCollection where Self: BidirectionalCollection {

    mutating func reverse() {
        var left = startIndex
        var right = index(before: endIndex)

        while left < right {
            // 交換
            swapAt(left, right)
            // left向右移動
            formIndex(after: &left)
            // right向左移動
            formIndex(before: &right)
        }
    }
}
```

