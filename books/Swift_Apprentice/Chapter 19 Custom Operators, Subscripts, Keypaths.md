# Chapter 19: Custom Operators, Subscripts, Keypaths

在Ch16學到如何使用operator overloading，例如實作Equatable 和Comparable的protocol，並針對這些標準operator加上客製化的行為。但有時候只是為標準operator加上新行為可能還不夠，我們想要自己的客製化operator。

------

## 大綱

- [Custom operators](#1)
  - [Types of operators](#2)
  - [Exponentiation operator](#3)
  - [Compound assignment operator](#4)
  - [Generic operators](#5)
  - [Precedence and associativity](#6)
- [Subscripts](#7)
  - [Subscript parameters](#8)
  - [Dynamic member lookup](#9)
- [Keypaths](#10)
  - Appending keypaths
  - Setting properties
- [Key points](#11)

------

<h2 id="1">Custom operators</h2>

- 首先，如何定義exponentiation operator ?

------

<h2 id="2">Types of operators</h2>

- **<u>Unary operators</u>** work with only one operand and are defined either as postfix, if they appear after the operand, or prefix, if they appear before the operand. The logical not operator is a unary prefix operator and the forced unwrapping operator is a unary postfix one. You learned about them in Chapter 3, “Basic Control Flow” and Chapter 6, “Optionals”.
- **<u>Binary operators</u>** work with two operands and are infix because they appear between the operands. All the arithmetic operators (+, -, *, /, %), comparison operators (==, !=, <, >, <=, >=) and most of the logical ones (&&, ||) are binary infix.
- **<u>Ternary operators</u>** work with three operands. You’ve learned about the conditional operator in Chapter 3, “Basic Control Flow”. This is the only ternary operator in Swift.

------

<h2 id="3">Exponentiation operator</h2>

- Swift是不允許有自己客製化Ternary operators。

```Swift
// 宣告我們定義的ExponentiationPrecedence高於MultiplicationPrecedence
precedencegroup ExponentiationPrecedence {
  associativity: right
  higherThan: MultiplicationPrecedence
}

// 自定義的custom operator
infix operator **: ExponentiationPrecedence

// 利用generic type擴充其應用範圍
func **<T: BinaryInteger>(base: T, power: Int) -> T {
  precondition(power >= 2)
  var result = base
  for _ in 2...power {
    result *= base
  }
  return result
}

// 測試
let base = 2
let exponent = 2
let result = base ** exponent // 4
```

------

<h2 id="4">Compound assignment operator</h2>

- 大部分內建的operator都有自己對應的Compound assignment operator。

```swift
infix operator **=

func **=<T: BinaryInteger>(lhs: inout T, rhs: Int) {
  lhs = lhs ** rhs
}

var number = 2
number **= exponent // 4
```

------

<h2 id="5">Generic operator</h2>

- 利用BinaryInteger來限制可以使用generic type類型。

```Swift
let unsignedBase: UInt = 2
let unsignedResult = unsignedBase ** exponent

let base8: Int8 = 2
let result8 = base8 ** exponent

let unsignedBase8: UInt8 = 2
let unsignedResult8 = unsignedBase8 ** exponent

let base16: Int16 = 2
let result16 = base16 ** exponent

let unsignedBase16: UInt16 = 2
let unsignedResult16 = unsignedBase16 ** exponent

let base32: Int32 = 2
let result32 = base32 ** exponent

let unsignedBase32: UInt32 = 2
let unsignedResult32 = unsignedBase32 ** exponent

let base64: Int64 = 2
let result64 = base64 ** exponent

let unsignedBase64: UInt64 = 2
let unsignedResult64 = unsignedBase64 ** exponent
```

------

<h2 id="6">Precedence and associativity</h2>

- 如果把我們客製化的operator用在複雜的表達式上

```Swift
2 * 2 ** 3 ** 2  // <-- Does not compile! 因為Swift不知道要如何處理
2 * (2 ** (3 ** 2)) // <-- 要自己替Swift加上括號 1024 
```

- 如果不想要用括號，那就要替客製化的operator加上額外的宣告

```Swift
// 宣告我們定義的ExponentiationPrecedence高於MultiplicationPrecedence
precedencegroup ExponentiationPrecedence {
  associativity: right
  higherThan: MultiplicationPrecedence
}

// 自定義的custom operator
infix operator **: ExponentiationPrecedence

2 * 2 ** 3 ** 2 // 1024, 如此一來Swift才知道如何進行運算
```

------

<h2 id="7">Subscripts</h2>

- Arrays, Dictionaries, Sets都具有Subscripts的功能，透過[ ]來取值。
- 也可以對自己的type加上Subscripts的功能。

```Swift
// 跟fun的寫法很像，有參數，也有回傳值
subscript(parameterList) -> ReturnType {
  // 裡面的寫法跟 computed property很類似
  get {
    // return someValue of ReturnType
  }
 
  set(newValue) {
    // set someValue of ReturnType to newValue
  }
}
```

```Swift
class Person {
  let name: String
  let age: Int
  
  init(name: String, age: Int) {
    self.name = name
    self.age = age
  }
}

let me = Person(name: "Cosmin", age: 32)

extension Person {
  // 定義自己客製化的subscript
  subscript(property key: String) -> String? {
    // 這個switch case一定要用default值
    switch key {
      case "name":
        return name
      case "age":
        return "\(age)"
      default:
        return nil
    }
  }
}

me[property: "name"] // Cosmin
me[property: "age"]  // 32
me[property: "gender"] // nil
```

------

<h2 id="8">Subscript parameters</h2>

- 利用external parameter names讓Subscript更加清楚

```Swift
subscript(key key: String) -> String? {
  // original code
}

me[key: "name"]
me[key: "age"]
me[key: "gender"]

// 如果沒有external parameter
me["name"]
me["age"]
me["gender"]
```

------

<h2 id="9">Dynamic member lookup</h2>

- 利用dynamic member lookup來替自己的type增加任意的dot syntax。

```Swift
// 宣告Instrument具有 @dynamicMemberLookup，可以使用dot syntax
@dynamicMemberLookup
class Instrument {
  let brand: String
  let year: Int
  private let details: [String: String]
  
  init(brand: String, year: Int, details: [String: String]) {
    self.brand = brand
    self.year = year
    self.details = details
  }
  // 透過subscript來Instrument具有@dynamicMemberLookup的能力
  subscript(dynamicMember key: String) -> String {
    switch key {
      case "info":
        return "\(brand) made in \(year)."
      default:
        return details[key] ?? ""
    }
  }
}

let instrument = Instrument(brand: "Roland", year: 2018, details: ["type": "accoustic", "pitch": "C"])
instrument.info // Roland made in 2018
instrument.pitch // C

instrument.brand // Roland
instrument.year // 2018

// 也具有繼承功能
class Guitar: Instrument {}
let guitar = Guitar(brand: "Fender", year: 2018, details: ["type": "electric", "pitch": "C"])
guitar.info
// dynamic member是在runtime進行運算，所以這個syntex在compiler是不會報錯，也降低程式的安全性
guitar.dlfksdf
```

> “Unlike computed properties and functions, subscripts have no name to make their intentions clear. **<u>Subscripts are almost exclusively used to access the elements of a collection, so don’t confuse the readers of your code</u>** by using them for something unrelated and unintuitive!”
>

------

<h2 id="10">Keypaths</h2>

```Swift
class Tutorial {
  let title: String
  let author: Person
  let type: String
  let publishDate: Date
  
  init(title: String, author: Person, type: String, publishDate: Date) {
    self.title = title
    self.author = author
    self.type = type
    self.publishDate = publishDate
  }
}

let tutorial = Tutorial(title: "Object Oriented Programming in Swift", author: me, type: "Swift", publishDate: Date())

// 利用keypath的方式
let title = \Tutorial.title
let tutorialTitle = tutorial[keyPath: title]

// keypath也可以進行多層的串接
let authorName = \Tutorial.author.name
var tutorialAuthor = tutorial[keyPath: authorName]

let authorPath = \Tutorial.author
// 也可以利用appending的方式串接
let authorNamePath = authorPath.appending(path: \.name)
tutorialAuthor = tutorial[keyPath: authorNamePath]
```



```Swift
class Jukebox {
  var song: String
  
  init(song: String) {
    self.song = song
  }
}

let jukebox = Jukebox(song: "Nothing else matters")

let song = \Jukebox.song
// 利用Keypaths改變property values
jukebox[keyPath: song] = "Stairway to heaven"
```

- 用keypath其實比用properties更加複雜，必須先宣告keypath才來進行讀取。

> “The benefit is that it you can parameterize the properties you use in your code. Instead of hard coding them, you can store them in variables as keypaths. You could even leave it up to your users to decide which properties to use!”
>

------

<h2 id="11">Key points</h2>

- Remember the custom operators mantra when creating brand new operators from scratch: With great power comes great responsibility. Make sure the additional cognitive overhead of a custom operator introduces pays for itself.
- Choose the appropriate type for custom operators: postfix, prefix or infix.
- Don’t forget to define any related operators, such as compound assignment operators, for custom operators.
- Use subscripts to overload the square brackets operator for classes, structures and enumerations.
- Use dynamic member lookup to provide dot syntax for subscripts.
- Use keypaths to create dynamic references to properties