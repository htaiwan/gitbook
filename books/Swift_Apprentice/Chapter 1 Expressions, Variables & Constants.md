# Chapter 1: Expressions, Variables & Constants

------

## 大綱

- How a computer works
  - Representing numbers
  - Binary numbers
  - Hexadecimal numbers
  - How code works
- Playgrounds
- Getting started with Swift
  - Code comments
- Printing out
- Arithmetic operations
  - Simple operations
  - Decimal numbers
  - The remainder operation
  - Shift operations
  - Order of operations
- Math functions
- Naming data
  - Constants
  - Variables
  - Using meaningful names
- Increment and decrement
- Key points

------

```swift

// COMMENTS
// This is a comment. It is not executed.

// This is also a comment.
// Over multiple lines.

/* This is also a comment.
   Over many..
   many...
   many lines. */

/* This is a comment.

 /* And inside it
 is
 another comment.
 */

 Back to the first.
 */


// PRINT
print("Hello, Swift Apprentice reader!")


// ARITHMETIC
2 + 6

10 - 2

2 * 4

24 / 3

2+6

22 / 7

22.0 / 7.0

28 % 10

(28.0).truncatingRemainder(dividingBy: 10.0)

1 << 3

32 >> 2

((8000 / (5 * 10)) - 32) >> (29 % 5)

350 / 5 + 2

350 / (5 + 2)


// MATH FUNCTIONS
sin(45 * Double.pi / 180)

cos(135 * Double.pi / 180)

(2.0).squareRoot()

max(5, 10)

min(-5, -10)

max((2.0).squareRoot(), Double.pi / 2)


// VARIABLES & CONSTANTS
let number: Int = 10
//number = 0 /* Cannot assign to value: 'constantNumber' is a 'let' constant */

let pi: Double = 3.14159

var variableNumber: Int = 42
variableNumber = 0
variableNumber = 1_000_000

var 🐶💩: Int = -1


// ARITHMETIC WITH VARIABLES
var counter: Int = 0
counter += 1
counter -= 1

counter = 10
counter *= 3
counter /= 2

```

​	