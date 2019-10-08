# Chapter 24: Priority Queue

#### 前言

Priority Queue顧名思義是一個Queue, 跟Queue有同樣的FIFO的概念，只是Priority Queue就是Priority高的element先出來。

------

#### 大綱

- Applications
- Common operations
- Implementation
- Testing

------

#### Applications

- Dijkstra’s algorithm, which uses a priority queue to calculate the minimum cost.
- A* pathfinding algorithm, which uses a priority queue to track the unexplored routes that will produce the path with the shortest length.
- Heap sort, which can be implemented using a priority queue.
- Huffman coding that builds a compression tree. A min-priority queue is used to repeatedly find two nodes with the smallest frequency that do not yet have a parent node.

------

#### Common operations

- 跟Queue擁有一樣相同的operation。

```swift
public protocol Queue {
  associatedtype Element
  mutating func enqueue(_ element: Element) -> Bool
  mutating func dequeue() -> Element?
  var isEmpty: Bool { get }
  var peek: Element? { get }
}
```

------

#### Implementation

- Priority Queue可以用三種不同資料結構來達成
  - Sorted array
    - O(1): 找最大最小
    - O(n): 插入
  - Balanced binary search tree
    - O(log n) : 找最大最小
    - O(log n)：插入
  - Heap
    - All heap operations are O(log n) 
    - except extracting the min value from a min priority heap is a lightning fast O(1).
    - Likewise, extracting the max value from a max priority heap is also O(1)

```swift
public struct PriorityQueue<Element: Equatable>: Queue { // 1

    private var heap: Heap<Element> // 2

    public init(sort: @escaping (Element, Element) -> Bool,
         elements: [Element] = []) { // 3
        heap = Heap(sort: sort, elements: elements)
    }

    public var isEmpty: Bool {
        return heap.isEmpty
    }

    public var peek: Element? {
        return heap.peek()
    }

    public mutating func enqueue(_ element: Element) -> Bool { // 1
        heap.insert(element)
        return true
    }

    public mutating func dequeue() -> Element? { // 2
        return heap.remove()
    }
```

