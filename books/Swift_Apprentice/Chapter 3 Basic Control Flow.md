# Chapter 3: Basic Control Flow

------

## 大綱

- [Comparison operators](#1)
  - Boolean operators
  - Boolean logic
  - String equality
  - Toggling a Bool
- [The if statement](#2)
  - Short circuiting
  - Encapsulating variables
- [The ternary conditional operator](#3)
- [Loops](#4)
  - While loops
  - Repeat-while loops
  - Breaking out of a loop
- [Key points](#5)

------

<h2 id="1">Comparison operators</h2>

```swift
// Boolean operators
let yes = true
let no = false

// Boolean logic
let doesOneEqualTwo = (1 == 2)
let doesOneNotEqualTwo = (1 != 2)
let alsoTrue = !(1 == 2)
let isOneGreaterThanTwo = (1 > 2)
let isOneLessThanTwo = (1 < 2)

let and = true && true
let or = true || false

let andTrue = 1 < 2 && 4 > 3
let andFalse = 1 < 2 && 3 > 4

let orTrue = 1 < 2 || 3 > 4
let orFalse = 1 == 2 || 3 == 4

let andOr = (1 < 2 && 3 > 4) || 1 < 4

// String equality
let guess = "dog"
let dogEqualsCat = guess == "cat"

let order = "cat" < "dog"

// Toggling a Bool
var switchState = true
switchState.toggle()  // false
switchState.toggle()  // true
```



------

<h2 id="2">The if statement</h2>

```Swift
if 2 > 1 {
  print("Yes, 2 is greater than 1.")
}

let animal = "Fox"
if animal == "Cat" || animal == "Dog" {
  print("Animal is a house pet.")
} else {
  print("Animal is not a house pet.")
}

let hourOfDay = 12
var timeOfDay = ""

if hourOfDay < 6 {
  timeOfDay = "Early morning"
} else if hourOfDay < 12 {
  timeOfDay = "Morning"
} else if hourOfDay < 17 {
  timeOfDay = "Afternoon"
} else if hourOfDay < 20 {
  timeOfDay = "Evening"
} else if hourOfDay < 24 {
  timeOfDay = "Late evening"
} else {
  timeOfDay = "INVALID HOUR!"
}
print(timeOfDay)

let name = "Matt Galloway"

if 1 > 2 && name == "Matt Galloway" {
  // ...
}

if 1 < 2 || name == "Matt Galloway" {
  // ...
}

// Encapsulating variables
var hoursWorked = 45

var price = 0
if hoursWorked > 40 {
  let hoursOver40 = hoursWorked - 40
  price += hoursOver40 * 50
  hoursWorked -= hoursOver40
}
price += hoursWorked * 25

print(price) // 1250
```



------

<h2 id="3">The ternary conditional operator</h2>

- (<CONDITION>) ? <TRUE VALUE> : <FALSE VALUE>

```Swift
let a = 5
let b = 10

let min = a < b ? a : b
let max = a > b ? a : b
```

------

<h2 id="4">Loops</h2>

```swift
var sum = 1
while sum < 1000 {
  sum = sum + (sum + 1)
}
sum // 1023

sum = 1
repeat {
  sum = sum + (sum + 1)
} while sum < 1000
sum // 1023

sum = 1
while sum < 1 {
  sum = sum + (sum + 1)
}
sum // 1

// 至少執行一次loop
sum = 1
repeat {
  sum = sum + (sum + 1)
} while sum < 1
sum  // 3

// break loop
sum = 1
while true {
  sum = sum + (sum + 1)
  if sum >= 1000 {
    break
  }
}
sum
```



------

<h2 id="5">Key points</h2>

- You use the Boolean data type Bool to represent true and false.
- The comparison operators, all of which return a Boolean, are:
  Equal: `==`
  Not equal: `!=`
  Less than: `<`
  Greater than: `>`
  Less than or equal: `<=`
  Greater than or equal: `>=`
- You can use Boolean logic to combine comparison conditions.
- You use if statements to make simple decisions based on a condition.
- You use else and else-if within an if statement to extend the decision-making beyond a single condition.
- Short circuiting ensures that only the minimal required parts of a Boolean expression are evaluated.
- You can use the ternary operator in place of simple if statements.
- Variables and constants belong to a certain scope, beyond which you cannot use them. A scope inherits visible variables and constants from its parent.
- while loops allow you to perform a certain task a number of times until a condition is met.
- The break statement lets you break out of a loop.