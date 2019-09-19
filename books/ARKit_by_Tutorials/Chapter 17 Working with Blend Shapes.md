# Chapter 17: Working with Blend Shapes

### 前言

這章主要就是對豬頭進行表情的操作。

------

### 大綱

- [Animojis and blend shapes](#1)
- [Animating the brow](#2)
- [Animating the eyes](#3)
  - Right eye
  - Left eye
- [Animating the mouth](#4)

------

<h2 id="1">Animojis and blend shapes</h2>

- [blend shapes](https://developer.apple.com/documentation/arkit/arfaceanchor/2928251-blendshapes)
  - 是ARFaceAnchor中的一個屬性。
  - 是一個字典。
  - 目前53個key, 分別對應臉部的不同特徵，value是0~1，依據不同的特徵變化。

------

<h2 id="1">Animating the brow</h2>

根據blend shapes來更動眉毛的位置。

```Swift
class Pig: SCNNode {
  let occlusionNode: SCNNode

    // set up brow
    private var neutralBrowY: Float = 0
    // 從scene graph中取出brow的位置
    private lazy var browNode = childNode(withName: "brow", recursively: true)!

    // - Tag: BlendShapeAnimation
    var blendShapes: [ARFaceAnchor.BlendShapeLocation: Any] = [:] {
        didSet {
            // 從blendShapes中取出browInnerUp的數值
            guard let browInnerUp = blendShapes[.browInnerUp] as? Float else { return }
            // 設定browNode的位置
            let browHeight = (browNode.boundingBox.max.y - browNode.boundingBox.min.y)
            browNode.position.y = neutralBrowY + browHeight * browInnerUp
        }
    }

  
  
     // - Tag: ARFaceAnchor Update
   func update(withFaceAnchor anchor: ARFaceAnchor) {
         // 不段從外面取得最新的blendShapes
         blendShapes = anchor.blendShapes
   }
```



------

<h2 id="1">Animating the eyes</h2>

一樣的類似動作，現在分別針對左右兩眼處理

```swift
    // Set up right eye
    private var neutralRightEyeX: Float = 0
    private var neutralRightEyeY: Float = 0
    private lazy var eyeRightNode = childNode(withName: "eyeRight", recursively: true)!
    private lazy var pupilRightNode = childNode(withName: "pupilRight", recursively: true)!

    // Get size of pupils(瞳孔)
    private lazy var pupilWidth: Float = {
        let (min, max) = pupilRightNode.boundingBox
        return max.x - min.x
    }()
    private lazy var pupilHeight: Float = {
        let (min, max) = pupilRightNode.boundingBox
        return max.y - min.y
    }()

/////////////////////////////

            // Right eye look
            let rightPupilPos = SCNVector3(
                x: (neutralRightEyeX - pupilWidth) * (eyeLookInRight - eyeLookOutRight),
                y: (neutralRightEyeY - pupilHeight) * (eyeLookUpRight - eyeLookDownRight),
                z: pupilRightNode.position.z
            )

            pupilRightNode.position = rightPupilPos
            // Right eye blink
            eyeRightNode.scale.y = 1 - eyeBlinkRight

```



------

<h2 id="1">Animating the mouth</h2>

嘴巴

```swift
    // Set up mouth
    private var neutralMouthY: Float = 0
    private lazy var mouthNode = childNode(withName: "mouth",
                                           recursively: true)!

    // Get size of mouth
    private lazy var mouthHeight: Float = {
        let (min, max) = mouthNode.boundingBox
        return max.y - min.y
        }()

////////////////////////


            // Mouth
            mouthNode.position.y = neutralMouthY - mouthHeight * mouthOpen

```

