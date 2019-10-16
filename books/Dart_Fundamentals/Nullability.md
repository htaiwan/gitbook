# Nullability

```dart
void main() {
  int age;
  double height;
  String err;
  
  print(age); // null
  print(height);
  print(err);
  
  var error = err ?? "No error";
  print(error); // No error
  
  err ??= error;
  print(err); // No error
  
  print(age.isEven); // Uncaught exception:
  print(age?.isEven);
}
```

