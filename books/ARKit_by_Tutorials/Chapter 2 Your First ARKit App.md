# Chapter 2: Your First ARKit App

### 前言

先從Xcode的AR template專案開始了解，並嘗試做簡單的修改。

------

### 大綱

- Getting started

  - [Creating an ARKit application project](#1)
  - [Building the ARKit application project](#1)
  - [Reviewing the project files](#2)
    - The launch screen storyboard
    - The main storyboard
    - The view controller
    - The SceneKit asset catalog
    - The project settings
    - The info property list file
  - [Creating an asset catalog](#3)
    - Creating a new SceneKit scene
    - Loading SceneKit scenes
  - [Adding UI elements](#4)

  ------

  

<h2 id="1">Creating & build an ARKit application project</h2>

XCode已經有預設的AR template專案，所以這階段我們先從template專案開始了解。

AR專案是無法用模擬器測試，只能用實機，且是要A9以上的處理器才能進行debug。至少要在iPhoneSE或iPhone 6s以上。

------

<h2 id="2">Reviewing the project files</h2>

- The main storyboard: 在storyboard只有一個畫面，且畫面上覆蓋ARSCNView。這個view是用來渲染3D物件在camera的live view上。
- The view controller: 預設import了三個framework. UIKit, SceneKit, ARKit。並實作 ARSCNViewDelegate。
- The SceneKit asset catalog: 這個是專門用來SceneKit assets的特殊資料夾。
- The info property list file: Privacy - Camera Usage Description，新增使用相機的權限要求。

------



<h2 id="3">Creating an asset catalog</h2>

接下來就是要將範例專案的3D飛機換成我們自訂的3D地球。

- 首先建立新的xxx.scnassets資料夾。
  - .scnassets是個特出副檔名，可以讓XCode知道這個是專門存放SceneKit相關的assets。
- 新增一個xxx.scn在xxx.scnassets資料夾中。
- 在xxx.scn中放入一個sphere(球體)node。
  - 調整球體的半徑。
  - 調整球體的位置。
    - AR Session啟動時會以手機在真實世界的位置當作中心(0,0,0)，因此想要一開始就看到球體，我們就將球體位置改成(0,0,-1)，表示距離手機前方1m的地方置放球體。
- 改變球體外觀
  - 直接將xxx.png拖拉到球體上，就會把想要的圖貼上球體的外表。
- 修改程式碼，換成3D地球。

```swift
//let scene = SCNScene(named: "art.scnassets/ship.scn")!

let scene = SCNScene(named: "xxx.scnassets/xxx.scn")!
```

------



<h2 id="4">Adding UI elements</h2>

這段倒沒什麼特別，就是SceneView上再放上其他的UI元素，例如View, Label, Button, 就不多提了。

[How to use actions in SceneKit Editor](https://stackoverflow.com/questions/31637237/how-to-use-actions-in-scenekit-editor)