# Chapter 24: Value Types and Reference Type

------

## 大綱

- Value types vs. reference types
  - [Reference types](#1)
  - [Value types](#2)
- [Defining value semantics](#3)
  - [When to prefer value semantics](#4)
- Implementing value semantics
  - [Case 1: Primitive value types](#5)
  - [Case 2: Composite value types](#6)
  - [Case 3: Reference types](#7)
  - [Case 4: value types containing mutable reference types](#8)
    - [Copy-on-write to the rescue](#9)
- [Recipes for value semantics](#10)
- [Key points](#11)

------

<h2 id="1">Reference types</h2>

- Reference types use assign-by-reference.

```swift
struct Color: CustomStringConvertible {
  var red, green, blue: Double
  
  var description: String {
    return "r: \(red) g: \(green) b: \(blue)"
  }
}

// Preset colors
extension Color {
  static var black = Color(red: 0, green: 0, blue: 0)
  static var white = Color(red: 1, green: 1, blue: 1)
  static var blue  = Color(red: 0, green: 0, blue: 1)
  static var green = Color(red: 0, green: 1, blue: 0)
  // more ...
}

// Paint bucket abstraction
class Bucket {
  var color: Color
  var isRefilled = false
  
  init(color: Color) {
    self.color = color
  }
  
  func refill() {
    isRefilled = true
  }
}
```

```swift
let azurePaint = Bucket(color: .blue)
let wallBluePaint = azurePaint // 這個是assign-by-reference
wallBluePaint.isRefilled // => false, initially
// 改變A(wallBluePaint)會影養到B(wallBluePaint)
azurePaint.refill()
wallBluePaint.isRefilled // => true, unsurprisingly!
```



------

<h2 id="2">Value types</h2>

```swift
extension Color {
  mutating func darken() {
    red *= 0.9; green *= 0.9; blue *= 0.9
  }
}

var azure = Color.blue
var wallBlue = azure // 這個是assign-by-copy
azure  // r: 0.0 g: 0.0 b: 1.0
// 改變A(wallBlue)不會影養到B(azure)
wallBlue.darken()
azure  // r: 0.0 g: 0.0 b: 1.0 (unaffected)
```

------

<h2 id="3">Defining value semantics</h2>

- 如何測試一個type是否支援value semantics

  > One benefit of value semantics is that they aid [**local reasoning**](https://developer.apple.com/videos/play/wwdc2016/419/?time=41), since to find out how a variable got its value you only need to consider the history of interactions with that variable”
  >

```swift
// 如何測試x是assign-by-copy?
var x = MysteryType()
// 先把x assign給y
var y = x
exposeValue(x) // => 先觀察原本x的值
// {改變y的內容}
exposeValue(x) // => 觀察原本的x是否有改變
// Q: are the initial and final values different?”

```



------

<h2 id="4">When to prefer value semantics</h2>

- Value semantics are good for representing inert, descriptive data
  - numbers, strings, and physical quantities like angle, length or color,
  - don’t forget mathematical objects like vectors and matrices, pure binary data and collections of such values, 
  - and lastly, large rich structures made from such values, like media
- Reference semantics are good for representing distinct items in your program or in the world. 
  - For example: constructs within your program such as specific buttons or memory buffers; an object that plays a specific role in coordinating certain other objects; or a particular person or physical object in the real world.

------

<h2 id="5">Case 1: Primitive value types</h2>

- Primitive value types like Int support value semantics automatically. This is because assign-by-copy ensures each variable holds its own instance

------

<h2 id="6">Case 2: Composite value types</h2>

- Composite value types, for example struct or enum
- a simple rule: **<u>A struct supports value semantics if all its stored properties support value semantics.</u>**
- If the type is an enumeration, it’s analogous: the instance copy is defined to have the same enumeration member, and it is as if that member’s associated values are directly assigned from the associated values of the existing instance.

------

<h2 id="7">Case 3: Reference types</h2>

- Reference types can also have value semantics.
- The solution is straightforward: **<u>To define a reference type with value semantics, you must define it to be immutable</u>**
- Many of the basic UIKit utility types adopt this pattern. For instance, consider this code handling a UIImage:
  - UIImage, along with many of the Cocoa types, **<u>are defined as immutable</u>** for this reason, **<u>because an immutable reference type has value semantics</u>**.

  - The UIImage type has dozens of properties (scale, capInsets, renderingMode, etc.), but since they are all read-only you can’t modify an instance. 



------

<h2 id=8>Case 4: value types containing mutable reference types</h2>

- The final case is mixed types: value types that contain mutable reference types

```swift
do {
  // No copy-on-write
  // Because of this structural sharing, PaintingPlan is a value type but lacks value semantics.
  struct PaintingPlan // 這個一個mixed type
  {
    // a value type
    var accent = Color.white
    // a mutable reference type
    var bucket = Bucket(color: .blue)
  }
  
  let artPlan = PaintingPlan()
  let housePlan = artPlan
  artPlan.bucket.color // => blue
  // 改變mix type中mutable reference type的內容會影響到另外一個
  housePlan.bucket.color = Color.green
  artPlan.bucket.color // => green. oops!
```



------

<h2 id="9">Copy-on-write to the rescue</h2>

- 如何讓mixed types也支援value semantics，就是透過Copy-on-write

```swift
do {
  
  // Copy-on-write
  
  struct PaintingPlan // a value type, containing ...
  {
    // a value type
    var accent = Color.white
    // 1. 利用access level將mutable reference type給隱藏起來 
    private var bucket = Bucket(color: .blue)
    
    // 2. 利用setter去控制隱藏mutable reference type
    var bucketColor: Color {
      get {
        return bucket.color
      }
      set {
        bucket = Bucket(color: newValue)
      }
    }
  }
  
  var artPlan = PaintingPlan()
  var housePlan = artPlan
  // 透過“copy-on-write COW, 此時PaintingPlan是支援value semantics, 因此改變Ａ也不會影響到B
  housePlan.bucketColor = Color.green 
  artPlan.bucketColor // blue. better!
```

- But to a client with internal access or higher, the type behaves just like a struct that has value semantics, with two properties accentColor and bucketColor.”

------

<h2 id="10">Recipes for value semantics</h2>

- **<u>For a reference type (a class):</u>**
  - The type must be immutable, so the requirement is that all its properties are constant and must be of types that have value semantics.

- **<u>For a value type (a struct or enum):</u>**
  - A primitive value type like Int always has value semantics.
  - If you define a struct type with properties, that type will have value semantics if all of its properties have value semantics.
  - Similarly, if you define an enum type with associated values, that type will have value semantics if all its associated values have value semantics.

- **<u>For COW value types —struct or enum:</u>**
  - Choose the “value-semantics access level”, that is, the access level that’ll expose an interface that preserves value semantics.
  - Make note of all mutable reference-type properties, as these are the ones that spoil automatic value semantics. Set their access level below the value-semantics level.
  - Define all the setters and mutating functions at and above the value-semantics access level so that they never actually modify a shared instance of those reference-type properties, but instead assign a copy of the instance to the reference-type property.

------

<h2 id="11">Key points</h2>

- Value types and reference types differ in their assignment behavior. Value types use assign-by-copy; reference types use assign-by-reference. This behavior describes whether a variable copies or refers to the instance assigned to it.
- This assignment behavior affects not only variables but also function calls.
- Value types help you implement types with value semantics. A type has value semantics if assigning to a variable seems to create a completely independent instance. When this is the case, the only way to affect a variable’s value is through the variable itself, and you can simply think about variables as if instances and references did not exist.
- Primitive value types and immutable reference types have value semantics automatically. Value types that contain reference types, such as mixed types, will only have value semantics if they are engineered that way. For instance, they might only share immutable properties, or privately copy shared components when they would be mutated.
- Structural sharing is when distinct instances refer to a common backing instance that contributes to their value. This economizes storage since multiple instances can depend on one large shared instance. But if one instance can modify the shared backing instance, it can indirectly modify the value of other instances, so that the distinct instances are not fully independent, undermining value semantics.
- Copy-on-write is the optimization pattern where a type relies on structural sharing but also preserves value semantics by copying its backing instance only at the moment when it itself is mutated. This allows the efficiency of a reference type in the read-only case, while deferring the cost of instance copying in the read-write case.
- Reference types also have value semantics if you define them to be fully immutable, meaning that they cannot be modified after initialization. To do this it suffices that all their stored properties are read-only and of types that themselves have value semantics.