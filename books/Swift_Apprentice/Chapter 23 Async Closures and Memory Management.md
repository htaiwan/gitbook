# Chapter 23: Async Closures and Memory Management

------

## 大綱

- [Playgrounds and reference cycles](#1)
- [Reference cycles for classes](#2)
  - [Weak references](#3)
  - [Unowned references](#4)
- [Reference cycles for closures](#5)
  - [Capture lists](#6)
  - [Unowned self](#7)
- [Handling escaping closures](#8)
  - [GCD](#9)
  - [Weak self](#10)
  - [The strong-weak pattern](#11)
- [Key points](#12)

------

<h2 id="1">Playgrounds and reference cycles</h2>

> “**<u>Playgrounds sometimes add extra "invisible" references to objects as you run them</u>**. Because of this, objects do not deallocate when then normally should. This makes it difficult to study reference counting using Playgrounds. **<u>Because of this, for this chapter you can use a Swift macOS command line tool to test out reference cycles.</u>**”
>

------

<h2 id="2">Reference cycles for classes</h2>

```swift
class Tutorial {
  let title: String
  var editor: Editor?
  
  init(title: String) {
    self.title = title
  }
  
  deinit {
    print("Goodbye Tutorial \(title)!")
  }
}

class Editor {
  let name: String
  var tutorials: [Tutorial] = []
  
  init(name: String) {
    self.name = name
  }
  
  deinit {
    print("Goodbye Editor \(name)!")
  }
}

do {
  let tutorial = Tutorial(title: "Memory management", author: author)
  let editor = Editor(name: "Ray")
}

do {
  let tutorial = Tutorial(title: "Memory management", author: author)
  let editor = Editor(name: "Ray")
  // 產生Reference cycles
  tutorial.editor = editor
  editor.tutorials.append(tutorial)
}

```

------

<h2 id="3">Weak references</h2>

- Weak references不會增加某個object的reference count。
- Weak只能用在宣告成optional的那些屬性上。
- 在上面的例子中，不是每個tutorial都一定有editor, 因此把它宣告成weak。

```swift
 weak var editor: Editor?
```

------

<h2 id="4">Unowned references</h2>

- 跟Weak一樣，不會增加reference count, 但只能用在那些不是optional的屬性上。
- 例如，每個tutorial都一定會有個author。

```swift
class Tutorial {
  let title: String
  unowned let author: Author
  weak var editor: Editor?
  
  init(title: String, author: Author) {
    self.title = title
    self.author = author
  }
  
  deinit {
    print("Goodbye Tutorial \(title)!")
  }
}

class Author {
  let name: String
  var tutorials: [Tutorial] = []
  
  init(name: String) {
    self.name = name
  }
  deinit {
    print("Goodbye Author \(name)!")
  }
}

do {
  let author = Author(name: "Cosmin")
  let tutorial = Tutorial(title: "Memory management", author: author)
  print(tutorial.createDescription())
  let editor = Editor(name: "Ray")
  author.tutorials.append(tutorial)
  tutorial.editor = editor
  editor.tutorials.append(tutorial)
}
```

------

<h2 id="5">Reference cycles for closures</h2>

- Clousure也是屬於一個reference type，所以也有可能跟其他type產生reference cycle。
- 在Tutorial class中加入一個lazy property
  - lazy property不會被assign任何值，直到第一次使用。

```swift
lazy var createDescription: () -> String = {
  // 產生一個strong reference cycle
 // tutorial object 和 closure by capturing self
  return "\(self.title) by \(self.author.name)"
}
print(tutorial.createDescription())
```

------

<h2 id="6">Capture lists</h2>

- A capture list is a list of variables captured by a closure. 
- A capture list appears at the very beginning of the closure before any arguments.
- With reference types, a capture list will cause the closure to capture and store the current reference stored inside the captured variable.


```swift
var counter = 0
var f = { print(counter) }
counter = 1
f() // 1

counter = 0
f = { [c = counter] in print(c) }
counter = 1
f() // 0

counter = 0
f = { [counter] in print(counter) }
counter = 1
f() // 0
```



------

<h2 id="7">Unowned self</h2>

- 在closure的capture list加上unowned 解除closure跟物件tutorial之間的reference cycle。

```swift
 lazy var createDescription: () -> String = {
    [unowned self] in
    return "\(self.title) by \(self.author.name)"
  }
```

------

<h2 id="8">Handling escaping closures</h2>

-  non-escaping: Array中的flitter closure
  - After the filter completes, the closure is never accessed again and is said to be non-escaping
- escaping closures: GCD closure
  - The danger is that since the order of completion changes from run to run, **<u>you must be careful not to access an unowned reference that may have disappeared</u>**


------

<h2 id="9">GCD</h2>

```swift
// 打印目前thread狀態
func log(message: String) {
  let thread = Thread.current.isMainThread ? "Main" : "Background"
  print("\(thread) thread: \(message)")
}

// 用這個func來當作複雜的工作
func addNumbers(upTo range: Int) -> Int {
  log(message: "Adding numbers...")
  return (1...range).reduce(0, +)
}

let queue = DispatchQueue(label: "queue")

func execute<Result>(backgroundWork: @escaping () -> Result, mainWork: @escaping (Result) -> ()) {
  queue.async {
    // 將複雜的工作放到背景中執行
    let result = backgroundWork()
    DispatchQueue.main.async {
      // 執行完成才會到main thread中
      mainWork(result)
    }
  }
}

execute(backgroundWork: { addNumbers(upTo: 100) },
        mainWork:  { log(message: "The sum is \($0)") })
```



------

<h2 id="10">Weak self</h2>

- you declared self as unowned. However, this code isn’t safe! **<u>If, for some reason, the editor went out of scope before editTutorial had completed, self.feedback would crash when it eventually executed.</u>**


```swift
func editTutorial(_ tutorial: Tutorial) {
  queue.async() {
    // 這裡使用unowned會有問題, 改用weak
    [unowned self] in
    let feedback = self.feedback(for: tutorial)
    DispatchQueue.main.async {
      print(feedback)
    }
  }
}
```



------

<h2 id="11">The strong-weak pattern</h2>

```swift
  func editTutorial(_ tutorial: Tutorial) {
    queue.async() {
      [weak self] in
      // 把weak改成strong, 確保使用ok
      guard let self = self else {
        print("I no longer exist so no feedback for you!")
        return
      }
      DispatchQueue.main.async {
        print(self.feedback(for: tutorial))
      }
    }
  }
}
```



------

<h2 id="12">Key points</h2>

- Use a weak reference to break a strong reference cycle if a reference may become nil at some point in its lifecycle.
- Use an unowned reference to break a strong reference cycle when you know a reference always has a value and will never be nil.
- You must use self inside a closure’s body. This is the way the Swift compiler hints to you that you need to be careful not to make a circular reference.
- Capture lists define how you capture values and references in closures.
- The strong weak pattern extends an object’s lifecycle for asynchronous closures.
- An escaping closure can be used after the corresponding function returns.
  



