# Enum and Switch

```dart
enum Month {
  january,
  february,
  march,
  april,
  may,
  june,
  july,
  august,
  september,
  october,
  november,
  december
}

enum Semester { fall, spring, summer }

void main() {
  
  final month = Month.october;
  print(month); // Month.october
  print(month.index); // 9
  
  print(Month.values); // [Month.january, Month.february, Month.march, Month.april, Month.may, Month.june, Month.july, Month.august, Month.september, Month.october, Month.november, Month.december]
  
  Semester semester;

  switch (month) {
    case Month.august:
    case Month.september:
    case Month.october:
    case Month.november:
    case Month.december:
      semester = Semester.fall;
      break;
    case Month.january:
    case Month.february:
    case Month.march:
    case Month.april:
    case Month.may:
      semester = Semester.spring;
      break;
    case Month.june:
    case Month.july:
      semester = Semester.summer;
      break;
  }
  
  print(semester); // Semester.fall
  
  var grade = 'B';
  switch (grade) {
    case 'A':
      print('Exceptional!');
      break;
    case 'B':
      print('Good job!');
      break;
    case 'C':
      print('Getting the job done!');
      break;
    case 'D':
      print('Some work to do here!');
      break;
    case 'F':
      print('Try again next time!');
      break;
    default:
      print('Your grade is a mystery!');
      break;
  }
  
}
```

```dart
/*
 * Copyright (c) 2019 Razeware LLC
 * 
 * Permission is hereby granted, free of charge, to any person obtaining a copy
 * of this software and associated documentation files (the "Software"), to deal
 * in the Software without restriction, including without limitation the rights
 * to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
 * copies of the Software, and to permit persons to whom the Software is
 * furnished to do so, subject to the following conditions:
 * 
 * The above copyright notice and this permission notice shall be included in
 * all copies or substantial portions of the Software.
 * 
 * Notwithstanding the foregoing, you may not use, copy, modify, merge, publish, 
 * distribute, sublicense, create a derivative work, and/or sell copies of the 
 * Software in any work that is designed, intended, or marketed for pedagogical or 
 * instructional purposes related to programming, coding, application development, 
 * or information technology.  Permission for such use, copying, modification,
 * merger, publication, distribution, sublicensing, creation of derivative works, 
 * or sale is expressly withheld.
 *
 * THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
 * IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
 * FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
 * AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
 * LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
 * OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
 * THE SOFTWARE.
 */

enum Rainbow {
  red,
  orange,
  yellow,
  green,
  blue,
  indigo,
  violet
}

void main() {
  
  // Challenge Time - Enum and Switch!

  /*
   * Create an enum for the colors of the rainbow.
   * Then, write a switch statement that checks a rainbow 
   * value and assigns a string with the name of the color.
   * Check the assigned value of the string by printing it.
   */
  
  final color = Rainbow.blue;
  
  String colorName;
  switch (color) {
    case Rainbow.red:
      colorName = "RED";
      break;
    case Rainbow.orange:
      colorName = "ORANGE";
      break;
    case Rainbow.yellow:
      colorName = "YELLOW";
      break;
    case Rainbow.green:
      colorName = "GREEN";
      break;
    case Rainbow.blue:
      colorName = "BLUE";
      break;
    case Rainbow.indigo:
      colorName = "INDIGO";
      break;
    case Rainbow.violet:
      colorName = "VIOLET";
      break;      
  }
  
  print(colorName);
}
```

