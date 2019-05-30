# Chapter 15: Enumerations

------

## 大綱

- Your first enumeration
  - [Declaring an enumeration](#1)
  - [Deciphering an enumeration in a function](#2)
  - [Code completion prevents typos](#3)
- [Raw values](#4)
  - [Accessing the raw value](#5)
  - [Initializing with the raw value](#6)
  - [String raw values](#7)
  - [Unordered raw values](#8)
- [Associated values](#9)
- [Enumeration as state machine](#10)
- [Iterating through all cases](#11)
- [Enumerations without any cases](#12)
- [Optionals](#13)
- [Key points](#14)

------

<h2 id="1">Declaring an enumeration</h2>

```swift
// 第一種宣告方式
enum Month {
  case january
  case february
  case march
  case april
  case may
  case june
  case july
  case august
  case september
  case october
  case november
  case december
}
// 第二種, 全部寫成一行, 用逗號分開
enum Month {
  case january, february, march, april, may, june, july, august,
  september, october, november, december
}
```



------

<h2 id="2">Deciphering an enumeration in a function</h2>

- 如何在function中使用enum 

- enum中所有case都需要被處理，不然compiler會報錯

  - 少用default

  > There is another **huge benefit to getting rid of the default**. If in a future update someone added .undecember or .duodecember to the Month enumeration, **the compiler would automatically flag this** and any other switch statement as being non-exhaustive, allowing you to handle this specific case.
  >

```swift
func semester(for month: Month) -> String {
  switch month {
  case .august, .september, .october, .november, .december:
    return "Autumn"
  case .january, .february, .march, .april, .may:
    return "Spring"
  case .june, .july:
    return "Summer"
  }
}

var month = Month.april
semester(for: month) // "Spring"
month = .september
semester(for: month) // "Autumn"
```

------

<h2 id="3">Code completion prevents typos</h2>

- you’ll never have a typo in your member values. Xcode provides code completion:


------

<h2 id="4">Raw values</h2>

- Swift enum values **are not backed by integers as a default**. That means january is itself the value
- you can associate a raw value with each enumeration case simply by **declaring the raw value in the enumeration declaration**


```swift
// the compiler will automatically increment the values if you provide the first one and leave out the rest
enum Month: Int {
  case january = 1, february, march, april, may, june, july, august, september, october, november, december
}
```

------

<h2 id="5">Accessing the raw value</h2>

```swift
func monthsUntilWinterBreak(from month: Month) -> Int {
  return Month.december.rawValue - month.rawValue
}
```

------

<h2 id="6">Initializing with the raw value</h2>

- use the raw value to instantiate an enumeration value with an initializer. 
- There’s no guarantee that the raw value you submitted exists in the enumeration, so **the initializer returns an optional.** 

```swift
let fifthMonth = Month(rawValue: 5) // 回傳是optional
monthsUntilWinterBreak(from: fifthMonth)  // Error: not unwrapped
```

------

<h2 id="7">String raw values</h2>

```swift
// 1 The enumeration sports a String raw value type
enum Icon: String {
  case music
  case sports
  case weather
  var filename: String {
    // 2 Calling rawValue inside the enumeration definition is equivalent to calling self.rawValue
    return "\(rawValue).png"
  }
}
let icon = Icon.weather
icon.filename
```

------

<h2 id="8">Unordered raw values</h2>

```swift
enum Coin: Int {
  case penny = 1
  case nickel = 5
  case dime = 10
  case quarter = 25
}

let coin = Coin.quarter
coin.rawValue
```



------

<h2 id="9">Associated values</h2>

- 這個是enum中最強的特性，也是讓enum變得無敵好用的工具。
- 實用時機
  - Each enumeration case has zero or more associated values.
  - The associated values for each enumeration case have their own data type.
  - You can define associated values with label names like you would for named function parameters.

```swift
// 先定義enum with Associated values 
enum WithdrawalResult {
  case success(newBalance: Int)
  case error(message: String)
}

// 在fun中處理各種enum狀況
func withdraw(amount: Int) -> WithdrawalResult {
  if amount <= balance {
    balance -= amount
    return .success(newBalance: balance)
  } else {
    return .error(message: "Not enough money!")
  }
}
// 使用
let result = withdraw(amount: 99)

switch result {
case .success(let newBalance):
  print("Your new balance is: \(newBalance)")
case .error(let message):
  print(message)
}
```

```swift
enum HTTPMethod {
  case get
  case post(body: String)
}

let request = HTTPMethod.post(body: "Hi there")
// 利用guard來解出body
guard case .post(let body) = request else {
  fatalError("No message was posted")
}
print(body)
```



------

<h2 id="10">Enumeration as state machine</h2>

- a state machine, meaning it can only ever be a single enumeration value at a time, never more

```swift
enum TrafficLight {
  case red, yellow, green
}
let trafficLight = TrafficLight.red
```



------

<h2 id="11">Iterating through all cases</h2>

- When you add : **CaseIterable** your enumeration gains a class method called allCases

```swift
enum Pet: CaseIterable {
  case cat, dog, bird, turtle, fish, hamster
}

for pet in Pet.allCases {
  print(pet)
}
```

------

<h2 id="12">Enumerations without any cases</h2>

- Enumerations are quite powerful. They can do most everything a structure can, including having custom initializers, computed properties and methods

```swift
struct Math {
  static func factorial(of number: Int) -> Int {
    return (1...number).reduce(1, *)
  }
}
let factorial = Math.factorial(of: 6) // 720”
let math = Math() // 不會有error, 但這個object並沒有任何意義, 不需要產生

// 像上面struct中只有一個static method的case, 應該改用enum比較恰當
enum Math {
  static func factorial(of number: Int) -> Int {
    return (1...number).reduce(1, *)
  }
}
let factorial = Math.factorial(of: 6)
let math = Math() // ERROR: No accessible initializers, compiler會幫我們擋住這種無所謂的object
```



------

<h2 id="13">Optionals</h2>

- Optionals are really enumerations with two cases:
  - .none means there’s no value.
  - .some means there is a value, which is attached to the enumeration case as an associated value.

```swift
var age: Int?
age = 17
age = nil

switch age {
case .none:
  print("No value") // 此時age = nil, 會對應到 .none
case .some(let value):
  print("Got a value: \(value)")
}

let optionalNil: Int? = .none
optionalNil == nil    // true
optionalNil == .none  // true
```



------

<h2 id="14">Key points</h2>

- An **enumeration** is a list of mutually exclusive cases that define a common type.
- Enumerations provide a type-safe alternative to old-fashioned integer values.
- You can use enumerations to handle responses, store state and encapsulate values.
- **CaseIterable** lets you loop through an enumeration with allCases.
- **Uninhabited enumerations** **can be used as namespaces** and **prevent the creation of instances**.