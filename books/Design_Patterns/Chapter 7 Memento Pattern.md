# Chapter 7: Memento Pattern

------

## 大綱

- [When should you use it?](#1)
- [Playground example](#2)
- [What should you be careful about?](#3)
- [Tutorial project](#4)
- [Key points](#5)

------

<h2 id="1">When should you use it?</h2>

- **The memento pattern** allows an object to be saved and restored. 
  - **The originator** is the object to be saved or restored.
  - **The memento** represents a stored state.
  - **The caretaker** requests a save from the originator and receives a memento in response. 
    - The caretaker is responsible for persisting the memento and, later on, providing the memento back to the originator to restore the originator’s state.

![](../.gitbook/assets/38.png)

- **When should you use it?**
  - Use the memento pattern whenever you want to save and later restore an object’s state.


------

<h2 id="2">Playground example</h2>

- 目標: use this pattern to implement a save game system, where the originator is the game state (such as level, health, number of lives, etc), 
  - the memento is saved data
  - the caretaker is the gaming system.

```swift
// MARK: - Example
var game = Game()
game.monstersEatPlayer()
game.rackUpMassivePoints()

// Save Game
let gameSystem = GameSystem()
try gameSystem.save(game, title: "Best Game Ever")

// New Game
game = Game()
print("New Game Score: \(game.state.score)") // New Game Score: 0

// Load Game
game = try! gameSystem.load(title: "Best Game Ever")
print("Loaded Game Score: \(game.state.score)") // Loaded Game Score: 9002
```

- Originator

```swift
// MARK: - Originator
// has an internal State that holds onto game properties, and it has methods to handle in-game actions. You also declare Game and State conform to Codable.
public class Game: Codable {
  
  public class State: Codable {
    public var attemptsRemaining: Int = 3
    public var level: Int = 1
    public var score: Int = 0
  }
  public var state = State()
  
  public func rackUpMassivePoints() {
    state.score += 9002
  }
  
  public func monstersEatPlayer() {
    state.attemptsRemaining -= 1
  }
}
```

- Memento

```swift
// MARK: - Memento
// Technically, you don’t need to declare this line at all. Rather, it’s here to inform you the GameMemento is actually Data
typealias GameMemento = Data
```

- CareTaker

```swift
// MARK: - CareTaker
public class GameSystem {
  
  // use decoder to decode Games from Data, encoder to encode Games to Data, and userDefaults to persist Data to disk
  private let decoder = JSONDecoder()
  private let encoder = JSONEncoder()
  private let userDefaults = UserDefaults.standard
  
  // use encoder to encode the passed-in game. This operation may throw an error, so you must prefix it with try. You then save the resulting data under the given title within userDefaults.
  public func save(_ game: Game, title: String) throws {
    let data = try encoder.encode(game)
    userDefaults.set(data, forKey: title)
  }
  
  // get data from userDefaults for the given title. You then use decoder to decode the Game from the data. If either operation fails, you throw a custom error for Error.gameNotFound.
  public func load(title: String) throws -> Game {
    guard let data = userDefaults.data(forKey: title),
      let game = try? decoder.decode(Game.self, from: data)
      else {
        throw Error.gameNotFound
    }
    return game
  }
  
  public enum Error: String, Swift.Error {
    case gameNotFound
  }
}
```

------

<h2 id="3">What should you be careful about?</h2>

- Be careful when adding or removing Codable properties: **both encoding and decoding can throw an error**. If you force unwrap these calls using try! and you’re missing any required data, your app will crash!
  - To mitigate this problem, **avoid using try! unless you’re absolutely sure the operation will succeed**. You should also plan ahead when changing your models.
- carefully consider how to **handle version upgrades**. 
  - You might choose to delete old data whenever you encounter a new version, create an upgrade path to convert from old to new data, or even use a combination of these approaches.

------

<h2 id="4">Tutorial project</h2>



------

<h2 id="5">Key points</h2>

- The memento pattern allows an object to be saved and restored. It involves three types: the originator, memento and caretaker.
- The originator is the object to be saved; the memento is a saved state; and the caretaker handles, persists and retrieves mementos.
- iOS provides Encoder for encoding a memento to, and Decoder for decoding from, a memento. This allows encoding and decoding logic to be used across originators.