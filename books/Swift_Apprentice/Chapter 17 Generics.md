# Chapter 17: Generics

------

## 大綱

- Introducing generics
  - [Values defined by other values](#1)
  - [Types defined by other types](#2)
- [Anatomy of generic types](#3)
  - [Using type parameters](#4)
  - [Type constraints](#5)
- [Arrays](#6)
- [Dictionaries](#7)
- [Optionals](#8)
- [Generic function parameters](#9)
- [Key points](#10)

------

<h2 id="1">Values defined by other values</h2>

- 什麼情況下最適合使用Generics ?
  - set of possible PetKind values defines the set of possible KeeperKind values


```swift
// 每當enum增加一個case
enum PetKind {
  case cat
  case dog
}
// KeeperKind就多一種value可以選擇
struct KeeperKind {
  var keeperOf: PetKind
}

let catKeeper = KeeperKind(keeperOf: .cat)
let dogKeeper = KeeperKind(keeperOf: .dog)
```



------

<h2 id="2">Types defined by other types</h2>

```swift
// 增加class
class Cat {}
class Dog {}
// 增加對應的class case
class KeeperForCats {}
class KeeperForDogs {}
```

------

<h2 id="3">Anatomy of generic types</h2>

- you can define **a generic type** for keepers

  - That Animal in angle brackets is the **type parameter** that specifies the type for the kind of animal you’re keeping.

  - you’ll encounter names like **T** (short for Type) from time to time, but **these names should be avoided when the type parameter has a clear role** such as Animal

```swift
class Keeper<Animal> {}

var aCatKeeper = Keeper<Cat>()
var aKeeper = Keeper()  // compile-time error!”
```

------

<h2 id="4">Using type parameters</h2>

- you can use type parameters such as Animal throughout your type definitions


```swift
class Cat {
  var name: String
  
  init(name: String) {
    self.name = name
  }
}

class Dog {
  var name: String
  
  init(name: String) {
    self.name = name
  }
}

// 宣告Animal是一種generic type
class Keeper<Animal> {
  var name: String
  var morningCare: Animal
  var afternoonCare: Animal
  
  // 利用type parameters來當作init的parameter
  init(name: String, morningCare: Animal, afternoonCare: Animal) {
    self.name = name
    self.morningCare = morningCare
    self.afternoonCare = afternoonCare
  }
}

let jason = Keeper(name: "Jason", morningCare: Cat(name: "Whiskers"), afternoonCare: Cat(name: "Sleepy"))
```



------

<h2 id="5">Type constraints</h2>

- the identifier Animal serves as a type parameter, which is a named **placeholder** for some actual type that will be supplied later.

- In Swift, you do this with various kinds of **type constraints**.

  ```swift
  // 對generic type (Animal)進行type constraints進行限制為Pet type
  class Keeper<Animal: Pet> {}
  
  // 利用where關鍵字來找Array中所有generic type中是Cat type
  extension Array where Element: Cat {
    func meow() {
      forEach { print("\($0.name) says meow!") }
    }
  }
  ```

- 

------

<h2 id="6">Arrays</h2>

- Array<Element> and [Element] are exactly interchangeable. So you could even call an array’s default initializer by writing [Int]() instead of Array<Int>().

```swift
let animalAges: [Int] = [2,5,7,9]
let animalAges: Array<Int> = [2,5,7,9]
```

------

<h2 id="7">Dictionaries</h2>

- To instantiate types such as Dictionary with multiple type parameters, simply provide a comma-separated type argument list:

```swift
let intNames: Dictionary<Int, String> = [42: "forty-two"]
let intNames2: [Int: String] = [42: "forty-two", 7: "seven"]
let intNames3 = [42: "forty-two", 7: "seven"]
```



------

<h2 id="8">Optionals</h2>

- 自定義optional type

```swift
// 自定義optional type
enum OptionalDate {
  case none
  case some(Date)
}

// 自定義optional type
enum OptionalString {
  case none
  case some(String)
}

struct FormResults {
  // other properties here
  var birthday: OptionalDate
  var lastName: OptionalString
}

// 判斷自己自定義的option type
var birthdate: Optional<Date> = .none
if birthdate == .none {
  // no birthdate
}

// 比較常使用的判斷方式
var birthdate2: Date? = nil
if birthdate2 == nil {
  // no birthdate
}
```

------

<h2 id="9">Generic function parameters</h2>

- “A function's **type parameter list** comes after the function name. You can then use the generic parameters in the rest of the definition.”


```swift
// <T, U>就是此function的 type parameter list
func swapped<T, U>(_ x: T, _ y: U) -> (U, T) {
  return (y, x)
}

swapped(33, "Jay")  // returns ("Jay", 33)
```

------

<h2 id="10">Key points</h2>

- **Generics** are everywhere in Swift: in optionals, arrays, dictionaries, other collection structures, and most basic operators like + and ==.
- Generics express systematic variation at the level of types via **type parameters** that range over possible concrete types.
- Generics are like functions for the **compiler**. They are evaluated at compile time and result in new types which are specializations of the generic type.
- A generic type is not a real type on its own, but more like a recipe, program, or template for defining new types.
- Swift provides a rich system of **type constraints**, which lets you specify what types are allowed for various type parameters.