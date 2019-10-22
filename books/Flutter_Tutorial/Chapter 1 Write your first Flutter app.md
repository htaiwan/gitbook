# Chapter 1: Getting Started with Flutter

[Getting Started with Flutter](https://www.raywenderlich.com/4529993-getting-started-with-flutter)

------

#### 大綱

- Introduction to Flutter
- Getting Started
- Setting up your development environment
- Creating a new project
- Hot Reload
- Importing a File
- Widgets
- Making Network Calls
- Using a ListView
- Adding dividers
- Parsing to Custom Types
- Downloading Images with NetworkImage
- Cleaning the Code
- Adding a Theme

------

#### Introduction to Flutter

- Flutter apps are written using the *[Dart](https://dart.dev/)* programming language, also originally from Google and now an ECMA standard
- Flutter allows for a ***reactive*** and ***declarative*** style of programming
- Dart achieves improve app startup times and overall performance by using ***Ahead-Of-Time*** or AOT compilation.
- use ***Just-In-Time*** or JIT compilation. JIT compilation with Flutter improves the development workflow by allowing a ***hot reload*** capability.
- Flutter will also give you a head start on developing for the *[Fuchsia](https://en.wikipedia.org/wiki/Google_Fuchsia)* platform, which is currently an experimental operating system in development at Google.

------

#### **Getting Started**

- In this tutorial, you’ll build a Flutter app that queries the *[GitHub API](https://developer.github.com/v3/teams/members/)* for team members in a GitHub organization and displays the team member information in a scrollable list.

------

#### Setting up your development environment

- [官方文件](https://flutter.dev/docs/get-started/install/macos)

------

#### Creating a new project

- 預設的範例專案 - 是一個計數app。
- 先把這些範例專案，做基本調整，只需要在頁面中間秀一段文字。

```dart
import 'package:flutter/material.dart';

//  => operator for a single line function to run the app.
void main() => runApp(GHFlutterApp());

// Most entities in a Flutter app are widgets, either stateless or stateful
class GHFlutterApp extends StatelessWidget {
  @override
  // override the widget build() method to create your app widget
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'GHFlutter',
      home: Scaffold(
        appBar: AppBar(
          title: Text('GHFlutter'),
        ),
        body: Center(
          child: Text('GHFlutter'),
        ),
      ),
    );
  }
}
```

------

#### Hot Reload

One of the best aspects of Flutter development is being able to *hot reload* your app as you make changes. This is similar to something like Android Studio’s *Instant Run/Apply Changes*.

------

#### Widgets

There are two fundamental types of widgets you will use:

- *Stateless*: widgets that depend only upon their own configuration info, such as a static image in an image view.
- *Stateful*: widgets that need to maintain dynamic information and do so by interacting with a *State* object.

將GHFlutterApp改寫成Stateful widget

```dart

class GHFlutter extends StatefulWidget {
  @override
  // made a StatefulWidget subclass and you’re overriding the createState()
  State<StatefulWidget> createState() {
    return GHFlutterState();
  }
}

// GHFlutterState extends State with a parameter of GHFlutter
class GHFlutterState extends State<GHFlutter> {
  @override
  Widget build(BuildContext context) {
    // A Scaffold is a container for material design widgets. 
    // It acts as the root of a widget hierarchy.
    return Scaffold(
      appBar: AppBar(
        title: Text(Strings.appTitle) ,
        ),
        body: Text(Strings.appTitle),
    );
  }
}

```

```diff
   Widget build(BuildContext context) {
     return MaterialApp(
       title: Strings.appTitle,
-      home: Scaffold(
-        appBar: AppBar(
-          title: Text(Strings.appTitle),
-        ),
-        body: Center(
-          child: Text(Strings.appTitle),
-        ),
-      ),
+      home: GHFlutter(),
     );    
   }
```

------

#### Making Network Calls

- Navigate to the *pubspec.yaml* file and right under `dependencies` and `cupertino_icons: ^0.1.2` add the below:

```
  cupertino_icons: ^0.1.2

  # HTTP package
  # Notice the indentation. Maintain the indentation of the http package declaration to be the same as that of the cupertino_icons package
  http: ^0.12.0+2
```

-  make an HTTP network call and also parse the resulting response JSON into Dart objects

```dart
import 'dart:convert';
import 'package:http/http.dart' as http;
```

```dart
class GHFlutterState extends State<GHFlutter> {
  var _members = [];
  final _biggerFont = const TextStyle(fontSize: 18.0);

 // async: tell Dart that it’s asynchronous,
  _loadData() async {
    String dataURL = "https://api.github.com/orgs/raywenderlich/members";
    // await: wait on the http.get()的response
    http.Response response = await http.get(dataURL);
    // pass a callback to setState() that runs synchronously on the UI thread. I
    setState(() {
      _members = json.decode(response.body);
    });
  }

  @override
  // calls _loadData() when the state is initialized:
  void initState() {
    super.initState();
    
    _loadData();
  }
```

------

#### Using a ListView

- Add a `_buildRow()` method to `GHFlutterState`

```dart
Widget _buildRow(int i) {
  return ListTile(
    // shows the “login” value parsed from the JSON for the ith member
    title: Text("${_members[i]["login"]}", style: _biggerFont)
  );
}
```

- Update the build method of `GHFlutterState` to have it’s body be a *ListView.builder*

```diff
       appBar: AppBar(
         title: Text(Strings.appTitle) ,
         ),
-        body: Text(Strings.appTitle),
+        body: ListView.builder(
+          padding: const EdgeInsets.all(16.0),
+          itemCount: _members.length,
+          itemBuilder: (BuildContext context, int position) {
+            return _buildRow(position);
+          },
+        ),
     );

```

------

#### Adding dividers

- 加入分隔線的做法，不像iOS的tableView是自動有分隔線，這裡要自己手動產生。

To add dividers into the list, you’re going to double the item count, and then return a *Divider* widget when the position in the list is odd

```diff
         body: ListView.builder(
           padding: const EdgeInsets.all(16.0),
-          itemCount: _members.length,
+          itemCount: _members.length * 2,
           itemBuilder: (BuildContext context, int position) {
-            return _buildRow(position);
+            if (position.isOdd) return Divider();
+            final index = position ~/2;
+            return _buildRow(index);
           },
         ),
     );
```

------

#### Parsing to Custom Types

- the `_members` list as a Dart *Map* type, the equivalent of a *Map* in Kotlin or a *Dictionary* in Swift.

- Add a new `Member` type to the Member.dart file:

```dart
class Member {
  final String login;

  Member(this.login) {
    if (login == null) {
       throw ArgumentError("login of Member cannot be null. "
          "Received: '$login'");
    }
  }
}
```

- Update the `_members` declaration in `GHFlutterState` so that it is a list of `Member` objects

```diff
 class GHFlutterState extends State<GHFlutter> {
-  var _members = [];
+  var _members = <Member>[];

```

- Now update the callback sent to `setState()` in `_loadData()` to turn the decoded map into a `Member` object and add it to the list of members

```diff
     // pass a callback to setState() that runs synchronously on the UI thread. I
     setState(() {
-      _members = json.decode(response.body);
+      final membersJSON = json.decode(response.body);
+
+      for (var memberJSON in membersJSON) {
+        final member = Member(memberJSON["login"]);
+        _members.add(member);
+      }
     });
   }
```

```diff
   Widget _buildRow(int i) {
     return ListTile(
-      title: Text("${_members[i]["login"]}", style: _biggerFont)
+      title: Text("${_members[i].login}", style: _biggerFont)
     );
```

------

#### Downloading Images with NetworkImage

- Update the `Member` class to add an `avatarUrl` property that cannot be null:

```diff
 class Member {
   final String login;
+  final String avatarUrl;
 
-  Member(this.login) {
+  Member(this.login, this.avatarUrl) {
     if (login == null) {
        throw ArgumentError("login of Member cannot be null. "
           "Received: '$login'");
     }
+    if (avatarUrl == null) {
+       throw ArgumentError("avatarUrl of Member cannot be null. "
+          "Received: '$avatarUrl'");
+    }
   }
 }
```

- Update `_buildRow()` to show the avatar using a *NetworkImage* and the *CircleAvatar* widget:

```diff
       final membersJSON = json.decode(response.body);
 
       for (var memberJSON in membersJSON) {
-        final member = Member(memberJSON["login"]);
+        final member = Member(memberJSON["login"], memberJSON["avatar_url"]);
         _members.add(member);
       }
     });
   }


   Widget _buildRow(int i) {
-    return ListTile(
-      title: Text("${_members[i].login}", style: _biggerFont)
+    return Padding(
+      padding: const EdgeInsets.all(16.0),
+      child: ListTile(
+        title: Text("${_members[i].login}", style: _biggerFont),
+        leading: CircleAvatar(
+          backgroundColor: Colors.green,
+          backgroundImage: NetworkImage(_members[i].avatarUrl)
+        ),
+      )
     );
   }
 }

```

------

#### Cleaning the Code

- Create files named *member.dart* and *ghflutter.dart* in the *lib* folder. 

- Move the `Member` class into *member.dart* and both the `GHFlutterState` and `GHFlutter` classes into *ghflutter.dart*.

------

#### Adding a Theme

```dart
  Widget build(BuildContext context) {
    return MaterialApp(
      title: Strings.appTitle,
      theme: ThemeData(primaryColor: Colors.pink.shade800),
      home: GHFlutter(),
    );    
  }
```

