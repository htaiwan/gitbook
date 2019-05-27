# Chapter 25: Protocol-Oriented Programming

------

## 大綱

- [Introducing protocol extensions](#1)
- [Default implementations](#2)
- [Understanding protocol extension dispatching](#3)
- [Type constraints](#4)
- Protocol-oriented benefits
  - [Programming to interfaces, not implementations](#5)
  - [Traits, mixins and multiple inheritance](#6)
  - [Simplicity](#7)
- [Why Swift is a protocol-oriented language](#8)
- [Key points](#9)

------

<h2 id="1">Introducing protocol extensions</h2>

- protocol extension跟protocol最大的差異就是可以進行內容的實作。

```swift
protocol TeamRecord {
  var wins: Int { get }
  var losses: Int { get }
  var winningPercentage: Double { get }
}

// protocol extensions
extension TeamRecord {
  // 替protocol新增變數，並實作內容
  var gamesPlayed: Int {
    return wins + losses
  }
}
```

------

<h2 id="2">Default implementations</h2>

- 可以幫protocol本身的內容進行實作。

> it differs in that winningPercentage is a member of the TeamRecord protocol itself whereas gamesPlayed isn’t. Implementing a member of a protocol in an extension creates a default implementation for that member.”

```swift
protocol TeamRecord {
  var wins: Int { get }
  var losses: Int { get }
  var winningPercentage: Double { get }
}

// Default implementations
// 直接幫宣告protocol本身的winningPercentage進行實作
extension TeamRecord {
  var winningPercentage: Double {
    return Double(wins) / Double(wins + losses)
  }
}
```

------

<h2 id="3">Understanding protocol extension dispatching</h2>

> If a type defines a method or property in protocol extension, without declaring it in the protocol itself, **<u>static dispatching</u>** comes into play. 
>

```swift
protocol WinLoss {
  var wins: Int { get }
  var losses: Int { get }
}

extension WinLoss {
  var winningPercentage: Double {
    return Double(wins) / Double(wins + losses)
  }
}

struct CricketRecord: WinLoss {
  var wins: Int
  var losses: Int
  var draws: Int
    
  // winningPercentage並沒有直接宣告WinLoss之中，而是宣告並實作在WinLoss的extension中
  var winningPercentage: Double {
    return Double(wins) / Double(wins + losses + draws)
  }
}

let miamiTuples = CricketRecord(wins: 8, losses: 7, draws: 1)
let winLoss: WinLoss = miamiTuples

miamiTuples.winningPercentage  // 0.5, 優先使用CricketRecord中已實作的方法
winLoss.winningPercentage  // 0.53, 使用WinLoss的extension中實作的方法
```

------

<h2 id="4">Type constraints</h2>

- 透過type constraint可以讓你在某個protocol中去使用另外其他protocol的東西。

> By using a type constraint on a protocol extension, you’re able to use methods and properties from another type inside the implementation of your extension.
>

```swift
protocol PostSeasonEligible {
  var minimumWinsForPlayoffs: Int { get }
}

// Type Constraints
extension TeamRecord where Self: PostSeasonEligible {
  var isPlayoffEligible: Bool {
   // TeamRecord中可以使用 PostSeasonEligible中宣告的minimumWinsForPlayoffs
    return wins > minimumWinsForPlayoffs
  }
}

protocol Tieable {
  var ties: Int { get }
}

// Type Constraints
extension TeamRecord where Self: Tieable {
  var winningPercentage: Double {
       // TeamRecord中可以使用 Tieable中宣告的ties
    return Double(wins) / Double(wins + losses + ties)
  }
}

struct RugyRecord: TeamRecord, Tieable {
  var wins: Int
  var losses: Int
  var ties: Int
}

let rugbyRecord = RugyRecord(wins: 8, losses: 7, ties: 1)
rugbyRecord.winningPercentage // 0.5
```

------

<h2 id="5">Programming to interfaces, not implementations</h2>

- [继承本身存在的问题](https://swift.gg/2015/12/15/mixins-over-inheritance/#fn1)

  - 继承的另一个问题就是很多面向对象的编程语言都不支持*多继承*

  > **<u>多用组合，少用继承。</u>**

------

<h2 id="6">Traits, mixins and multiple inheritance</h2>

- [參考](https://swift.gg/2015/12/15/mixins-over-inheritance/)

- Mixins 和 Traits 的概念[1](https://swift.gg/2015/12/15/mixins-over-inheritance/#fn1)由此引入。

  - 通过继承，你定义你的类是什么。例如每条 `Dog` 都*是*一个 `Animal`。
  - 通过 Traits，你定义你的类*能做什么*。例如每个 `Animal` 都*能* `eat()`，但是人类也可以吃，而且[异世奇人（Doctor Who）也能吃鱼条和蛋挞](https://www.youtube.com/watch?v=Oo2RKAHu-kI)，甚至即使是位 Gallifreyan（既不是人类也不是动物）。

  使用 Traits，重要的不是「是什么」，而是能「做什么」。

  > 继承描述了一个对象是什么，而 Traits 描述了这个对象能做什么。

  

  - Traits 很赞的一点就是它们并不依赖于使用到它们的对象本身的身份。Traits 并不关心类是什么，亦或是类是从哪里继承的：Traits 仅仅在类上定义了一些函数。

  > 异世奇人（Doctor Who）可以既是一位时间旅行者，同时还是一个外星人；而爱默·布朗博士（Dr Emmett Brown）既是一位时间旅行者，同时还属于人类；钢铁侠（Iron Man）是一个能飞的人，而超人（Superman）是一个能飞的外星人。

  

  ```swift
  protocol TieableRecord {
    var ties: Int { get }
  }
  
  protocol DivisionalRecord {
    var divisionalWins: Int { get }
    var divisionalLosses: Int { get }
  }
  
  protocol ScoreableRecord {
    var totalPoints: Int { get }
  }
  
  extension ScoreableRecord where Self: TieableRecord, Self: TeamRecord {
    var totalPoints: Int {
      return (2 * wins) + (1 * ties)
    }
  }
  
  // Traits，重要的不是「是什么」，而是能「做什么」
  struct NewHockeyRecord: TeamRecord, TieableRecord, DivisionalRecord, CustomStringConvertible, Equatable {
    var wins: Int
    var losses: Int
    var ties: Int
    var divisionalWins: Int
    var divisionalLosses: Int
      
    var description: String {
      return "\(wins) - \(losses) - \(ties)"
    }
  }
  ```

  

------

<h2 id="7">Simplicity</h2>

------

<h2 id="8">Why Swift is a protocol-oriented language</h2>

- 先以Swift中Array當做例子，如何利用POP來定義出Array
  - Array is a struct means it’s a value type
    - it can’t be subclassed nor can it be a superclass.
  - Array will get numerous properties and methods common to every **<u>Collection</u>**, such as first, count or isEmpty
  - you can split() an Array or find the index(of:) an element, assuming the type of that element conforms to **<u>Equatable</u>**


```swift
// From the Swift standard library
public struct Array<Element> : RandomAccessCollection, MutableCollection {
  // ...
}
```



------

<h2 id="9">Key points</h2>

- **<u>Protocol extensions</u>** let you write implementation code for protocols, and even write default implementations on methods required by a protocol.
- Protocol extensions are the primary driver for **<u>protocol-oriented programming</u>** and let you write code that will work on any type that conforms to a protocol.
- **<u>Type constraints</u>** on protocol extensions provide additional context and let you write more specialized implementations.
- You can decorate a type with **<u>traits and mixins</u>** to extend behavior without requiring inheritance.