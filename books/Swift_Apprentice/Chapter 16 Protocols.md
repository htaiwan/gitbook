# Chapter 16: Protocols

------

## 大綱

- Introducing protocols
  - [Protocol syntax](#1)
  - [Methods in protocols](#2)
  - [Properties in protocols](#3)
  - [Initializers in protocols](#4)
  - [Protocol inheritance](#5)
- Implementing protocols
  - [Implementing properties](#6)
  - [Associated types in protocols](#7)
  - [Implementing multiple protocols](#8)
  - [Protocol composition](#9)
  - [Extensions and protocol conformance](#10)
  - [Requiring reference semantics](#11)
  - [Protocols: More than bags of syntax](#12)
- Protocols in the standard library
  - [Equatable](#13)
  - [Comparable](#14)
- [“Free” functions](#15)
- Other useful protocols
  - [Hashable](#16)
  - [CustomStringConvertible](#17)
- [Key points](#18)

------

<h2 id="1">Protocol syntax</h2>

- A protocol can be adopted by a class, struct or enum
  - Once a type implements all members of a protocol, the type is said to **conform** to the protocol.


```swift
// 宣告一個protocol
protocol Vehicle {
  func accelerate()
  func stop()
}
// conform: 實作一個protocol
class Unicycle: Vehicle {
  var peddling = false
    
  func accelerate() {
    peddling = true
  }
    
  func stop() {
    peddling = false
  }
}
```

------

<h2 id="2">Methods in protocols</h2>

- You don’t, and in fact can’t, define any implementation for the methods
- methods defined in protocols can’t contain default parameters

```swift
protocol OptionalDirectionVehicle {
  // Build error!
  func turn(_ direction: Direction = .left)
}
```

------

<h2 id="3">Properties in protocols</h2>

- you must explicitly mark them as **get or get set**, somewhat similar to the way you declare computed properties”

```swift
protocol VehicleProperties {
  var weight: Int {get}
  var name: String { get set }
}
```

------

<h2 id="4">Initializers in protocols</h2>

- If you conform to a protocol with required initializers using a class type, those initializers must use the **required** keyword:

```swift
protocol Account {
  var value: Double { get set }
  init(initialAmount: Double)
  init?(transferAccount: Account)
}

class BitcoinAccount: Account {
  var value: Double
  // 實作Account protocol的init
  required init(initialAmount: Double) {
    value = initialAmount
  }
  
   // 實作Account protocol的init
  required init?(transferAccount: Account) {
    guard transferAccount.value > 0.0 else {
      return nil
    }
    value = transferAccount.value
  }
}
```

------

<h2 id="5">Protocol inheritance</h2>

- you can have protocols that inherit from other protocols, much like you can have classes that inherit from other classes

```swift
protocol WheeledVehicle: Vehicle {
  var numberOfWheels: Int { get }
  var wheelSize: Double { get set }
}
```

------

<h2 id="6">Implementing properties</h2>

- implementing a **get** requirement are:
  - A constant stored property.
  - A variable stored property.
  - A read-only computed property.
  - A read-write computed property.
- Your choices for implementing both a **get and a set** property are limited to **a variable stored property** or **a read-write computed property**


```swift
class Bike: WheeledVehicle {
  // get: A constant stored property
  let numberOfWheels = 2
  // get, ser: a variable stored property
  var wheelSize = 16.0

  var peddling = false
  var brakesApplied = false

  func accelerate() {
    peddling = true
    brakesApplied = false
  }

  func stop() {
    peddling = false
    brakesApplied = true
  }
}
```

------

<h2 id="7">Associated types in protocols</h2>

```swift
protocol WeightCalculatable {
  associatedtype WeightType
  var weight: WeightType { get }
}

class HeavyThing: WeightCalculatable {
  // This heavy thing only needs integer accuracy
  typealias WeightType = Int
  
  var weight: Int {
    return 100
  }
}

class LightThing: WeightCalculatable {
  // This light thing needs decimal places
  typealias WeightType = Double
  
  var weight: Double {
    return 0.0025
  }
}
```

------

<h2 id="8">Implementing multiple protocols</h2>

- A class can only inherit from a single class — this is the property of “single inheritance”. In contrast, a class can adopt as many protocols as you’d like!”


------

<h2 id="9">Protocol composition</h2>

- 這招很好用, 可以將不同protocol的內容組合再一起。

```swift
func roundAndRound(transportation: Vehicle & Wheeled) {
  // stop是Vehicle的方法
  transportation.stop()
  // numberOfWheels是Wheeled的屬性
  print("The brakes are being applied to \(transportation.numberOfWheels) wheels.")
}

// Bike是實作Vehicle & Wheeled兩個protocol
roundAndRound(transportation: Bike())
```

------

<h2 id="10">Extensions and protocol conformance</h2>

- Extensions可以擴充任何type, 即使是standard library. Ex String.

```swift
protocol Reflective {
  var typeName: String { get }
}

extension String: Reflective {
  var typeName: String {
    return "I'm a String"
  }
}

let title = "Swift Apprentice!"
title.typeName // I'm a String
```

- Extensions可以讓我們將protocol進行分群。

```swift
class AnotherBike: Wheeled {
  var peddling = false
  let numberOfWheels = 2
  var wheelSize = 16.0
}

// 利用extension將相近功能的protocol的實作放在一起。
extension AnotherBike: Vehicle {
  func accelerate() {
    peddling = true
  }
    
  func stop() {
    peddling = false
  }
}
```



------

<h2 id="11">Requiring reference semantics</h2>

- 不同性質的type實作的protocol, 也具有不同semantics
- If you have an instance of a class or struct assigned to a variable of a protocol type, it will express value or reference semantics that match the type it was defined as.

```swift
protocol Named {
  var name: String { get set }
}

class ClassyName: Named {
  // 此時name是reference semantics
  var name: String

  init(name: String) {
   self.name = name
  }
}

struct StructyName: Named {
  // 此時name是value semantics
  var name: String
}

// 測試reference semantics
var named: Named = ClassyName(name: "Classy")
var copy = named

named.name = "Still Classy"
named.name // Still Classy
copy.name // Still Classy

// 測試value semantics
named = StructyName(name: "Structy")
copy = named

named.name = "Still Structy?"
named.name // Still Structy?
copy.name // Structy
```



------

<h2 id="12">Protocols: More than bags of syntax</h2>



------

<h2 id="13">Equatable</h2>

- 如何實作custom type的Equatable

```swift
class Record {
  var wins: Int
  var losses: Int
    
    init(wins: Int, losses: Int) {
        self.wins = wins
        self.losses = losses
    }
}

let recordA = Record(wins: 10, losses: 5)
let recordB = Record(wins: 10, losses: 5)

// 實作Equatable中“==”
extension Record: Equatable {
  static func ==(lhs: Record, rhs: Record) -> Bool {
    return lhs.wins == rhs.wins &&
           lhs.losses == rhs.losses
  }
}

recordA == recordB // true
```

------

<h2 id="14">Comparable</h2>

- 如何實作custom type的Comparable

```swift
extension Record: Comparable {
  static func <(lhs: Record, rhs: Record) -> Bool {
    if lhs.wins == rhs.wins {
      return lhs.losses > rhs.losses
    }
    return lhs.wins < rhs.wins
  }
}

let teamA = Record(wins: 14, losses: 11)
let teamB = Record(wins: 23, losses: 8)
let teamC = Record(wins: 23, losses: 9)

teamA < teamB // true
```

------

<h2 id="15">“Free” functions</h2>

- 當做了Equatable和Comparable就可以另外享受其他另外的function。

```swift
var leagueRecords = [teamA, teamB, teamC]

// type實作了Equatable和Comparable就可以另外享受其他另外的function
leagueRecords.sort()
leagueRecords.max()
leagueRecords.min()
leagueRecords.starts(with: [teamA, teamC])
leagueRecords.contains(teamA)
```

------

<h2 id="16">Hashable</h2>

- For value types (structs, enums) the compiler will generate Equatable and Hashable conformance for you automatically, but you will need to do it yourself for reference (class) types.
- Hash values help you quickly find elements in a collection.

```swift
class Student {
  let email: String
  let firstName: String
  let lastName: String
  weak var buddy: Student?
    
  init(email: String, firstName: String, lastName: String) {
    self.email = email
    self.firstName = firstName
    self.lastName = lastName
  }
}

// 實作Hashable
extension Student: Hashable {
  static func ==(lhs: Student, rhs: Student) -> Bool {
    return lhs.email == rhs.email &&
      lhs.firstName == rhs.firstName &&
      lhs.lastName == rhs.lastName
  }

  // 根據需求，進行對應的hash
  func hash(into hasher: inout Hasher) {
    hasher.combine(email)
    hasher.combine(firstName)
    hasher.combine(lastName)
  }
}

let john = Student(email: "johnny.appleseed@apple.com", firstName: "Johnny", lastName: "Appleseed")
// 因為有hash就可以把Student這種type的object當作key來使用
let lockerMap = [john: "14B"]
```



------

<h2 id="17">CustomStringConvertible</h2>

```swift
extension Student: CustomStringConvertible {
  var description: String {
    return "\(firstName) \(lastName)"
  }
}

print(john) // Johnny Appleseed
```

------

<h2 id="18">Key points</h2>

- **Protocols** define a contract that classes, structs and enums can adopt.
- By adopting a protocol, a type is **required to conform to** the protocol by implementing all methods and properties of the protocol.
- A type can adopt any number of protocols, which allows for a quasi-multiple inheritance not permitted through subclassing.
- You can use **extensions** for protocol adoption and conformance.
- The Swift standard library uses protocols extensively. You can use many of them, such as Equatable and Hashable, on your own named types.
  