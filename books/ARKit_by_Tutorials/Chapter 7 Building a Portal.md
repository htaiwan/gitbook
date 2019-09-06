# Chapter 7: Building a Portal

## 大綱

骰子遊戲已經在上一章為最終篇了，接下來的四章會另外一個新的app為範例開始進行學習。Portal app可以是用來教育方面的AR App, 例如介紹宇宙中的太陽系之類。

------

## 摘要

- [The portal app](#1)
- [Getting started](#2)
- [Setting up ARKit](#3)
- [Plane detection and rendering](#4)

------



<h2 id="1">The portal app</h2>

我們會做一個裝潢好的房子的虛擬門口，可以走進去這個虛擬房間並進行參觀。

------



<h2 id="2">Getting started</h2>

基本上UI目前只有3樣東西

- messageLabel: 用來告訴使用者接下來要做哪些事情。
- sessionStateLabel: 用來了解目前session的狀況，是否有中斷現象，例如走到一個很暗的地方。
- sceneView: 用來繪製3D物件。
  - **Note:** ARKit只是用來camera中sensor data, 並不包含任何3D物件繪製動作。這些繪製動作是交給其他Framewrok處理，如SceneKit或SpiritKit。ARSCNView可以讓我們輕鬆整合ARKit的data到SceneKit中。

------



<h2 id="3">Setting up ARKit</h2>

這段內容之前就有講過，就是建立config加到session中。

```swift
  func runSession() {
    let configuration = ARWorldTrackingConfiguration.init()
    configuration.planeDetection = .horizontal
    configuration.isLightEstimationEnabled = true
    sceneView?.session.run(configuration)

    #if DEBUG
    sceneView?.debugOptions = [ARSCNDebugOptions.showFeaturePoints]
    #endif
  }
```

------



<h2 id="4">Plane detection and rendering</h2>

透過ARSCNViewDelegate可以獲得camera偵測到真實環境的資料

第一步先偵測平面

```Swift
  func renderer(_ renderer: SCNSceneRenderer, didAdd node: SCNNode, for anchor: ARAnchor) {
    // 所有delegate mathod都是在background thread執行，所以需要丟到Main thread進行UI更新
    DispatchQueue.main.async {
      if let planeAnchor = anchor as? ARPlaneAnchor {
        #if DEBUG
        let debugPlaneNode = createPlaneNode(center: planeAnchor.center, extent: planeAnchor.extent)
        node.addChildNode(debugPlaneNode)
        #endif

        self.messageLabel?.text = "Tap on the detected horizontal plane to place the portal"
      }
    }
  }
```

再過來就是隨著camera的真實環境的資料更新，我們也要同步更新3D物件，例如大小，位置等。

```Swift
  func renderer(_ renderer: SCNSceneRenderer, didUpdate node: SCNNode, for anchor: ARAnchor) {
    DispatchQueue.main.async {
      if let planeAnchor = anchor as? ARPlaneAnchor, node.childNodes.count > 0 {
        updatePlaneNode(node.childNodes[0], center: planeAnchor.center, extent: planeAnchor.extent)
      }
    }
  }
```

接下是一些helper的內容，建立3D平面，更新平面。

```swift
func createPlaneNode(center: vector_float3, extent: vector_float3) -> SCNNode {
  let plane = SCNPlane(width: CGFloat(extent.x), height: CGFloat(extent.z))

  let planeMaterial = SCNMaterial()
  planeMaterial.diffuse.contents =  UIColor.yellow.withAlphaComponent(0.4)
  plane.materials = [planeMaterial]

  let planeNode = SCNNode(geometry: plane)
  planeNode.position = SCNVector3Make(center.x, 0, center.z)
  // SceneKit的defult是vertical,因此要轉90度變成平面
  planeNode.transform = SCNMatrix4MakeRotation(-Float.pi / 2, 1, 0, 0)

  return planeNode
}

func updatePlaneNode(_ node: SCNNode, center: vector_float3, extent: vector_float3) {
  // 檢查是否為SCNPlane
  let gemorty = node.geometry as? SCNPlane

  // 根據傳進來anchor的extent訊息來更新gemorty大小
  gemorty?.width = CGFloat(extent.x)
  gemorty?.height = CGFloat(extent.z)

  // 根據傳進來anchor的extent訊息來更新gemorty位置
  node.position = SCNVector3Make(center.x, 0, center.z)
}
```

