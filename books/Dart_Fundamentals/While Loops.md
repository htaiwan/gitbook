# While Loops

```dart
void main() {
  var i = 1;
  
  while (i < 10) {
    print(i);
    i++;
  }
  print("---");
  
  i = 1;
  do {
    print(i);
    if (i == 5) {
      continue;
//       break;
    }
    ++i;
  } while (i < 10);
}

```

```dart
 /*
   Create a variable named count and set it equal to 0.
   Create a while loop with the condition count < 10 which prints out
   "Count is X" (where X is replaced with the count value) and then
   increments count by 1.
   */
  
  var count = 0;
     
  while (count < 10) {
    print("Count is $count");
    count++;
  }
  
  /*
  Simulate the roll of a six-sided die, and roll until you get a 6.
  Create a variable named count and set it equal to 0. Create another
  variable named roll and make it an int. Create a do-while loop.
  Inside the loop, set roll equal to next(1, 6), which will
  pick a random number between 1 and 6. Then increment count by 1.
  Finally, print "After X roll(s), roll is Y" (where X is the value of
  count and Y is the value of roll). Set the loop condition so that
  the loop finishes when the first 6 is rolled.
  */
  
  count = 0;
  int roll;
    
  do {
    roll = next(1, 6);
    count++;
    print("After $count roll(s), roll is $roll");    
  } while (roll != 6);  
}

```

