# Function Basics

```dart
void printIfBanana(String fruit) {
  if (fruit == 'banana') {
    print('Banana!');
  }
}

bool isBanana(String fruit) {
  return fruit == 'banana';
}

bool withinTolerance({int value, int min = 0, int max = 10}) {
  return min <= value && value <= max;
}

void main() {
  
  var fruit = 'apple';
  printIfBanana(fruit);
  
  print(isBanana(fruit));
  
  fruit = 'orange';
  printIfBanana(fruit);
  print(isBanana(fruit));
  
  print(withinTolerance(min: 1, max: 10, value: 11));
  print(withinTolerance(value: 5));
  
  String fullName(String first, String last, [String title]) {
    return "${title == null ? '' : '$title '}$first $last";
  }
  
  print(fullName("Joe", "Howard"));
  print(fullName("Albert", "Einstein", "Professor"));
  
  int applyTo(int value, int Function(int) op) {
    return op(value);
  }
  
  int square(int n) {
    return n * n;
  }
  
  print(applyTo(3, square)); // 9
  
  var op = square;
  print(op(5));  // 25
}
```

```dart
import 'dart:math';

void main() {
  
  // Challenge Time - Functions!
  
  /*
   * Your challenge is to write a function that checks if 
   * a number is prime. First, write a function
   * 
   * bool isNumberDivisible(int number, int divisor)
   * 
   * to determine if one number is divisible by another.
   * Hint: Use the modulo operator %.
   * 
   * Then, write the function
   * 
   * bool isPrime(int number)
   * 
   * that returns true if prime and false otherwise. A number
   * is prime if it's only divisible by 1 and itself. Loop
   * through the numbers from 1 to the number and find the numbers
   * divisors. If it has any divisors other than 1 and itself, it is not
   * prime.
   * 
   * Check the following cases:
   * isPrime(6); // false
   * isPrime(13); // true
   * isPrime(8893); // true
   * 
   * Hint 1: Numbers less than zero are not considered prime.
   * Hint 2: Use a for loop to look for divisors. You can start at 2
   *         and if you end before the number, return false.
   * Hint 3: If you're clever, you can loop from 2 until you reach
   *         the square root of the number. Add the following import
   *         to the top of the file to access the sqrt function:
   * 
   * import 'dart:math';
   */
  
  bool isNumberDivisible(int number, int divisor) {
    return number % divisor == 0;
  }
  
  bool isPrime(int number) {
    var isPrime = true;
    for (var i = 2; i <= sqrt(number); i++) {
      if (isNumberDivisible(number, i)) {
        isPrime = false;
      }
    }
    return isPrime;
  }
  
  print(isPrime(6)); //false
  print(isPrime(13)); // true
  print(isPrime(8893)); // true
```

