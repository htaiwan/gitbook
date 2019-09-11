# Chapter 9: Geometry, Textures & Lighting

## 大綱

上一章學到如何利用SceneKit在Scene中增加一個3D物件，接下來要做個比較複雜的3D物件。

要做個房間，這個房間由下列東西組成

- 房間有天花板，牆壁，地板，當然還要有個入口。
- 利用不同的textures讓物件更加真實。
- 在scene中增加光源。

------

## 摘要

- Getting Started
- [The SceneKit coordinate system](#1)
- [Textures](#2)
- [Building the portal](#3)
- [Adding the doorway](#4)
- [Placing lights](#5)

------

<h2 id="1">The SceneKit coordinate system</h2>

在之前番外篇就有提到關於座標系統，這一章最困難部分就是要清楚了解座標系統，才有辦法順利組成這個房間。

[番外篇](ARKit_by_Tutorials/番外篇 - 座標系統.md)

------

<h2 id="2">Textures</h2>

在SceneKit asset catalog已經有提供不同的textures可以使用。

------

<h2 id="3">Building the portal</h2>

準備完成這章最主要的app, 透過SceneKit建立一個栩栩如生的房間，會先分下列步驟

- **將texture image覆蓋到物件上。**

```swift
// input是個geometry並且還有對應的scaleX, scaleY
func repeatTextures(geometry: SCNGeometry, scaleX: Float, scaleY: Float) {
  // wrapS的"S"表示是對應X的座標系統
  geometry.firstMaterial?.diffuse.wrapS = SCNWrapMode.repeat
  geometry.firstMaterial?.selfIllumination.wrapS = SCNWrapMode.repeat
  geometry.firstMaterial?.normal.wrapS = SCNWrapMode.repeat
  geometry.firstMaterial?.specular.wrapS = SCNWrapMode.repeat
  geometry.firstMaterial?.emission.wrapS = SCNWrapMode.repeat
  geometry.firstMaterial?.roughness.wrapS = SCNWrapMode.repeat

  // wrapT的"T"表示是對應Y的座標系統
  geometry.firstMaterial?.diffuse.wrapT = SCNWrapMode.repeat
  geometry.firstMaterial?.selfIllumination.wrapT = SCNWrapMode.repeat
  geometry.firstMaterial?.normal.wrapT = SCNWrapMode.repeat
  geometry.firstMaterial?.specular.wrapT = SCNWrapMode.repeat
  geometry.firstMaterial?.emission.wrapT = SCNWrapMode.repeat
  geometry.firstMaterial?.roughness.wrapT = SCNWrapMode.repeat

  // contentsTransform用來設定scale transform, 單位是SCNMatrix4
  geometry.firstMaterial?.diffuse.contentsTransform = SCNMatrix4MakeScale(scaleX, scaleY, 0)
  geometry.firstMaterial?.selfIllumination.contentsTransform = SCNMatrix4MakeScale(scaleX, scaleY, 0)
  geometry.firstMaterial?.normal.contentsTransform = SCNMatrix4MakeScale(scaleX, scaleY, 0)
  geometry.firstMaterial?.specular.contentsTransform = SCNMatrix4MakeScale(scaleX, scaleY, 0)
  geometry.firstMaterial?.emission.contentsTransform = SCNMatrix4MakeScale(scaleX, scaleY, 0)
  geometry.firstMaterial?.roughness.contentsTransform = SCNMatrix4MakeScale(scaleX, scaleY, 0)
}
```

- **只有在進到房間內，才會看到天花板，地板，其餘時候要隱藏。**

```swift

func makeOuterSurfaceNode(width: CGFloat, height: CGFloat, length: CGFloat) -> SCNNode {
  // 建立房間的外層
  let outerSurface = SCNBox(width: width, height: height, length: length, chamferRadius: 0)

  // 利用很小的transparency來隱藏這個外層
  outerSurface.firstMaterial?.diffuse.contents = UIColor.white
  outerSurface.firstMaterial?.transparency = 0.000001

  // renderingOrder越大，表示越晚被render
  // portal外面時，要讓天花板跟牆是看不到，此時要將天花板跟牆的renderingOrder的值大於10
  let outerSurfaceNode = SCNNode(geometry: outerSurface)
  outerSurfaceNode.renderingOrder = 10
  
  return outerSurfaceNode
}
```

- **建立地板。**

```swift
// 建立地板
func makeFloorNode() -> SCNNode {
  // 建立地板的外層
  let outerFloorNode = makeOuterSurfaceNode(width: SURFACE_WIDTH, height: SURFACE_HEIGHT, length: SURFACE_LENGTH)

  // 設立地板外層位置
  outerFloorNode.position = SCNVector3(SURFACE_HEIGHT * 0.5, -SURFACE_HEIGHT, 0)
  let floorNode = SCNNode()
  floorNode.addChildNode(outerFloorNode)

  // 建立地板的內層
  let innerFloor = SCNBox(width: SURFACE_WIDTH, height: SURFACE_HEIGHT, length: SURFACE_LENGTH, chamferRadius: 0)

  // physicallyBased的燈光，可以讓Material的看起來更真實
  innerFloor.firstMaterial?.lightingModel = .physicallyBased
  innerFloor.firstMaterial?.diffuse.contents =
    UIImage(named:
      "Assets.scnassets/floor/textures/Floor_Diffuse.png")
  innerFloor.firstMaterial?.normal.contents =
    UIImage(named:
      "Assets.scnassets/floor/textures/Floor_Normal.png")
  innerFloor.firstMaterial?.roughness.contents =
    UIImage(named:
      "Assets.scnassets/floor/textures/Floor_Roughness.png")
  innerFloor.firstMaterial?.specular.contents =
    UIImage(named:
  "Assets.scnassets/floor/textures/Floor_Specular.png")
  innerFloor.firstMaterial?.selfIllumination.contents =
    UIImage(named:
  "Assets.scnassets/floor/textures/Floor_Gloss.png")

  // 將Textures重複採樣貼到地板內層上
  repeatTextures(geometry: innerFloor, scaleX: SCALEX, scaleY: SCALEY)

  // renderingOrder的數值比outerFloorNode高，確保當使用者在portal外面，這個FloorNode是不會被看到
  let innerFloorNode = SCNNode(geometry: innerFloor)
  innerFloorNode.renderingOrder = 100

  // 設立地板內層位置
  innerFloorNode.position = SCNVector3(SURFACE_HEIGHT * 0.5, 0, 0)
  floorNode.addChildNode(innerFloorNode)

  // 此時floorNode有兩個child nodes: outerFloorNode, innerFloorNode
  // 座標系統是以floorNode為中心的座標
  return floorNode
}

  let POSITION_Y: CGFloat = -WALL_HEIGHT*0.5
  let POSITION_Z: CGFloat = -SURFACE_LENGTH*0.5


  let floorNode = makeFloorNode()
  floorNode.position = SCNVector3(0, POSITION_Y, POSITION_Z)
  portal.addChildNode(floorNode)
```

- **建立天花板。**

```swift
// 建立天花板
func makeCeilingNode() -> SCNNode {
  // 建立天花板的外層
  let outerCeilingNode = makeOuterSurfaceNode(width: SURFACE_WIDTH, height: SURFACE_HEIGHT, length: SURFACE_LENGTH)

  // 設立天花板外層位置
  outerCeilingNode.position = SCNVector3(SURFACE_HEIGHT * 0.5, SURFACE_HEIGHT, 0)
  let ceilingNode = SCNNode()
  ceilingNode.addChildNode(outerCeilingNode)

  // 建立天花板的內層
  let innerCeiling = SCNBox(width: SURFACE_WIDTH, height: SURFACE_HEIGHT, length: SURFACE_LENGTH, chamferRadius: 0)

  // physicallyBased的燈光，可以讓Material的看起來更真實
  innerCeiling.firstMaterial?.lightingModel = .physicallyBased
  innerCeiling.firstMaterial?.diffuse.contents =
    UIImage(named:
      "Assets.scnassets/ceiling/textures/Ceiling_Diffuse.png")
  innerCeiling.firstMaterial?.emission.contents =
    UIImage(named:
      "Assets.scnassets/ceiling/textures/Ceiling_Emis.png")
  innerCeiling.firstMaterial?.normal.contents =
    UIImage(named:
      "Assets.scnassets/ceiling/textures/Ceiling_Normal.png")
  innerCeiling.firstMaterial?.specular.contents =
    UIImage(named:
      "Assets.scnassets/ceiling/textures/Ceiling_Specular.png")
  innerCeiling.firstMaterial?.selfIllumination.contents =
    UIImage(named:
      "Assets.scnassets/ceiling/textures/Ceiling_Gloss.png")

  // 將Textures重複採樣貼到天花板內層上
  repeatTextures(geometry: innerCeiling, scaleX:
    SCALEX, scaleY: SCALEY)

  let innerCeilingNode = SCNNode(geometry: innerCeiling)
  innerCeilingNode.renderingOrder = 100

  innerCeilingNode.position = SCNVector3(SURFACE_HEIGHT * 0.5, 0, 0)
  ceilingNode.addChildNode(innerCeilingNode)

  // 此時ceilingNode有兩個child nodes: outerCeilingNode, innerCeilingNode
  // 座標系統是以ceilingNode為中心的座標
  return ceilingNode
}


 let ceilingNode = makeCeilingNode()
 ceilingNode.position = SCNVector3(0, POSITION_Y+WALL_HEIGHT, POSITION_Z)
 portal.addChildNode(ceilingNode)
```

- **建立3面牆面。**

```Swift
// 建立牆壁
func makeWallNode(length: CGFloat = WALL_LENGTH, height: CGFloat = WALL_HEIGHT, maskLowerSide:Bool = false) -> SCNNode {

  // 建立外牆的geometry
  let outerWall = SCNBox(width: WALL_WIDTH, height: height, length: length, chamferRadius: 0)
  outerWall.firstMaterial?.diffuse.contents = UIColor.white
  outerWall.firstMaterial?.transparency = 0.000001

  // 建立外牆node
  let outerWallNode = SCNNode(geometry: outerWall)
  // If maskLowerSide is set to true, the outer wall is placed below the inner wall in the wall node’s coordinate system; otherwise, it’s placed above
  let multiplier: CGFloat = maskLowerSide ? -1 : 1
  outerWallNode.position = SCNVector3(WALL_WIDTH*multiplier,0,0)
  outerWallNode.renderingOrder = 10

  //
  let wallNode = SCNNode()
  wallNode.addChildNode(outerWallNode)

  // 建立內牆
  let innerWall = SCNBox(width: WALL_WIDTH, height: height, length: length, chamferRadius: 0)

  innerWall.firstMaterial?.lightingModel = .physicallyBased

  innerWall.firstMaterial?.diffuse.contents =
    UIImage(named:
      "Assets.scnassets/wall/textures/Walls_Diffuse.png")
  innerWall.firstMaterial?.metalness.contents =
    UIImage(named:
      "Assets.scnassets/wall/textures/Walls_Metalness.png")
  innerWall.firstMaterial?.roughness.contents =
    UIImage(named:
      "Assets.scnassets/wall/textures/Walls_Roughness.png")
  innerWall.firstMaterial?.normal.contents =
    UIImage(named:
      "Assets.scnassets/wall/textures/Walls_Normal.png")
  innerWall.firstMaterial?.specular.contents =
    UIImage(named:
      "Assets.scnassets/wall/textures/Walls_Spec.png")
  innerWall.firstMaterial?.selfIllumination.contents =
    UIImage(named:
      "Assets.scnassets/wall/textures/Walls_Gloss.png")

  let innerWallNode = SCNNode(geometry: innerWall)
  wallNode.addChildNode(innerWallNode)

  return wallNode
}

    let farWallNode = makeWallNode()
    farWallNode.eulerAngles = SCNVector3(0, 90.0.degreesToRadians, 0)
    farWallNode.position = SCNVector3(0, POSITION_Y+WALL_HEIGHT*0.5, POSITION_Z-SURFACE_LENGTH*0.5)
    portal.addChildNode(farWallNode)

    let rightSideWallNode = makeWallNode(maskLowerSide: true)
    rightSideWallNode.eulerAngles = SCNVector3(0, 180.0.degreesToRadians, 0)
    rightSideWallNode.position = SCNVector3(WALL_LENGTH*0.5, POSITION_Y+WALL_HEIGHT*0.5, POSITION_Z)
    portal.addChildNode(rightSideWallNode)

    let leftSideWallNode = makeWallNode(maskLowerSide: true)
    leftSideWallNode.position = SCNVector3(-WALL_LENGTH*0.5, POSITION_Y+WALL_HEIGHT*0.5, POSITION_Z)
    portal.addChildNode(leftSideWallNode)
```



------



<h2 id="4">Adding the doorway</h2>

接下來，是比較複雜的是建立房間的入口處，現在房間已經有三面牆，還差一面牆，但這面牆要有入口，要像個"ㄇ"字型的入口，但目前沒辦法做到將一個box的立方體挖個洞變成"ㄇ"字型，所以只好將"ㄇ"拆成"|","-","|"三段，然後組合起來。

```Swift
let DOOR_WIDTH:CGFloat = 1.0
let DOOR_HEIGHT:CGFloat = 2.4


  func addDoorway(node: SCNNode) {
    let halfWallLength: CGFloat = WALL_LENGTH * 0.5
    let frontHalfWallLength: CGFloat = (WALL_LENGTH - DOOR_WIDTH) * 0.5

    let rightDoorSideNode = makeWallNode(length: frontHalfWallLength)
    rightDoorSideNode.eulerAngles = SCNVector3(0,270.0.degreesToRadians, 0)
    rightDoorSideNode.position = SCNVector3(halfWallLength - 0.5 * DOOR_WIDTH,
                                            POSITION_Y+WALL_HEIGHT*0.5,
                                            POSITION_Z+SURFACE_LENGTH*0.5)
    node.addChildNode(rightDoorSideNode)

    let leftDoorSideNode = makeWallNode(length: frontHalfWallLength)
    leftDoorSideNode.eulerAngles = SCNVector3(0, 270.0.degreesToRadians, 0)
    leftDoorSideNode.position = SCNVector3(-halfWallLength + 0.5 * frontHalfWallLength,
                                           POSITION_Y+WALL_HEIGHT*0.5,
                                           POSITION_Z+SURFACE_LENGTH*0.5)
    node.addChildNode(leftDoorSideNode)

    let aboveDoorNode = makeWallNode(length: DOOR_WIDTH,
                                     height: WALL_HEIGHT - DOOR_HEIGHT)

    aboveDoorNode.eulerAngles = SCNVector3(0, 270.0.degreesToRadians, 0)
    aboveDoorNode.position =
      SCNVector3(0,
                 POSITION_Y+(WALL_HEIGHT-DOOR_HEIGHT)*0.5+DOOR_HEIGHT,
                 POSITION_Z+SURFACE_LENGTH*0.5)
    node.addChildNode(aboveDoorNode)
  }
```

------



<h2 id="5">Placing lights</h2>

最後一步，就是置放光源，讓整個場景更加的真實。

```swift
  func placeLightSource(rootNode: SCNNode) {
    // 建立SCNLight並設定強度，預設強度1000
    let light = SCNLight()
    light.intensity = 10
    // omnidirectional 無向性光源, 光源向四面八方發射
    light.type = .omni
    // 建立node, 並指定其光源
    let lightNode = SCNNode()
    lightNode.light = light
    // 指定位置
    lightNode.position = SCNVector3(0,
                                    POSITION_Y+WALL_HEIGHT,
                                    POSITION_Z)
    rootNode.addChildNode(lightNode)
  }	
```

