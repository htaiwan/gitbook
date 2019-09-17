# Chapter 12: Advanced User Interaction

## 前言

目前已經完成功能有

- 偵測長方形
- 偵測QR Code
- 可以在再偵測到的QR Code或長方形上秀出一個虛擬立體平面
- 可以在虛擬平面置放相關內容

接下來這章要做的

- 利用storyboard完成更複雜的content
- 進入全屏模式
- 偵測一個定義好的特殊圖片

------

## 大綱

- [Updating the starter project](#1)
  - Removing unused files
  - Updating the code
  - Adding new files
  - Creating the storyboard-based billboard
- Interacting with the billboard
  - [Feeding the collection](#2)
  - [Showing the video player](#3)
    - Video cell implementation
    - The video player delegate
    - Back to the video cell
    - Updating the video player creation
    - Implementing the video node handler
- [Going fullscreen](#4)
  - Triggering the fullscreen mode
  - Entering fullscreen
  - Exiting fullscreen
  - Fixing the video player
- [Detecting reference images](#5)
  - Selecting the reference images
  - Registering the reference images
  - Providing a hook
  - Adding a reference image at runtime


------

<h2 id="1">Updating the starter project</h2>

這一段倒沒什麼東西，只是前一章是利用XIB來做content, 但這張要改換成storyboard, 所以先做了一些事前步驟，刪除舊的XIB檔案，更新對應的一些程式碼，加入新的storyboard, 並在原本呈現XIB的地方，改成呈現storyboard。

------

<h2 id="2">Feeding the collection</h2>

因為content內容改成collection view, 所以這段內容主是實作collection view相關的delegate和datasource。colletion view裡面有3個主要不同的cell組成- Image, Video, Web，用來分別呈現不同的內容。

------

<h2 id="3">Showing the video player</h2>

Video, 則是把之前放在view controller中控制播放影片的code都搬到cell去做，再透過封裝的container的delegate和handler把需要的東西丟到view controller中。

跟ARKit比較相關的code, 就是要建立一個video anchor, 好讓ARKit可以進行置放Video

```swift
    func createVideoPlayerAnchor() {
        guard let billboard = billboard else { return }
        guard let sceneView = sceneView else { return }

        // 在Z軸進行90度旋轉
        let center = billboard.plane.center * matrix_float4x4(SCNMatrix4MakeRotation(Float.pi / 2.0, 0.0, 0.0, 1.0))
        let anchor = ARAnchor(transform: center)

        sceneView.session.add(anchor: anchor)

        billboard.videoAnchor = anchor
    }

///////////////////////////////////////////

protocol VideoPlayerDelegate: class {
    func didStartPlay()
    func didEndPlay()
}

protocol VideoNodeHandler: class {
    func createNode() -> SCNNode?
    func removeNode()
}

```

------

<h2 id="4">Going fullscreen</h2>

要如何進入全屏模式

> To enter fullscreen mode, you have to detach the billboard’s view from its superview and attach it to the parent view controller’s view instead.
>

```SWift

extension BillboardViewController {
    func showFullScreen() {
        guard let billboard = billboard else { return }
        guard billboard.isFullScreen == false else { return }

        guard let mainViewController = parent as? AdViewController else { return }

        // 記住當前的parent VC和view, 之前離開全螢幕模式會用到
        self.mainViewController = mainViewController
        mainView = view.superview

        // 將自己從parent中移除
        willMove(toParent: nil)
        view.removeFromSuperview()
        removeFromParent()

        // 加入到前一個parent
        willMove(toParent: mainViewController)
        mainViewController.view.addSubview(view)
        mainViewController.addChild(self)

        billboard.isFullScreen = true
    }

}
```

有進入全屏，就要有退出

```Swift
    func restoreFromFullScreen() {
        guard let billboard = billboard else { return }
        guard billboard.isFullScreen == true else { return }
        guard let mainViewController =
            mainViewController else { return }
        guard let mainView = mainView else { return }

        // 將BillboardViewController的view從superview(AdViewController)移除
        willMove(toParent: nil)
        view.removeFromSuperview()
        removeFromParent()

        // 將view放回到原來的view中
        willMove(toParent: mainViewController)
        mainView.addSubview(view)
        mainViewController.addChild(self)

        billboard.isFullScreen = false
        self.mainViewController = nil
        self.mainView = nil
    }
```

------

<h2 id="5">Detecting reference images</h2>

這功能目前只支援在XCode 9.3和iOS 11.3以上。

- Open the RazeAd/res/Assets.xcassets assets catalog, and then click the + button located at the bottom-right side of the assets window. 
- A popup menu is shown with a long list of options. Find these two: **New AR Resource Group and New AR Reference Image.**
- Create a new AR resource group and name it RMK-ARKit-triggers.
  - 加入想要偵測的圖片
  - 另外有兩個參數可以設定unit, size
- 進行註冊

```Swift
    let triggerImages = ARReferenceImage.referenceImages(inGroupNamed:"RMK-ARKit-triggers", bundle: nil)
    configuration.detectionImages = triggerImages
```



- 收到已經偵測到的圖片

```swift

    func session(_ session: ARSession, didAdd anchors: [ARAnchor]) {
        if let imageAnchor = anchors.compactMap({ $0 as? ARImageAnchor}).first {
            self.createBillboard(center: imageAnchor.transform,
                size: imageAnchor.referenceImage.physicalSize)
        }
    }


    func createBillboard(center: matrix_float4x4, size: CGSize) {
        let plane = RectangularPlane(center: center, size: size)
        // 之前都是在Z軸進行90度旋轉，現在是要對X軸進行反方向的90度旋轉
        let rotation = SCNMatrix4MakeRotation(Float.pi / 2, -1.0, 0.0, 0.0)

        let rotationCenter = plane.center * matrix_float4x4(rotation)
        let anchor = ARAnchor(transform: rotationCenter)
        billboard = BillboardContainer(billboardAnchor: anchor, plane: plane)
        billboard?.videoPlayerDelegate = self
        sceneView.session.add(anchor: anchor)

        print("New billboard created")
    }

```

