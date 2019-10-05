# Chapter 17: AVL Tree Challenges

> Challenge 1:  How many leaf nodes are there in a perfectly balanced tree of height 3? What about a perfectly balanced tree of height h?”

- 思路: 完美2元樹其葉子數跟高度的關係

```swift
func leafNodes(inTreeOfHeight height: Int) -> Int {
    return Int(pow(2.0, Double(height)))
}
```



------

> Challenge 2: How many nodes are there in a perfectly balanced tree of height 3? What about a perfectly balanced tree of height h?
>

- 思路: 找非葉子個數，就每層依序相加

```swift
func nodes(inTreeOfHeight height: Int) -> Int {
    var totalHeight = 0
    for currentHeight in 0...height {
        totalHeight += Int(pow(2.0, Double(currentHeight)))
    }
    return totalHeight
}
```



------

> Challenge 3 : Since there are many variants of binary trees, it makes sense to group shared functionality in a protocol. The traversal methods are a good candidate for this.
> Create a TraversableBinaryNode protocol that provides a default implementation of the traversal methods so that conforming types get these methods for free. Have AVLNode conform to this

- 思路: 這題利用Swift的protocol的概念，把所有不同的tree都擁有的相同traversal function抽離獨立出來。

```swift
protocol TraversableBinaryNode {
    associatedtype Element

    var value: Element { get }
    var leftChild: Self? { get }
    var rightChild: Self? { get }

    func traverseInOrder(visit: (Element) -> Void)
    func traversePreOrder(visit: (Element) -> Void)
    func traversePostOrder(visit: (Element) -> Void)
}

// 利用protocol extension提供default implementation
extension TraversableBinaryNode {

    func traverseInOrder(visit: (Element) -> Void) {
        leftChild?.traverseInOrder(visit: visit)
        visit(value)
        rightChild?.traverseInOrder(visit: visit)
    }

    func traversePreOrder(visit: (Element) -> Void) {
        visit(value)
        leftChild?.traversePreOrder(visit: visit)
        rightChild?.traversePreOrder(visit: visit)
    }

    func traversePostOrder(visit: (Element) -> Void) {
        leftChild?.traversePostOrder(visit: visit)
        rightChild?.traversePostOrder(visit: visit)
        visit(value)
    }  
}

// 讓AVL Tree擁有Traverse的能力
extension AVLNode: TraversableBinaryNode {}
```

