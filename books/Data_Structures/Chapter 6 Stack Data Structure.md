# Chapter 6: Stack Data Structure

#### 前言

談談另外一種常見的基本資料結構，以及實作常見的操作。

------

#### 大綱

- [Stack operations](#1)
- [Implementation](#2)
- [push and pop operations](#3)
- [Non-essential operations](#4)
- [Less is more](#5)

------

<h2 id="1">Stack operations</h2>

- Stack的最大特色就是LIFO(Last In First Out)
- 兩個基本操作push, pop
- Stack應用的地方
  - iOS - navigation stack 用來push和pop畫面。
  - Memory allocation uses stacks at the architectural level. Memory for local variables is also managed using a stack
  - Search and conquer algorithms, such as finding a path out of a maze, use stacks to facilitate backtracking

------

<h2 id="2">Implementation</h2>

- 實作Stack結構

```Swift
public struct Stack<Element> {

    private var storage: [Element] = []

    public init() {}

}

extension Stack: CustomStringConvertible {

    public var description: String {
        let topDivider = "----頂端----\n"
        let bottomDivder = "\n-----------"

        let stackElements = storage
            .map { "\($0)" }
            .reversed()
            .joined(separator: "\n")

        return topDivider + stackElements + bottomDivder
    }
}
```

------

<h2 id="3">push and pop operations</h2>

```Swift
    public mutating func push(_ element: Element) {
        storage.append(element)
      }

    @discardableResult
    public mutating func pop() -> Element? {
        return storage.popLast()
    }
```



------

<h2 id="4">Non-essential operations </h2>

```Swift
    public func peek() -> Element? {
        return storage.last
    }

    public var isEmpty: Bool {
        return peek() == nil
    }
```



------

<h2 id="5">Less is more</h2>

- You may have wondered if you could adopt the Swift collection protocols for the stack. 

  > A stack’s purpose is to limit the number of ways to access your data, and adopting protocols such as Collection would go against this goal by exposing all the elements via iterators and the subscript. In this case, less is more!

- 當然我們可以像LinkedList實作Swift的基本collections protocol, 讓Stack變得更加有能力，但Stack的存在就是要嚴格限制data的存取的方式，因此對Stack來說擁有許多不同的操作方式，並不是Stack需要的。