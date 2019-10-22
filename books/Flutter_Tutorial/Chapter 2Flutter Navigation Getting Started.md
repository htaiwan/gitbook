# Chapter 2: Flutter Navigation: Getting Started

[Flutter Navigation: Getting Started](https://www.raywenderlich.com/4562634-flutter-navigation-getting-started)

------

#### 大綱

- Getting Started
- Creating a Widget to Navigate To
- Navigation using Routes
- Popping the Stack
- Returning a Value
- Creating Custom Transitions
- Where to Go From Here?

------

#### Creating a Widget to Navigate To

- create a new file named *memberwidget.dart*:

```dart
import 'package:flutter/material.dart';

import 'member.dart';

class MemberWidget extends StatefulWidget {
 // Add a Member property for the widget.
  final Member member;

  MemberWidget(this.member) {
    if (member == null) {
      // Make sure that the member argument is not-null in the widget constructor
      throw ArgumentError("member of MemberWidget cannot be null. Received: '$member'");
    }
  }

@override
  // Use a MemberState class for the state, passing along a Member object to the MemberState.
createState() => MemberState(member);
}

class MemberState extends State<MemberWidget> {
  final Member member;
 //  given MemberState a Member property and a constructor.
  MemberState(this.member);

  @override
  Widget build(BuildContext context) {
    // creating a Scaffold, a material design container, which holds an AppBar and a Padding with a child Image for the member avatar.
    return Scaffold(
      appBar: AppBar(
        title: Text(member.login),
      ),
      body: Padding(
        padding: EdgeInsets.all(16.0),
        child: Image.network(member.avatarUrl)
        ),
    );
  }
}

```

------

#### Navigation using Routes

- Navigation in Flutter is centered around the idea of ***routes*.**
- Routes are similar in concept to the routes that you would use in a REST API, where each route is relative to some root. The widget `main()` acts like the root in your app (*“/”*) and all other routes are relative to that one.

- One way to use routes is with **`PageRoute`**
  - Since you’re working with a Flutter *MaterialApp*, you’ll use the **`MaterialPageRoute`**

```dart
import 'memberwidget.dart';

  _pushMember(Member member) {
    //  using Navigator to push a new MaterialPageRoute onto the stack, and you build the MaterialPageRoute using a MemberWidget
    // MemberWidget利用收到member資料產生新頁面
    // MaterialPageRoute再把MemberWidget進行push動作
    Navigator.push(context, 
    MaterialPageRoute(builder: (context) => MemberWidget(member)));
  }

Widget _buildRow(int i) {
  return Padding(
    padding: const EdgeInsets.all(16.0),
    child: ListTile(
      title: Text("${_members[i].login}", style: _biggerFont),
      leading: CircleAvatar(
          backgroundColor: Colors.green,
          backgroundImage: NetworkImage(_members[i].avatarUrl)
      ),
      // Add onTap here:
      onTap: () { 
        // 將member的資料結構傳入
        _pushMember(_members[i]); 
      },
    )
  );
}

```

------

#### Popping the Stack

- 在*memberwidget*增加一個back button, 來進行pop動作

```diff
-      body: Padding(
-        padding: EdgeInsets.all(16.0),
-        child: Image.network(member.avatarUrl)
-        ),
+      body: Column(
+        children: [
+          Image.network(member.avatarUrl),
+          IconButton(
+            icon: Icon(Icons.arrow_back, color: Colors.green, size: 48.0),
+            onPressed: () {
+              // set its onPressed value to call Navigator and pop the stack
+              Navigator.pop(context);
+            },
+          )
+        ],
+      )

```

------

#### Returning a Value

- Routes can return values

- Add the following private `async` method to `MemberState`

```dart
_showOKScreen(BuildContext context) async {
  //  push a new MaterialPageRoute onto the stack with a type parameter of bool
  //  using await when pushing the new route, which waits until the route is popped.
  //  the bool type parameter in MaterialPageRoute, which you can replace with any other type you want coming back from the route
  bool value = await Navigator.push(context, MaterialPageRoute<bool>(builder: (BuildContext context) {
    return Padding(
      padding: const EdgeInsets.all(32.0),
      child: Column(children: <Widget>[
        // In the calls to pop(), 
        // you pass a return value of true if the user tapped the “OK” text on the screen, and false if the user tapped “NOT OK”. 
        // If the user presses the back button instead, the value returned is null.
        GestureDetector(
          child: Text('OK'),
          onTap: () {
            // 這裡的true會透過MaterialPageRoute<bool>回傳到value中
            Navigator.pop(context, true);
          }),
        GestureDetector(
          child: Text('Not OK'),
          onTap: () {
            Navigator.pop(context, false);
          })
      ]));
  }));

  var alert = AlertDialog(
    // 根據value來確定是哪個按鈕被觸發
    content: Text((value != null && value) ? "OK was pressed" : "NOT OK or BACK was pressed"),
    actions: <Widget>[
      FlatButton(
        child: Text('OK'),
        onPressed: () {
          Navigator.pop(context);
        })
    ],
  );

  //  call showDialog() to show the alert
  showDialog(context: context, child: alert);
}

```

- Inside `MemberState`‘s `build()`, add a *RaisedButton* that calls `_showOKScreen()` as another child of the `Column` 

```dart
@override
Widget build(BuildContext context) {
  return Scaffold(
      appBar: AppBar(
        title: Text(member.login),
      ),
      body: Padding(
          padding: EdgeInsets.all(16.0),
          child: Column(children: [
            Image.network(member.avatarUrl),
            IconButton(
                icon: Icon(Icons.arrow_back, color: Colors.green, size: 48.0),
                onPressed: () {
                  Navigator.pop(context);
                }),
            // Add this raised button
            RaisedButton(
                child: Text('PRESS ME'),
                onPressed: () {
                  _showOKScreen(context);
                })
          ])));
}
```

------

#### Creating Custom Transitions

```diff
-  _pushMember(Member member) {
-    Navigator.push(context, 
-    MaterialPageRoute(builder: (context) => MemberWidget(member)));
-  }
+_pushMember(Member member) {
+  // ush a new PageRouteBuilder onto the stack.
+  Navigator.push(
+      context,
+      PageRouteBuilder(
+          opaque: true,
+          // Specify the duration using transitionDuration.
+          transitionDuration: const Duration(milliseconds: 1000),
+          // Create the MemberWidget screen using pageBuilder
+          pageBuilder: (BuildContext context, _, __) {
+            return MemberWidget(member);
+          },
+          // Use the transitionsBuilder to create fade and rotation transitions when showing the new route.
+          transitionsBuilder:
+              (_, Animation<double> animation, __, Widget child) {
+            return FadeTransition(
+              opacity: animation,
+              child: RotationTransition(
+                turns: Tween<double>(begin: 0.0, end: 1.0).animate(animation),
+                child: child,
+              ),
+            );
+          }));
+}
+

```

------

#### Where to Go From Here?

- [Navigation and Routing](https://flutter.dev/docs/development/ui/navigation) in the Flutter docs.
- The [Navigator API](https://api.flutter.dev/flutter/widgets/Navigator-class.html) docs.