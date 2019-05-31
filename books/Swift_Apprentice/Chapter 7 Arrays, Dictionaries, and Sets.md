# Chapter 7: Arrays, Dictionaries, and Sets

------

## 大綱

- [Mutable versus immutable collections](#1)
- [Arrays](#2)
  - Creating arrays
  - Accessing elements
    - Using properties and methods
    - Using subscripting
  - Checking for an element
  - Modifying array
    - Appending elements
    - Inserting elements
    - Removing elements
    - Updating elements
    - Moving elements
  - Iterating through an array
  - [Running time for array operations](#3)
- [Dictionaries](#4)
  - Creating dictionaries
  - Accessing elements
    - Using properties and methods
    - Using subscripting
  - Modifying dictionaries
    - Adding pairs
    - Updating values
    - Removing pairs
  - Iterating through dictionaries
  - [Running time for dictionary operations](#5)
- [Sets](#6)
  - Creating sets
  - Set literals
  - Accessing elements
  - Adding and removing elements
  - Running time for set operations
- [Key points](#7)

------

<h2 id="1">Mutable versus immutable collections</h2>

- If the collection doesn’t need to change after you’ve created it, you should make it immutable by declaring it as a constant with let. 
- if you need to add, remove or update values in the collection, then you should create a mutable collection by declaring it as a variable with var.

------

<h2 id="2">Arrays</h2>

```swift
// -------------------
// | CREATING ARRAYS |
// -------------------

let evenNumbers = [2, 4, 6, 8]

var subscribers: [String] = []

let allZeros = Array(repeating: 0, count: 5)

let vowels = ["A", "E", "I", "O", "U"]

// ----------------------
// | ACCESSING ELEMENTS |
// ----------------------

var players = ["Alice", "Bob", "Cindy", "Dan"]

print(players.isEmpty)
// > false

if players.count < 2 {
  print("We need at least two players!")
} else {
  print("Let's start!")
}
// > Let's start!

var currentPlayer = players.first

print(currentPlayer as Any)
// > Optional("Alice")

print(players.last as Any)
// > Optional("Dan")

currentPlayer = players.min()
print(currentPlayer as Any)
// > Optional("Alice")

print([2, 3, 1].first as Any)
// > Optional(2)
print([2, 3, 1].min() as Any)
// > Optional(1)

if let currentPlayer = currentPlayer {
  print("\(currentPlayer) will start")
}
// > Alice will start

var firstPlayer = players[0]
print("First player is \(firstPlayer)")
// > First player is "Alice"

//var player = players[4]
// > fatal error: Index out of range

let upcomingPlayersSlice = players[1...2]
print(upcomingPlayersSlice[1], upcomingPlayersSlice[2])
// > "Bob Cindy\n"

let upcomingPlayersArray = Array(players[1...2])
print(upcomingPlayersArray[0], upcomingPlayersArray[1])
// > "Bob Cindy\n"


func isEliminated(player: String) -> Bool {
  return !players.contains(player)
}

print(isEliminated(player: "Bob"))
// > false

players[1...3].contains("Bob")

// -------------------------
// | MANIPULATING ELEMENTS |
// -------------------------

players.append("Eli")

players += ["Gina"]

print(players)
// > ["Alice", "Bob", "Cindy", "Dan", "Eli", "Gina"]

players.insert("Frank", at: 5)

// ---------------------
// | REMOVING ELEMENTS |
// ---------------------

var removedPlayer = players.removeLast()
print("\(removedPlayer) was removed")
// > Gina was removed

removedPlayer = players.remove(at: 2)
print("\(removedPlayer) was removed")
// > Cindy was removed

// ---------------------
// | UPDATING ELEMENTS |
// ---------------------

print(players)
// > ["Alice", "Bob", "Dan", "Eli", "Frank"]
players[4] = "Franklin"
print(players)
// > ["Alice", "Bob", "Dan", "Eli", "Franklin"]

players[0...1] = ["Donna", "Craig", "Brian", "Anna"]
print(players)
// > ["Donna", "Craig", "Brian", "Anna", "Dan", "Eli", "Franklin"]

let playerAnna = players.remove(at: 3)
players.insert(playerAnna, at: 0)
print(players)
// > ["Anna", "Donna", "Craig", "Brian", "Dan", "Eli", "Franklin"]

players.swapAt(1, 3)
print(players)
// > ["Anna", "Brian", "Craig", "Donna", "Dan", "Eli", "Franklin"]

players.sort()
print(players)
// > ["Anna", "Brian", "Craig", "Dan", "Donna", "Eli", "Franklin"]

// -------------
// | ITERATION |
// -------------

let scores = [2, 2, 8, 6, 1, 2, 1]

for player in players {
  print(player)
}
// > Anna
// > Brian
// > Craig
// > Dan
// > Donna
// > Eli
// > Franklin

for (index, player) in players.enumerated() {
  print("\(index + 1). \(player)")
}
// > 1. Anna
// > 2. Brian
// > 3. Craig
// > 4. Dan
// > 5. Donna
// > 6. Eli
// > 7. Franklin

func sumOfElements(in array: [Int]) -> Int {
  var sum = 0
  for number in array {
    sum += number
  }
  return sum
}

print(sumOfElements(in: scores))
// > 22
```



------

<h2 id="3">Running time for array operations</h2>

- Accessing elements: O(1)
- Inserting elements: 
  - add to the beginning of the array:  O(n).
  - add to the middle of the array: O(n).
  - add to the end of the array using append and there’s room: O(1).
- Deleting elements: O(1)
- Searching for an element: O(n)


------

<h2 id="4">Dictionaries</h2>

```swift
// -------------------------
// | CREATING DICTIONARIES |
// -------------------------

var namesAndScores = ["Anna": 2, "Brian": 2, "Craig": 8, "Donna": 6]
print(namesAndScores)
// > ["Craig": 8, "Anna": 2, "Donna": 6, "Brian": 2]

namesAndScores = [:]

var pairs: [String: Int] = [:]

pairs.reserveCapacity(20)

// --------------------
// | ACCESSING VALUES |
// --------------------

namesAndScores = ["Anna": 2, "Brian": 2, "Craig": 8, "Donna": 6]
// Restore the values

print(namesAndScores["Anna"]!) // 2

namesAndScores["Greg"] // nil

namesAndScores.isEmpty // false

namesAndScores.count // 4

Array(namesAndScores.keys) // ["Craig", "Anna", "Donna", "Brian"]
Array(namesAndScores.values) // [8, 2, 6, 2]

// -----------------
// | ADDING VALUES |
// -----------------

var bobData = ["name": "Bob", "profession": "Card Player", "country": "USA"]

bobData.updateValue("CA", forKey: "state")

bobData["city"] = "San Francisco"

// -------------------
// | UPDATING VALUES |
// -------------------

bobData.updateValue("Bobby", forKey: "name")

bobData["profession"] = "Mailman"

bobData.removeValue(forKey: "state")

bobData["city"] = nil

// -------------
// | ITERATION |
// -------------

for (player, score) in namesAndScores {
  print("\(player) - \(score)")
}
// > Craig - 8
// > Anna - 2
// > Donna - 6
// > Brian - 2

for player in namesAndScores.keys {
  print("\(player), ", terminator: "") // no newline
}
print("") // print one final newline
// > Craig, Anna, Donna, Brian,

```

------

<h2 id="5">Running time for dictionary operations</h2>

- While all of these running times compare favorably to arrays, remember that **you lose order information** when using dictionaries.. 


- Accessing elements: O(1)
- Inserting elements: O(1)
- Deleting elements: O(1)
- Searching for an element: O(1)

------

<h2 id="6">Sets</h2>

```swift
// -----------------
// | CREATING SETS |
// -----------------

let setOne: Set<Int> = [1]

let someArray = [1, 2, 3, 1]

var explicitSet: Set<Int> = [1, 2, 3, 1]

var someSet = Set([1, 2, 3, 1])


print(someSet)
// > [1, 3, 2]  but the order is not defined

// ----------------------
// | ACCESSING ELEMENTS |
// ----------------------

print(someSet.contains(1))
// > true
print(someSet.contains(4))
// > false

// ------------------------------
// | ADDING & REMOVING ELEMENTS |
// ------------------------------

someSet.insert(5)

let removedElement = someSet.remove(1)
print(removedElement!)
// > 1
```



------

<h2 id="7">Key points</h2>

- Sets
  - Are unordered collections of unique values of the same type.
  - Are most useful when you need to know whether something is included in the collection or not.
- Dictionaries
  - Are unordered collections of key-value pairs.
  - The keys are all of the same type, and the values are all of the same type.
  - Use subscripting to get values and to add, update or remove pairs.
  - If a key is not in a dictionary, lookup returns nil.
  - The key of a dictionary must be a type that conforms to the Hashable protocol.
  - Basic Swift types such as String, Int, Double are Hashable out of the box.
- Arrays:
  - Are ordered collections of values of the same type.
  - Use subscripting, or one of the many properties and methods, to access and update elements.
  - Be wary of accessing an index that’s out of bounds.
    