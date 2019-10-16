# Strings and Runes

[Runes](https://www.jianshu.com/p/a1224a42aea5)

```dart
void main() {
  var firstName = 'Albert';
  String lastName = "Einstein";
  
  var physicist = "$firstName $lastName";
  print(physicist); // Albert Einstein
  
  var quote = 'If you can\'t'  + ' explain it simply\n'
    "you don't understand it well enough.";
  print(quote); 
  /* 
  If you can't explain it simply
  you don't understand it well enough.
  */
  
  var rawString = r"If you can't explain it simply\n you don't understand it well enough.";
  print(rawString);  // If you can't explain it simply\nyou don't understand it well
   
  const String myNameIs = "My name is ";
  print(myNameIs);
//   myNameIs = 'Joe'; // error compile-time constant
  
  var apples = 3;
  var oranges = 5;
  var totalFruit = "Total amount of fruit is ${apples + oranges} pieces.";
  print(totalFruit); // Total amount of fruit is 8 pieces.
  print('${totalFruit.length}');  // 34

  apples = int.parse('2'); 
  double bananas = double.parse('2.5');
  print('$apples and $bananas'); // 2 and 2.5

  var smiley = '\u263a';
  print(smiley);  // ☺
  
  var womanScientist = '\u{1f469}';
  print(womanScientist); // 👩
  print(womanScientist.codeUnits); // [55357, 56425]
  print(womanScientist.runes); // (128105)
 
  Runes ou812 = Runes('\u{1f62f}\u{1f447}\u{1f963}\u{1f550}\u{0032}');
  print(ou812); // (128559, 128071, 129379, 128336, 50)
  print(String.fromCharCodes(ou812)); // 😯👇🥣🕐2
}
```

