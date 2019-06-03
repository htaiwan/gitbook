# Chapter 4: Advanced Flow Control

------

## 大綱

- [Countable ranges](#1)
  - [A Random Interlude](#2)
- [For loops](#3)
  - Continue and labeled statements
- [Switch statements](#4)
  - Advanced switch statements
  - Partial matching
- [Key points](#5)

------

<h2 id="1">Countable ranges</h2>

```Swift
let closedRange = 0...5

let halfOpenRange = 0..<5
```



------

<h2 id="2">A Random Interlude</h2>

```Swift
while Int.random(in: 1...6) != 6 {
  print("Not a six")
}
```



------

<h2 id="3">For loops</h2>

```Swift
let count = 10

// Triangle numbers
var sum = 0
for i in 1...count {
  sum += i
}
sum

// Fibonacci
sum = 1
var lastSum = 0
for _ in 0..<count {
  let temp = sum
  sum = sum + lastSum
  lastSum = temp
}
sum

// Sum odd numbers
sum = 0
for i in 1...count where i % 2 == 1 {
  sum += i
}
sum
```



------

<h2 id="4">Switch statements</h2>

```Swift
let number = 10

switch number {
case 0:
  print("Zero")
default:
  print("Non-zero")
}

switch number {
case 10:
  print("It's ten!")
default:
  break
}

let string = "Dog"
switch string {
case "Cat", "Dog":
  print("Animal is a house pet.")
default:
  print("Animal is not a house pet.")
}

switch number {
case _ where number % 2 == 0:
  print("Even")
default:
  print("Odd")
}

let coordinates = (x: 3, y: 2, z: 5)

// pattern matching
switch coordinates {
case (0, 0, 0):
  print("Origin")
case (_, 0, 0):
  print("On the x-axis.")
case (0, _, 0):
  print("On the y-axis.")
case (0, 0, _):
  print("On the z-axis.")
default:
  print("Somewhere in space")
}

// pattern matching, binding value
switch coordinates {
case (0, 0, 0):
  print("Origin")
case (let x, 0, 0):
  print("On the x-axis at x = \(x)")
case (0, let y, 0):
  print("On the y-axis at y = \(y)")
case (0, 0, let z):
  print("On the z-axis at z = \(z)")
case let (x, y, z):
  print("Somewhere in space at x = \(x), y = \(y), z = \(z)")
}

switch coordinates {
case let (x, y, _) where y == x:
  print("Along the y = x line.")
case let (x, y, _) where y == x * x:
  print("Along the y = x^2 line.")
default:
  break
}
```



------

<h2 id="5">Key points</h2>

- You can use ranges to create a sequence of numbers, incrementing to move from one value to another.
- Closed ranges include both the start and end values.
- Half-open ranges include the start value and stop one before the end value.
- For loops allow you to iterate over a range.
- The continue statement lets you finish the current iteration of a loop and begin the next iteration.
- Labeled statements let you use break and continue on an outer loop.
- You use switch statements to decide which code to run depending on the value of a variable or constant.
- The power of a switch statement comes from leveraging **pattern matching** to compare values using complex rules.