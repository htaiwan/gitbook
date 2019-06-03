

# Chapter 6: Optionals

------

## 大綱

- [Introducing nil](#1)
  - [Sentinel values](#2)
- [Introducing optionals](#3)
- [Unwrapping optionals](#5)
  - [Force unwrapping](#6)
  - [Optional binding](#7)
- [Introducing guard](#8)
- [Nil coalescing](#9)
- [Key points](#10)

------

<h2 id="1">Introducing nil</h2>

- it’s useful to represent the absence of a value


------

<h2 id="2">Sentinel values</h2>

- it’s useful to represent the absence of a value
  - 0 is a sentinel value.
- Nil is the name given to the absence of a value, and you’re about to see how Swift incorporates this concept directly into the language in a rather elegant way.


```swift
var errorCode = 0
```

------

<h2 id="3">Introducing optionals</h2>

- Optionals are Swift’s solution to the problem of representing both a value and the absence of a value

```swift
var errorCode: Int?
```

------

<h2 id="5">Unwrapping optionals</h2>



------

<h2 id="6">Force unwrapping</h2>

- you get this error at runtime rather than compile time 
- if this code were inside an app, the runtime error would cause the app to crash


```swift
authorName = nil
print("Author is \(authorName!)") // Fatal error: Unexpectedly found nil while unwrapping an Optional value
```

------

<h2 id="7">Optional binding</h2>

- You can combine unwrapping multiple optionals with additional Boolean checks


```swift
if let authorName = authorName,
   let authorAge = authorAge,
   authorAge >= 40 {
  print("The author is \(authorName) who is \(authorAge) years old.")
} else {
  print("No author or no age or age less than 40.")
}
```

------

<h2 id="8">Introducing guard</h2>

```swift
func maybePrintSides(shape: String) {
  guard let sides = calculateNumberOfSides(shape: shape) else {
    print("I don't know the number of sides for \(shape).")
    return
  }

  print("A \(shape) has \(sides) sides.")
}
```

------

<h2 id="9">Nil coalescing</h2>

```swift
var optionalInt: Int? = 10
var mustHaveResult = optionalInt ?? 0
```

------

<h2 id="10">Key points</h2>

- nil represents the absence of a value.
- Non-optional variables and constants must always have a non-nil value.
- Optional variables and constants are like boxes that can contain a value or be empty (nil).
- To work with the value inside an optional, you must first unwrap it from the optional.
- The safest ways to unwrap an optional’s value is by using optional binding or nil coalescing. 
- Use forced unwrapping only when appropriate, as it could produce a runtime error.