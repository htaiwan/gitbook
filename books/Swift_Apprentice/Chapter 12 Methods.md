# Chapter 12: Methods

------

## 大綱

- Method refresher
  - [Comparing methods to computed properties](#1)
  - [Turning a function into a method](#2)
- [Introducing self](#3)
- [Introducing initializers](#4)
  - [Initializers in structures](#5)
- [Introducing mutating methods](#6)
- [Type methods](#7)
- [Adding to an existing structure with extensions](#8)
  - [Keeping the compiler-generated initializer using extensions](#9)
- [Key points](#10)

------

<h2 id="1">Comparing methods to computed properties</h2>

- methods和computed properties很類似，但什麼時候該用哪個？
  - 如果只是進行簡單的計算可以考慮用computed properties，複雜的運算則是寫個method。
  - 如果計算的東西只是type本身有關可以考慮用computed properties, 如果需要外部的資訊才能計算就寫個method。

------

<h2 id="2">Turning a function into a method</h2>

- There’s no identifying keyword for a method; it really is just a function inside a named type

------

<h2 id="3">Introducing self</h2>

- **<u>self</u>** is your reference to the instance, but most of the time you don’t need to use it because Swift understands your intent if you just use a variable name
- Most programmers use self only when it is required, for example, **<u>to disambiguate between a local variable and a property with the same name.</u>**


------

<h2 id="4">Introducing initializers</h2>

- Initializers are special methods you call to **<u>create a new instance</u>**. 
- They omit the func keyword and even a name. Instead, they use **<u>init</u>**. 
- An initializer **<u>can have parameters, but it doesn’t have to</u>**.

```swift
struct SimpleDate {
  var month: String
  var day: Int
  
  func monthsUntilWinterBreak() -> Int {
    return months.index(of: "December")! - months.index(of: month)!
  }
  
  mutating func advance() {
    day += 1
  }
}

extension SimpleDate {
  // In that code, there aren’t any parameters with the same names as the properties. Therefore, self isn’t necessary for the compiler to understand you’re referring to properties.
Excerpt From: By Ray Fix. “Swift Apprentice.” Apple Books. 
  init() {
    month = "January"
    day = 1
  }
}
```



------

<h2 id="5">Initializers in structures</h2>



------

<h2 id="6">Introducing mutating methods</h2>

- The **<u>mutating</u>** keyword marks a method that changes a structure’s value
- By marking a method as mutating, you’re also telling the Swift compiler this method must not be called on constants. 
  - If you call a mutating method on a constant instance of a structure, the compiler will flag it as an error that must be corrected before you can run your program.
- For mutating methods, **<u>Swift secretly passes in self</u>** just like it did for normal methods. But for mutating methods, **<u>the secret self gets marked as an inout parameter.</u>**


------

<h2 id="7">Type methods</h2>

```swift
struct Math {
  static func factorial(of number: Int) -> Int {
    return (1...number).reduce(1, *)
  }
}

Math.factorial(of: 6)
```

------

<h2 id="8">Adding to an existing structure with extensions</h2>

- added a method to Math without changing its original definition. Verify that the extension works with this code

```swift
extension Math {
  static func primeFactors(of value: Int) -> [Int] {
    var remainingValue = value
    var testFactor = 2
    var primes: [Int] = []
    while testFactor * testFactor <= remainingValue {
      if remainingValue % testFactor == 0 {
        primes.append(testFactor)
        remainingValue /= testFactor
      }
      else {
        testFactor += 1
      }
    }
    if remainingValue > 1 {
      primes.append(remainingValue)
    }
    return primes
  }
}

Math.primeFactors(of: 81)
```

------

<h2 id="9">Keeping the compiler-generated initializer using extensions</h2>

- 一但新增自己客製化的init的method, compiler幫我們自動建立的init的就是消失。
- 如何可以保有原本自動建立的init的method, 那就是把客製化的init寫在extension中。

------

<h2 id="10">Key points</h2>

- Methods are functions associated with a type.
- Methods are the behaviors that define the functionality of a type.
- A method can access the data of an instance by using the keyword **<u>self</u>**.
- **<u>Initializer</u>**s create new instances of a type. They look a lot like methods that are called init with no return value.
- A **<u>type method</u>** adds behavior to a type instead of the instances of that type. To define a type method, you prefix it with the **<u>static</u>** modifier.
- You can open an existing structure and add methods, initializers and computed properties to it by using an **<u>extension</u>**.
- By **<u>adding your own initializers in extensions</u>**, you can keep the compiler’s member-wise initializer for a structure.
- **<u>Methods</u>** can exist in all the named types — structures, classes and enumerations.
  