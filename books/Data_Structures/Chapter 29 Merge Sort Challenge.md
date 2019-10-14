# Chapter 29: Merge Sort Challenge

> Challenge
>
> Write a function that takes two sorted sequences and merges them into a single sequence. Here’s the function signature to start off:

```swift
func merge<T: Sequence>(first: T, second: T)
  -> AnySequence<T.Element> where T.Element: Comparable {}
```

- 思路: 如何寫好一個合併，兩根指針不同squence不斷移動，最後還要考慮剩下沒到底的sequence

```swift
   func merge<T: Sequence>(first: T, second: T)
  -> AnySequence<T.Element> where T.Element: Comparable { 
     var result: [T.Element] = []

    var firstIterator = first.makeIterator()
    var secondIterator = second.makeIterator()

    var firstNextValue = firstIterator.next()
    var secondNextValue = secondIterator.next()

    while let first = firstNextValue, let second = secondNextValue {
        if first < second {
            result.append(first)
            firstNextValue = firstIterator.next()
        } else if second < first {
            result.append(second)
            secondNextValue = secondIterator.next()
        } else {
            result.append(first)
            result.append(second)
            firstNextValue = firstIterator.next()
            secondNextValue = secondIterator.next()
        }
    }

    while let first = firstNextValue {
      result.append(first)
      firstNextValue = firstIterator.next()
    }

    while let second = secondNextValue {
      result.append(second)
      secondNextValue = secondIterator.next()
    }

    return AnySequence<T.Element>(result)

}

```

