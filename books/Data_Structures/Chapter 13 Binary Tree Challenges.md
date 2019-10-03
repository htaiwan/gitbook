# Chapter 13: Binary Tree Challenges

> Challenge 1
> Given a binary tree, find the height of the tree.



```swift
func height<T>(of node: BinaryNode<T>?) -> Int {
    guard let node = node else {
        return -1
    }

    return 1 + max(height(of: node.leftChild), height(of: node.rightChild))
}
```



------

> Challenge 2
> Your task is to devise a way to serialize a binary tree into an array, and a way to deserialize the array back into the same binary tree.



```swift
extension BinaryNode {

    public func traversePreOrder(visit: (Element?) -> Void) {
        visit(value)
        if let leftChild = leftChild {
            leftChild.traversePreOrder(visit: visit)
        } else {
            // 這些nil node都要記錄下來，這樣才能正確serialization ＆ deserialization.
            visit(nil)
        }
        if let rightChild = rightChild {
            rightChild.traversePreOrder(visit: visit)
        } else {
            visit(nil)
        }
    }

}

func serialize<T>(_ node: BinaryNode<T>) -> [T?] {
    var array: [T?] = []
    node.traversePreOrder { array.append($0) }

    return array
}

func deserialize<T>(_ array: inout [T?]) -> BinaryNode<T>? {

    // 注意: removeFirst是O(n)
    // 因為每個element都需要向前位移
    guard let value = array.removeFirst() else {
        return nil
    }

    // 遞迴n個node是O(n), 每次遞迴都要removeFirst, 所以總共是O(n^2)
    let node = BinaryNode(value: value)
    node.leftChild = deserialize(&array)
    node.rightChild = deserialize(&array)

    return node
}
```

- 優化版

```swift
 func deserialize<T>(_ array: inout [T?]) -> BinaryNode<T>? {
 
-    // 注意: removeFirst是O(n)
-    // 因為每個element都需要向前位移
-    guard let value = array.removeFirst() else {
+    // 注意: removeLast是O(1)
+    guard let value = array.removeLast() else {
         return nil
     }
 
-    // 遞迴n個node是O(n), 每次遞迴都要removeFirst, 所以總共是O(n^2)
+    // 遞迴n個node是O(n), 每次遞迴都要removeLast, 所以總共是O(n)
     let node = BinaryNode(value: value)
     node.leftChild = deserialize(&array)
     node.rightChild = deserialize(&array)

  
  func deserialize<T>(_ array: [T?]) -> BinaryNode<T>? {
    // 優化技巧
    var reversed = Array(array.reversed())

    return deserialize(&reversed)
}

```

