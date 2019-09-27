# Chapter 5: Linked List Challenges

> Challenge 1: Create a function that prints out the elements of a linked list in reverse order
>

- 思路
  - 利用遞迴的方式來建立call stack, 然後最早被呼叫的print會最晚被執行到。

```swift
func printInReverse<T>(_ list: LinkedList<T>) {
    printInReverse(list.head)
}

private func printInReverse<T>(_ node: Node<T>?) {
    // 當node = nil, 表示已經到list末端
    guard let node = node else { return }
    // 技巧:利用遞迴呼叫來建立call stack
    printInReverse(node.next)
    // 最早被呼叫的print會最晚被執行
    print(node.value)
}
```



------

> Challenge 2: Create a function that returns the middle node of a linked list

- 思路
  - 利用快慢兩個指針
  - 快指針前速度是慢指針兩倍，當快指針到末端時，慢指針剛好指向中間。

```swift
func getMiddle<T>(_ list: LinkedList<T>) -> Node<T>? {
  // 利用快慢兩個指針
    var slow = list.head
    var fast = list.head

    while let nextFast = fast?.next {
        // 快指針前速度是慢指針兩倍
        // 快指針呼叫兩次next
        fast = nextFast.next
        slow = slow?.next
    }

    return slow
}
```

------

> Challenge 3: Create a function that reverses a linked list
>

- 思路
  - 利用三個指針prv, cur, next，來進行反轉動作。
  - 投結點要獨立出來處理。

```swift
  mutating func reverse() {
    tail = head
    var prev = head
    var current = head?.next
    // 先處理頭節點
    prev?.next = nil

    while current != nil {
        // 先利用next指針紀錄current的next
        let next = current?.next
        // 進行反轉
        current?.next = prev
        // 將pre, cur繼續往前移動
        prev = current
        current = next
    }

    head = prev
  }
```

------

> Challenge 4: Create a function that takes two sorted linked lists and merges them into a single sorted linked list

- 思路
  - newHead指向當前兩個list中最小的node
  - tail指針的移動來進行合併list

```swift
func mergeSorted<T: Comparable>(_ left: LinkedList<T>,
                                _ right: LinkedList<T>) -> LinkedList<T> {
    // 先確保left & right都有數值
    guard !left.isEmpty else {
        return right
    }

    guard !right.isEmpty else {
        return left
    }

    var newHead: Node<T>?
    var tail: Node<T>?
    var currentLeft = left.head
    var currentRight = right.head

    // 找出當前最小的node
    if let leftNode = currentLeft, let rightNode = currentRight {
        if leftNode.value < rightNode.value {
            newHead = leftNode
            currentLeft = leftNode.next
        } else {
            newHead = rightNode
            currentRight = rightNode.next
        }
        tail = newHead
    }

    // 利用tail指針的next來合併list
    while let leftNode = currentLeft, let rightNode = currentRight  {
        if leftNode.value < rightNode.value {
            tail?.next = leftNode
            currentLeft = leftNode.next
        } else {
            tail?.next = rightNode
            currentRight = rightNode.next
        }

        tail = tail?.next
    }

    // 當left list還有剩
    if let leftNode = currentLeft {
        tail?.next = leftNode
    }

    // 當right list還有剩
    if let rightNode = currentRight {
        tail?.next = rightNode
    }

    // 產生新的list
    var list = LinkedList<T>()
    list.head = newHead
    list.tail = {
        while let next = tail?.next {
            tail = next
        }
        return tail
    }()

  return list
}
```

------

> Challenge 5: Create a function that removes all occurrences of a specific element from a linked list
>

- 思路
  - 一樣用兩根指針pre, cur不斷搜尋
  - 當遇到需要刪除的值就是把pre的next跳過cur, 直接指向cur的next

```swift
extension LinkedList where Value: Equatable {
  
  mutating func removeAll(_ value: Value) {

    // Edge case: 當被刪的節點是head
    while let head = head, head.value == value {
        self.head = head.next
    }

    // 斷開節點
    var prev = head
    var current = head?.next
    while let currentNode = current {
        guard currentNode.value != value else {
            // 刪除current節點
            prev?.next = currentNode.next
            current = prev?.next
            continue
        }
        prev = current
        current = current?.next
    }
    tail = prev
  }
}
```

