# Chapter 9: Challenges: Queue Data Structure

> Challenge 1: Explain the difference between a stack and a queue. Provide two real-life examples for each data structure.
>

- Queues have a behavior of first-in-first-out
  - Line in a movie theatre
  - Printer
- Stacks have a behavior of last-in-first-out
  - Stack of plates
  - Undo functionality

------

> Challenge 2



------

> Challenge 3: Imagine that you are playing a game of Monopoly with your friends. The problem is that everyone always forget whose turn it is! Create a Monopoly organizer that always tells you whose turn it is. Below is a protocol that you can conform to
>

```Swift
extension QueueArray: BoardGameManager {

    public typealias Player = T

    public mutating func nextPlayer() -> T? {
        // 大富翁的順序FIFO，符合queue本質
        guard let person = dequeue() else {
            return nil
        }
        // 把next player再次加入queue中，等待下次循環
        enqueue(person)

        return person
    }
}
```



------

> Challenge 4: Implement a method to reverse the contents of a queue
>

```Swift
 func reversed() -> QueueArray {
     // replace the body of this method
    var queue = self
    var stack = Stack<T>()

    // 把queue元素已出來放到stack中
    while let element = queue.dequeue() {
        stack.push(element)
    }

    // 把stack元素放回queue中
    while let element = stack.pop() {
        queue.enqueue(element)
    }

    return queue // return a copy of the reversed queue
  }
```

