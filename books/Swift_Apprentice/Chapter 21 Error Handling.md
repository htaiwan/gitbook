# Chapter 21: Error Handling

------

## 大綱

- [What is error handling?](#1)
- First level error handling with optionals
  - [Failable initializers](#2)
  - [Optional chaining](#3)
  - [Map and compactMap](#4)
- [Error protocol](#5)
- [Throwing errors](#6)
- [Handling errors](#7)
  - [Not looking at the detailed error](#8)
  - [Stoping your program on an error](#9)
- Advanced error handling
  - [PugBot](#10)
  - [Handling multiple errors](#11)
- [Rethrows](#12)
- [Key points](#13)

------

<h2 id="1">What is error handling?</h2>

- 一種處理失敗的藝術。
- app中並不是所有事件都可以如我們程式碼預期一樣的結果，例如網路狀態，讀取文件的格式， 想想app什麼時會發生錯誤，並該如何處理。

------

<h2 id="2">Failable initializers</h2>

- 透過Failable initializers回傳的是一個optional，而不絕對是一個instance, 用來處理init fail的error handle, 當init fail會回傳nil。

```swift
let value = Int("3")
// 透過Failable initializers來回傳error情況的case
let failedValue = Int("nope") // nil

enum PetFood: String {
  case kibble, canned
}

let morning = PetFood(rawValue: "kibble")
// 透過Failable initializers來回傳error情況的case
let snack = PetFood(rawValue: "fuuud!") // nil

struct PetHouse {
  let squareFeet: Int
  // 如何自己寫一個Failable initializers
  init?(squareFeet: Int) {
    if squareFeet < 1 {
      return nil
    }
    self.squareFeet = squareFeet
  }
}

let tooSmall = PetHouse(squareFeet: 0)
let house = PetHouse(squareFeet: 1)

```



------

<h2 id="3">Optional chaining</h2>

- 在層層複雜資料下，如何快速判斷某個property是否為optional, 不用一層一層的去解開，而是透過Optional chaining，只要當chaing的過程中，發現其中一個property為nil，就回傳nil。

```swift
class Toy {
    
  enum Kind {
    case ball
    case zombie
    case bone
    case mouse
  }
    
  enum Sound {
    case squeak
    case bell
  }
    
  let kind: Kind
  let color: String
  var sound: Sound?
    
  init(kind: Kind, color: String, sound: Sound? = nil) {
    self.kind = kind
    self.color = color
    self.sound = sound
  }
}

class Pet {
    
  enum Kind {
    case dog
    case cat
    case guineaPig
  }
    
  let name: String
  let kind: Kind
  let favoriteToy: Toy?
    
  init(name: String, kind: Kind, favoriteToy: Toy? = nil) {
    self.name = name
    self.kind = kind
    self.favoriteToy = favoriteToy
  }
}

class Person {
  let pet: Pet?
    
  init(pet: Pet? = nil) {
    self.pet = pet
  }
}

// 複雜的資料結構
let janie = Person(pet: Pet(name: "Delia", kind: .dog, favoriteToy: Toy(kind: .ball, color: "Purple", sound: .bell)))
let tammy = Person(pet: Pet(name: "Evil Cat Overlord", kind: .cat, favoriteToy: Toy(kind: .mouse, color: "Orange")))
let felipe = Person()

// 透過Optional chaining快速判斷某個值是否nil
if let sound = janie.pet?.favoriteToy?.sound {
  print("Sound \(sound)")
} else {
  print("No sound.")
}

if let sound = tammy.pet?.favoriteToy?.sound {
  print("Sound \(sound)")
} else {
  print("No sound.")
}

if let sound = felipe.pet?.favoriteToy?.sound {
  print("Sound \(sound)")
} else {
  print("No sound.")
}
```



------

<h2 id="4">Map and compactMap</h2>

- Map的功能就是快速將array中的內容mapping到另外一個內容。
- compactMap, 具有map的功能，另外還處理array萬一有nil的情況。

```swift
let team = [janie, tammy, felipe]
let petNames = team.map { $0.pet?.name }

for pet in petNames {
  // compiler warns you about conversion from Optional to Any
  print(pet as Any) // cast to Any to shut the warning off
  // Optional("Delia")
  // Optional("Evil Cat Overlord")
  // nil
}

// compactMap, 具有map的功能，另外還處理array萬一有nil的情況
let betterPetNames = team.compactMap { $0.pet?.name }

for pet in betterPetNames {
  print(pet)
  // Delia
 // Evil Cat Overlord
}
```

------

<h2 id="5">Error protocol</h2>

- 任何type都可以實作error protocol, 但最好的type就是用enum。

```swift
class Pastry {
  let flavor: String
  var numberOnHand: Int
    
  init(flavor: String, numberOnHand: Int) {
    self.flavor = flavor
    self.numberOnHand = numberOnHand
  }
}

// 實作error protocol
enum BakeryError: Error {
  case tooFew(numberOnHand: Int)
  case doNotSell
  case wrongFlavor
}
```



------

<h2 id="6">Throwing errors</h2>

- 定義好了error, 就是要用來處理程式中無法處理的case並throw對應的error。

```swift
class Bakery {
  var itemsForSale = [
    "Cookie": Pastry(flavor: "ChocolateChip", numberOnHand: 20),
    "PopTart": Pastry(flavor: "WildBerry", numberOnHand: 13),
    "Donut" : Pastry(flavor: "Sprinkles", numberOnHand: 24),
    "HandPie": Pastry(flavor: "Cherry", numberOnHand: 6)
  ]
    
  func orderPastry(item: String,
                 amountRequested: Int,
                 flavor: String)  throws  -> Int {
    guard let pastry = itemsForSale[item] else {
      // 無法處理的case並throw對應的error
      throw BakeryError.doNotSell
    }
    guard flavor == pastry.flavor else {
      // 無法處理的case並throw對應的error
      throw BakeryError.wrongFlavor
    }
    guard amountRequested <= pastry.numberOnHand else {
      // 無法處理的case並throw對應的error
      throw BakeryError.tooFew(numberOnHand: pastry.numberOnHand)
    }
    pastry.numberOnHand -= amountRequested
        
    return pastry.numberOnHand
  }
}

```



------

<h2 id="7">Handling errors</h2>

- Try: 根據不同的error進行對應的處理。

```swift
let bakery = Bakery()

do {
    // 此種try需要根據不同的error進行對應的處理
    try bakery.orderPastry(item: "Albatross",
                           amountRequested: 1,
                           flavor: "AlbatrossFlavor")
} catch BakeryError.doNotSell {
  print("Sorry, but we don't sell this item")
} catch BakeryError.wrongFlavor {
  print("Sorry, but we don't carry this flavor")
} catch BakeryError.tooFew {
  print("Sorry, we don't have enough items to fulfill your order")
}

```



------

<h2 id="8">Not looking at the detailed error</h2>

- Try?: 只想知道有error發生，不須特別知道是哪種error, 就可以使用，可以不用寫catch。

```swift
let remaining = try? bakery.orderPastry(item: "Albatross",
                                        amountRequested: 1,
                                        flavor: "AlbatrossFlavor")
```



------

<h2 id="9">Stoping your program on an error</h2>

- Try!: 當有error發生，強制中斷程式。

```swift
do {
  try bakery.orderPastry(item: "Cookie", amountRequested: 11, flavor: "ChocolateChip")
}
catch {
  fatalError()
}

// 等同上面的寫法
try! bakery.orderPastry(item: "Cookie", amountRequested: 11, flavor: "ChocolateChip")
```

 

------

<h2 id="10">PugBot</h2>

```swift
enum Direction {
  case left
  case right
  case forward
}
// 定義error
enum PugBotError: Error {
  case invalidMove(found: Direction, expected: Direction)
  case endOfPath
}

class PugBot {
  let name: String
  let correctPath: [Direction]
  private var currentStepInPath = 0
    
  init(name: String, correctPath: [Direction]) {
    self.correctPath = correctPath
    self.name = name
  }
   
  // 此func可能會丟出error
  func turnLeft() throws {
    guard currentStepInPath < correctPath.count else {
            throw PugBotError.endOfPath
    }
    let nextDirection = correctPath[currentStepInPath]
    guard nextDirection == .left else {
            // 無法處理, 丟出error
            throw PugBotError.invalidMove(found: .left,
                                          expected: nextDirection)
    }
    currentStepInPath += 1
  }
    
  func turnRight() throws {
    guard currentStepInPath < correctPath.count else {
            throw PugBotError.endOfPath
    }
    let nextDirection = correctPath[currentStepInPath]
    guard nextDirection == .right else {
            throw PugBotError.invalidMove(found: .right,
                                          expected: nextDirection)
    }
    currentStepInPath += 1
  }
    
  func moveForward() throws {
    guard currentStepInPath < correctPath.count else {
            throw PugBotError.endOfPath
    }
    let nextDirection = correctPath[currentStepInPath]
    guard nextDirection == .forward else {
            throw PugBotError.invalidMove(found: .forward,
                                          expected: nextDirection)
    }
    currentStepInPath += 1
  }
  
  func reset() {
    currentStepInPath = 0
  }
}

let pug = PugBot(name: "Pug", correctPath: [.forward, .left, .forward, .right])

// 將內部使用fun可能拋出的error, 再向外拋出，不直接處理內部fun所拋出的error
func goHome() throws {
  try pug.moveForward()
  try pug.turnLeft()
  try pug.moveForward()
  try pug.turnRight()
}

// 在外層method被使用到時，才開始處理
do {
  try goHome()
} catch {
  print("PugBot failed to get home.")
}
```



------

<h2 id="11">Handling multiple errors</h2>

> “Unfortunately, at this point, Swift’s do-try-catch system isn’t type-specific. There’s no way to tell the compiler that it should only expect PugBotErrors. To the compiler, that isn’t exhaustive, because it doesn’t handle each and every possible error that it knows about, **<u>so you still need a default case</u>**”
>

```swift
func moveSafely(_ movement: () throws -> ()) -> String {
  do {
    try movement()
    return "Completed operation successfully."
  } catch PugBotError.invalidMove(let found, let expected) {
    return "The PugBot was supposed to move \(expected), but moved \(found) instead."
  } catch PugBotError.endOfPath {
    return "The PugBot tried to move past the end of the path."
  } catch {
    // 需要加上default case
    return "An unknown error occurred"
  }
}

pug.reset()
moveSafely(goHome)

pug.reset()
moveSafely {
  try pug.moveForward()
  try pug.turnLeft()
  try pug.moveForward()
  try pug.turnRight()
}
```



------

<h2 id="12">Rethrows</h2>

- 利用rethrow關鍵字，可以明確知道某個fun, 並沒有直接處理error, 而是將內部error在外拋出去。

```swift
func perform(times: Int, movement: () throws -> ()) rethrows {
  for _ in 1...times {
    try movement()
  }
}
```



------

<h2 id="13">Key points</h2>

- A type can conform to the Error protocol to work with Swift’s error-handling system.
- Any function that can throw an error, or call a function that can throw an error, has to be marked with throws or rethrows.
- When calling an error-throwing function, you must embed the function call in a do block. Within that block, you try the function, and if it fails, you catch the error.