# Chapter 11: Tree Challenge

> Challenge: Print all the values in a tree in an order based on their level. Nodes belonging in the same level should be printed in the same line.
>

```swift
func printEachLevel<T>(for tree: TreeNode<T>) {
    // 利用queue來記錄當前level中
    var queue = Queue<TreeNode<T>>()
    // 紀錄當前queue中還剩下幾個node
    var nodeLeftInCurrentLevel = 0

    queue.enqueue(tree)

    // 透過quene把當前同個level的node抓出來
    while !queue.isEmpty {
        nodeLeftInCurrentLevel = queue.count

        while nodeLeftInCurrentLevel > 0 {
            guard let node = queue.dequeue() else { break }
            print("\(node.value) ", terminator: "")
            // 將下一層加入queue中
            node.children.forEach { queue.enqueue($0) }
            nodeLeftInCurrentLevel -= 1
        }

        // 結束裡層的while loop表示當層level結束
        print()
    }
}
```

