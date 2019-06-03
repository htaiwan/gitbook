# Chapter 5: Functions

------

## 大綱

- [Function basics](#1)
  - Function parameters
  - Return values
  - Advanced parameter handling
  - Overloading
- [Functions as variables](#2)
  - The land of no return
  - Writing good functions
- [Key points](#3)

------

<h2 id="1">Function basics</h2>

```Swift
// Function parameters
func printMultipleOf(multiplier: Int, and value: Int) {
  print("\(multiplier) * \(value) = \(multiplier * value)")
}
printMultipleOf(multiplier: 4, and: 2)

func printMultipleOf(_ multiplier: Int, and value: Int) {
  print("\(multiplier) * \(value) = \(multiplier * value)")
}
printMultipleOf(4, and: 2)

func printMultipleOf(_ multiplier: Int, _ value: Int) {
  print("\(multiplier) * \(value) = \(multiplier * value)")
}
printMultipleOf(4, 2)
```

```swift
// Return values
func multiply(_ number: Int, by multiplier: Int) -> Int {
  return number * multiplier
}
let result = multiply(4, by: 2)

func multiplyAndDivide(_ number: Int, by factor: Int)
                   -> (product: Int, quotient: Int) {
  return (number * factor, number / factor)
}
let results = multiplyAndDivide(4, by: 2)
let product = results.product
let quotient = results.quotient
```

```Swift
// Advanced parameter handling
func incrementAndPrint(_ value: Int) {
  value += 1
  print(value) // Left side of mutating operator isn't mutable: 'value' is a 'let' constant
}

// 使用inout
// The parameter value is the equivalent of a constant declared with let. Therefore, when the function attempts to increment it, the compiler emits an error.
// inout before the parameter type indicates that this parameter should be copied in, that local copy used within the function, and copied back out when the function returns.
func incrementAndPrint(_ value: inout Int) {
  value += 1
  print(value)
}

var value = 5
incrementAndPrint(&value) // 6
print(value) // 6
```

```Swift
// Overloading
func printMultipleOf(multiplier: Int, andValue: Int)
func printMultipleOf(multiplier: Int, and value: Int)
func printMultipleOf(_ multiplier: Int, and value: Int)
func printMultipleOf(_ multiplier: Int, _ value: Int)
```

- Whenever you call a function, it should always be clear which function you’re calling. This is usually achieved through **a difference in the parameter list**:
  - A different number of parameters.
  - Different parameter types.
  - Different external parameter names, such as the case with printMultipleOf.

```Swift
func getValue() -> Int {
  return 31
}

func getValue() -> String {
  return "Matt Galloway"
}

let value = getValue() // error: ambiguous use of 'getValue()'

let valueInt: Int = getValue()
let valueString: String = getValue()
```

- Only use overloading for functions that are related and similar in behavior. When only the return type is overloaded, as in the above example, **you loose type inference and so is not recommended**.

------

<h2 id="2">Functions as variables</h2>

```Swift
func add(_ a: Int, _ b: Int) -> Int {
  return a + b
}

var function = add
function(4, 2)

func subtract(_ a: Int, _ b: Int) -> Int {
  return a - b
}
function = subtract
function(4, 2)

func printResult(_ function: (Int, Int) -> Int, _ a: Int, _ b: Int) {
  let result = function(a, b)
  print(result)
}
printResult(add, 4, 2)
```



------

<h2 id="3">Key points</h2>

- You use a function to define a task that you can execute as many times as you like without having to write the code multiple times.
- Functions can take zero or more parameters and optionally return a value.
- You can add an external name to a function parameter to change the label you use in a function call, or you can use an underscore to denote no label.
- Parameters are passed as constants, unless you mark them as inout, in which case they are copied-in and copied-out.
- Functions can have the same name with different parameters. This is called overloading.
- Functions can have a special Never return type to inform Swift that this function will never exit.
- You can assign functions to variables and pass them to other functions.
- Strive to create functions that are clearly named and have one job with repeatable inputs and outputs.