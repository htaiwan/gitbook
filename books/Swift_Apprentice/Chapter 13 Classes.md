# Chapter 13: Classes

------

## 大綱

- [Creating classes](#1)
- [Reference types](#2)
  - [The heap vs. the stack](#3)
  - [Working with references](#4)
  - [Object identity](#5)
  - [Methods and mutability](#6)
  - [Mutability and constants](#7)
- [Understanding state and side effects](#8)
- [Extending a class using an extension](#9)
- When to use a class versus a struct
  - [Values vs. objects](#10)
  - [Speed](#11)
  - [Minimalist approach](#12)
- [Structures vs. classes recap](#13)
- [Key points](#14)

------

<h2 id="1">Creating classes</h2>

- Unlike a struct, a class doesn’t provide a memberwise initializer automatically — which means you must provide it yourself if you need it.

```swift
class Person {
  var firstName: String
  var lastName: String
  
  init(firstName: String, lastName: String) {
    self.firstName = firstName
    self.lastName = lastName
  }
  
  var fullName: String {
    return "\(firstName) \(lastName)"
  }
}

let john = Person(firstName: "Johnny", lastName: "Appleseed")
```

------

<h2 id="2">Reference types</h2>

- Classes are reference types, so <u>**a variable of a class type doesn’t store an actual instance**</u> — it stores a reference to a location in memory that stores the instance.


------

<h2 id="3">The heap vs. the stack</h2>

- When you create a reference type such as class, the system stores the actual instance in a region of memory known as the heap. Instances of a value type such as a struct resides in a region of memory called the stack。
- **<u>Stack:</u>** The system uses the stack to store anything on the immediate thread of execution; it is tightly managed and optimized by the CPU. 
  - it’s very efficient, and thus quite fast

- **<u>Heap:</u>** The system uses the heap to store instances of reference types. The heap is generally a large pool of memory from which the system can request and dynamically allocate blocks of memory.
  - The heap doesn’t automatically destroy its data like the stack does; additional work is required to do that

------

<h2 id="4">Working with references</h2>

- sharing among class instances results in a new way of thinking when passing things around

```swift
var homeOwner = john
john.firstName = "John" // John wants to use his short name!
john.firstName // "John"
// homeOwner的firstName會跟著john的firstName變動
homeOwner.firstName // "John"
```



------

<h2 id="5">Object identity</h2>

- In Swift, the === operator lets you check if the identity of one object is equal to the identity of another

```swift
john === homeOwner // true

let imposterJohn = Person(firstName: "Johnny", lastName: "Appleseed")

john === homeOwner // true
john === imposterJohn // false
imposterJohn === homeOwner // false
```

```swift
// Create fake, imposter Johns. Use === to see if any of these imposters are our real John.
var imposters = (0...100).map { _ in
  Person(firstName: "John", lastName: "Appleseed")
}

// Equality (==) is not effective when John cannot be identified by his name alone
imposters.contains {
  $0.firstName == john.firstName && $0.lastName == john.lastName
} // true

// Check to ensure the real John is not found among the imposters.
imposters.contains {
  $0 === john
} // false
```

------

<h2 id="6">Methods and mutability</h2>

```swift
struct Grade {
  let letter: String
  let points: Double
  let credits: Double
}

class Student {
  var firstName: String
  var lastName: String
  var grades: [Grade] = []
  var credits = 0.0
  
  init(firstName: String, lastName: String) {
    self.firstName = firstName
    self.lastName = lastName
  }
  // 可以直接變更grades的內容，不用像strunt需要加mutating
  func recordGrade(_ grade: Grade) {
    grades.append(grade)
    credits += grade.credits
  }
}

let jane = Student(firstName: "Jane", lastName: "Appleseed")
let history = Grade(letter: "B", points: 9.0, credits: 3.0)
var math = Grade(letter: "A", points: 16.0, credits: 4.0)

jane.recordGrade(history)
jane.recordGrade(math)
```



------

<h2 id="7">Mutability and constants</h2>

- 透過let, var來進行class的mutation限制。

> Any individual member of a class can be protected from modification through the use of constants, but because reference types are not themselves treated as values, they are not protected as a whole from mutation.

```swift
// Error: jane is a `let` constant
jane = Student(firstName: "John", lastName: "Appleseed")

var jane = Student(firstName: "Jane", lastName: "Appleseed")
jane = Student(firstName: "John", lastName: "Appleseed")
```



------

<h2 id="8">Understanding state and side effects</h2>

- If you update a class instance with a new value every reference to that instance will also see the new value
- class instances are mutable, you need to be careful about unexpected behavior around shared references.


------

<h2 id="9">Extending a class using an extension</h2>

- classes can be re-opened using the extension keyword to add methods and computed properties”

  Excerpt From: By Ray Fix. “Swift Apprentice.” Apple Books. 

------

<h2 id="10">Values vs. objects</h2>

- An object is an instance of a reference type, and such instances have identity meaning that every object is unique. **<u>No two objects are considered equal simply because they hold the same state</u>**. 
  - use === to see if objects are truly equal and not just containing the same state. 
  -  instances of value types, which are values, are considered equal if they are the same value.
  - For example: A delivery range is a value, so you implement it as a struct. A student is an object so you implement it as a class. In non-technical terms, no two students are considered equal, even if they have the same name!

------

<h2 id="11">Speed</h2>

- structs rely on the faster stack while classes rely on the slower heap. 

------

<h2 id="12">Minimalist approach</h2>

- If your data will never change or you need a simple data store, then use structures. 
- If you need to update your data and you need it to contain logic to update its own state, then use classes.
- it’s best to begin with a struct. If you need the added capabilities of a class sometime later, then you just convert the struct to a class.

------

<h2 id="13">Structures vs. classes recap</h2>

- Structures
  - Useful for representing values.
  - Implicit copying of values.
  - Becomes completely immutable when declared with let.
    Fast memory allocation (stack)
- Classes
  - Useful for representing objects with an identity.
  - Implicit sharing of objects.
  - Internals can remain mutable even when declared with let.
  - Slower memory allocation (heap).

------

<h2 id="14">Key points</h2>

- Like structures, classes are a named type that can have properties and methods.
- Classes use **references** that are shared on assignment.
- Class instances are called **objects**.
- Objects are **mutable**.
- Mutability introduces **state**, which adds complexity when managing your objects.
- Use classes when you want **reference semantics**; structures for **value semantics**.