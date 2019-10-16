# For Loops

```dart
void main() {
  var sum = 0;
  
  for (var i = 1; i <= 10; i++) {
    sum += i;  
  }
  print("The sum is $sum");
    
  var numbers = [1, 2, 3, 4];
  
  for (var number in numbers) {
    if (number == 3) {
      continue;
    }
    print(number); // 1,2,4
  }
  
  numbers.forEach((number) => print(number));  // 1,2,3,4
}
```

```dart
void main() {

  // Challenge Time - For Loops!
  /*
   * Use a for loop to print the square of each number from 1 to 10.
   */

  for (var i =  0; i <= 10; i++) {
    print(i * i);
  }

  /*
   * The code below iterates over only odd rows.
   * Change this to use a different row step size
   * to skip even rows instead of using continue.
   * Check that the sum is still 448 after your modifications.
   */

  var sum = 0;
  for (var row = 1; row < 8; row += 2) {
    for (var column = 0; column < 8; column++) {
      sum += row * column;
    }
  }
  print(sum == 448);
}
```

