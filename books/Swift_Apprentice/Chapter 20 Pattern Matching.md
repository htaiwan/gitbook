# Chapter 20: Pattern Matching

最基本的pattern matching就是switch去mapping不同的case, 這章要學習更進階的mapping方法。

------

## 大綱

- Introducing patterns
- Basic pattern matching
  - [If and guard](#1)
  - [Switch](#2)
  - [for](#3)
- Patterns
  - [Wildcard pattern](#4)
  - [Value-binding pattern](#5)
  - [Identifier pattern](#6)
  - [Tuple pattern](#7)
  - [Enumeration case pattern](#8)
  - [Optional pattern](#9)
  - [”Is” type-casting pattern](#10)
  - [”As” type-casting pattern](#11)
- Advanced patterns
  - [Qualifying with where](#12)
  - [Chaining with commas](#13)
  - [Custom tuple](#14)
- Fun with wildcards
  - [Validate that an optional exists](#15)
  - [Organize an if-else-if](#16)
- [Expression pattern](#17)
  - [Overloading ~=](#18)
- [Key points](#19)

------

<h2 id="1">If and guard</h2>

- If 和 guard可以搭配case進行混搭

```Swift
func process(point: (x: Int, y: Int, z: Int)) -> String {
  // if 可以搭配 pattern match
  if case (0, 0, 0) = point {
    return "At origin"
  }
  return "Not at origin"
}

func process(point: (x: Int, y: Int, z: Int)) -> String {
  // guard 也可以搭配 pattern match
  guard case (0, 0, 0) = point else {
    return "Not at origin"
  }
  // guaranteed point is at the origin
  return "At origin"
}

let point = (x: 0, y: 0, z: 0)
let response = process(point: point)
```

------

<h2 id="2">Switch</h2>

- 利用switch可以搭多個pattern matching

```Swift
func process(point: (x: Int, y: Int, z: Int)) -> String {
  // 可以match range, 神奇 !
  let closeRange = -2...2
  let midRange = -5...5
  // 利用switch進行多個case的pattern match
  switch point {
  case (0, 0, 0):
    return "At origin"
  case (closeRange, closeRange, closeRange):
    return "Very close to origin"
  case (midRange, midRange, midRange):
    return "Nearby origin"
  case _:
    return "Not near origin"
  }
}
let point = (x: 15, y: 5, z: 3)
let response = process(point: point)
```

- switch使用會比if else來得更安全，因為switch會幫忙檢查所有case。

> That’s why you place the midRange condition second. Even though the midRange condition would match a closeRange value, it won’t be evaluated unless the previous condition fails
>

- Switch case, 優先case放在上面優先處理。

------

<h2 id="3">for</h2>

- Pattern match可以用來filter array。

```Swift
let groupSizes = [1, 5, 4, 6, 2, 1, 3]
// 利用case來進行filter
for case 1 in groupSizes {
  print("Found an individual")
}
```

------

<h2 id="4">Wildcard pattern</h2>

```Swift
// 利用＿進行wildcard, 表示允許任意值
if case (_, 0, 0) = coordinate {
  // x can be any value. y and z must be exactly 0.
  print("On the x-axis")
}
```

------

<h2 id="5">Value-binding pattern</h2>

```swift
// 利用let, var來找出符合pattern的某個值
if case (let x, 0, 0) = coordinate {
  print("On the x-axis at \(x)")
}
// 也可以找出符合pattern的多個值
if case let (x, y, 0) = coordinate {
  print("On the x-y plane at (\(x), \(y))")
}
```

------

<h2 id="6">Identifier pattern</h2>

- the identifier pattern is a sub-pattern of the value-binding pattern


```swift
if case (let x, 0, 0) = coordinate {
  // Identifier pattern: 
  // You’re telling the compiler, “When you find a value of (something, 0, 0), assign the something to x
  print("On the x-axis at \(x)")
}
```

------

<h2 id="7">Tuple pattern</h2>

- Tuple pattern, (something, 0, 0) =  (identifier, expression, expression).
- Tuple pattern combines many patterns into one and helps you write terse code

------

<h2 id="8">Enumeration case pattern</h2>

- associated value: 在這裡是指legs
- 只有當要使用時(print), 此時會透過value-binding把值給取出來。

```swift
enum Organism {
  case plant
  case animal(legs: Int)
}
let pet = Organism.animal(legs: 4)
switch pet {
case .animal(let legs):
  print("Potentially cuddly with \(legs) legs")
default:
  print("No chance for cuddles")
}
```

------

<h2 id="9">Optional pattern</h2>

- 用來處理當enumeration case patterns 包含nil的狀況。

```swift
let names: [String?] =
  ["Michelle", nil, "Brandon", "Christine", nil, "David"]

for case let name? in names {
  print(name) // 4 times
}
```

------

<h2 id="10">”Is” type-casting pattern</h2>

```swift
let array: [Any] = [15, "George", 2.0]
for element in array {
  switch element {
    // 利用is來判斷case的型別
  case is String:
    print("Found a string")
  default:
    print("Found something else")
  }
}
```

------

<h2 id="11">”As” type-casting pattern</h2>

- 只有當compiler找到一個object可以轉型成string, 然後compiler會將值binding到text中。

```swift
for element in array {
  switch element {
    // 利用as來進行type轉型
  case let text as String:
    print("Found a string: \(text)")
  default:
    print("Found something else")
  }
}
```

------

<h2 id="12">”Qualifying with where</h2>

- 可以利用“where”進行更近一步的filter。

```swift
enum LevelStatus {
  case complete
  case inProgress(percent: Double)
  case notStarted
}
let levels: [LevelStatus] = [.complete, .inProgress(percent: 0.9), .notStarted]
for level in levels {
  switch level {
  case .inProgress(let percent) where percent > 0.8 :
    print("Almost there!")
  case .inProgress(let percent) where percent > 0.5 :
    print("Halfway there!")
  case .inProgress(let percent) where percent > 0.2 :
    print("Made it through the beginning!")
  default:
    break
  }
}
```

------

<h2 id="13">Chaining with commas</h2>

- 可以將多個if簡化成單個if完成。

```swift
enum Number {
  case integerValue(Int)
  case doubleValue(Double)
  case booleanValue(Bool)
}
let a = 5
let b = 6
let c: Number? = .integerValue(7)
let d: Number? = .integerValue(8)

// 一般寫法，多個if
if a != b {
  if let c = c {
    if let d = d {
      if case .integerValue(let cValue) = c {
        if case .integerValue(let dValue) = d {
          if dValue > cValue {
            print("a and b are different")
            print("d is greater than c")
            print("sum: \(a + b + cValue + dValue)")
          }
        }
      }
    }
  }
}

// 利用Chaining with commas, 將多個if簡化成單個if
if a != b,
  let c = c,
  let d = d,
  case .integerValue(let cValue) = c,
  case .integerValue(let dValue) = d,
  dValue > cValue {
  print("a and b are different")
  print("d is greater than c")
  print("sum: \(a + b + cValue + dValue)")
}

```

------

<h2 id="14">Custom tuple</h2>

- 利用custom tuple進行case轉換

```swift
var username: String?
var password: String?
// Custom tuple = (username, password)
switch (username, password) {
case let (username?, password?): // 進行binding
  print("Success! User: \(username) Pass: \(password)")
case let (username?, nil):
  print("Password is missing. User: \(username)")
case let (nil, password?):
  print("Username is missing. Pass: \(password)")
case (nil, nil):
  print("Both username and password are missing")
}
```

------

<h2 id="15">Validate that an optional exists</h2>

- 檢查optional狀態

```swift
let user: String? = "Bob"
guard let _ = user else {
  print("There is no user.")
  fatalError()
}
print("User exists, but identity not needed.")
```

------

<h2 id="16">Organize an if-else-if</h2>

- 利用switch搭配pattern matching來替代if-else-if，可以讓code更加清楚。

```swift
let view = Rectangle(width: 15, height: 60, color: "Green")
switch view {
case _ where view.height < 50:
  print("Shorter than 50 units")
case _ where view.width > 20:
  print("Over 50 tall, & over 20 wide")
case _ where view.color == "Green":
  print("Over 50 tall, at most 20 wide, & green")
default:
  print("This view can't be described by this example")
}
```

------

<h2 id="17">Expression pattern</h2>

- “The expression pattern compares values with the ~= pattern matching operator”

```swift
// expression pattern寫法
let matched = (1...10 ~= 5) // true

if case 1...10 = 5 {
  print("In the range")
}
```

------

<h2 id="18">Overloading ~=</h2>

```swift
func ~=(pattern: [Int], value: Int) -> Bool {
  for i in pattern {
    if i == value {
      return true
    }
  }
  return false
}

let list = [0, 1, 2, 3]
let integer = 2

let isInArray = (list ~= integer) // true

if case list = integer {
  print("The integer is in the array")
} else {
  print("The integer is not in the array")
}
```

------

<h2 id="19">Key points</h2>

- A pattern represents the structure of a value.
- Pattern matching can help you write more readable code than the alternative logical conditions.
- Pattern matching is the only way to extract associated values from enumeration values.