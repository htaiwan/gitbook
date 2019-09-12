# Chapter 11: Beginning User Interaction

### 大綱

接下來要繼續上一章的功能的接續。已經可以偵測到一個長方形平面，並在上面置放一個虛擬平面。

- 目前這個平面的方向是有問題
- 從平面偵測變成可以偵測QR Code
- 增加一些使用者互動
- 虛擬平面變成image再變成carousel
- 播放影片

------

### 摘要

- Getting started
  - [The world coordinate system](#1)
  - [The camera world alignment](#2)
- [Detecting a QR code](#3)
- [Showing an image](#4)
- [Showing an image carousel](#5)
- [Playing a video](#6)
- [Removing the video player](#7)

------

<h2 id="1">The world coordinate system</h2>

**worldTransform property**

- 記錄了有關於hit test在world coordinate system的結果，包含了position和orientation。



------

<h2 id="2">The camera world alignment</h2>



------

<h2 id="3">Detecting a QR code</h2>

只要把VNDetectRectanglesRequest換成VNDetectBarcodesRequest, VNRectangleObservation換成VNBarcodeObservation就可以。

```Swift
     DispatchQueue.global(qos: .background).async {
       do {
-        let request = VNDetectRectanglesRequest { (request, error) in
+        let request = VNDetectBarcodesRequest { (request, error) in
           // Access the first result in the array,
           // after converting to an array
           // of VNRectangleObservation
-          guard let results = request.results?.compactMap({ $0 as? VNRectangleObservation }), let result = results.first else {
+          guard let results = request.results?.compactMap({ $0 as? VNBarcodeObservation }), let result = results.first else {
             print ("[Vision] VNRequest produced no result")
             return
           }
```

------



<h2 id="4">Showing an image</h2>

在虛擬平面上秀出一張圖片。

[A UIView can be assigned to a material](https://forums.developer.apple.com/thread/89423)

```Swift

    func setBillboardImage(image: UIImage) {
        let material = SCNMaterial()
        material.isDoubleSided = true

        DispatchQueue.main.async {
            let imageView = UIImageView(image: image)
            // UIView 可以被指定為一種 material
            // 當然也可以直接將image 指定一種 material
            // material.diffuse.contents = image
            material.diffuse.contents = imageView

            self.billboard?.billboardNode?.geometry?.materials = [material]
        }
    }

```

------



<h2 id="5">Showing an image carousel</h2>

既然可以用UIImageView當作content，當然可以用更複雜的UIView來當作content，設計一個比較複雜的UIView carousel layout。

```Swift
-    func setBillboardImage(image: UIImage) {
+    func setBillboardImage(_ images: [UIImage]) {
         let material = SCNMaterial()
         material.isDoubleSided = true
 
         DispatchQueue.main.async {
-            let imageView = UIImageView(image: image)
+            let billboardViewController = BillboardViewController(
+            nibName: "BillboardViewController", bundle: nil)
+            billboardViewController.images = images
+            self.billboard?.viewController = billboardViewController
             // UIView 可以被指定為一種 material
             // 當然也可以直接將image 指定一種 material
             // material.diffuse.contents = image
-            material.diffuse.contents = imageView
-
+            material.diffuse.contents = billboardViewController.view
             self.billboard?.billboardNode?.geometry?.materials = [material]
         }
```

------



<h2 id="6">Playing a video</h2>

在UIView carousel layout中有個play button, 當play button被觸發時會開始播放影片。

```swift
billboardViewController.delegate = self

////////////////////////////////

extension AdViewController: BillboardViewDelegate {

    func billboardViewDidSelectPlayVideo(_ view: BillboardView) {
        createVideo()
    }
}

////////////////////////////////

func createVideo() {
        guard let billboard = self.billboard else { return }

        let rotation = SCNMatrix4MakeRotation(Float.pi / 2.0, 0.0, 0.0, 1.0)
        let rotaionCentor = billboard.plane.center * matrix_float4x4(rotation)

        // 建立一個新anchor其中心跟billboard相同
        let anchor = ARAnchor(transform: rotaionCentor)
        sceneView.session.add(anchor: anchor)
        // 記錄video anchor
        self.billboard?.videoAnchor = anchor
    }

////////////////////////////////

    
// 在renderer(_:nodeFor:) 中的switch case新增一個case
case (let videoAnchor) where videoAnchor == billboard.videoAnchor:
      node = addVideoPlayerNode()

////////////////////////////////

func addVideoPlayerNode() -> SCNNode? {
        guard let billboard = self.billboard else { return nil }

        // 等下會用到一些變數
        let billboardSize = CGSize(width: billboard.plane.width, height: billboard.plane.height / 2)
        let frameSize = CGSize(width: 1024, height: 512)
        let videoUrl = URL(string:"https://www.rmp-streaming.com/media/bbb-360p.mp4")!

        // 建立player和SpriteKit video node
        let player = AVPlayer(url: videoUrl)
        let videoPlayerNode = SKVideoNode(avPlayer: player)
        videoPlayerNode.size = frameSize
        videoPlayerNode.position = CGPoint(x: frameSize.width / 2, y: frameSize.height / 2)
        // it’s an undocumented “feature” which causes a SpriteKit node to be rendered on a SceneKit node rotated by 90 degrees counterclockwise
        videoPlayerNode.zRotation = CGFloat.pi

        // 建立SpriteKit scene並加入video node
        let spritekitScene = SKScene(size: frameSize)
        // 此時再SceneView加上一個spriteKit video node
        spritekitScene.addChild(videoPlayerNode)

        // 建立plane並加入node中
        let plane = SCNPlane(width: billboardSize.width, height: billboardSize.height)
        plane.firstMaterial?.isDoubleSided = true
        // 跟billboard做法類似，將某個scence直接當作diffuse的content使用
        plane.firstMaterial?.diffuse.contents = spritekitScene
        let node = SCNNode(geometry: plane)

        self.billboard?.videoNode = node

        self.billboard?.billboardNode?.isHidden = true
        videoPlayerNode.play()

        return node
    }
```

------



<h2 id="7">Removing the video player</h2>

若已經在播放影片，在點擊螢幕一次，可以停止播放並換成billboard。

```Swift
 override func touchesBegan(_ touches: Set<UITouch>, with event: UIEvent?) {
    if billboard?.hasVideoNode == true {
        billboard?.billboardNode?.isHidden = false
        removeVideo()
        return
    }
   
////////////////////////////////

    func removeVideo() {
        if let videoAnchor = billboard?.videoAnchor {
            sceneView.session.remove(anchor: videoAnchor)
            billboard?.videoNode?.removeFromParentNode()

            billboard?.videoAnchor = nil
            billboard?.videoNode = nil
        }
    }
```

