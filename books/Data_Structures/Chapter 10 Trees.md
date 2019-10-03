# Chapter 10: Trees

#### 前言

Tree跟linked list有點像，只是linked list只是連接左右鄰居的兩個node，但tree可以連接多個nodes。

------

#### 大綱

- [Terminology](#1)
  - Node
  - Parent and child
  - Root
  - Leaf
- [Implementation](#2)
- [Traversal algorithms](#3)
  - Depth-first traversal
  - Level-order traversal
- [Search](#4)

------

<h2 id="1">Terminology</h2>

- 這些專用術語不多做解釋了。

------

<h2 id="2">Implementation</h2>

```swift
// 構成樹的基本資料結構
public class TreeNode<T> {
    public var value: T
    // 所連結的children
    public var children: [TreeNode] = []

    public init(_ value: T) {
        self.value = value
    }
    // 建立一個parent和child的連結
    public func add(_ child:TreeNode) {
        children.append(child)
    }
}
```



------

<h2 id="3">Traversal algorithms</h2>

- 怎樣可以拜訪樹中所有的節點，有兩種不同的拜訪方式
- Depth-first traversal

```Swift
extension TreeNode {
    public func forEachDepthFirst(visit: (TreeNode) -> Void) {
        visit(self)
        // 針對每個child在進行深度遞迴拜訪
        children.forEach {
            $0.forEachDepthFirst(visit: visit)
        }
    }
}
```

- Level-order traversal

```swift
extension TreeNode {
    public func forEachLevelOrder(visit: (TreeNode) -> Void) {
        visit(self)
        // 利用queue紀錄當前同個level的所有node
        var queue = Queue<TreeNode>()
        children.forEach { queue.enqueue($0) }
        while let node = queue.dequeue() {
            visit(node)
            // level遞迴
            node.children.forEach { queue.enqueue($0) }
        }
    }
}
```

- 不管深度或者階層式拜訪的時間複雜度都是O(n), 因為都是需要把所有node都走過一遍。

------

<h2 id="4">Search</h2>

- 既然我們有方式可以拜訪所有node，當然就可以找到特定的node。
- 時間複雜度，因為需要透過深度或者階層式拜訪來尋找，所以複雜度為O(n)

```swift
extension TreeNode where T: Equatable {
    public func search(_ value: T) -> TreeNode? {
        var result: TreeNode?
        forEachLevelOrder { node in
            if node.value == value {
                result = node
            }
        }
        return result
    }
}
```

