# Chapter 10: Structures

------

## 大綱

- [Introducing structures](#1)
- [Accessing members](#2)
- [Introducing methods](#3)
- [Structures as values](#4)
- [Structures everywhere](#5)
- [Conforming to a protocol](#6)
- [Key points](#7)

------

<h2 id="1">Introducing structures</h2>

- Structures are one of the named types in Swift that allow you to encapsulate related properties and behaviors.
- Swift **<u>automatically provides initializers</u>** for structures with all the properties in the parameter list
  - Initializers **<u>enforce that all properties are set before you start using them</u>**. This is one of the key safety features of Swift.


```swift
struct Location {
  let x: Int
  let y: Int
}

struct DeliveryArea: CustomStringConvertible {
  // sturct中可以再有個sturct
  let center: Location
  var radius: Double
  
  var description: String {
    return """
      Area with center: x: \(center.x) - y: \(center.y),
      radius: \(radius)
      """
  }
  
  // 定義method
  func contains(_ location: Location) -> Bool {
    let distanceFromCenter = distance(from: (center.x, center.y), to: (location.x, location.y))
    return distanceFromCenter < radius
  }
}

// Swift自動產生的Initializers, 會強制所有變數都有初始值
let storeLocation = Location(x: 2, y: 4)
var storeArea = DeliveryArea(center: storeLocation, radius: 4)
```



------

<h2 id="2">Accessing members</h2>

- you use dot syntax to access members


```swift
print(storeArea.radius) // 4.0
print(storeArea.center.x) // 2
```

- That code causes the compiler to emit an error. Change fixedArea from a let constant to a var variable to make it mutable

```swift
let fixedArea = DeliveryArea(center: storeLocation, radius: 4)

// Error: change let to var above to make it mutable.
fixedArea.radius = 250
```



------

<h2 id="3">Introducing methods</h2>

- defines a function contains, which is now a member of DeliveryArea. Functions that are members of types are called methods. 

```swift
let area = DeliveryArea(center: Location(x: 5, y: 5), radius: 4.5)
let customerLocation = Location(x: 2, y: 2)
area.contains(customerLocation) // true
```



------

<h2 id="4">Structures as values</h2>

- A value type is a type whose instances are **<u>copied on assignment</u>**

```swift
var area1 = DeliveryArea(center: Location(x: 2, y: 4), radius: 2.5)
var area2 = area1
print(area1.radius) // 2.5
print(area2.radius) // 2.5

area1.radius = 4
print(area1.radius) // 4.0
print(area2.radius) // 2.5
```



------

<h2 id="5">Structures everywhere</h2>

- many of the standard Swift types are defined as structures, such as: Double, String, Bool, Array and Dictionary. 


------

<h2 id="6">Conforming to a protocol</h2>

- Any named type can use protocols to extend its behavior. In this case, you conformed your structure to a protocol defined in the Swift standard library.

```swift
struct DeliveryArea: CustomStringConvertible {
  var description: String {
    return """
      Area with center: x: \(center.x) - y: \(center.y),
      radius: \(radius)
      """
  }
}
```



------

<h2 id="7">Key points</h2>

- Structures are named types you can define and use in your code.
- Structures are value types, which means their values are copied on assignment.
- You use dot syntax to access the members of named types such as structures.
- Named types can have their own variables and functions, which are called properties and methods.
- Conforming to a protocol requires implementing the properties and methods required by that protocol.
  Where to go from here?