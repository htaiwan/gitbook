# Chapter 10: Detecting Placeholders

## 大綱

接下來，我們準備開始新的app。這個app是AR的互動式廣告看板，可以利用這個看板放些廣告跟優惠訊息。

這章的目標是先做到偵測到長方形，然後可以抓到長方形的四個角落， 並產生一個平面。

------

## 摘要

- Introducing Razeware Mobile Kiosk
- RazeAd
- Getting started
- [Plane detection vs. object detection](#1)
- [Detecting a rectangle](#2)
- [Creating the billboard](#3)
- [Displaying placemarks](#4)
- [Adding SceneKit nodes](#5)
- [Handling interruptions](#6)

------

<h2 id="1">Plane detection vs. object detection</h2>

ARKit中的Plane detection是用來幫助使用者可以置放3D物件到某個平面上，隨著裝置的移動，會即時更新平面的狀態。然而，在這個例子是要偵測一個長方形平面，這屬於object detection的一部分，是由Vision Framework提供。

**Vision Framework**

- 利用Vision來偵測真實世界中長方形平面，然後將偵測到真實平面的資訊送到ARKit中，ARKit再利用這些資訊通知SceneKit繪製出所需要的虛擬平面在真實世界中。

------



<h2 id="2">Detecting a rectangle</h2>

- 每次點擊螢幕時，會對Vision發送一個`VNDetectRectanglesRequest`對當前的frame進行長方形平面偵測，若偵測到平面，則取到平面相關資訊，再利用ARKit的`hitTest`將Vision取得平面資訊轉換成虛擬世界的資訊。
  - currentFrame.hitTest(_:types:)
    - featurePoint
    - estimatedHorizontalPlane
    - existingPlane
    - existingPlaneUsingExtent
  - 如果是要偵測任何平面的長方形形狀，那就設定featurePoint就好。

```swift
  override func touchesBegan(_ touches: Set<UITouch>, with event: UIEvent?) {
    // 確保可以取得當前session的frame
    guard let currentFrame = sceneView.session.currentFrame else { return }

    // 影像的處理是非常耗CPU，千萬不要放在main thread中
    DispatchQueue.global(qos: .background).async {
        do {
            // VNDetectRectanglesRequest只有一個參數，那就是一個closure
            // 這個closure負責當影像分析完畢的callback
            // VNDetectRectanglesRequest最多只會回傳一個result, 因為他的一個屬性maximumObservations的預設值是1
            let request = VNDetectRectanglesRequest { (request, error) in
                // 利用compactMap將[Any]轉成[VNRectangleObservation]
                guard let results = request.results?.compactMap({ $0 as? VNRectangleObservation }),
                      let result = results.first else {
                        // 沒有找到任何Rectangles形狀
                        print("[Vision] VNRequest produced no result ")
                        return
                }
                // 透過Vison只能取得2D image的特征
                // 利用VNRectangleObservation的4個property(2D特徵), 透過ARKit中提供的hitTest轉化成3Ｄ空間的座標
                let coordinates: [matrix_float4x4] = [
                    result.topLeft,
                    result.topRight,
                    result.bottomRight,
                    result.bottomLeft
                    ].compactMap {
                        // currentFrame.hitTest(_:types:) projects a point to a 3D object
                        guard let hitFeature = currentFrame.hitTest($0, types: .featurePoint).first else { return nil }

                        return hitFeature.worldTransform
                    }

                guard coordinates.count == 4 else { return }

                DispatchQueue.main.async {
                    // 如果有先前的Billboard存在，那先移除掉
                    self.removeBillboard()

                    let (topLeft, topRight, bottomRight, bottomLeft) =
                    (coordinates[0], coordinates[1], coordinates[2], coordinates[3])

                    self.createBillboard(topLeft: topLeft, topRight: topRight,
                        bottomRight: bottomRight, bottomLeft: bottomLeft)
                }
            }
            // 執行request
            let handler = VNImageRequestHandler(cvPixelBuffer: currentFrame.capturedImage)
            try handler.perform([request])
        } catch(let error) {
            print("An error occurred during rectangle detection: \(error)")
        }
    }
  }
```

------



<h2 id="3">Creating the billboard</h2>

```Swift

    func createBillboard(topLeft: matrix_float4x4, topRight: matrix_float4x4, bottomRight: matrix_float4x4, bottomLeft: matrix_float4x4) {
        // 利用matrix產生RectangularPlane
        let plane = RectangularPlane(topLeft: topLeft, topRight: topRight, bottomLeft: bottomLeft, bottomRight: bottomRight)
        let anchor = ARAnchor(transform: plane.center)
        billboard = BillboardContainer(billboardAnchor: anchor, plane: plane)

        sceneView.session.add(anchor: anchor)
        print("New billboard created")
    }

    func removeBillboard() {
        if let anchor = billboard?.billboardAnchor {
            sceneView.session.remove(anchor: anchor)
            billboard?.billboardNode?.removeFromParentNode()
            billboard = nil
        }
    }
```

------



<h2 id="4">Displaying placemarks</h2>

顯示長方形平面的四個角落，可以幫助我們在測試的時候，觀察是否有正確偵測到長方形平面。

```swift
                    // Debug, 顯示偵測到平面的四個方塊角落
                    for coordinate in coordinates {
                        let box = SCNBox(width: 0.01, height: 0.01, length: 0.01, chamferRadius: 0)
                        let node = SCNNode(geometry: box)
                        node.transform = SCNMatrix4(coordinate)
                        self.sceneView.scene.rootNode.addChildNode(node)
                    }
```

------



<h2 id="5">Adding SceneKit nodess</h2>

到目前為止，我們利用Vision取得的資訊，轉化成ARKit的anchor並加到session之中，下一步就是把anchor的訊息傳送到SceneKit的node中。

```swift
extension AdViewController: ARSCNViewDelegate {

    // 將ARKit的anchor轉成SceneKit node
    func renderer(_ renderer: SCNSceneRenderer, nodeFor anchor: ARAnchor) -> SCNNode? {
        guard let billboard = billboard else { return nil }
        var node: SCNNode? = nil

        DispatchQueue.main.async {
            switch anchor {
            case billboard.billboardAnchor:
                let billboardNode = self.addBillboardNode()
                 node = billboardNode
            default:
                break
            }
        }

        return node
    }
}

    func addBillboardNode() -> SCNNode? {
        guard let billboard = billboard else { return nil }
        let rectangle = SCNPlane(width: billboard.plane.width, height: billboard.plane.height)
        let rectangleNode = SCNNode(geometry: rectangle)
        self.billboard?.billboardNode = rectangleNode

        return rectangleNode
    }
```

------



<h2 id="6">Handling interruptions</h2>

目前看起來都還滿不錯，但如果把app退到背景然後旋轉手機(直立變橫放)，在把app開啟，會發現剛剛偵測到placemark都位置錯亂了。主要原因就是退到背景後，session就沒辦法收到orientation的資訊，也就沒辦法更新位置。此時，最佳的作法就是在`ARSCNViewDelegate: sessionWasInterrupted`中做`removeBillboard()`，清除所有plane，在session恢復時再重新執行平面的請求。

