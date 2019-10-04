# Chapter 15: Binary Search Tree Challenges

> Challenge 1:  Create a function that checks if a binary tree is a binary search tree.

- 思路: 暴力法就是檢查每個node是否都符合BST的定義。
  - T: O(n), 每個node都樣檢查

```swift
extension BinaryNode where Element: Comparable {

    var isBinarySearchTree: Bool {
        return isBST(self, min: nil, max: nil)
    }

    private func isBST(_ tree: BinaryNode<Element>?, min: Element?, max: Element?) -> Bool {
        guard let tree = tree else {
            // 空tree也是BST一種
            return true
        }

        // 進行node特質檢查
        if let min = min, tree.value <= min {
            return false
        } else if let max = max, tree.value > max {
            return false
        }

        // 遞迴檢查每個node
        return isBST(tree.leftChild, min: min, max: tree.value) && isBST(tree.rightChild, min: tree.value, max: max)
    }
}
```



> Challenge 2: The binary search tree currently lacks Equatable conformance. Your challenge is to conform adopt the Equatable protocol.

- 思路: 兩棵樹應該有相同的數量，相同的順序。

```swift
extension BinarySearchTree: Equatable {
    public static func == (lhs: BinarySearchTree, rhs: BinarySearchTree) -> Bool {
        return isEqual(lhs.root, rhs.root)
    }

    private static func isEqual<Element: Equatable>(_ node1: BinaryNode<Element>?, _ node2: BinaryNode<Element>?) -> Bool {

        guard let leftNode = node1, let rightNode = node2 else {
            // 表示兩個樹的個數一定不相同，或者有可能剛好都沒有
            return node1 == nil && node2 == nil
        }

        return leftNode.value == rightNode.value &&
        isEqual(leftNode.rightChild, rightNode.rightChild) &&
        isEqual(rightNode.leftChild, rightNode.rightChild)
    }
```



> Challenge 3: Create a method that checks if the current tree contains all the elements of another tree.

- 思路: 另外一棵樹的所有node應該都被包含到另外的樹中

```swift
    public func contains(_ subtree: BinarySearchTree) -> Bool {

        var set: Set<Element> = []
        root?.traverseInOrder { set.insert($0) }

        var isEqual = true

        // subtree中的所有node應該都在set中被找到
        subtree.root?.traverseInOrder {
            isEqual = isEqual && set.contains($0)
        }

        return isEqual
    }

}
```

