# Chapter 11: Properties

------

## 大綱

- [Stored properties](#1)
  - [Default values](#2)
- [Computed properties](#3)
  - [Getter and setter](#4)
- [Type properties](#5)
- [Property observers](#6)
  - [Limiting a variable](#7)
- [Lazy properties](#8)
- [Key points](#9)

------

<h2 id="1">Stored properties</h2>

```swift
struct Contact {
  // provide a data type for each one but opt not to assign a default value, because you plan to assign the value upon initialization.
  var fullName: String
  let emailAddress: String
  var relationship = "Friend"
}

// Swift automatically creates an initializer for you based on the properties you defined in your structure
var person = Contact(fullName: "Grace Murray",
                     emailAddress: "grace@navy.mil",
                     relationship: "Friend")

// access the individual properties using dot notation
let name = person.fullName // Grace Murray
let email = person.emailAddress // grace@navy.mil

// assign values to properties as long as they’re defined as variables, and the parent instance is stored in a variable
person.fullName = "Grace Hopper"
let grace = person.fullName // Grace Hopper

// If you’d like to prevent a value from changing, you can define a property as a constant using "let"
person.emailAddress = "grace@gmail.com" // Error!
```



------

<h2 id="2">Default values</h2>

- the automatic initializer doesn’t notice default values, so you’ll still need to provide a value for each property unless you create your own custom initializer


```swift
struct Contact {
  var fullName: String
  let emailAddress: String
  // By assigning a value in the definition of relationship, you give this property a default value
  var relationship = "Friend"
}
```

------

<h2 id="3">Computed properties</h2>

- properties that are computed, which simply means they perform a calculation before returning a value
- Computed properties must also include a type, because the compiler needs to know what to expect as a return value.
- Computed properties don’t store any values; they return values based on calculations.


```swift
struct TV {
  var height: Double
  var width: Double
  
  // Computed properties
  // read-only computed property
  var diagonal: Int {
    let result = (height * height + width * width).squareRoot().rounded()
      return Int(result)
}

var tv = TV(height: 53.93, width: 95.87)
let size = tv.diagonal // 110
```

------

<h2 id="4">Getter and setter</h2>

```swift
struct TV {
  var height: Double
  var width: Double
  
  // This specificity isn’t required for read-only computed properties, as their single code block is implicitly a getter.
  var diagonal: Int {
    get {
      let result = (height * height + width * width).squareRoot().rounded()
      return Int(result)
    }
    // there’s no return statement in a setter — it only modifies the other stored properties.
    set {
      // provide a reasonable default value for the screen ratio
      let ratioWidth = 16.0
      let ratioHeight = 9.0
      let ratioDiagonal = (ratioWidth * ratioWidth + ratioHeight * ratioHeight).squareRoot()
      // The newValue constant lets you use whatever value was passed in during the assignment.b”, the newValue is an Int, so to use it in a calculation with a Double, you must first convert it to a Double.c.
      height = Double(newValue) * ratioHeight / ratioDiagonal
      width = height * ratioWidth / ratioHeight
    }
  }
}
```



------

<h2 id="5">Type properties</h2>

- A type property is declared with the modifier static.
- Using a type property means you can retrieve the same stored property value from anywhere in the code for your app or algorithm

```swift
struct Level {
  static var highestLevel = 1
  let id: Int
  var boss: String
}

let highestLevel = Level.highestLevel
```



------

<h2 id="6">Property observers</h2>

- A willSet observer is called when a property is about to be changed while a didSet observer is called after a property has been changed. 
- willSet and didSet observers are **<u>only available for stored properties</u>**. If you want to listen for changes to a computed property, simply add the relevant code to the property’s setter.
- the willSet and didSet observers are not called when a property is set during initialization

```swift
struct Level {
  static var highestLevel = 1
  let id: Int
  var boss: String
  var unlocked: Bool {
    didSet {
      // You are required to use the full name Level.highestLevel rather than just highestLevel alone to indicate you’re accessing a type property
      if unlocked && id > Level.highestLevel {
        Level.highestLevel = id
      }
    }
  }
}
```

------

<h2 id="7">Limiting a variable</h2>

- use property observers to limit the value of a variable

```swift
struct LightBulb {
  static let maxCurrent = 40
  var current = 0 {
    didSet {
      if current > LightBulb.maxCurrent {
        print("""
              Current is too high,
              falling back to previous setting.
              """)
        current = oldValue
      }
    }
  }
}


var light = LightBulb()
light.current = 50
var current = light.current // 0
light.current = 40
current = light.current // 40
```

------

<h2 id="8">Lazy properties</h2>

- If you have a property that might take some time to calculate, you don’t want to slow things down until you actually need the property. Say hello to **<u>the lazy stored property</u>**

```swift
struct Circle {
  // The lazy property must be a variable, defined with var, instead of a constant defined with let
  lazy var pi = {
    return ((4.0 * atan(1.0 / 5.0)) - atan(1.0 / 239.0)) * 4.0
  }()
  var radius = 0.0
  var circumference: Double {
    // the circumference getter must be marked as mutating. Accessing the value of pi changes the value of the structure.
    mutating get {
      return pi * radius * 2
    }
  }
  // Since pi is a stored property of the structure, you need a custom initializer to use only the radius. Remember the automatic initializer of a structure includes all of the stored properties.
  init(radius: Double) {
    self.radius = radius
  }
}

var circle = Circle(radius: 5) // got a circle, pi has not been run
let circumference = circle.circumference // 31.42
// also, pi now has a value
```



------

<h2 id="9">Key points</h2>

- **<u>Properties</u>** are variables and constants that are part of a named type.
- **<u>Stored properties</u>** allocate memory to store a value.
- **<u>Computed properties</u>** are calculated each time your code requests them and aren’t stored as a value in memory.
- **<u>The static modifier</u>** marks a **<u>type property</u>** that’s universal to all instances of a particular type.
- **<u>The lazy modifier</u>** prevents a value of a stored property from being calculated until your code uses it for the first time. You’ll want to **<u>use lazy initialization</u>** when a property’s initial value is computationally intensive or when you won’t know the initial value of a property until after you’ve initialized the object.