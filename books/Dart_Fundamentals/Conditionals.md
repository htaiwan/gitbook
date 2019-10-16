# Conditionals

```dart
  var animal = 'fox';
  
  if (animal == 'cat' || animal == 'dog') {
    print('Animal is a house pet.');
  } else {
    print('Animal is NOT a house pet.');
  }
  
  String timeOfDay;
  print(timeOfDay);
  
  var hourOfDay = 12;
  
  if (hourOfDay < 6) {
    timeOfDay = "Early morning";
  } else if (hourOfDay < 12) {
    timeOfDay = "Morning";
  } else if (hourOfDay < 17) {
    timeOfDay = "Afternoon";
  } else if (hourOfDay < 20) {
    timeOfDay = "Evening";
  } else if (hourOfDay < 24) {
    timeOfDay = "Late evening";
  } else {
    timeOfDay = "INVALID HOUR!";
  }
  
  print(timeOfDay);

  var piVsE = e > pi ? 'e' : 'pi';
  print(piVsE); // pi
  print(e); // 2.718281828459045
  print(pi); // 3.141592653589793

}
```

