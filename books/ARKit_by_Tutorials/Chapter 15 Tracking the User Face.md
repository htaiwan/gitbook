# Chapter 15: Tracking the User’s Face

### 前言

上一章只是做到叫ARKit開始進行人臉追蹤，這一章開始要根據ARKit辨識出來的內容進行應用。

------

### 大綱

- [Detecting the user’s face](#1)
- [Working with face geometry](#2)
  - Creating the Mask class
  - Using the Mask class
  - Connecting the mask to the anchor
- [Handling updates](#3)
- [Adding additional masks](#4)
- [Connecting the switch button](#4)
- [Setting up a basic environment](#5)
- [Adjust the lighting](#6)

------

<h2 id="1">Detecting the user’s face</h2>

- ARKit辨識人臉結果後，會以ARFaceAnchor的物件傳出來。
- 目前ARKit只能辨識單一人臉，若有多個人臉同時出現在鏡頭，只會挑出最大最容易辨識的那位。
- 臉部座標系統是以右手方向為主軸且測量單位都是用公尺。
- ARFaceAnchor中所有辨識出right選項，在真實世界是對應到使用者left(鏡射概念)。

```Swift
    var anchorNode: SCNNode?

    // 當偵測到對應的ARKit中的anchor時，會自動轉化對應的sceneKit的node
    func renderer(_ renderer: SCNSceneRenderer, didAdd node: SCNNode, for anchor: ARAnchor) {
        anchorNode = node
    }
```

------

<h2 id="2">Working with face geometry</h2>

- Creating the Mask class

```swift
import ARKit
import SceneKit

// 繼承SCNNode, 之後就可以直接加入Scene graph中
class Mask: SCNNode {

    // 傳入ARKit辨識人臉結果
    init(geometry: ARSCNFaceGeometry) {
        super.init()

        let material = geometry.firstMaterial
        material?.lightingModel = .physicallyBased
      // 簡易的綠色面具
        material?.diffuse.contents = UIColor(red: 0.0, green: 0.68, blue: 0.37, alpha: 1)
        self.geometry = geometry
    }

    required init?(coder aDecoder: NSCoder) {
        fatalError("\(#function) has not been implemented")
    }

}
```

- Using the Mask class

```swift
    var mask: Mask?

    func createFaceGenometry() {
        updateMessage(text: "Creating face geometry")

        let device = MTLCreateSystemDefaultDevice()

        let maskGeometry = ARSCNFaceGeometry(device: device!)!
        mask = Mask(geometry: maskGeometry)
    }	
```

- Connecting the mask to the anchor
  - 接下來要把mask node加到ARKit所辨識出來的face node下。

```swift
    // 當偵測到對應的ARKit中的anchor時，會自動轉化對應的sceneKit的node
    func renderer(_ renderer: SCNSceneRenderer, didAdd node: SCNNode, for anchor: ARAnchor) {
        anchorNode = node
        setupFaceNodeContent()
    }

    func setupFaceNodeContent() {
        guard let node = anchorNode else { return }
        // 先移除face node所有的child node
        node.childNodes.forEach { $0.removeFromParentNode() }
        // 確保mask node已經正確被建立
        if let content = mask {
            // mask node加到ARKit所辨識出來的face node
            node.addChildNode(content)
        }
    }
```

------

<h2 id="3">Handling updates</h2>

mask的幾何變化要根據人臉變化做即時的改變。

```Swift
// 在mask class添加update的功能
    func update(withFaceAnchor anchor: ARFaceAnchor) {
        // 確保當前geometry是ARSCNFaceGeometry
        let faceGeometry = geometry as! ARSCNFaceGeometry
        // 根據最新偵測到的anchor資訊進行更新 
        faceGeometry.update(from: anchor.geometry)
    }

// 在view controller
    func renderer(_ renderer: SCNSceneRenderer, didUpdate node: SCNNode, for anchor: ARAnchor) {
        guard let faceAnchor = anchor as? ARFaceAnchor else { return }

        updateMessage(text: "Tracking your face")
        mask?.update(withFaceAnchor: faceAnchor)
    }
```

------

<h2 id="4">Adding additional mask & Connecting the switch button</h2>

這兩節主要只是更換不同的面具，並不是主要重點。

```Swift
    // MARK: Materials setup
    func swapMaterials(maskType: MaskType) {
        guard let material = geometry?.firstMaterial! else { return }

        material.lightingModel = .physicallyBased

        // 重置materal之前的原本屬性
        material.diffuse.contents = nil
        material.normal.contents = nil
        material.transparent.contents = nil

        switch maskType {
        case .basic:
            material.lightingModel = .physicallyBased
            material.diffuse.contents = UIColor(red: 0.0, green: 0.68, blue: 0.37, alpha: 1)
        case .painted:
            material.diffuse.contents =
                "Models.scnassets/Masks/Painted/Diffuse.png"
            material.normal.contents =
                "Models.scnassets/Masks/Painted/Normal_v1.png"
            material.transparent.contents =
                "Models.scnassets/Masks/Painted/Transparency.png"
        case .zombie:
            material.diffuse.contents =
                "Models.scnassets/Masks/Zombie/Diffuse.png"
            material.normal.contents =
                "Models.scnassets/Masks/Zombie/Normal_v1.png"
        }
    }
```

------

<h2 id="5">Setting up a basic environment</h2>

- 調整環境光源，可以讓AR看起來更加真實。

```Swift
   /* default settings */
    sceneView.automaticallyUpdatesLighting = true
    sceneView.autoenablesDefaultLighting = false
    sceneView.scene.lightingEnvironment.intensity = 1.0
```



------

<h2 id=6>Adjust the lighting</h2>

當在設定ARsession的config時，會加入`isLightEstimationEnabled=true`，此時ARKit就會透過下列delegate method回傳光源的相關資訊。可以根據光源的改變，及時調整scene的光源。

```swift
    func renderer(_ renderer: SCNSceneRenderer, updateAtTime time: TimeInterval) {
        guard let estimate = session.currentFrame?.lightEstimate else { return }

        // grab the ambientIntensity and divide it by 1000. In ARKit, a value of 1000 represents neutral light.
        let intensity = estimate.ambientIntensity / 1000.0
        sceneView.scene.lightingEnvironment.intensity = intensity

        let intensityStr = String(format: "%.2f", intensity)
        let sceneLighting = String(format: "%.2f", sceneView.scene.lightingEnvironment.intensity)

        print("Intensity: \(intensityStr) - \(sceneLighting)")
    }
```

