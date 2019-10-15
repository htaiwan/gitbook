# Chapter 35: Quicksort Challenges

> Challenge 1
>
> In this chapter, you learned how to implement quicksort recursively. Your challenge here is to implement it iteratively. Choose any partition strategy you learned in this chapter.

思路: 取代recursively改用iteratively的最佳資料結構就是stack

```Swift
public func quicksortIterativeLomuto<T: Comparable>(_ a: inout [T], low: Int, high: Int) {
    var stack = Stack<Int>()
    stack.push(low)
    stack.push(high)
    // iteratively
    while !stack.isEmpty {
        guard let end = stack.pop(), let start = stack.pop() else {
            continue
        }
        // 進行 Lomuto’s partition strategy.
        let p = partitionLomuto(&a, low: start, high: end)

        // 表示pivot-1跟start之間還可以切割
        if p - 1 > start {
            stack.push(start)
            stack.push(p - 1)
        }

        // 表示pivot+1跟end之間還可以切割
        if p + 1 < end {
            stack.push(p + 1)
            stack.push(end)
        }
    }
}
```



------

> Challenge 2
>
> Explain when and why you would use merge sort over quicksort.

[淺談 quick sort](https://blog.kuoe0.tw/posts/2013/03/15/sort-about-quick-sort/)

- Merge sort is preferable over quicksort when you need stability. Merge sort is a stable sort and guarantees O(n log n). This is not the case with quicksort, which isn’t stable and can perform as bad as O(n²).
- Merge sort works better for larger data structures or data structures where elements are scattered throughout memory. Quicksort works best when elements are stored in a contiguous block.