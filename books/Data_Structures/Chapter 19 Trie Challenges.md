# Chapter 19: Trie Challenges

> Challenge: 
>
> The current implementation of the trie is missing some notable operations. Your task for this challenge is to augment the current implementation of the trie by adding the following:
> A collections property that returns all the collections in the trie.
> A count property that tells you how many collections are currently in the trie.
> A isEmpty property that returns true if the trie is empty, false otherwise.

思路: 宣告一個set來記錄每一次insert或remove的結果

```swift
// 因為要將CollectionType加入set中，所以要將CollectionType進一步至限制成hashable
// CollectionType.Element是Hashable跟CollectionType本身是不是Hashable是兩回事
public class Trie<CollectionType: Collection & Hashable> where CollectionType.Element: Hashable {
  
  public typealias Node = TrieNode<CollectionType.Element>
  
  private let root = Node(key: nil, parent: nil)

    // 利用set來記錄當前trie中所有的key
    public private(set) var collections: Set<CollectionType> = []

    public var count: Int {
        return collections.count
    }

    public var isEmpty: Bool {
        return collections.isEmpty
    }

  ///////////////////////////
  
    current.isTerminating = true
    // 完成一次插入
    collections.insert(collection)
  
   
  ///////////////////////////

    current.isTerminating = false
      // 完成一次刪除
    collections.remove(collection)


```

