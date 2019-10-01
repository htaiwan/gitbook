# Chapter 7: Stack Challenges

> Challenge 1: Print a linked list in reverse without using recursion. Given a linked list, print the nodes in reverse order. You should not use recursion to solve this problem.
>

- 思路
  - 利用Stack來進行reverse

```swift
func printInReverse<T>(_ list: LinkedList<T>) {
    var current = list.head
    var stack = Stack<T>()

    while let node = current {
        stack.push(node.value)
        current = node.next
    }

    while let value = stack.pop() {
        print(value)
    }
}

```



------

> Challenge 2: Check for balanced parentheses. Given a string, check if there are ( and ) characters, and return true if the parentheses in the string are balanced
>

- 思路
  - 當遇到左括號push到stack中，遇到右括號就進行pop

```Swift
func checkParentheses(_ string: String) -> Bool {
    var stack = Stack<Character>()

    for character in string {
        if character == "(" {
            stack.push(character)
        } else if character == ")" {
            if stack.isEmpty {
                return false
            } else {
                stack.pop()
            }
        }
    }

    return stack.isEmpty

```

