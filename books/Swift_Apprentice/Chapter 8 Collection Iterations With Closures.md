# Chapter 8: Collection Iterations With Closures 

------

## 大綱

- [Closure basics](#1)
  - [Shorthand syntax](#2)
  - [Closures with no return value](#3)
  - [Capturing from the enclosing scope](#4)
- [Custom sorting with closures](#5)
- [Iterating over collections with closures](#6)
- [Key points](#7)

------

<h2 id="1">Closure basics</h2>

- Closures are so named because they have the ability to “**close over**” the variables and constants within the closure’s own scope. 
  - This simply means that a closure can access, store and manipulate the value of any variable or constant from the surrounding context. 
  - Variables and constants used within the body of a closure are said to have been **captured** by the closure.

```swift
var multiplyClosure = { (a: Int, b: Int) -> Int in
  return a * b
}
let result = multiplyClosure(4, 2)
```



------

<h2 id="2">Shorthand syntax</h2>

```swift
var multiplyClosure = { (a: Int, b: Int) -> Int in
  return a * b
}

// 簡化return
multiplyClosure = { (a: Int, b: Int) -> Int in
  a * b
}

// 簡化型別
multiplyClosure = { (a, b) in
  a * b
}

// 簡化參數
multiplyClosure = {
  $0 * $1
}
```

```swift
// 完整寫法
func operateOnNumbers(_ a: Int, _ b: Int, operation: (Int, Int) -> Int) -> Int {
      let result = operation(a, b)
      print(result)
      return result
    }

let addClosure = { (a: Int, b: Int) in
  a + b
}
operateOnNumbers(4, 2, operation: addClosure)

// 簡化1
operateOnNumbers(4, 2, operation: { (a: Int, b: Int) -> Int in
  return a + b
})

// 簡化2
operateOnNumbers(4, 2, operation: { $0 + $1 })

// 簡化3
operateOnNumbers(4, 2, operation: +)

// 簡化4
operateOnNumbers(4, 2) {
  $0 + $1
}
```



------

<h2 id="3">Closures with no return value</h2>

- Just like functions, closures aren’t required to do these things.
  - Void is actually just a typealias for (). This means you could have written () -> Void as () -> (). 


```swift
let voidClosure: () -> Void = {
  print("Swift Apprentice is awesome!")
}
voidClosure()
```

------

<h2 id="4">Capturing from the enclosing scope</h2>

```swift
func countingClosure() -> () -> Int {
  var counter = 0
  let incrementCounter: () -> Int = {
    counter += 1
    return counter
  }
  return incrementCounter
}

let counter1 = countingClosure()
let counter2 = countingClosure()

// The two counters created by the function are mutually exclusive and count independently
counter1() // 1
counter2() // 1
counter1() // 2
counter1() // 3
counter2() // 2
```



------

<h2 id="5">Custom sorting with closures</h2>

```swift
// SORTING
let names = ["ZZZZZZ", "BB", "A", "CCCC", "EEEEE"]
names.sorted()

let sortedByLength = names.sorted {
  $0.count > $1.count
}
sortedByLength // ["A", "BB", "CCCC", "EEEEE", "ZZZZZZ"]
```

------

<h2 id="6">Iterating over collections with closures</h2>

- In Swift, collections implement some very handy features often associated with **functional programming**.

```swift
let values = [1, 2, 3, 4, 5, 6]
values.forEach {
  print("\($0): \($0*$0)")
}

var prices = [1.5, 10, 4.99, 2.30, 8.19]

let largePrices = prices.filter {
  return $0 > 5
}

let largePrice = prices.first {
  $0 > 5
}

let salePrices = prices.map {
  return $0 * 0.9
}

let userInput = ["0", "11", "haha", "42"]

let numbers1 = userInput.map {
  Int($0)
}

let numbers2 = userInput.compactMap {
  Int($0)
}

let sum = prices.reduce(0) {
  return $0 + $1
}

let stock = [1.5: 5, 10: 2, 4.99: 20, 2.30: 5, 8.19: 30]
let stockSum = stock.reduce(0) {
  return $0 + $1.key * Double($1.value)
}

let farmAnimals = ["🐎": 5, "🐄": 10, "🐑": 50, "🐶": 1]
let allAnimals = farmAnimals.reduce(into: []) {
  (result, this: (key: String, value: Int)) in
  for _ in 0 ..< this.value {
    result.append(this.key)
  }
}

let removeFirst = prices.dropFirst()
let removeFirstTwo = prices.dropFirst(2)

let removeLast = prices.dropLast()
let removeLastTwo = prices.dropLast(2)

let firstTwo = prices.prefix(2)
let lastTwo = prices.suffix(2)

prices.removeAll()
```

------

<h2 id="7">Key points</h2>

- Closures are functions without names. They can be assigned to variables and passed as parameters to functions.
  Closures have shorthand syntax that makes them a lot easier to use than other functions.
- A closure can capture the variables and constants from its surrounding context.
- A closure can be used to direct how a collection is sorted.
- A handy set of functions exists on collections that you can use to iterate over a collection and transform it. Transforms comprise mapping each element to a new value, filtering out certain values and reducing the collection down to a single value.